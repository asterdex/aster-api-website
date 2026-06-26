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


## **更改STP模式(TRADE)**

> **响应:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``
POST /fapi/v3/stpMode``

变换用户在 ***所有symbol*** 合约上的STP（自成交防止）模式。

**权重:**
1

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| stpMode | ENUM | YES | `EXPIRE_TAKER`: 撤销taker订单；`EXPIRE_MAKER`: 撤销maker订单；`EXPIRE_BOTH`: 同时撤销双方订单 |
| nonce | LONG | YES | 微秒级时间戳 |
| signer | STRING | YES | API钱包地址 |
| signature | STRING | YES | 签名 |

## **查询STP模式(USER_DATA)**

> **响应:**

```javascript
{
	"stpMode": "EXPIRE_TAKER" // 当前STP模式
}
```

``
GET /fapi/v3/stpMode``

查询用户目前在 ***所有symbol*** 合约上的STP（自成交防止）模式。

**权重:**
30

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| nonce | LONG | YES | 微秒级时间戳 |
| signer | STRING | YES | API钱包地址 |
| signature | STRING | YES | 签名 |

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
pegPriceType     | ENUM    | NO       | BBO peg 模式: `COUNTERPARTY_1` 或 `QUEUE_1`。在 `LIMIT` 订单上设置此参数后，撮合引擎在触发时基于订单簿的 BBO 加 `pegOffset` 解析实际价格。默认不使用 peg。
pegOffset        | DECIMAL | NO       | 当 `pegPriceType` 已设置时，相对 BBO 的有符号偏移量。买单应为非正值（如 `-0.5`），卖单为非负值。单位与 `price` 相同，必须是 `tickSize` 的倍数。
stpMode          | ENUM    | NO       | 本订单的自成交防止（STP）模式，覆盖账户级默认设置。`EXPIRE_TAKER`：撤销taker订单；`EXPIRE_MAKER`：撤销maker订单；`EXPIRE_BOTH`：同时撤销双方订单。

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



## **修改订单 (TRADE)**

> **响应:**

```javascript
{ 
	'orderId': 405,  //订单号
	'symbol': 'GOOGLUSDT', //交易对
	'status': 'NEW', //订单状态
	'clientOrderId': 'uOLCC3aiCou3z2YL0sxWIX',  //用户自定义的订单号
	'price': '295.50',  //委托价格
	'avgPrice': '0.00000', // 平均成交价
	'origQty': '0.150',  // 原始委托数量
	'executedQty': '0', // 成交量
	'cumQty': '0', 
	'cumQuote': '0',  // 成交金额
	'timeInForce': 'GTC',  // 有效方法
	'type': 'LIMIT',  // 订单类型
	'reduceOnly': False,  // 仅减仓
	'closePosition': False,  // 是否条件全平仓
	'side': 'BUY',   //买卖方向
	'positionSide': 'BOTH',   // 持仓方向
	'stopPrice': '0',  // 触发价，对`TRAILING_STOP_MARKET`无效
	'workingType': 'CONTRACT_PRICE',  // 条件价格触发类型
	'priceProtect': False,  // 是否开启条件单触发保护
	'origType': 'LIMIT',  // 触发前订单类型
	'updateTime': 1776311274450 //更新时间
}
```

``
PUT /fapi/v3/order ``

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
---------------- | ------- | -------- | ----
orderId | LONG | NO |系统订单号
origClientOrderId | STRING | NO | 用户自定义的订单号
symbol | STRING | YES| 交易对
quantity | DECIMAL| NO | 下单数量
price | DECIMAL | NO | 委托价格

* orderId 与 origClientOrderId 必须至少发送一个，同时发送则以 order id为准
* quantity 与 price 均必须发送
* 当新订单的quantity 或 price不满足PRICE_FILTER / PERCENT_FILTER / LOT_SIZE限制，修改会被拒绝，原订单依旧被保留
订单只支持limit类型
* 同一订单修改次数最多10000次
* **BBO peg 订单**（使用 `pegPriceType` = `COUNTERPARTY_1` / `QUEUE_1` 下的订单）：撮合引擎在触发时从订单簿解析实际价格。普通 modify 无法改变 peg 解析后的价格。若需要持续追踪 BBO，请使用追单接口 `POST /fapi/v3/chase`。

## **追单 (TRADE)**

> **响应:**

```javascript
{
    "strategyId": 12345,
    "clientStrategyId": "my_chase_1",
    "symbol": "BTCUSDT",
    "side": "BUY",
    "positionSide": "BOTH",
    "quantity": "0.1",
    "quantityUnit": "BASE",
    "reduceOnly": false,
    "chaseOffset": "0.5",
    "chaseOffsetType": "ABSOLUTE",
    "maxChaseOffset": "10.0",
    "maxChaseOffsetType": "ABSOLUTE",
    "timeInForce": "GTX",
    "strategyStatus": "NEW",
    "bookTime": 1747728000000,
    "updateTime": 1747728000000
}
```

``POST /fapi/v3/chase``

下一笔**追单策略订单** —— BBO peg GTX 限价单，自动跟随最优买/卖价重新挂单。策略服务每个 tick 轮询订单簿，实时修改委托价格，使订单保持在盘口前列，直到成交或市场偏离原始 BBO 超过 `maxChaseOffset`。

**权重:** 1

**参数:**

| 名称               | 类型    | 是否必需   | 描述                                                                                                                                                              |
| ------------------ | ------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| symbol             | STRING  | YES        | 交易对。                                                                                                                                                          |
| side               | ENUM    | YES        | `BUY` 或 `SELL`。缺失/空值返回 `"Mandatory parameter 'side' was not sent, was empty/null, or malformed."`                                                          |
| positionSide       | ENUM    | NO         | 单向持仓模式默认 `BOTH`；双向持仓模式必须传 `LONG` 或 `SHORT`。                                                                                                    |
| quantityUnit       | STRING  | YES        | `BASE`（数量以标的资产计价，如 BTC）或 `QUOTE`（数量以计价资产计价，如 USDT）。`QUOTE` 时系统按 mark price 换算到 BASE。                                              |
| quantity           | DECIMAL | YES        | 数量，单位由 `quantityUnit` 决定。                                                                                                                                |
| reduceOnly         | STRING  | NO         | `"true"` 或 `"false"`（大小写不敏感）。任何其他值会被拒绝。默认 `"false"`。                                                                                        |
| chaseOffset        | DECIMAL | NO         | 距 BBO 的偏移量。默认 `"0"`（完全贴近 BBO）。必须 ≥ 0 且为 `tickSize` 的倍数。买单价 = `bid1 − chaseOffset`；卖单价 = `ask1 + chaseOffset`。                          |
| chaseOffsetType    | STRING  | NO         | `ABSOLUTE`（默认）。v1 仅支持 `ABSOLUTE`。`PERCENTAGE` 后续支持。                                                                                                  |
| maxChaseOffset     | DECIMAL | NO         | 相对原始 BBO 允许偏移的最大距离，超出后追单自动撤销。必须 > 0。若不传，则不应用基于距离的自动撤销，且所传的 `maxChaseOffsetType` 将被忽略。                          |
| maxChaseOffsetType | STRING  | NO         | `ABSOLUTE` 或 `PERCENTAGE`（默认 `ABSOLUTE`）。`ABSOLUTE`：同价格单位，必须为 `tickSize` 倍数；`PERCENTAGE`：≤ 2 位小数。                                            |
| timeInForce        | ENUM    | NO         | 默认 `GTX`（post-only）。**不允许 `NO_FILL`**，否则返回 `INVALID_TIF`。                                                                                            |
| clientStrategyId   | STRING  | NO         | 用户自定义策略 id。未传则自动生成。**长度 ≤ 28 字符**（DB 字段为 `varchar(28)`）。须满足 `^[\.A-Z\:/a-z0-9_-]{1,28}$`。                                              |

**校验规则:**

* `side` 必填。
* `reduceOnly` 仅接受 `"true"` / `"false"`（大小写不敏感）。其他值返回 `INVALID_PARAMETER`，参数名为 `reduceOnly`。
* `chaseOffset` 必须 ≥ 0 且为 `tickSize` 倍数。
* `chaseOffsetType` / `maxChaseOffsetType` 必须为 `ABSOLUTE` 或 `PERCENTAGE`；非法值返回 `INVALID_PARAMETER`（不再误用 `INVALID_CHASE_OFFSET`）。
* `chaseOffsetType` 目前仅支持 `ABSOLUTE`。`PERCENTAGE` 为合法值，但 `chaseOffset` 尚未实现，返回 `UNSUPPORTED_OPERATION`。（`maxChaseOffsetType` 两者均支持。）
* 数量 / 名义价值下限（仅开仓单；`reduceOnly` 单豁免名义价值下限）：`quantityUnit = BASE` 时，`quantity` 必须 ≥ 交易对 `marketMinQty`（否则 `QTY_LESS_THAN_MIN_QTY`），且 `quantity × markPrice` 必须 ≥ 交易对 `minNotional`（否则 `MIN_NOTIONAL`）；`quantityUnit = QUOTE` 时，计价金额必须 ≥ `minNotional`（否则 `MIN_NOTIONAL`），且 `quoteQty / markPrice` 必须 ≥ `marketMinQty`（否则 `QTY_LESS_THAN_MIN_QTY`）。
* `maxChaseOffsetType = PERCENTAGE` 时，输入值小数位数 ≤ 2（线上以 ×100 存储，如 `"1"` → 1.00%，`"100"` → 100.00%）。
* `maxChaseOffsetType = ABSOLUTE` 时，`maxChaseOffset` 必须为 `tickSize` 倍数。
* `timeInForce` 不允许 `NO_FILL`。
* `clientStrategyId` 长度必须 ≤ 28 字符。
* OI cap 校验：`quantityUnit = QUOTE` 时按 mark price 换算到 BASE 数量进行 symbol-leverage OI 档位校验，公式 `qtyBase = qtyQuote × 10^quantityDecimal / markPrice`。

**行为:**

* 初始订单以 GTX（post-only）LIMIT 形式发出，`pegPriceType = QUEUE_1`、`pegOffset` 取符号（买单为负，卖单为正）。
* 策略服务每秒轮询，将订单价格修改为 `bid1 − chaseOffset`（买单）或 `ask1 + chaseOffset`（卖单），跟随 BBO 移动。
* 当市场偏移超过 `maxChaseOffset` 时**自动撤销**，原因为 `OFFSET_CANCELLED`。
* 追单在以下情形终止：成交、用户撤单（`DELETE /fapi/v3/order`）、`maxChaseOffset` 触发。

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
GET /fapi/v3/accountWithJoinMargin 
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


## **更新用户MMP (USER_DATA)**

> **响应:**

```javascript
true
```

``POST /fapi/v3/mmp``

**权重:**
1


**参数:**

名称  |  类型  | 是否必需 |  描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	
windowTimeInMilliseconds | LONG | YES | 计算qtyLimit、valueLimit、deltaLimit的时间窗口(单位ms)
frozenTimeInMilliseconds | LONG | YES | 在frozenTimeInMilliseconds的时间内，禁止下MMP的订单
qtyLimit | LONG | FALSE | 在windowTime内，累计成交的数量上限（不区分买卖），如在windowTime内到达了设置的qtyLimit，则触发frozenTimeInMilliseconds
valueLimit | LONG | FALSE | 在windowTime内，累计成交的名义价值上限，如在windowTime内到达了设置的valueLimit，则触发frozenTimeInMilliseconds
deltaLimit | LONG | FALSE | 在windowTime内，累计持仓变化上限（买卖对冲后），如在windowTime内到达了设置的deltaLimit，则触发frozenTimeInMilliseconds


## **获取用户MMP (USER_DATA)**

> **响应:**

```javascript
[
	{
		"symbol":"BTCUSDT",
		"windowTimeInMilliseconds":5000,
		"frozenTimeInMilliseconds":10000,
		"qtyLimit":10,
		"valueLimit":200000000,
		"deltaLimit":100000000
	}
]
```

``GET /fapi/v3/mmp``

**权重:**
1


**参数:**

名称  |  类型  | 是否必需 |  描述
------------ | ------------ | ------------ | ------------
symbol | STRING | FALSE	


## **删除用户MMP (USER_DATA)**

> **响应:**

```javascript
true
```

``DELETE /fapi/v3/mmp``

**权重:**
1


**参数:**

名称  |  类型  | 是否必需 |  描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	


## **重置用户MMP (USER_DATA)**

> **响应:**

```javascript
true
```

``POST /fapi/v3/mmpReset``

**权重:**
1


**参数:**

名称  |  类型  | 是否必需 |  描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES	

## **策略下单 (TRADE)**

> **响应:**

```javascript
{
    "strategyId": 123456789,
    "clientStrategyId": "myStrategy1",
    "strategyType": "OTO",
    "strategyStatus": "WORKING",
    "updateTime": 1699900000000,
    "failureCode": 0,
    "failureReason": ""
}
```

`POST /fapi/v3/placeStrategyOrder`

下策略订单。支持 OTO（一单触发另一单）、OCO（一单取消另一单）、OTOCO（一单触发并取消另外两单）三种策略类型。

**权重:** 50

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| clientStrategyId | STRING | NO | 客户端自定义策略 ID |
| strategyType | ENUM | YES | 策略类型：`OTO`、`OCO`、`OTOCO` |
| subOrderList | JSON Array | YES | 子订单列表。`OTO` 和 `OCO` 恰好需要 2 个子订单；`OTOCO` 恰好需要 3 个 |

**`subOrderList` 中每个子订单的字段：**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| strategySubId | STRING | YES | 子订单序号，从 1 开始，必须与数组位置保持一致 |
| securityType | STRING | YES | 订单的资产类型：`USDT_FUTURES`、`COIN_FUTURES`、`OPTIONS` |
| symbol | STRING | YES | 交易对 |
| side | STRING | YES | `BUY` 或 `SELL` |
| positionSide | STRING | NO | `BOTH`、`LONG`、`SHORT`，单向持仓模式下默认 `BOTH` |
| type | STRING | YES | `LIMIT`、`MARKET`、`STOP`、`STOP_MARKET`、`TAKE_PROFIT`、`TAKE_PROFIT_MARKET`、`TRAILING_STOP_MARKET` |
| quantity | STRING | YES* | 委托数量。`closePosition=true` 时可不填 |
| price | STRING | YES* | `LIMIT`、`STOP`、`TAKE_PROFIT` 时必填 |
| stopPrice | STRING | YES* | `STOP`、`STOP_MARKET`、`TAKE_PROFIT`、`TAKE_PROFIT_MARKET` 时必填 |
| timeInForce | STRING | YES* | `LIMIT` 时必填；止损类订单可选（默认 `GTC`）。不支持 `IOC` 和 `FOK` |
| workingType | STRING | NO | `CONTRACT_PRICE` 或 `MARK_PRICE`，默认 `CONTRACT_PRICE` |
| reduceOnly | STRING | NO | 是否仅减仓 |
| closePosition | STRING | NO | 是否全部平仓 |
| priceProtect | STRING | NO | 是否开启价格保护 |
| clientOrderId | STRING | NO | 客户端自定义订单 ID |
| activationPrice | STRING | NO | `TRAILING_STOP_MARKET` 专用激活价格 |
| callbackRate | STRING | NO | `TRAILING_STOP_MARKET` 回调比例 |
| firstDrivenId | STRING | NO | 第一触发条件的驱动子订单 `strategySubId` |
| firstDrivenOn | STRING | NO | 激活第一触发条件的事件类型：`NEW`、`PARTIALLY_FILLED_OR_FILLED`、`FILLED`、`CANCELED`、`REPLACED`、`STOPPED`、`REJECTED`、`EXPIRED` |
| firstTrigger | STRING | NO | 第一触发动作：`PLACE_ORDER`、`CANCEL_ORDER` |
| secondDrivenId | STRING | NO | 第二触发条件的驱动子订单 `strategySubId` |
| secondDrivenOn | STRING | NO | 激活第二触发条件的事件类型：`NEW`、`PARTIALLY_FILLED_OR_FILLED`、`FILLED`、`CANCELED`、`REPLACED`、`STOPPED`、`REJECTED`、`EXPIRED` |
| secondTrigger | STRING | NO | 第二触发动作：`PLACE_ORDER`、`CANCEL_ORDER` |

---

## **更新策略订单 (TRADE)**

> **响应:**

```javascript
[
    {
        "strategyId": 123456789,
        "clientStrategyId": "myStrategy1",
        "strategyType": "OTO",
        "strategyStatus": "WORKING",
        "updatedSubOrder": 1,
        "updateStatus": "SUCCESS",
        "updateTime": 1699900000000,
        "failureCode": 0,
        "failureReason": ""
    }
]
```

`POST /fapi/v3/updateStrategyOrder`

修改已有策略订单中的一个或多个子订单。返回数组，每个子订单对应一条更新结果。

**权重:** 50

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| strategyId | LONG | YES | 要修改的策略订单 ID |
| strategyType | ENUM | YES | 策略类型：`OTO`、`OCO`、`OTOCO` |
| subOrderList | JSON Array | YES | 要修改的子订单列表。`OTO`/`OCO` 最多 2 个，`OTOCO` 最多 3 个 |

**`subOrderList` 中每个子订单的字段：**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| strategySubId | STRING | YES | 要修改的子订单序号 |
| securityType | STRING | YES | 资产类型 |
| symbol | STRING | YES | 交易对 |
| side | STRING | YES | `BUY` 或 `SELL` |
| positionSide | STRING | NO | `BOTH`、`LONG`、`SHORT` |
| type | STRING | YES | 订单类型 |
| quantity | STRING | NO | 新委托数量 |
| price | STRING | NO | 新价格（适用于 `LIMIT`、`STOP`、`TAKE_PROFIT`） |
| stopPrice | STRING | NO | 新止损价 |
| timeInForce | STRING | NO | 新的有效方式 |
| workingType | STRING | NO | 新的触发价格类型 |
| reduceOnly | STRING | NO | |
| closePosition | STRING | NO | |
| priceProtect | STRING | NO | |
| activationPrice | STRING | NO | 仅适用于 `TRAILING_STOP_MARKET` |
| callbackRate | STRING | NO | 仅适用于 `TRAILING_STOP_MARKET` |

---

## **查询策略当前挂单 (USER_DATA)**

> **响应:**

```javascript
{
    "strategyId": 123456789,
    "clientStrategyId": "myStrategy1",
    "strategyType": "OTO",
    "strategyStatus": "WORKING",
    "bookTime": 1699900000000,
    "updateTime": 1699900001000,
    "subOrders": [
        {
            "strategyId": 123456789,
            "orderId": 987654321,
            "clientOrderId": "myOrder1",
            "status": "NEW",
            "strategySubId": 1,
            "firstDrivenId": 0,
            "firstDrivenOn": "",
            "firstTrigger": "",
            "secondDrivenId": 0,
            "secondDrivenOn": "",
            "secondTrigger": "",
            "securityType": "FUTURE",
            "symbol": "BTCUSDT",
            "side": "BUY",
            "positionSide": "LONG",
            "type": "LIMIT",
            "timeInForce": "GTC",
            "quantity": "0.001",
            "reduceOnly": false,
            "closePosition": false,
            "price": "50000",
            "avgPrice": "0",
            "priceProtect": false,
            "stopPrice": "0",
            "activatePrice": null,
            "callbackRate": null,
            "workingType": "CONTRACT_PRICE",
            "triggerTime": 0
        }
    ]
}
```

`GET /fapi/v3/strategyOpenOrder`

查询当前挂单中的策略订单。`strategyId` 与 `clientStrategyId` 必须且只能提供其中一个。

**权重:** 5

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| strategyId | LONG | NO* | 策略订单 ID |
| clientStrategyId | STRING | NO* | 客户端自定义策略 ID |
| strategyType | STRING | YES | 策略类型：`OTO`、`OCO`、`OTOCO` |

*`strategyId` 与 `clientStrategyId` 二选一，不可同时传入。

---

## **查询策略历史订单 (USER_DATA)**

> **响应:**

```javascript
{
    "strategyId": 123456789,
    "clientStrategyId": "myStrategy1",
    "strategyType": "OTO",
    "strategyStatus": "EXPIRED",
    "bookTime": 1699800000000,
    "updateTime": 1699800060000,
    "subOrders": [
        {
            "strategyId": 123456789,
            "orderId": 987654321,
            "clientOrderId": "myOrder1",
            "status": "FILLED",
            "strategySubId": 1,
            "firstDrivenId": 0,
            "firstDrivenOn": "",
            "firstTrigger": "",
            "secondDrivenId": 0,
            "secondDrivenOn": "",
            "secondTrigger": "",
            "securityType": "FUTURE",
            "symbol": "BTCUSDT",
            "side": "BUY",
            "positionSide": "LONG",
            "type": "LIMIT",
            "timeInForce": "GTC",
            "quantity": "0.001",
            "reduceOnly": false,
            "closePosition": false,
            "price": "50000",
            "avgPrice": "50000",
            "priceProtect": false,
            "stopPrice": "0",
            "activatePrice": null,
            "callbackRate": null,
            "workingType": "CONTRACT_PRICE",
            "triggerTime": 1699800030000
        }
    ]
}
```

`GET /fapi/v3/strategyHistoryOrder`

查询历史策略订单。`strategyId` 与 `clientStrategyId` 必须且只能提供其中一个。最大可查询范围为 90 天。

**权重:** 5

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| strategyId | LONG | NO* | 策略订单 ID |
| clientStrategyId | STRING | NO* | 客户端自定义策略 ID |
| strategyType | STRING | YES | 策略类型：`OTO`、`OCO`、`OTOCO` |
| startTime | LONG | NO | 起始时间（毫秒），最远可查 90 天前的数据 |
| endTime | LONG | NO | 结束时间（毫秒） |
| limit | INT | NO | 返回条数，默认 500，最大 1000 |

*`strategyId` 与 `clientStrategyId` 二选一，不可同时传入。



## **绑定子账户 (USER_DATA)**

> **响应:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/sub-accounts/bind`

**权重:** 5

**参数:**

| 名称           | 类型   | 是否必需 | 描述 |
| -------------- | ------ | -------- | ---- |
| childAddress   | STRING | YES      | 子账户钱包地址 |
| name           | STRING | YES      | 子账户名称 |
| nonce          | LONG   | YES      | 微秒级时间戳，用于防重放 |
| user           | STRING | YES      | 母账户钱包地址 |
| childSignature | STRING | YES      | 子账户对消息体的签名（见下方签名说明） |
| signature      | STRING | YES      | 母账户对消息体的签名，**必须使用母账户钱包私钥签名**（见下方签名说明） |

---

### 签名说明

本接口需要**两次独立签名**，两次签名的消息体不同：

#### 第一步：子账户签名（childSignature）

子账户使用**自己的钱包私钥**对以下消息体签名：

```
childAddress={childAddress}&name={name}&nonce={nonce}&user={user}
```

#### 第二步：母账户签名（signature）

母账户使用**自己的钱包私钥**（非 API Key）对以下消息体签名，消息体在第一步基础上**追加了 `childSignature`**：

```
childAddress={childAddress}&name={name}&nonce={nonce}&user={user}&childSignature={childSignature}
```

> 两次签名消息体的关键区别：母账户的签名消息体**包含** `childSignature`，子账户的不包含。

#### 支持的签名算法

| 账户类型    | 签名算法                                                     | 编码格式 |
| ----------- | ------------------------------------------------------------ | -------- |
| EVM 地址    | EIP-712 Typed Data（chainId=1666，message.msg=消息体）        | Hex      |
| Solana 地址 | Ed25519                                                      | Base58   |

> 母账户与子账户的地址类型必须一致（EVM 对 EVM，Solana 对 Solana）。

---

### 注意事项

* `signature` 必须使用**母账户钱包私钥**签名，不可使用 signer 私钥替代
* `childSignature` 与 `signature` 不可互换
* `user` 字段必须与已认证的母账户地址一致，不可伪造
* 只支持**白名单地址**创建，需要联系**项目方**配置


## **创建子账户 (TRADE)**

> **响应:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/createSubAccount`

**权重:** 5

**所需权限:** TRADE

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| subSourceAddr | STRING | YES | 子账户钱包地址 |
| subAccountName | STRING | YES | 子账户名称 |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| user | STRING | YES | 母账户钱包地址 |
| signer | STRING | YES | 与母账户关联的 signer 地址 |
| childSignature | STRING | YES | 子账户对消息体的签名（见下方签名说明） |
| signature | STRING | YES | 母账户对消息体的签名，**必须使用母账户钱包私钥签名**（见下方签名说明） |

---

### 签名说明

本接口需要**两次独立签名**，两次签名的消息体不同：

#### 第一步：子账户签名（childSignature）

子账户使用**自己的钱包私钥**对以下消息体签名：

```
subAccountName={subAccountName}&subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}
```

#### 第二步：母账户签名（signature）

母账户使用**自己的钱包私钥**（非 signer 私钥）对以下消息体签名，消息体在第一步基础上**追加了 `childSignature`**：

```
subAccountName={subAccountName}&subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}&childSignature={childSignature}
```

> 两次签名消息体的关键区别：母账户的签名消息体**包含** `childSignature`，子账户的不包含。

#### 支持的签名算法

| 账户类型 | 签名算法 | 编码格式 |
|---|---|---|
| EVM 地址 | EIP-712 Typed Data（chainId=1666，message.msg=消息体） | Hex |
| Solana 地址 | Ed25519 | Base58 |

> 母账户与子账户的地址类型必须一致（EVM 对 EVM，Solana 对 Solana）。

---

### 注意事项

* `signature` **必须使用母账户钱包私钥签名**，不可使用 signer 私钥替代。
* `childSignature` 与 `signature` 不可互换。
* `user` 字段必须与已认证的母账户地址一致，不可伪造。



## **查询子账户列表 (USER_DATA)**

> **响应:**

```javascript
[
    {
        "accountId": 123456,
        "subAccountName": "trading-desk-1",
        "parentAccount": false
    },
    {
        "accountId": 100000,
        "subAccountName": "",
        "parentAccount": true
    }
]
```

`GET /fapi/v3/getSubAccountList`

**权重:** 5

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| user | STRING | YES | 母账户钱包地址 |
| signer | STRING | YES | 与母账户关联的 signer 地址 |
| signature | STRING | YES | 对消息体的签名（见下方签名说明） |

**响应字段:**

| 名称 | 类型 | 描述 |
|------|------|------|
| accountId | LONG | 账户 ID |
| subAccountName | STRING | 子账户名称 |
| parentAccount | BOOLEAN | 是否为母账户（`true` 表示母账户，`false` 表示子账户） |

---

### 签名说明

使用 **signer 私钥**（EIP-712）对以下消息体签名（无论母账户是 EVM 还是 Solana 地址，均使用 EIP-712）：

```
nonce={nonce}&user={user}&signer={signer}
```

#### 签名算法

| 算法 | 编码格式 |
|---|---|
| EIP-712 Typed Data（chainId=1666，message.msg=消息体） | Hex |

> 与其他子账户接口不同，`getSubAccountList` 始终使用 **signer 私钥**（EIP-712）签名，即使母账户为 Solana 地址也不例外。



## **更新子账户 (TRADE)**

> **响应:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/updateSubAccount`

**权重:** 5

**所需权限:** TRADE

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| subSourceAddr | STRING | YES | 待更新的子账户钱包地址 |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| user | STRING | YES | 母账户钱包地址 |
| signer | STRING | YES | 与母账户关联的 signer 地址 |
| subAccountName | STRING | NO | 新的子账户名称（`subAccountName` 与 `status` 至少需传其一） |
| status | STRING | NO | 子账户状态：`NORMAL`（正常）或 `FROZEN`（冻结） |
| signature | STRING | YES | 母账户对消息体的签名，**必须使用母账户钱包私钥签名**（见下方签名说明） |

---

### 签名说明

母账户使用**自己的钱包私钥**（非 signer 私钥）对以下消息体签名：

```
subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}[&subAccountName={subAccountName}][&status={status}]
```

> 可选字段（`subAccountName`、`status`）仅在请求中包含时才加入消息体。

#### 支持的签名算法

| 账户类型 | 签名算法 | 编码格式 |
|---|---|---|
| EVM 地址 | EIP-712 Typed Data（chainId=1666，message.msg=消息体） | Hex |
| Solana 地址 | Ed25519 | Base58 |

---

### 注意事项

* `signature` **必须使用母账户钱包私钥签名**，不可使用 signer 私钥替代。
* `subAccountName` 与 `status` 至少需传其一。
* `status` 取值：`NORMAL`（解冻 / 正常），`FROZEN`（冻结）。
* 已冻结的子账户无法发起划转。



## **子账户划转 (TRADE)**

> **响应:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/subAccountTransfer`

**权重:** 5

**所需权限:** TRADE

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| toAccountAddress | STRING | YES | 目标钱包地址 |
| asset | STRING | YES | 资产名称（如 `USDT`） |
| amount | STRING | YES | 划转金额 |
| kindType | ENUM | YES | 划转方向（见下表） |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| user | STRING | YES | 签名账户的钱包地址（通常为母账户地址；子账户向母账户划转时为子账户地址） |
| signer | STRING | YES | 与 `user` 关联的 signer 地址 |
| fromAccountAddress | STRING | NO | 转出钱包地址。当转出账户与 `user` 不一致时必传（如子→子划转，或以第三方身份发起的母→子划转） |
| signature | STRING | YES | 对消息体的签名，**必须使用 `user` 账户的钱包私钥签名**（见下方签名说明） |

**`kindType` 取值:**

| 取值 | 说明 |
|------|------|
| `FUTURE_FUTURE` | 合约账户 → 合约账户 |
| `FUTURE_SPOT` | 合约账户 → 现货账户 |
| `SPOT_FUTURE` | 现货账户 → 合约账户 |
| `SPOT_SPOT` | 现货账户 → 现货账户 |

---

### 签名说明

使用 **`user` 账户的钱包私钥**（非 signer 私钥）对以下消息体签名：

**不含 `fromAccountAddress`：**
```
toAccountAddress={toAccountAddress}&asset={asset}&amount={amount}&kindType={kindType}&nonce={nonce}&user={user}&signer={signer}
```

**含 `fromAccountAddress`：**
```
toAccountAddress={toAccountAddress}&asset={asset}&amount={amount}&kindType={kindType}&nonce={nonce}&user={user}&signer={signer}&fromAccountAddress={fromAccountAddress}
```

#### 支持的签名算法

| 账户类型 | 签名算法 | 编码格式 |
|---|---|---|
| EVM 地址 | EIP-712 Typed Data（chainId=1666，message.msg=消息体） | Hex |
| Solana 地址 | Ed25519 | Base58 |

---

### 划转场景说明

| 场景 | `user` | `fromAccountAddress` | 签名私钥 |
|------|--------|----------------------|---------|
| 母账户 → 子账户 | 母账户地址 | *(无需传入)* | 母账户钱包私钥 |
| 子账户 → 母账户 | 子账户地址 | *(无需传入)* | 子账户钱包私钥 |
| 子账户 A → 子账户 B | 母账户地址 | 子账户 A 地址 | 母账户钱包私钥 |
| 子账户 A → 子账户 B | 子账户地址 | *(无需传入)* | 子账户钱包私钥 |

---

### 注意事项

* `signature` **必须使用 `user` 账户的钱包私钥签名**，不可使用 signer 私钥替代。
* `user` 字段必须与签名所用私钥对应的地址一致。
* 使用**子账户私钥**签名时，支持向**母账户**划转；也支持子→子划转,此时转账的来源地址为签名的**子账户**
* 向**已冻结的子账户**划转或从**已冻结的子账户**划出均会失败。
* 不支持向**非子账户关系内的外部地址**划转。



## **用户资产迁移 (WITHDRAW)**

> **响应:**

```javascript
{
    "batchId": "a1B2c3D4e5F6g7H8i9J0k1"   // 批次 ID，用于查询迁移历史
}
```

`POST /fapi/v3/asset/migrateUser`

**权重:** 50

**所需权限:** WITHDRAW

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| user | STRING | YES | 来源账户钱包地址，即待迁移资产所在的账户 |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| signature | STRING | YES | 对请求体的签名，**必须使用 `user` 账户的钱包私钥签名**（EVM 地址使用 EIP-712，Solana 地址使用 Ed25519） |

**注意事项:**

* 来源账户必须**无持仓**，否则请求将被拒绝。
* 来源账户必须**无挂单**，否则请求将被拒绝。
* 所有可用余额大于 0 的资产将自动包含在迁移批次中；余额为 0 的资产将被跳过。
* 单次迁移批次最多处理 **300 种资产**。
* 资产将转移至**当前已认证用户的账户**（目标账户）。
* 若来源账户无可用资产，接口将返回空响应，不发起实际转账。
* 请记录返回的 `batchId`，以便通过下方「用户资产迁移历史」接口查询迁移状态。

---

## **用户资产迁移历史 (USER_DATA)**

> **响应:**

```javascript
{
    "batchId": "a1B2c3D4e5F6g7H8i9J0k1",
    "totalCount": 2,                    // 总条数
    "successCount": 1,                  // 成功条数（status=S）
    "processingCount": 0,               // 处理中条数（status=P）
    "failCount": 0,                     // 失败条数（status=F）
    "initCount": 1,                     // 待执行条数（status=I）
    "details": [
        {
            "id": 1001,
            "asset": "USDT",            // 币种
            "amount": "500.00000000",   // 金额
            "tranId": 9876543210,       // 划转ID（null 表示尚未处理）
            "status": "S",              // 执行状态：I=待处理, S=划转成功, F=划转失败
            "fromStatus": "S",          // 转出状态：S=成功, F=失败, P=处理中
            "fromErrorCode": null,      // 转出错误码
            "fromResponse": null,       // 转出响应信息
            "toStatus": "S",            // 转入状态：S=成功, F=失败, P=处理中
            "toErrorCode": null,        // 转入错误码
            "toResponse": null          // 转入响应信息
        },
        {
            "id": 1002,
            "asset": "BTC",
            "amount": "0.05000000",
            "tranId": null,             // null 表示尚未处理
            "status": "I",              // 执行状态：I=待处理, S=划转成功, F=划转失败
            "fromStatus": null,
            "fromErrorCode": null,
            "fromResponse": null,
            "toStatus": null,
            "toErrorCode": null,
            "toResponse": null
        }
    ]
}
```

`GET /fapi/v3/asset/migrateUser/history`

**权重:** 50

**所需权限:** USER_DATA

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| batchId | STRING | YES | 调用「用户资产迁移」接口时返回的批次 ID |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| user | STRING | YES | 当前认证用户的钱包地址 |
| signer | STRING | YES | 与 `user` 关联的 signer 地址 |
| signature | STRING | YES | 对请求体的签名 |

**注意事项:**

* `batchId` 为必传参数，每个 `batchId` 对应一次迁移批次。
* 查询结果仅限当前认证用户——只返回与该账户相关的迁移记录。

## **注册并授权 Agent (PUBLIC)**

> **响应:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/registerAndApproveAgent`

注册新的 API Agent 账户并一次性授予其交易/提现权限。Agent 授权后，可使用 API Key 在指定权限范围内代表用户进行操作。

**权重:** 50

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| user | STRING | YES | 用户钱包地址 |
| nonce | LONG | YES | 微秒级时间戳，用于防重放攻击 |
| agentName | STRING | YES | Agent 的显示名称 |
| agentAddress | STRING | YES | Agent 的钱包地址 |
| expired | LONG | YES | Agent 到期时间戳（毫秒） |
| signatureChainId | LONG | YES | 生成签名时所使用的链 ID（EVM 地址填 `56`，Solana 地址填 `101`） |
| signature | STRING | YES | 对消息体的签名，必须使用用户钱包私钥签名（见下方签名说明） |
| canSpotTrade | BOOLEAN | YES | 是否允许 Agent 下现货订单 |
| canPerpTrade | BOOLEAN | YES | 是否允许 Agent 下永续合约订单 |
| canWithdraw | BOOLEAN | YES | 是否允许 Agent 发起提现 |
| ipWhitelist | STRING | NO | 允许访问的 IP 地址或 CIDR 段列表，以**空格**分隔（如 `192.168.1.1 10.0.0.0/24`）。**当 `canWithdraw` 为 `true` 时必填，不可为空。** |
| agentCode | STRING | NO | Agent 注册邀请码 |

---

### 签名说明

使用**用户钱包私钥**对以下消息体签名：

```
msg: user={user}&nonce={nonce}&agentName={agentName}&agentAddress={agentAddress}&expired={expired}&signatureChainId={signatureChainId}&canSpotTrade={canSpotTrade}&canPerpTrade={canPerpTrade}&canWithdraw={canWithdraw}&ipWhitelist={ipWhitelist}
```

> **EVM 地址专用：** 将上述 `msg` 字符串作为 EIP-712 结构化数据中 `message.msg` 的值进行签名，结构如下：

> **⚠️ 注意：** `domain.chainId` 必须使用请求参数 `signatureChainId` 的值（EVM 地址填 `56`，Solana 地址填 `101`），而非固定值 `1666`。

```
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
    "chainId": signatureChainId,
    "verifyingContract": "0x0000000000000000000000000000000000000000"
  },
  "message": {
    "msg": "$msg"
  }
}
```

#### 支持的签名算法

| 账户类型 | 签名算法 | 编码格式 |
|---|---|---|
| EVM 地址 | EIP-712 Typed Data（chainId=signatureChainId，message.msg=消息体） | Hex |
| Solana 地址 | Ed25519 | Base58 |

---

### 注意事项

* `nonce` 必须为微秒精度时间戳，与服务器时间差不得超过 **10 秒**，且同一 nonce 不可重复使用。
* `expired` 为 Agent 的有效期截止时间，单位为**毫秒**，超时后 Agent 将自动失效。
* `ipWhitelist` 以**空格**作为分隔符，支持 CIDR 格式（如 `192.168.1.1 10.0.0.0/24`）。**当 `canWithdraw` 为 `true` 时，`ipWhitelist` 必填且不可为空。**
* 本接口**无需鉴权**——无需传入 API Key 或 HMAC 请求头，所有授权均通过链上 `signature` 验证。
* 本接口将 Agent 注册与权限授予合并为单次调用，等同于独立注册接口与 `approveAgent` 接口的组合操作。

---

## **查询直发公告列表 (USER_DATA)**

> **响应:**

```javascript
{
  "total": 2,
  "rows": [
    {
      "id": 1001,
      "contents": [
        {
          "language": "en",
          "title": "Platform Maintenance Notice",
          "subtitle": "Scheduled downtime",
          "content": "The platform will undergo scheduled maintenance on 2026-06-20 from 02:00 to 04:00 UTC."
        },
        {
          "language": "zh",
          "title": "平台维护公告",
          "subtitle": "定期停机",
          "content": "平台将于2026年6月20日UTC时间02:00至04:00进行定期维护。"
        }
      ],
      "category": "MAINTENANCE",
      "publishTime": 1750000000000,
      "jumpLink": "https://www.asterdex.com/zh-CN/announcement/1001"
    }
  ]
}
```

`GET /fapi/v3/announcement/direct`

查询当前已认证用户的直发公告列表，支持分页。

**权重:** 1

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| page | INT | NO | 页码，从 `1` 开始，默认值: `1` |
| size | INT | NO | 每页返回数量，默认值: `20` |

**响应字段:**

| 字段 | 类型 | 描述 |
|------|------|------|
| total | LONG | 公告总数 |
| rows | ARRAY | 公告对象列表 |
| rows[].id | LONG | 公告 ID |
| rows[].contents | ARRAY | 多语言内容列表 |
| rows[].contents[].language | STRING | 语言代码（如 `en`、`zh`） |
| rows[].contents[].title | STRING | 公告标题 |
| rows[].contents[].subtitle | STRING | 公告副标题 |
| rows[].contents[].content | STRING | 公告正文 |
| rows[].category | STRING | 公告分类 |
| rows[].publishTime | LONG | 发布时间戳（毫秒） |
| rows[].jumpLink | STRING | 公告详情页链接 |

---

## **按ID查询直发公告 (USER_DATA)**

> **响应:**

```javascript
{
  "id": 1001,
  "contents": [
    {
      "language": "en",
      "title": "Platform Maintenance Notice",
      "subtitle": "Scheduled downtime",
      "content": "The platform will undergo scheduled maintenance on 2026-06-20 from 02:00 to 04:00 UTC."
    },
    {
      "language": "zh",
      "title": "平台维护公告",
      "subtitle": "定期停机",
      "content": "平台将于2026年6月20日UTC时间02:00至04:00进行定期维护。"
    }
  ],
  "category": "MAINTENANCE",
  "publishTime": 1750000000000,
  "jumpLink": "https://www.asterdex.com/zh-CN/announcement/1001"
}
```

`GET /fapi/v3/announcement/directById`

根据公告 ID 查询当前已认证用户的单条直发公告。

**权重:** 1

**参数:**

| 名称 | 类型 | 是否必需 | 描述 |
|------|------|---------|------|
| id | LONG | YES | 公告 ID |

**响应字段:**

| 字段 | 类型 | 描述 |
|------|------|------|
| id | LONG | 公告 ID |
| contents | ARRAY | 多语言内容列表 |
| contents[].language | STRING | 语言代码（如 `en`、`zh`） |
| contents[].title | STRING | 公告标题 |
| contents[].subtitle | STRING | 公告副标题 |
| contents[].content | STRING | 公告正文 |
| category | STRING | 公告分类 |
| publishTime | LONG | 发布时间戳（毫秒） |
| jumpLink | STRING | 公告详情页链接 |
