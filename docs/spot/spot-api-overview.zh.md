

## **API 基本信息**
* 本篇列出接口的baseurl: **https://sapi.asterdex.com**
* 所有接口的响应都是 JSON 格式。
* 所有时间、时间戳均为UNIX时间，单位为**毫秒**。

## **API Key 设置**
* 很多接口需要API Key才可以访问.
* 设置API Key的同时，为了安全，建议设置IP访问白名单.
* **永远不要把你的API key/secret告诉给任何人**

<aside class="warning">
如果不小心泄露了API key，请立刻删除此Key, 并可以另外生产新的Key.
</aside>

### 注意事项
* TESTUSDT 或任何其他以 TEST 开头的交易对仅用于 Aster 的内部测试。请不要在这些以 TEST 开头的交易品种上进行交易。Aster 对因交易这些交易对而造成的资金损失不承担任何责任。但是，如果您遇到问题，您可以随时联系支持人员，我们将尽力帮助您收回资金。


### HTTP 返回代码
* HTTP `4XX` 错误码用于指示错误的请求内容、行为、格式。问题在于请求者。
* HTTP `403` 错误码表示违反WAF限制(Web应用程序防火墙)。
* HTTP `429` 错误码表示警告访问频次超限，即将被封IP。
* HTTP `418` 表示收到429后继续访问，IP已经被封禁。
* HTTP `5XX` 错误码用于指示Aster服务侧的问题。    

### 接口错误代码
* 使用接口 `/api/v1`, 每个接口都有可能抛出异常; 
> API的错误代码返回形式如下:
```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

### 接口的基本信息

* `GET` 方法的接口, 参数必须在 `query string`中发送。
* `POST`, `PUT`, 和 `DELETE` 方法的接口,参数可以在内容形式为`application/x-www-form-urlencoded`的 `query string` 中发送，也可以在 `request body` 中发送。 
* 对参数的顺序不做要求。

---
## **访问限制**
### 访问限制基本信息

* 在 `/api/v1/exchangeInfo` `rateLimits` 数组中包含与交易的有关REQUEST_WEIGHT和ORDERS速率限制相关的对象。这些在 `限制种类 (rateLimitType)` 下的 `枚举定义` 部分中进一步定义。
* 违反任何一个速率限制时，将返回429。

### IP 访问限制
* 每个请求的回报中包含一个`X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)`的头，其中包含当前IP所有请求的已使用权重。
* 每一个接口均有一个相应的权重(weight)，有的接口根据参数不同可能拥有不同的权重。越消耗资源的接口权重就会越大。
* 收到429时，您有责任停止发送请求，不得滥用API。
* **收到429后仍然继续违反访问限制，会被封禁IP，并收到418错误码**
* 频繁违反限制，封禁时间会逐渐延长，**从最短2分钟到最长3天**。
* `Retry-After`的头会与带有418或429的响应发送，并且会给出**以秒为单位**的等待时长(如果是429)以防止禁令，或者如果是418，直到禁令结束。
* **访问限制是基于IP的，而不是API Key**

<aside class="notice">
建议您尽可能多地使用websocket消息获取相应数据，以减少请求带来的访问限制压力。
</aside>


### 下单频率限制
* 每个成功的下单回报将包含一个`X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)`的头，其中包含当前账户已用的下单限制数量。
* 当下单数超过限制时，会收到带有429但不含`Retry-After`头的响应。请检查 `GET api/v1/exchangeInfo` 的下单频率限制 (rateLimitType = ORDERS) 并等待封禁时间结束。
* 被拒绝或不成功的下单并不保证回报中包含以上头内容。
* **下单频率限制是基于每个账户计数的。**

### WEB SOCKET 连接限制

* Websocket服务器每秒最多接受5个消息。消息包括:
	* PING帧
	* PONG帧
	* JSON格式的消息, 比如订阅, 断开订阅.
* 如果用户发送的消息超过限制，连接会被断开连接。反复被断开连接的IP有可能被服务器屏蔽。
* 单个连接最多可以订阅 **1024** 个Streams。


---
## **接口鉴权类型**
* 每个接口都有自己的鉴权类型，鉴权类型决定了访问时应当进行何种鉴权。
* 鉴权类型会在本文档中各个接口名称旁声明，如果没有特殊声明即默认为 `NONE`。
* 如果需要 API-keys，应当在HTTP头中以 `X-MBX-APIKEY`字段传递。
* API-keys 与 secret-keys **是大小写敏感的**。
* 默认 API-keys 可访问所有鉴权路径.

鉴权类型 | 描述
------------ | ------------
NONE | 不需要鉴权的接口
TRADE | 需要有效的 API-Key 和签名
USER_DATA | 需要有效的 API-Key 和签名
USER_STREAM | 需要有效的 API-Key
MARKET_DATA | 需要有效的 API-Key


* `TRADE` 和`USER_DATA` 接口是 签名(SIGNED)接口.

---
## **SIGNED (TRADE AND USER_DATA) Endpoint security**
* 调用`SIGNED` 接口时，除了接口本身所需的参数外，还需要在`query string` 或 `request body`中传递 `signature`, 即签名参数。
* 签名使用`HMAC SHA256`算法. API-KEY所对应的API-Secret作为 `HMAC SHA256` 的密钥，其他所有参数作为`HMAC SHA256`的操作对象，得到的输出即为签名。
* `签名` **大小写不敏感**.
* "totalParams"定义为与"request body"串联的"query string"。

### 时间同步安全
* 签名接口均需要传递 `timestamp`参数，其值应当是请求发送时刻的unix时间戳(毫秒)。
* 服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间空窗值可以通过发送可选参数 `recvWindow`来定义。

> 逻辑伪代码如下:
```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow)
  {
    // process request
  } 
  else 
  {
    // reject request
  }
```

**关于交易时效性** 互联网状况并不完全稳定可靠,因此你的程序本地到Aster服务器的时延会有抖动。这是我们设置`recvWindow`的目的所在，如果你从事高频交易，对交易时效性有较高的要求，可以灵活设置`recvWindow`以达到你的要求。

<aside class="notice">
推荐使用5秒以下的 recvWindow! 最多不能超过 60秒!
</aside>

### POST /api/v1/order 的示例
以下是在linux bash环境下使用 echo openssl 和curl工具实现的一个调用接口下单的示例 apikey、secret仅供示范

Key | Value
------------ | ------------
apiKey | 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795
secretKey | fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313


参数 | 取值
------------ | ------------
symbol | BNBUSDT
side | BUY
type | LIMIT
timeInForce | GTC
quantity | 5
price | 1.1
recvWindow | 5000
timestamp | 1756187806000


#### 示例 1: 所有参数通过 request body 发送

> **Example 1**
> **HMAC SHA256 signature:**
```shell
    $ echo -n "symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000" | openssl dgst -sha256 -hmac "fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313"
    (stdin)= e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0
```


> **curl command:**
```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795" -X POST 'https://sapi.asterdex.com/api/v1/order' -d 'symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000&signature=e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0'
```

* **requestBody:** 

symbol=BNBUSDT   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=5   
&price=1.1   
&recvWindow=5000   
&timestamp=1756187806000


#### 示例 2: 所有参数通过 query string 发送

> **Example 2**
> **HMAC SHA256 signature:**
```shell
    $ echo -n "symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000" | openssl dgst -sha256 -hmac "fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313"
    (stdin)= e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0 
```
> **curl command:**
```shell
    (HMAC SHA256)
   $ curl -H "X-MBX-APIKEY: 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795" -X POST 'https://sapi.asterdex.com/api/v1/order?symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000&signature=e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0'
```
* **queryString:**  

symbol=BNBUSDT   
&side=BUY   
&type=LIMIT   
&timeInForce=GTC   
&quantity=5   
&price=1.1   
&recvWindow=5000   
&timestamp=1756187806000

---

## **公开 API 参数**
### 术语

这里的术语适用于全部文档，建议特别是新手熟读，也便于理解。

* `base asset` 指一个交易对的交易对象，即写在靠前部分的资产名, 比如`BTCUSDT`, `BTC`是`base asset`。
* `quote asset` 指一个交易对的定价资产，即写在靠后部分的资产名, 比如`BTCUSDT`, `USDT`是`quote asset`。

### 枚举定义
**交易对状态 (状态 status):**

* TRADING 交易中


**交易对类型:**

* SPOT 现货

**订单状态 (状态 status):**

状态 | 描述
-----------| --------------
`NEW` | 订单被交易引擎接受
`PARTIALLY_FILLED`| 部分订单被成交
`FILLED` | 订单完全成交
`CANCELED` | 用户撤销了订单
`REJECTED`       | 订单没有被交易引擎接受，也没被处理
`EXPIRED` | 订单被交易引擎取消, 比如 <br/>LIMIT FOK 订单没有成交<br/>市价单没有完全成交<br/>交易所维护期间被取消的订单


**订单类型 (orderTypes, type):**

* LIMIT 限价单
* MARKET 市价单
* STOP 限价止损单
* TAKE_PROFIT 限价止盈单
* STOP_MARKET 市价止损单
* TAKE_PROFIT_MARKET 市价止盈单

**订单返回类型 (newOrderRespType):**

* ACK
* RESULT
* FULL

**订单方向 (方向 side):**

* BUY 买入
* SELL 卖出

**有效方式 (timeInForce):**

这里定义了订单多久能够失效

Status | Description
-----------| --------------
`GTC` | 成交为止 <br> 订单会一直有效，直到被成交或者取消。
`IOC` | 无法立即成交的部分就撤销 <br> 订单在失效前会尽量多的成交。
`FOK` | 无法全部立即成交就撤销 <br> 如果无法全部成交，订单会失效。
`GTX` | 直到挂单成交 <br> 限价只挂单。
`HIDDEN` | 该类型订单在订单薄里不可见

**K线间隔:**

m -> 分钟; h -> 小时; d -> 天; w -> 周; M -> 月

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**限制种类 (rateLimitType)**

> REQUEST_WEIGHT
```json
    {
      "rateLimitType": "REQUEST_WEIGHT",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 1200
    }
```

> ORDERS
```json
    {
      "rateLimitType": "ORDERS",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 100
    }
```


* REQUEST_WEIGHT 单位时间请求权重之和上限

* ORDERS 单位时间下单次数限制


**限制间隔 (interval)**

* MINUTE 分

---
## **过滤器**
过滤器，即Filter，定义了一系列交易规则。
共有两类，分别是针对交易对的过滤器`symbol filters`，和针对整个交易所的过滤器`exchange filters`(暂不支持)

### 交易对过滤器  

#### PRICE_FILTER 价格过滤器

> **/exchangeInfo 响应中的格式:**
```javascript
  {                     
    "minPrice": "556.72",
    "maxPrice": "4529764",
    "filterType": "PRICE_FILTER",
    "tickSize": "0.01"   
  }
```

`价格过滤器` 用于检测订单中 `price` 参数的合法性。包含以下三个部分:

* `minPrice` 定义了 `price`/`stopPrice` 允许的最小值。
* `maxPrice` 定义了 `price`/`stopPrice` 允许的最大值。
* `tickSize` 定义了 `price`/`stopPrice` 的步进间隔，即price必须等于minPrice+(tickSize的整数倍)

以上每一项均可为0，为0时代表这一项不再做限制。

逻辑伪代码如下:

* `price` >= `minPrice` 
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0


#### PERCENT_PRICE 价格振幅过滤器

> **/exchangeInfo 响应中的格式:**
```javascript
  {                    
	"multiplierDown": "0.9500",
	"multiplierUp": "1.0500",
	"multiplierDecimal": "4",
	"filterType": "PERCENT_PRICE"
  }
```

`PERCENT_PRICE`过滤器基于指数价格来定义价格的有效范围。   

为了通过"价格百分比"，"价格"必须符合以下条件：

* `price` <=`indexPrice` *`multiplierUp`
* `price`> =`indexPrice` *`multiplierDown`


#### LOT_SIZE 订单尺寸

> **/exchangeInfo 响应中的格式:**
```javascript
  {
    "stepSize": "0.00100000",
    "filterType": "LOT_SIZE",
    "maxQty": "100000.00000000",
    "minQty": "0.00100000"
  }
```

Lots是拍卖术语，`LOT_SIZE` 过滤器对订单中的 `quantity` 也就是数量参数进行合法性检查。包含三个部分:

* `minQty` 表示 `quantity` 允许的最小值。
* `maxQty` 表示 `quantity` 允许的最大值。
* `stepSize` 表示 `quantity` 允许的步进值。

逻辑伪代码如下:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0




#### MARKET_LOT_SIZE 市价订单尺寸

> ***/exchangeInfo 响应中的格式:**
```javascript
  {
    "stepSize": "0.00100000",
    "filterType": "MARKET_LOT_SIZE"
	"maxQty": "100000.00000000",
	"minQty": "0.00100000"
  }
```


`MARKET_LOT_SIZE`过滤器为交易对上的`MARKET`订单定义了`数量`(即拍卖中的"手数")规则。 共有3部分：

* `minQty`定义了允许的最小`quantity`。
* `maxQty`定义了允许的最大数量。
* `stepSize`定义了可以增加/减少数量的间隔。

为了通过`market lot size`，`quantity`必须满足以下条件：

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0









