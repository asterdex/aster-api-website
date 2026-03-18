## 接口基本信息

* 本接口可能需要用户的 AGENT。
* 如需创建 AGENT，请[点击此处](https://www.asterdex-testnet.com/en/api-wallet)，并在顶部切换至 `Pro API`
* 接口 baseurl：**https://fapi.asterdex-testnet.com**
* 所有接口的响应都是 JSON 格式。
* 响应中如有数组，数组元素以时间升序排列，越早的数据越提前。
* 所有时间、时间戳均为 UNIX 时间，单位为毫秒。
* 所有数据类型采用 JAVA 的数据类型定义。

### HTTP 返回代码

* HTTP `4XX` 错误码用于指示错误的请求内容、行为、格式。
* HTTP `403` 错误码表示违反 WAF 限制（Web 应用程序防火墙）。
* HTTP `429` 错误码表示警告访问频次超限，即将被封 IP。
* HTTP `418` 表示收到 429 后继续访问，于是被封了。
* HTTP `5XX` 错误码用于指示 Aster Finance 服务侧的问题。
* HTTP `503` 表示 API 服务端已经向业务核心提交了请求但未能获取响应，特别需要注意的是其不代表请求失败，而是未知。很可能已经得到了执行，也有可能执行失败，需要做进一步确认。

### 接口错误代码

* 每个接口都有可能抛出异常

> ***异常响应格式如下：***

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* 具体的错误码及其解释在**错误代码**页面。

### 接口的基本信息

* `GET` 方法的接口，参数必须在 `query string` 中发送。
* `POST`、`PUT` 和 `DELETE` 方法的接口，在 `request body` 中发送（content type `application/x-www-form-urlencoded`）。
* 对参数的顺序不做要求。

## 访问限制

* `/fapi/v3/exchangeInfo` 接口中 `rateLimits` 数组里包含有 REST 接口的访问限制，包括带权重的访问频次限制、下单速率限制。本篇`枚举定义`章节有限制类型的进一步说明。
* 违反上述任何一个访问限制都会收到 HTTP 429，这是一个警告。

<aside class="notice">
请注意，若用户被认定利用频繁挂撤单且故意低效交易意图发起攻击行为，Aster Finance 有权视具体情况进一步加强对其访问限制。
</aside>

### IP 访问限制

* 每个请求将包含一个 `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` 的响应头，其中包含当前 IP 所有请求的已使用权重。
* 每个路由都有一个"权重"，该权重确定每个接口计数的请求数。较重的接口和对多个交易对进行操作的接口将具有较重的"权重"。
* 收到 429 时，您有责任停止发送请求，不得持续请求 API。
* **重复违反访问限制或收到 429 后未停止请求，将导致 IP 被自动封禁（HTTP 418）。**
* IP 封禁时间会随违规次数累计增长，**最短 2 分钟，最长 3 天**。
* **访问限制基于 IP，而非 API Key。**

<aside class="notice">
强烈建议尽可能使用 WebSocket 数据流获取数据，既能保证消息的实时性，又能减少请求带来的访问限制压力。
</aside>

### 下单频率限制

* 每次下单响应将包含 `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` 响应头，其中包含该账户当前已使用的下单频率限制数量。
* 被拒绝或未成功的订单不保证包含上述响应头。
* **下单频率限制基于账户计算。**

**严肃的交易关乎时效性。** 网络可能不稳定，导致请求到达服务器所需的时间有所波动。通过使用 `recvWindow`，您可以指定请求必须在一定毫秒数内被处理，否则将被服务器拒绝。

<aside class="notice">
建议使用 5000 毫秒以内的 recvWindow！
</aside>

## 接口鉴权类型

* 每个接口都有自己的鉴权类型，决定了访问时应当进行何种鉴权。
* 如需鉴权，请求体中必须包含 signer 参数。

| 鉴权类型 | 描述 |
| ------------- | ----------------------------------------- |
| NONE | 不需要鉴权的接口 |
| TRADE | 需要有效的 signer 和 signature |
| USER_DATA | 需要有效的 signer 和 signature |
| USER_STREAM | 需要有效的 signer 和 signature |
| MARKET_DATA | 需要有效的 signer 和 signature |

## 鉴权签名载荷

| 参数 | 描述 |
| --------- | ---------------------------------- |
| user | 主账户钱包地址 |
| signer | API 钱包地址 |
| nonce | 当前时间戳，单位为微秒 |
| signature | 签名 |

## 需要签名的接口

* 鉴权类型：TRADE、USER_DATA、USER_STREAM、MARKET_DATA
* 将接口参数转换为字符串后，按照 key 值的 ASCII 顺序排序生成最终字符串。注意：签名过程中所有参数值均需以字符串形式处理。
* 生成字符串后，将其与鉴权签名参数 user、signer 和 nonce 结合，使用 Web3 的 ABI 参数编码生成字节码。
* 使用 Keccak 算法对字节码生成哈希值。
* 使用 **API 钱包地址**的私钥，通过 Web3 的 ECDSA 签名算法对哈希值进行签名，生成最终 signature。

## POST /fapi/v3/order 的示例

#### 所有参数均通过 request body 发送（Python 3.9.6）

#### 示例：以下参数为 API 注册信息，user、signer、privateKey 仅供示范（privateKey 为 signer 的私钥）

| Key | Value | 描述 |
| ---------- | ------------------------------------------------------------------ | ---------- |
| user | 0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e | 登录钱包地址 |
| signer | 0x21cF8Ae13Bb72632562c6Fff438652Ba1a151bb0 | [点击此处](https://www.asterdex-testnet.com/en/api-wallet) |
| privateKey | 0x4fd0a42218f3eae43a6ce26d22544e986139a01e5b34a62db53757ffca81bae1 | [点击此处](https://www.asterdex-testnet.com/en/api-wallet) |

#### 示例：nonce 参数为当前系统微秒值，超过系统时间或落后系统时间超过 5 秒为非法请求。

```python
#python
nonce = math.trunc(time.time()*1000000)
print(nonce)
#1748310859508867
```

```java
//java
Instant now = Instant.now();
long microsecond = now.getEpochSecond() * 1000000 + now.getNano() / 1000;
```

#### 示例：以下为业务请求参数

```python

typed_data = {
  "types": {
    "EIP712Domain": [
      {"name": "name", "type": "string"},
      {"name": "version", "type": "string"},
      {"name": "chainId", "type": "uint256"},
      {"name": "verifyingContract", "type": "address"}
    ],
    "Message": [
      { "name": "msg", "type": "string" }
    ]
  },
  "primaryType": "Message",
  "domain": {
    "name": "AsterSignTransaction",
    "version": "1",
    "chainId": 714,
    "verifyingContract": "0x0000000000000000000000000000000000000000"
  },
  "message": {
    "msg": "$msg"
  }
}           
```

#### 示例：通过 query string 发送（以 Python 为例）

> **方式一：通过 query string**

```python
import random
import time

import requests
from eth_account.messages import encode_typed_data
from eth_account import Account

from eip712 import typed_data


def get_url(my_dict) -> str:
       return '&'.join(f'{key}={str(value)}'for key, value in my_dict.items())

def send_by_url() :
    import random
    import time

    import requests
    from eth_account.messages import encode_typed_data
    from eth_account import Account

    test_net_end_point = 'https://fapi.asterdex-testnet.com/fapi/v3/order'
    param = 'symbol=BTCUSDT&side=BUY&type=LIMIT&quantity=1&price=9000&timeInForce=GTC'

    nonce = int(time.time()) * 1_000_000 + random.randint(0, 999999)
    param += '&nonce=' + str(nonce)
    param += '&user=' + '0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e'
    param += '&signer=' + '0xbEf084ad26b44F002C3b55DB3bf7241003dABdab'

    typed_data['message']['msg'] = param

    message = encode_typed_data(full_message=typed_data)

    private_key = "*"
    signed = Account.sign_message(message, private_key=private_key)
    print(signed.signature.hex())

    url = test_net_end_point + '?' + param + '&signature=' + signed.signature.hex()

    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'User-Agent': 'PythonApp/1.0'
    }
    print(url)
    res = requests.post(url, headers=headers)

    print(res.text)

if __name__ == '__main__':
    send_by_url()


```

> **方式二：通过 request body**

```python
import random
import time

import requests
from eth_account.messages import encode_typed_data
from eth_account import Account

from eip712 import typed_data


def get_url(my_dict) -> str:
       return '&'.join(f'{key}={str(value)}'for key, value in my_dict.items())

def send_by_body() :
       my_dict = {"symbol": "BTCUSDT", "positionSide": "BOTH", "type": "LIMIT", "side": "BUY",
                  "timeInForce": "GTC", "quantity": "0.01", "price": "81000"}

       test_net_end_point = 'https://fapi.asterdex-testnet.com/fapi/v3/order'

       nonce = int(time.time()) * 1_000_000 + random.randint(0, 999999)
       my_dict['nonce'] = str(nonce)
       my_dict['user'] = '0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e'
       my_dict['signer'] = '0xbEf084ad26b44F002C3b55DB3bf7241003dABdab'

       content = get_url(my_dict)

       typed_data['message']['msg'] = content

       message = encode_typed_data(full_message=typed_data)

       private_key = "*"
       signed = Account.sign_message(message, private_key=private_key)
       print(signed.signature.hex())

       my_dict['signature'] = signed.signature.hex()

       headers = {
           'Content-Type': 'application/x-www-form-urlencoded',
           'User-Agent': 'PythonApp/1.0'
       }
       print(my_dict)
       res = requests.post(test_net_end_point, data=my_dict, headers=headers)

       print(res.text)

if __name__ == '__main__':
    send_by_body()
```

## 公开API参数

### 术语解释

* `base asset` 指一个交易对的交易对象，即写在靠前部分的资产名，比如 `BTCUSDT` 中的 `BTC`。
* `quote asset` 指一个交易对的定价资产，即写在靠后部分的资产名，比如 `BTCUSDT` 中的 `USDT`。

### 枚举定义

**交易对类型:**

* FUTURE

**合约类型 (contractType):**

* PERPETUAL

**合约状态 (contractStatus, status):**

* PENDING_TRADING
* TRADING
* PRE_SETTLE
* SETTLING
* CLOSE

**订单状态 (status):**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* REJECTED
* EXPIRED

**订单种类 (orderTypes, type):**

* LIMIT
* MARKET
* STOP
* STOP_MARKET
* TAKE_PROFIT
* TAKE_PROFIT_MARKET
* TRAILING_STOP_MARKET

**订单方向 (side):**

* BUY
* SELL

**持仓方向 (positionSide):**

* BOTH
* LONG
* SHORT

**有效方式 (timeInForce):**

* GTC - 成交为止
* IOC - 无法立即成交的部分就撤销
* FOK - 无法全部立即成交就撤销
* GTX - 无法成为挂单方就撤销（Post Only）
* HIDDEN - HIDDEN 该类型订单在订单簿里不可见

**条件价格触发类型 (workingType):**

* MARK_PRICE
* CONTRACT_PRICE

**响应类型 (newOrderRespType):**

* ACK
* RESULT

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

**限制类型 (rateLimitType):**

> REQUEST_WEIGHT

```javascript
{
  	"rateLimitType": "REQUEST_WEIGHT",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 2400
  }
```

> ORDERS

```javascript
{
  	"rateLimitType": "ORDERS",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 1200
   }
```

* REQUEST_WEIGHT
* ORDERS

**限制间隔 (interval):**

* MINUTE

## 过滤器

过滤器，即 Filter，定义某个交易对或整个 exchange 上各种交易规则。

### 交易对过滤器

#### PRICE_FILTER 价格过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
  }
```

`PRICE_FILTER` 定义某个交易对的 `price` 字段的合法范围，由三部分组成：

* `minPrice` 定义 `price`/`stopPrice` 允许的最小值；当值为 0 时，该规则失效。
* `maxPrice` 定义 `price`/`stopPrice` 允许的最大值；当值为 0 时，该规则失效。
* `tickSize` 定义 `price`/`stopPrice` 的步进间隔；当值为 0 时，该规则失效。

以上任意变量可设为 0，表示该条规则不生效。价格/停止价格需满足以下条件（已启用的规则）：

* `price` >= `minPrice`
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0

#### LOT_SIZE 订单尺寸过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

`LOT_SIZE` 过滤器对订单中的 `quantity` 字段进行限制，由三部分组成：

* `minQty` 表示 `quantity` 允许的最小值。
* `maxQty` 表示 `quantity` 允许的最大值。
* `stepSize` 表示 `quantity` 允许的步进值。

`quantity` 需满足以下条件：

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0

#### MARKET_LOT_SIZE 市价订单尺寸过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "MARKET_LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

`MARKET_LOT_SIZE` 过滤器对市价订单中的 `quantity` 字段进行限制，规则同 `LOT_SIZE`。

`quantity` 需满足以下条件：

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0

#### MAX_NUM_ORDERS 最大订单数过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "MAX_NUM_ORDERS",
    "limit": 200
  }
```

`MAX_NUM_ORDERS` 过滤器定义某个账户在一个交易对上允许挂单的最大数量。

注意，普通订单和"algo"订单均计入此过滤器。

#### MAX_NUM_ALGO_ORDERS 最大条件订单数过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "MAX_NUM_ALGO_ORDERS",
    "limit": 100
  }
```

`MAX_NUM_ALGO_ORDERS` 过滤器定义某账户在单个交易对上允许同时挂出所有类型条件单的最大数量。

条件单类型包括：`STOP`、`STOP_MARKET`、`TAKE_PROFIT`、`TAKE_PROFIT_MARKET` 和 `TRAILING_STOP_MARKET`。

#### PERCENT_PRICE 价格振幅过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.1500",
    "multiplierDown": "0.8500",
    "multiplierDecimal": 4
  }
```

`PERCENT_PRICE` 过滤器基于标记价格定义合法的价格范围。

价格需满足以下条件：

* 买单：`price` <= `markPrice` * `multiplierUp`
* 卖单：`price` >= `markPrice` * `multiplierDown`

#### MIN_NOTIONAL 最小名义价值过滤器

> **/exchangeInfo 格式：**

```javascript
{
    "filterType": "MIN_NOTIONAL",
    "notional": "1"
  }
```

`MIN_NOTIONAL` 过滤器定义某个交易对订单所允许的最小名义价值（成交额）。
订单名义价值为 `price` * `quantity`。
由于市价单没有价格，使用标记价格计算。

---
