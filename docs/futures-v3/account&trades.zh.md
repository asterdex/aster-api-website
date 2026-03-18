## **概览**

<aside class="warning">
考虑到剧烈行情下, RESTful接口可能存在查询延迟，我们强烈建议您优先从Websocket user data stream推送的消息来获取订单，成交，仓位等信息。
</aside>


## **更改持仓模式(TRADE)**

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v3/positionSide/dual 
``

变换用户在 ***所有symbol*** 合约上的持仓模式：双向持仓或单向持仓。   

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------
dualSidePosition | STRING   | YES      | "true": 双向持仓模式；"false": 单向持仓模式



## **查询持仓模式(USER_DATA)**

> **响应:**

```javascript
{
	"dualSidePosition": true // "true": 双向持仓模式；"false": 单向持仓模式
}
```

``
GET /fapi/v3/positionSide/dual 
``

查询用户目前在 ***所有symbol*** 合约上的持仓模式：双向持仓或单向持仓。     

**权重:**
30

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------


## **更改联合保证金模式(TRADE)**

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v3/multiAssetsMargin 
``

变换用户在 ***所有symbol*** 合约上的联合保证金模式：开启或关闭联合保证金模式。   

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------
multiAssetsMargin | STRING   | YES      | "true": 联合保证金模式开启；"false": 联合保证金模式关闭



## **查询联合保证金模式(USER_DATA)**

> **响应:**

```javascript
{
	"multiAssetsMargin": true // "true": 联合保证金模式开启；"false": 联合保证金模式关闭
}
```

``
GET /fapi/v3/multiAssetsMargin 
``

查询用户目前在 ***所有symbol*** 合约上的联合保证金模式。      

**权重:**
30

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------


## **下单 (TRADE)**


> **响应:**

```javascript
{
 	"clientOrderId": "testOrder", // 用户自定义的订单号
 	"cumQty": "0",
 	"cumQuote": "0", // 成交金额
 	"executedQty": "0", // 成交量
 	"orderId": 22542179, // 系统订单号
 	"avgPrice": "0.00000",	// 平均成交价
 	"origQty": "10", // 原始委托数量
 	"price": "0", // 委托价格
 	"reduceOnly": false, // 仅减仓
 	"side": "SELL", // 买卖方向
 	"positionSide": "SHORT", // 持仓方向
 	"status": "NEW", // 订单状态
 	"stopPrice": "0", // 触发价，对`TRAILING_STOP_MARKET`无效
 	"closePosition": false,   // 是否条件全平仓
 	"symbol": "BTCUSDT", // 交易对
 	"timeInForce": "GTC", // 有效方法
 	"type": "TRAILING_STOP_MARKET", // 订单类型
 	"origType": "TRAILING_STOP_MARKET",  // 触发前订单类型
 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
 	"updateTime": 1566818724722, // 更新时间
 	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
}
```

``
POST /fapi/v3/order  
``

**权重:**
1

**参数:**

名称              |  类型   | 是否必需   | 描述
---------------- | ------- | -------- | ---
symbol           | STRING  | YES      | 交易对
side             | ENUM    | YES      | 买卖方向 `SELL`, `BUY`
positionSide     | ENUM	    | NO       | 持仓方向，单向持仓模式下非必填，默认且仅可填`BOTH`;在双向持仓模式下必填,且仅可选择 `LONG` 或 `SHORT`  
type             | ENUM    | YES      | 订单类型 `LIMIT`, `MARKET`, `STOP`, `TAKE_PROFIT`, `STOP_MARKET`, `TAKE_PROFIT_MARKET`, `TRAILING_STOP_MARKET`
reduceOnly       | STRING  | NO       | `true`, `false`; 非双开模式下默认`false`；双开模式下不接受此参数； 使用`closePosition`不支持此参数。
quantity         | DECIMAL | NO     	 | 下单数量,使用`closePosition`不支持此参数。
price            | DECIMAL | NO       | 委托价格
newClientOrderId | STRING  | NO       | 用户自定义的订单号，不可以重复出现在挂单中。如空缺系统会自动赋值。必须满足正则规则 `^[\.A-Z\:/a-z0-9_-]{1,36}$`
stopPrice        | DECIMAL | NO       | 触发价, 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数
closePosition    | STRING  | NO       | `true`, `false`；触发后全部平仓，仅支持`STOP_MARKET`和`TAKE_PROFIT_MARKET`；不与`quantity`合用；自带只平仓效果，不与`reduceOnly` 合用
activationPrice  | DECIMAL | NO       | 追踪止损激活价格，仅`TRAILING_STOP_MARKET` 需要此参数, 默认为下单当前市场价格(支持不同`workingType`)
callbackRate     | DECIMAL | NO       | 追踪止损回调比例，可取值范围[0.1, 5],其中 1代表1% ,仅`TRAILING_STOP_MARKET` 需要此参数
timeInForce      | ENUM    | NO       | 有效方法
workingType      | ENUM    | NO       | stopPrice 触发类型: `MARK_PRICE`(标记价格), `CONTRACT_PRICE`(合约最新价). 默认 `CONTRACT_PRICE`
priceProtect | STRING | NO | 条件单触发保护："TRUE","FALSE", 默认"FALSE". 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数
newOrderRespType | ENUM    | NO       | "ACK", "RESULT", 默认 "ACK"

根据 order `type`的不同，某些参数强制要求，具体如下:

Type                 |           强制要求的参数
----------------------------------- | ----------------------------------
`LIMIT`                             | `timeInForce`, `quantity`, `price`
`MARKET`                            | `quantity`
`STOP`, `TAKE_PROFIT`               | `quantity`,  `price`, `stopPrice`
`STOP_MARKET`, `TAKE_PROFIT_MARKET` | `stopPrice`
`TRAILING_STOP_MARKET`              | `callbackRate`



* 条件单的触发必须:
	
	* 如果订单参数`priceProtect`为true:
		* 达到触发价时，`MARK_PRICE`(标记价格)与`CONTRACT_PRICE`(合约最新价)之间的价差不能超过改symbol触发保护阈值
		* 触发保护阈值请参考接口`GET /fapi/v3/exchangeInfo` 返回内容相应symbol中"triggerProtect"字段

	* `STOP`, `STOP_MARKET` 止损单:
		* 买入: 最新合约价格/标记价格高于等于触发价`stopPrice`
		* 卖出: 最新合约价格/标记价格低于等于触发价`stopPrice`
	* `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 止盈单:
		* 买入: 最新合约价格/标记价格低于等于触发价`stopPrice`
		* 卖出: 最新合约价格/标记价格高于等于触发价`stopPrice`

	* `TRAILING_STOP_MARKET` 跟踪止损单:
		* 买入: 当合约价格/标记价格区间最低价格低于激活价格`activationPrice`,且最新合约价格/标记价高于等于最低价设定回调幅度。
		* 卖出: 当合约价格/标记价格区间最高价格高于激活价格`activationPrice`,且最新合约价格/标记价低于等于最高价设定回调幅度。

* `TRAILING_STOP_MARKET` 跟踪止损单如果遇到报错 ``{"code": -2021, "msg": "Order would immediately trigger."}``    
表示订单不满足以下条件:
	* 买入: 指定的`activationPrice` 必须小于 latest price
	* 卖出: 指定的`activationPrice` 必须大于 latest price

* `newOrderRespType` 如果传 `RESULT`:
	* `MARKET` 订单将直接返回成交结果；
	* 配合使用特殊 `timeInForce` 的 `LIMIT` 订单将直接返回成交或过期拒绝结果。

* `STOP_MARKET`, `TAKE_PROFIT_MARKET` 配合 `closePosition`=`true`:
	* 条件单触发依照上述条件单触发逻辑
	* 条件触发后，平掉当时持有所有多头仓位(若为卖单)或当时持有所有空头仓位(若为买单)
	* 不支持 `quantity` 参数
	* 自带只平仓属性，不支持`reduceOnly`参数
	* 双开模式下,`LONG`方向上不支持`BUY`; `SHORT` 方向上不支持`SELL`


## **测试下单接口 (TRADE)**


> **响应:**

```javascript
字段与下单接口一致，但均为无效值
```


``
POST /fapi/v3/order/test 
``

用于测试订单请求，但不会提交到撮合引擎

**权重:**
1

**参数:**

参考 `POST /fapi/v3/order`



## **批量下单 (TRADE)**


> **响应:**

```javascript
[
	{
	 	"clientOrderId": "testOrder", // 用户自定义的订单号
	 	"cumQty": "0",
	 	"cumQuote": "0", // 成交金额
	 	"executedQty": "0", // 成交量
	 	"orderId": 22542179, // 系统订单号
	 	"avgPrice": "0.00000",	// 平均成交价
	 	"origQty": "10", // 原始委托数量
	 	"price": "0", // 委托价格
	 	"reduceOnly": false, // 仅减仓
	 	"side": "SELL", // 买卖方向
	 	"positionSide": "SHORT", // 持仓方向
	 	"status": "NEW", // 订单状态
	 	"stopPrice": "0", // 触发价，对`TRAILING_STOP_MARKET`无效
	 	"closePosition": false,   // 是否条件全平仓
	 	"symbol": "BTCUSDT", // 交易对
	 	"timeInForce": "GTC", // 有效方法
	 	"type": "TRAILING_STOP_MARKET", // 订单类型
	 	"origType": "TRAILING_STOP_MARKET",  // 触发前订单类型
	 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
	  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
	 	"updateTime": 1566818724722, // 更新时间
	 	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
	 	"priceProtect": false            // 是否开启条件单触发保护
	},
	{
		"code": -2022, 
		"msg": "ReduceOnly Order is rejected."
	}
]
```

``
POST /fapi/v3/batchOrders  
``

**权重:**
5

**参数:**


名称              |  类型   | 是否必需   | 描述
---------------- | ------- | -------- | ----
batchOrders |	list<JSON> | 	YES |	订单列表，最多支持5个订单

**其中``batchOrders``应以list of JSON格式填写订单参数**

名称              |  类型   | 是否必需   | 描述
---------------- | ------- | -------- | ----
symbol           | STRING  | YES      | 交易对
side             | ENUM    | YES      | 买卖方向 `SELL`, `BUY`
positionSide     | ENUM	    | NO       | 持仓方向，单向持仓模式下非必填，默认且仅可填`BOTH`;在双向持仓模式下必填,且仅可选择 `LONG` 或 `SHORT`   
type             | ENUM    | YES      | 订单类型 `LIMIT`, `MARKET`, `STOP`, `TAKE_PROFIT`, `STOP_MARKET`, `TAKE_PROFIT_MARKET`, `TRAILING_STOP_MARKET`
reduceOnly       | STRING  | NO       | `true`, `false`; 非双开模式下默认`false`；双开模式下不接受此参数。
quantity         | DECIMAL | YES      | 下单数量
price            | DECIMAL | NO       | 委托价格
newClientOrderId | STRING  | NO       | 用户自定义的订单号，不可以重复出现在挂单中。如空缺系统会自动赋值. 必须满足正则规则 `^[\.A-Z\:/a-z0-9_-]{1,36}$`
stopPrice        | DECIMAL | NO       | 触发价, 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数
activationPrice  | DECIMAL | NO       | 追踪止损激活价格，仅`TRAILING_STOP_MARKET` 需要此参数, 默认为下单当前市场价格(支持不同`workingType`)
callbackRate     | DECIMAL | NO       | 追踪止损回调比例，可取值范围[0.1, 4],其中 1代表1% ,仅`TRAILING_STOP_MARKET` 需要此参数
timeInForce      | ENUM    | NO       | 有效方法
workingType      | ENUM    | NO       | stopPrice 触发类型: `MARK_PRICE`(标记价格), `CONTRACT_PRICE`(合约最新价). 默认 `CONTRACT_PRICE`
priceProtect | STRING | NO | 条件单触发保护："TRUE","FALSE", 默认"FALSE". 仅 `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET` 需要此参数
newOrderRespType | ENUM    | NO       | "ACK", "RESULT", 默认 "ACK"


* 具体订单条件规则，与普通下单一致
* 批量下单采取并发处理，不保证订单撮合顺序
* 批量下单的返回内容顺序，与订单列表顺序一致




## **期货现货互转 (TRANSFER)**

> **响应:**

```javascript
{
    "tranId": 21841, //交易id
    "status": "SUCCESS" //状态
}
```

``
POST /fapi/v3/asset/wallet/transfer  (TRANSFER)
``

**权重:**
5

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
| ---------------- | ------- | -------- | ---- |
| amount | DECIMAL | YES | 数量 |
| asset | STRING | YES | 资产 |
| clientTranId | STRING | YES | 交易id |
| kindType | STRING | YES | 交易类型 |

* kindType 取值为FUTURE_SPOT(期货转现货),SPOT_FUTURE(现货转期货)

## **查询订单 (USER_DATA)**


> **响应:**

```javascript
{
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",					// 成交金额
  	"executedQty": "0",					// 成交量
  	"orderId": 1573346959,				// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",						// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", 			// 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSDT",				// 交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020",			// 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",					// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
}
```

``
GET /fapi/v3/order 
``

查询订单状态

* 请注意，如果订单满足如下条件，不会被查询到：
	* 订单的最终状态为 `CANCELED` 或者 `EXPIRED`, **并且** 
	* 订单没有任何的成交记录, **并且**
	* 订单生成时间 + 7天 < 当前时间

**权重:**
1

**参数:**

名称        |  类型  | 是否必需 | 描述
----------------- | ------ | -------- | ----
symbol            | STRING | YES      | 交易对
orderId           | LONG   | NO       | 系统订单号
origClientOrderId | STRING | NO       | 用户自定义的订单号

注意:

* 至少需要发送 `orderId` 与 `origClientOrderId`中的一个


## **撤销订单 (TRADE)**

> **响应:**

```javascript
{
 	"clientOrderId": "myOrder1", // 用户自定义的订单号
 	"cumQty": "0",
 	"cumQuote": "0", // 成交金额
 	"executedQty": "0", // 成交量
 	"orderId": 283194212, // 系统订单号
 	"origQty": "11", // 原始委托数量
 	"price": "0", // 委托价格
	"reduceOnly": false, // 仅减仓
	"side": "BUY", // 买卖方向
	"positionSide": "SHORT", // 持仓方向
 	"status": "CANCELED", // 订单状态
 	"stopPrice": "9300", // 触发价，对`TRAILING_STOP_MARKET`无效
 	"closePosition": false,   // 是否条件全平仓
 	"symbol": "BTCUSDT", // 交易对
 	"timeInForce": "GTC", // 有效方法
 	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
 	"type": "TRAILING_STOP_MARKET", // 订单类型
 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
 	"updateTime": 1571110484038, // 更新时间
 	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
}
```

``
DELETE /fapi/v3/order  
``

**权重:**
1

**Parameters:**

名称               |  类型   | 是否必需  |        描述
----------------- | ------ | -------- | ------------------
symbol            | STRING | YES      | 交易对
orderId           | LONG   | NO       | 系统订单号
origClientOrderId | STRING | NO       | 用户自定义的订单号

`orderId` 与 `origClientOrderId` 必须至少发送一个


## **撤销全部订单 (TRADE)**

> **响应:**

```javascript
{
	"code": "200", 
	"msg": "The operation of cancel all open order is done."
}
```

``
DELETE /fapi/v3/allOpenOrders  
``

**权重:**
1

**Parameters:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol     | STRING | YES      | 交易对


## **批量撤销订单 (TRADE)**

> **响应:**

```javascript
[
	{
	 	"clientOrderId": "myOrder1", // 用户自定义的订单号
	 	"cumQty": "0",
	 	"cumQuote": "0", // 成交金额
	 	"executedQty": "0", // 成交量
	 	"orderId": 283194212, // 系统订单号
	 	"origQty": "11", // 原始委托数量
	 	"price": "0", // 委托价格
		"reduceOnly": false, // 仅减仓
		"side": "BUY", // 买卖方向
		"positionSide": "SHORT", // 持仓方向
	 	"status": "CANCELED", // 订单状态
	 	"stopPrice": "9300", // 触发价，对`TRAILING_STOP_MARKET`无效
	 	"closePosition": false,   // 是否条件全平仓
	 	"symbol": "BTCUSDT", // 交易对
	 	"timeInForce": "GTC", // 有效方法
	 	"origType": "TRAILING_STOP_MARKET", // 触发前订单类型
 		"type": "TRAILING_STOP_MARKET", // 订单类型
	 	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  		"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
	 	"updateTime": 1571110484038, // 更新时间
	 	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
	 	"priceProtect": false            // 是否开启条件单触发保护
	},
	{
		"code": -2011,
		"msg": "Unknown order sent."
	}
]
```

``
DELETE /fapi/v3/batchOrders  
``

**权重:**
1

**Parameters:**

  名称          |      类型      | 是否必需 |       描述
--------------------- | -------------- | -------- | ----------------
symbol                | STRING         | YES      | 交易对
orderIdList           | LIST\<LONG\>   | NO       | 系统订单号, 最多支持10个订单 <br/> 比如`[1234567,2345678]`
origClientOrderIdList | LIST\<STRING\> | NO       | 用户自定义的订单号, 最多支持10个订单 <br/> 比如`["my_id_1","my_id_2"]` 需要encode双引号。逗号后面没有空格。

`orderIdList` 与 `origClientOrderIdList` 必须至少发送一个，不可同时发送


## **倒计时撤销所有订单 (TRADE)**

> **响应:**

```javascript
{
	"symbol": "BTCUSDT", 
	"countdownTime": "100000"
}
```


``
POST /fapi/v3/countdownCancelAll  
``

**权重:**
10

**Parameters:**

  名称          |      类型      | 是否必需 |       描述
--------------------- | -------------- | -------- | ----------------
symbol | STRING | YES |
countdownTime | LONG | YES | 倒计时。 1000 表示 1 秒； 0 表示取消倒计时撤单功能。

* 该接口可以被用于确保在倒计时结束时撤销指定symbol上的所有挂单。 在使用这个功能时，接口应像心跳一样在倒计时内被反复调用，以便可以取消既有的倒计时并开始新的倒数计时设置。

* 用法示例：
	以30s的间隔重复此接口，每次倒计时countdownTime设置为120000(120s)。   
	如果在120秒内未再次调用此接口，则您指定symbol上的所有挂单都会被自动撤销。   
	如果在120秒内以将countdownTime设置为0，则倒数计时器将终止，自动撤单功能取消。
	
* 系统会**大约每10毫秒**检查一次所有倒计时情况，因此请注意，使用此功能时应考虑足够的冗余。    
我们不建议将倒记时设置得太精确或太小。





## **查询当前挂单 (USER_DATA)**

> **响应:**

```javascript

{
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"status": "NEW",					// 订单状态
  	"positionSide": "SHORT", // 持仓方向
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSDT",				// 交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
}
```

``
GET /fapi/v3/openOrder  
``

请小心使用不带symbol参数的调用

**权重: 1**


**参数:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol | STRING | YES | 交易对
orderId | LONG | NO | 系统订单号
origClientOrderId | STRING | NO | 用户自定义的订单号

* `orderId` 与 `origClientOrderId` 中的一个为必填参数
* 查询的订单如果已经成交或取消，将返回报错 "Order does not exist."


## **查看当前全部挂单 (USER_DATA)**

> **响应:**

```javascript
[
  {
  	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", // 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,   // 是否条件全平仓
  	"symbol": "BTCUSDT",				// 交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
  }
]
```

``
GET /fapi/v3/openOrders  
``

请小心使用不带symbol参数的调用

**权重:**
- 带symbol ***1***
- 不带 ***40***

**参数:**

   名称    |  类型  | 是否必需 |  描述
---------- | ------ | -------- | ------
symbol     | STRING | NO      | 交易对

* 不带symbol参数，会返回所有交易对的挂单



## **查询所有订单(包括历史订单) (USER_DATA)**


> **响应:**

```javascript
[
  {
   	"avgPrice": "0.00000",				// 平均成交价
  	"clientOrderId": "abc",				// 用户自定义的订单号
  	"cumQuote": "0",						// 成交金额
  	"executedQty": "0",					// 成交量
  	"orderId": 1917641,					// 系统订单号
  	"origQty": "0.40",					// 原始委托数量
  	"origType": "TRAILING_STOP_MARKET",	// 触发前订单类型
  	"price": "0",					// 委托价格
  	"reduceOnly": false,				// 是否仅减仓
  	"side": "BUY",						// 买卖方向
  	"positionSide": "SHORT", // 持仓方向
  	"status": "NEW",					// 订单状态
  	"stopPrice": "9300",					// 触发价，对`TRAILING_STOP_MARKET`无效
  	"closePosition": false,  			// 是否条件全平仓
  	"symbol": "BTCUSDT",				// 交易对
  	"time": 1579276756075,				// 订单时间
  	"timeInForce": "GTC",				// 有效方法
  	"type": "TRAILING_STOP_MARKET",		// 订单类型
  	"activatePrice": "9020", // 跟踪止损激活价格, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"priceRate": "0.3",	// 跟踪止损回调比例, 仅`TRAILING_STOP_MARKET` 订单返回此字段
  	"updateTime": 1579276756075,		// 更新时间
  	"workingType": "CONTRACT_PRICE", // 条件价格触发类型
 	"priceProtect": false            // 是否开启条件单触发保护
  }
]
```

``
GET /fapi/v3/allOrders 
``

* 请注意，如果订单满足如下条件，不会被查询到：
	* 订单的最终状态为 `CANCELED` 或者 `EXPIRED`, **并且** 
	* 订单没有任何的成交记录, **并且**
	* 订单生成时间 + 7天 < 当前时间

**权重:**
5 

**Parameters:**

   名称    |  类型  | 是否必需 |                      描述
---------- | ------ | -------- | -----------------------------------------------
symbol     | STRING | YES      | 交易对
orderId    | LONG   | NO       | 只返回此orderID及之后的订单，缺省返回最近的订单
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值:500 最大值:1000

* 查询时间范围最大不得超过7天
* 默认查询最近7天内的数据



## **账户余额v3 (USER_DATA)**

> **响应:**

```javascript
[
 	{
 		"accountAlias": "SgsR",    // 账户唯一识别码
 		"asset": "USDT",		// 资产
 		"balance": "122607.35137903",	// 总余额
 		"crossWalletBalance": "23.72469206", // 全仓余额
  		"crossUnPnl": "0.00000000"  // 全仓持仓未实现盈亏
  		"availableBalance": "23.72469206",       // 下单可用余额
  		"maxWithdrawAmount": "23.72469206",     // 最大可转出余额
  		"marginAvailable": true,    // 是否可用作联合保证金
  		"updateTime": 1617939110373
	}
]
```

``
GET /fapi/v3/balance 
``

**Weight:**
5

**Parameters:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------




## **账户信息v3 (USER_DATA)**

> **响应:**

```javascript

{
	"feeTier": 0,  // 手续费等级
 	"canTrade": true,  // 是否可以交易
 	"canDeposit": true,  // 是否可以入金
 	"canWithdraw": true, // 是否可以出金
 	"updateTime": 0,
 	"totalInitialMargin": "0.00000000",  // 但前所需起始保证金总额(存在逐仓请忽略), 仅计算usdt资产
 	"totalMaintMargin": "0.00000000",  // 维持保证金总额, 仅计算usdt资产
 	"totalWalletBalance": "23.72469206",   // 账户总余额, 仅计算usdt资产
 	"totalUnrealizedProfit": "0.00000000",  // 持仓未实现盈亏总额, 仅计算usdt资产
 	"totalMarginBalance": "23.72469206",  // 保证金总余额, 仅计算usdt资产
 	"totalPositionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格), 仅计算usdt资产
 	"totalOpenOrderInitialMargin": "0.00000000",  // 当前挂单所需起始保证金(基于最新标记价格), 仅计算usdt资产
 	"totalCrossWalletBalance": "23.72469206",  // 全仓账户余额, 仅计算usdt资产
 	"totalCrossUnPnl": "0.00000000",	// 全仓持仓未实现盈亏总额, 仅计算usdt资产
 	"availableBalance": "23.72469206",       // 可用余额, 仅计算usdt资产
 	"maxWithdrawAmount": "23.72469206"     // 最大可转出余额, 仅计算usdt资产
 	"assets": [
 		{
 			"asset": "USDT",	 	//资产
 			"walletBalance": "23.72469206",  //余额
		   	"unrealizedProfit": "0.00000000",  // 未实现盈亏
		   	"marginBalance": "23.72469206",  // 保证金余额
		   	"maintMargin": "0.00000000",	// 维持保证金
		   	"initialMargin": "0.00000000",  // 当前所需起始保证金
		   	"positionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格)
		   	"openOrderInitialMargin": "0.00000000", // 当前挂单所需起始保证金(基于最新标记价格)
		   	"crossWalletBalance": "23.72469206",  //全仓账户余额
		   	"crossUnPnl": "0.00000000" // 全仓持仓未实现盈亏
		   	"availableBalance": "23.72469206",       // 可用余额
		   	"maxWithdrawAmount": "23.72469206",     // 最大可转出余额
		   	"marginAvailable": true,   // 是否可用作联合保证金
		   	"updateTime": 1625474304765  //更新时间
		},
		{
 			"asset": "BUSD",	 	//资产
 			"walletBalance": "103.12345678",  //余额
		   	"unrealizedProfit": "0.00000000",  // 未实现盈亏
		   	"marginBalance": "103.12345678",  // 保证金余额
		   	"maintMargin": "0.00000000",	// 维持保证金
		   	"initialMargin": "0.00000000",  // 当前所需起始保证金
		   	"positionInitialMargin": "0.00000000",  // 持仓所需起始保证金(基于最新标记价格)
		   	"openOrderInitialMargin": "0.00000000", // 当前挂单所需起始保证金(基于最新标记价格)
		   	"crossWalletBalance": "103.12345678",  //全仓账户余额
		   	"crossUnPnl": "0.00000000" // 全仓持仓未实现盈亏
		   	"availableBalance": "103.12345678",       // 可用余额
		   	"maxWithdrawAmount": "103.12345678",     // 最大可转出余额
		   	"marginAvailable": true,   // 否可用作联合保证金
		   	"updateTime": 0  // 更新时间
	       }
	],
 	"positions": [  // 头寸，将返回所有市场symbol。
 		//根据用户持仓模式展示持仓方向，即单向模式下只返回BOTH持仓情况，双向模式下只返回 LONG 和 SHORT 持仓情况
 		{
		 	"symbol": "BTCUSDT",  // 交易对
		   	"initialMargin": "0",	// 当前所需起始保证金(基于最新标记价格)
		   	"maintMargin": "0",	//维持保证金
		   	"unrealizedProfit": "0.00000000",  // 持仓未实现盈亏
		   	"positionInitialMargin": "0",  // 持仓所需起始保证金(基于最新标记价格)
		   	"openOrderInitialMargin": "0",  // 当前挂单所需起始保证金(基于最新标记价格)
		   	"leverage": "100",	// 杠杆倍率
		   	"isolated": true,  // 是否是逐仓模式
		   	"entryPrice": "0.00000",  // 持仓成本价
		   	"maxNotional": "250000",  // 当前杠杆下用户可用的最大名义价值
		   	"positionSide": "BOTH",  // 持仓方向
		   	"positionAmt": "0",		 // 持仓数量
		   	"updateTime": 0         // 更新时间 
		}
  	]
}
```


``
GET /fapi/v3/account 
``

**权重:**
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------




## **调整开仓杠杆 (TRADE)**

> **响应:**

```javascript
{
 	"leverage": 21,	// 杠杆倍数
 	"maxNotionalValue": "1000000", // 当前杠杆倍数下允许的最大名义价值
 	"symbol": "BTCUSDT"	// 交易对
}
```

``
POST /fapi/v3/leverage 
``

调整用户在指定symbol合约的开仓杠杆。

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |            描述
---------- | ------ | -------- | ---------------------------
symbol     | STRING | YES      | 交易对
leverage   | INT    | YES      | 目标杠杆倍数：1 到 125 整数


## **变换逐全仓模式 (TRADE)**

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v3/marginType 
``

变换用户在指定symbol合约上的保证金模式：逐仓或全仓。

**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |       描述
---------- | ------ | -------- | -----------------
symbol     | STRING | YES      | 交易对
marginType | ENUM   | YES      | 保证金模式 ISOLATED(逐仓), CROSSED(全仓)


## **调整逐仓保证金 (TRADE)**

> **响应:**

```javascript
{
	"amount": 100.0,
  	"code": 200,
  	"msg": "Successfully modify position margin.",
  	"type": 1
}
```

``
POST /fapi/v3/positionMargin 
``

针对逐仓模式下的仓位，调整其逐仓保证金资金。

**权重:**
1

**参数:**

   名称    |  类型   | 是否必需 |                 描述
---------- | ------- | -------- | ------------------------------------
symbol     | STRING  | YES      | 交易对
positionSide| ENUM   | NO		  | 持仓方向，单向持仓模式下非必填，默认且仅可填`BOTH`;在双向持仓模式下必填,且仅可选择 `LONG` 或 `SHORT` 
amount     | DECIMAL | YES      | 保证金资金
type       | INT     | YES      | 调整方向 1: 增加逐仓保证金，2: 减少逐仓保证金

* 只针对逐仓symbol 与 positionSide(如有)


## **逐仓保证金变动历史 (TRADE)**

> **响应:**

```javascript
[
	{
		"amount": "23.36332311", // 数量
	  	"asset": "USDT", // 资产
	  	"symbol": "BTCUSDT", // 交易对
	  	"time": 1578047897183, // 时间
	  	"type": 1，	// 调整方向
	  	"positionSide": "BOTH"  // 持仓方向
	},
	{
		"amount": "100",
	  	"asset": "USDT",
	  	"symbol": "BTCUSDT",
	  	"time": 1578047900425,
	  	"type": 1，
	  	"positionSide": "LONG" 
	}
]
```

``
GET /fapi/v3/positionMargin/history 
``



**权重:**
1

**参数:**

   名称    |  类型  | 是否必需 |                 描述
---------- | ------ | -------- | ------------------------------------
symbol     | STRING | YES      | 交易对
type       | INT    | NO       | 调整方向 1: 增加逐仓保证金，2: 减少逐仓保证金
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值: 500




## **用户持仓风险v3 (USER_DATA)**

> **响应:**

> 单向持仓模式下：

```javascript
[
  	{
  		"entryPrice": "0.00000", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "0.00000000",	// 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "0", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxNotionalValue": "20000000", // 当前杠杆倍数允许的名义价值上限
  		"positionAmt": "0.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSDT", // 交易对
  		"unRealizedProfit": "0.00000000", // 持仓未实现盈亏
  		"positionSide": "BOTH", // 持仓方向
  		"updateTime": 1625474304765   // 更新时间
  	}
]
```

> 双向持仓模式下：

```javascript
[
  	{
  		"entryPrice": "6563.66500", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "15517.54150468", // 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "5930.78", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxNotionalValue": "20000000", // 当前杠杆倍数允许的名义价值上限
  		"positionAmt": "20.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSDT", // 交易对
  		"unRealizedProfit": "2316.83423560" // 持仓未实现盈亏
  		"positionSide": "LONG", // 持仓方向
  		"updateTime": 1625474304765  // 更新时间
  	},
  	{
  		"entryPrice": "0.00000", // 开仓均价
  		"marginType": "isolated", // 逐仓模式或全仓模式
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "5413.95799991", // 逐仓保证金
  		"leverage": "10", // 当前杠杆倍数
  		"liquidationPrice": "7189.95", // 参考强平价格
  		"markPrice": "6679.50671178",	// 当前标记价格
  		"maxNotionalValue": "20000000", // 当前杠杆倍数允许的名义价值上限
  		"positionAmt": "-10.000", // 头寸数量，符号代表多空方向, 正数为多，负数为空
  		"symbol": "BTCUSDT", // 交易对
  		"unRealizedProfit": "-1156.46711780" // 持仓未实现盈亏
  		"positionSide": "SHORT", // 持仓方向
  		"updateTime": 1625474304765  //更新时间
  	}  	
]
```

``
GET /fapi/v3/positionRisk 
``

**权重:**
5

**参数:**

   名称    | 类型 | 是否必需 | 描述
---------- | ---- | -------- | ----
symbol     | STRING | NO     |


**注意**    
请与账户推送信息`ACCOUNT_UPDATE`配合使用，以满足您的及时性和准确性需求。




## **账户成交历史 (USER_DATA)**


> **响应:**

```javascript
[
  {
  	"buyer": false,	// 是否是买方
  	"commission": "-0.07819010", // 手续费
  	"commissionAsset": "USDT", // 手续费计价单位
  	"id": 698759,	// 交易ID
  	"maker": false,	// 是否是挂单方
  	"orderId": 25851813, // 订单编号
  	"price": "7819.01",	// 成交价
  	"qty": "0.002",	// 成交量
  	"quoteQty": "15.63802",	// 成交额
  	"realizedPnl": "-0.91539999",	// 实现盈亏
  	"side": "SELL",	// 买卖方向
  	"positionSide": "SHORT",  // 持仓方向
  	"symbol": "BTCUSDT", // 交易对
  	"time": 1569514978020 // 时间
  }
]
```

``
GET /fapi/v3/userTrades  
``

获取某交易对的成交历史

**权重:**
5

**参数:**

   名称    |  类型  | 是否必需 |                     描述
---------- | ------ | -------- | --------------------------------------------
symbol     | STRING | YES      | 交易对
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
fromId     | LONG   | NO       | 返回该fromId及之后的成交，缺省返回最近的成交
limit      | INT    | NO       | 返回的结果集数量 默认值:500 最大值:1000.

* 如果`startTime` 和 `endTime` 均未发送, 只会返回最近7天的数据。
* startTime 和 endTime 的最大间隔为7天


## **获取账户损益资金流水(USER_DATA)**

> **响应:**

```javascript
[
	{
    	"symbol": "", // 交易对，仅针对涉及交易对的资金流
    	"incomeType": "TRANSFER",	// 资金流类型
    	"income": "-0.37500000", // 资金流数量，正数代表流入，负数代表流出
    	"asset": "USDT", // 资产内容
    	"info":"TRANSFER", // 备注信息，取决于流水类型
    	"time": 1570608000000, // 时间
    	"tranId":"9689322392",		// 划转ID
    	"tradeId":""					// 引起流水产生的原始交易ID
	},
	{
   		"symbol": "BTCUSDT",
    	"incomeType": "COMMISSION", 
    	"income": "-0.01000000",
    	"asset": "USDT",
    	"info":"COMMISSION",
    	"time": 1570636800000,
    	"tranId":"9689322392",		
    	"tradeId":"2059192"					
	}
]
```

``
GET /fapi/v3/income 
``

**权重:**
30

**参数:**

   名称    |  类型  | 是否必需 |                                              描述
---------- | ------ | -------- | -----------------------------------------------------------------------------------------------
symbol     | STRING | NO       | 交易对
incomeType | STRING | NO       | 收益类型 "TRANSFER"，"WELCOME_BONUS", "REALIZED_PNL"，"FUNDING_FEE", "COMMISSION", "INSURANCE_CLEAR", and "MARKET_MERCHANT_RETURN_REWARD"
startTime  | LONG   | NO       | 起始时间
endTime    | LONG   | NO       | 结束时间
limit      | INT    | NO       | 返回的结果集数量 默认值:100 最大值:1000

* 如果`startTime` 和 `endTime` 均未发送, 只会返回最近7天的数据。
* 如果`incomeType`没有发送，返回所有类型账户损益资金流水。
* "trandId" 在相同用户的同一种收益流水类型中是唯一的。


## **杠杆分层标准 (USER_DATA)**


> **响应:**

```javascript
[
    {
        "symbol": "ETHUSDT",
        "brackets": [
            {
                "bracket": 1,   // 层级
                "initialLeverage": 75,  // 该层允许的最高初始杠杆倍数
                "notionalCap": 10000,  // 该层对应的名义价值上限
                "notionalFloor": 0,  // 该层对应的名义价值下限 
                "maintMarginRatio": 0.0065, // 该层对应的维持保证金率
                "cum":0 // 速算数
            },
        ]
    }
]
```

> **或** (若发送symbol)

```javascript

{
    "symbol": "ETHUSDT",
    "brackets": [
        {
            "bracket": 1,
            "initialLeverage": 75,
            "notionalCap": 10000,
            "notionalFloor": 0,
            "maintMarginRatio": 0.0065,
            "cum":0
        },
    ]
}
```


``
GET /fapi/v3/leverageBracket
``


**权重:** 1

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol	| STRING | NO



## **持仓ADL队列估算 (USER_DATA)**


> **响应:**

```javascript
[
	{
		"symbol": "ETHUSDT", 
		"adlQuantile": 
			{
				// 对于全仓状态下的双向持仓模式的交易对，会返回 "LONG", "SHORT" 和 "HEDGE", 其中"HEDGE"的存在仅作为标记;如果多空均有持仓的情况下,"LONG"和"SHORT"应返回共同计算后相同的队列分数。
				"LONG": 3,  
				"SHORT": 3, 
				"HEDGE": 0   // HEDGE 仅作为指示出现，请忽略数值
			}
		},
 	{
 		"symbol": "BTCUSDT", 
 		"adlQuantile": 
 			{
 				// 对于单向持仓模式或者是逐仓状态下的双向持仓模式的交易对，会返回 "LONG", "SHORT" 和 "BOTH" 分别表示不同持仓方向上持仓的adl队列分数
 				"LONG": 1, 	// 双开模式下多头持仓的ADL队列估算分
 				"SHORT": 2, 	// 双开模式下空头持仓的ADL队列估算分
 				"BOTH": 0		// 单开模式下持仓的ADL队列估算分
 			}
 	}
 ]
```

``
GET /fapi/v3/adlQuantile
``


**权重:** 5

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol	| STRING | NO

* 每30秒更新数据

* 队列分数0，1，2，3，4，分数越高说明在ADL队列中的位置越靠前

* 对于单向持仓模式或者是逐仓状态下的双向持仓模式的交易对，会返回 "LONG", "SHORT" 和 "BOTH" 分别表示不同持仓方向上持仓的adl队列分数

* 对于全仓状态下的双向持仓模式的交易对，会返回 "LONG", "SHORT" 和 "HEDGE", 其中"HEDGE"的存在仅作为标记;其中如果多空均有持仓的情况下,"LONG"和"SHORT"返回共同计算后相同的队列分数。


## **用户强平单历史 (USER_DATA)**


> **响应:**

```javascript
[
  {
  	"orderId": 6071832819, 
  	"symbol": "BTCUSDT", 
  	"status": "FILLED", 
  	"clientOrderId": "autoclose-1596107620040000020", 
  	"price": "10871.09", 
  	"avgPrice": "10913.21000", 
  	"origQty": "0.001", 
  	"executedQty": "0.001", 
  	"cumQuote": "10.91321", 
  	"timeInForce": "IOC", 
  	"type": "LIMIT", 
  	"reduceOnly": false, 
  	"closePosition": false, 
  	"side": "SELL", 
  	"positionSide": "BOTH", 
  	"stopPrice": "0", 
  	"workingType": "CONTRACT_PRICE", 
  	"origType": "LIMIT", 
  	"time": 1596107620044, 
  	"updateTime": 1596107620087
  }
  {
   	"orderId": 6072734303, 
   	"symbol": "BTCUSDT", 
   	"status": "FILLED", 
   	"clientOrderId": "adl_autoclose", 
   	"price": "11023.14", 
   	"avgPrice": "10979.82000", 
   	"origQty": "0.001", 
   	"executedQty": "0.001", 
   	"cumQuote": "10.97982", 
   	"timeInForce": "GTC", 
   	"type": "LIMIT", 
   	"reduceOnly": false, 
   	"closePosition": false, 
   	"side": "BUY", 
   	"positionSide": "SHORT", 
   	"stopPrice": "0", 
   	"workingType": "CONTRACT_PRICE", 
   	"origType": "LIMIT", 
   	"time": 1596110725059, 
   	"updateTime": 1596110725071
  }
]
```


``
GET /fapi/v3/forceOrders
``


**权重:** 带symbol 20, 不带symbol 50

**参数:**

  名称      |  类型  | 是否必需 |                   描述
------------- | ------ | -------- | ----------------------------------------
symbol        | STRING | NO       |
autoCloseType | ENUM   | NO       | "LIQUIDATION": 强平单, "ADL": ADL减仓单.
startTime     | LONG   | NO       |
endTime       | LONG   | NO       |
limit         | INT    | NO       | Default 50; max 100.

* 如果没有传 "autoCloseType", 强平单和ADL减仓单都会被返回
* 如果没有传"startTime", 只会返回"endTime"之前7天内的数据



## **用户手续费率 (USER_DATA)**

> **响应:**

```javascript
{
	"symbol": "BTCUSDT",
  	"makerCommissionRate": "0.0002",  // 0.02%
  	"takerCommissionRate": "0.0004"   // 0.04%
}
```

``
GET /fapi/v3/commissionRate 
``

**权重:**
20


**参数:**

名称  |  类型  | 是否必需 |  描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	





