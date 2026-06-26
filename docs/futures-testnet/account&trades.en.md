## **Account/Trades**

<aside class="warning">
Considering the possible data latency from RESTful endpoints during an extremely volatile market, it is highly recommended to get the order status, position, etc from the Websocket user data stream.
</aside>

## Change Position Mode(TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /fapi/v3/positionSide/dual (HMAC SHA256)``

Change user's position mode (Hedge Mode or One-way Mode ) on ***EVERY symbol***

**Weight:**
1

**Parameters:**

| Name             | Type   | Mandatory | Description                               |
| ---------------- | ------ | --------- | ----------------------------------------- |
| dualSidePosition | STRING | YES       | "true": Hedge Mode; "false": One-way Mode |
| recvWindow       | LONG   | NO        |                                           |
| timestamp        | LONG   | YES       |                                           |

## Get Current Position Mode(USER_DATA)

> **Response:**

```javascript
{
	"dualSidePosition": true // "true": Hedge Mode; "false": One-way Mode
}
```

``GET /fapi/v3/positionSide/dual (HMAC SHA256)``

Get user's position mode (Hedge Mode or One-way Mode ) on ***EVERY symbol***

**Weight:**
30

**Parameters:**

| Name       | Type | Mandatory | Description |
| ---------- | ---- | --------- | ----------- |
| recvWindow | LONG | NO        |             |
| timestamp  | LONG | YES       |             |

## Change STP Mode (TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /fapi/v3/stpMode``

Change user's Self-Trade Prevention (STP) mode on ***EVERY symbol***

**Weight:**
1

**Parameters:**

| Name | Type | Mandatory | Description |
|------|------|-----------|-------------|
| stpMode | ENUM | YES | `EXPIRE_TAKER`: expire taker order; `EXPIRE_MAKER`: expire maker order; `EXPIRE_BOTH`: expire both maker and taker orders |
| nonce | LONG | YES | Microsecond-level timestamp |
| signer | STRING | YES | API wallet address |
| signature | STRING | YES | Signature |

## Get Current STP Mode (USER_DATA)

> **Response:**

```javascript
{
	"stpMode": "EXPIRE_TAKER" // Current STP mode
}
```

``GET /fapi/v3/stpMode``

Get user's current Self-Trade Prevention (STP) mode on ***EVERY symbol***

**Weight:**
30

**Parameters:**

| Name | Type | Mandatory | Description |
|------|------|-----------|-------------|
| nonce | LONG | YES | Microsecond-level timestamp |
| signer | STRING | YES | API wallet address |
| signature | STRING | YES | Signature |

## Change Multi-Assets Mode (TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /fapi/v3/multiAssetsMargin (HMAC SHA256)``

Change user's Multi-Assets mode (Multi-Assets Mode or Single-Asset Mode) on ***Every symbol***

**Weight:**
1

**Parameters:**

| Name              | Type   | Mandatory | Description                                           |
| ----------------- | ------ | --------- | ----------------------------------------------------- |
| multiAssetsMargin | STRING | YES       | "true": Multi-Assets Mode; "false": Single-Asset Mode |
| recvWindow        | LONG   | NO        |                                                       |
| timestamp         | LONG   | YES       |                                                       |

## Get Current Multi-Assets Mode (USER_DATA)

> **Response:**

```javascript
{
	"multiAssetsMargin": true // "true": Multi-Assets Mode; "false": Single-Asset Mode
}
```

``GET /fapi/v3/multiAssetsMargin (HMAC SHA256)``

Get user's Multi-Assets mode (Multi-Assets Mode or Single-Asset Mode) on ***Every symbol***

**Weight:**
30

**Parameters:**

| Name       | Type | Mandatory | Description |
| ---------- | ---- | --------- | ----------- |
| recvWindow | LONG | NO        |             |
| timestamp  | LONG | YES       |             |

## New Order  (TRADE)

> **Response:**

```javascript
{
 	"clientOrderId": "testOrder",
 	"cumQty": "0",
 	"cumQuote": "0",
 	"executedQty": "0",
 	"orderId": 22542179,
 	"avgPrice": "0.00000",
 	"origQty": "10",
 	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",		// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"origType": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",	// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",			// callback rate, only return with TRAILING_STOP_MARKET order
 	"updateTime": 1566818724722,
 	"workingType": "CONTRACT_PRICE",
 	"priceProtect": false            // if conditional order trigger is protected
}
```

``POST /fapi/v3/order  (HMAC SHA256)``

Send in a new order.

**Weight:**
1

**Parameters:**

| Name             | Type    | Mandatory | Description                                                                                                                            |
| ---------------- | ------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| symbol           | STRING  | YES       |                                                                                                                                        |
| side             | ENUM    | YES       |                                                                                                                                        |
| positionSide     | ENUM    | NO        | Default`BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent in Hedge Mode.                                      |
| type             | ENUM    | YES       |                                                                                                                                        |
| timeInForce      | ENUM    | NO        |                                                                                                                                        |
| quantity         | DECIMAL | NO        | Cannot be sent with`closePosition`=`true`(Close-All)                                                                                   |
| reduceOnly       | STRING  | NO        | "true" or "false". default "false". Cannot be sent in Hedge Mode; cannot be sent with`closePosition`=`true`                            |
| price            | DECIMAL | NO        |                                                                                                                                        |
| newClientOrderId | STRING  | NO        | A unique id among open orders. Automatically generated if not sent. Can only be string following the rule:`^[\.A-Z\:/a-z0-9_-]{1,36}$` |
| stopPrice        | DECIMAL | NO        | Used with`STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.                                                                |
| closePosition    | STRING  | NO        | `true`, `false`；Close-All，used with `STOP_MARKET` or `TAKE_PROFIT_MARKET`.                                                           |
| activationPrice  | DECIMAL | NO        | Used with`TRAILING_STOP_MARKET` orders, default as the latest price(supporting different `workingType`)                                |
| callbackRate     | DECIMAL | NO        | Used with`TRAILING_STOP_MARKET` orders, min 0.1, max 5 where 1 for 1%                                                                  |
| workingType      | ENUM    | NO        | stopPrice triggered by: "MARK_PRICE", "CONTRACT_PRICE". Default "CONTRACT_PRICE"                                                       |
| priceProtect     | STRING  | NO        | "TRUE" or "FALSE", default "FALSE". Used with`STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.                            |
| newOrderRespType | ENUM    | NO        | "ACK", "RESULT", default "ACK"                                                                                                         |
| pegPriceType     | ENUM    | NO        | BBO peg mode: `COUNTERPARTY_1` or `QUEUE_1`. When set on a `LIMIT` order, the engine resolves the actual price from the order book at trigger time using the BBO + `pegOffset`. Defaults to no peg. |
| pegOffset        | DECIMAL | NO        | Signed offset from BBO when `pegPriceType` is set. BUY orders should use a non-positive value (e.g. `-0.5`); SELL non-negative. Units: same scale as `price` (must be a `tickSize` multiple).        |
| stpMode          | ENUM    | NO        | Self-Trade Prevention mode for this order. Overrides the account-level default. `EXPIRE_TAKER`: cancel the taker side; `EXPIRE_MAKER`: cancel the maker side; `EXPIRE_BOTH`: cancel both sides.      |
| recvWindow       | LONG    | NO        |                                                                                                                                        |
| timestamp        | LONG    | YES       |                                                                                                                                        |

Additional mandatory parameters based on `type`:

| Type                             | Additional mandatory parameters    |
| -------------------------------- | ---------------------------------- |
| `LIMIT`                          | `timeInForce`, `quantity`, `price` |
| `MARKET`                         | `quantity`                         |
| `STOP/TAKE_PROFIT`               | `quantity`,  `price`, `stopPrice`  |
| `STOP_MARKET/TAKE_PROFIT_MARKET` | `stopPrice`                        |
| `TRAILING_STOP_MARKET`           | `callbackRate`                     |

* Order with type `STOP`,  parameter `timeInForce` can be sent ( default `GTC`).
* Order with type `TAKE_PROFIT`,  parameter `timeInForce` can be sent ( default `GTC`).
* Condition orders will be triggered when:
  
  * If parameter`priceProtect`is sent as true:
    
    * when price reaches the `stopPrice` ，the difference rate between "MARK_PRICE" and "CONTRACT_PRICE" cannot be larger than the "triggerProtect" of the symbol
    * "triggerProtect" of a symbol can be got from `GET /fapi/v3/exchangeInfo`
  * `STOP`, `STOP_MARKET`:
    
    * BUY: latest price ("MARK_PRICE" or "CONTRACT_PRICE") >= `stopPrice`
    * SELL: latest price ("MARK_PRICE" or "CONTRACT_PRICE") <= `stopPrice`
  * `TAKE_PROFIT`, `TAKE_PROFIT_MARKET`:
    
    * BUY: latest price ("MARK_PRICE" or "CONTRACT_PRICE") <= `stopPrice`
    * SELL: latest price ("MARK_PRICE" or "CONTRACT_PRICE") >= `stopPrice`
  * `TRAILING_STOP_MARKET`:
    
    * BUY: the lowest price after order placed <= `activationPrice`, and the latest price >= the lowest price * (1 + `callbackRate`)
    * SELL: the highest price after order placed >= `activationPrice`, and the latest price <= the highest price * (1 - `callbackRate`)
* For `TRAILING_STOP_MARKET`, if you got such error code.``{"code": -2021, "msg": "Order would immediately trigger."}``means that the parameters you send do not meet the following requirements:
  
  * BUY: `activationPrice` should be smaller than latest price.
  * SELL: `activationPrice` should be larger than latest price.
* If `newOrderRespType ` is sent as `RESULT` :
  
  * `MARKET` order: the final FILLED result of the order will be return directly.
  * `LIMIT` order with special `timeInForce`: the final status result of the order(FILLED or EXPIRED) will be returned directly.
* `STOP_MARKET`, `TAKE_PROFIT_MARKET` with `closePosition`=`true`:
  
  * Follow the same rules for condition orders.
  * If triggered，**close all** current long position( if `SELL`) or current short position( if `BUY`).
  * Cannot be used with `quantity` paremeter
  * Cannot be used with `reduceOnly` parameter
  * In Hedge Mode,cannot be used with `BUY` orders in `LONG` position side. and cannot be used with `SELL` orders in `SHORT` position side

## Modify Order (TRADE)

> **Response:**

```javascript
{ 
	'orderId': 405,  // Order ID
	'symbol': 'GOOGLUSDT', // Trading pair
	'status': 'NEW', // Order status
	'clientOrderId': 'uOLCC3aiCou3z2YL0sxWIX',  // User-defined order ID
	'price': '295.50',  // Order price
	'avgPrice': '0.00000', // Average fill price
	'origQty': '0.150',  // Original order quantity
	'executedQty': '0', // Filled quantity
	'cumQty': '0', 
	'cumQuote': '0',  // Filled amount
	'timeInForce': 'GTC',  // Time in force
	'type': 'LIMIT',  // Order type
	'reduceOnly': False,  // Reduce only
	'closePosition': False,  // Close all positions on trigger
	'side': 'BUY',   // Order side
	'positionSide': 'BOTH',   // Position side
	'stopPrice': '0',  // Trigger price, invalid for `TRAILING_STOP_MARKET`
	'workingType': 'CONTRACT_PRICE',  // Condition price trigger type
	'priceProtect': False,  // Conditional order trigger protection enabled
	'origType': 'LIMIT',  // Original order type before trigger
	'updateTime': 1776311274450 // Update time
}
```

``
PUT /fapi/v3/order ``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
---------------- | ------- | -------- | ----
orderId | LONG | NO | Order ID
origClientOrderId | STRING | NO | User-defined order ID
symbol | STRING | YES| Trading pair
quantity | DECIMAL| NO | Order quantity
price | DECIMAL | NO | Order price

* Either `orderId` or `origClientOrderId` must be sent. If both are sent, `orderId` takes precedence.
* Both `quantity` and `price` must be sent.
* If the new `quantity` or `price` does not meet `PRICE_FILTER` / `PERCENT_FILTER` / `LOT_SIZE` restrictions, the modification will be rejected and the original order will remain.
* Only `LIMIT` order type is supported.
* Maximum 10000 modifications per order.
* **BBO-pegged orders** (those placed with `pegPriceType` = `COUNTERPARTY_1` / `QUEUE_1`): the engine resolves the actual price from the order book at trigger time. To auto-track the BBO with a continuously re-pegged limit order, use a **Chase order** (see `POST /fapi/v3/chase` below) which the strategy service amends in real time as the BBO moves.

## Place Chase Order (TRADE)

> **Response:**

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

Place a **Chase strategy order** — a BBO-pegged GTX limit order that automatically re-pegs to the best bid/ask as the market moves. The strategy service polls the order book each tick and amends the order price in real time to keep the order near the top of the book until it fills or until the market moves beyond `maxChaseOffset` from the original BBO.

**Weight:** 1

**Parameters:**

| Name               | Type    | Mandatory | Description                                                                                                                                                                                |
| ------------------ | ------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| symbol             | STRING  | YES       | Trading pair.                                                                                                                                                                              |
| side               | ENUM    | YES       | `BUY` or `SELL`. Missing/blank returns `"Mandatory parameter 'side' was not sent, was empty/null, or malformed."`                                                                          |
| positionSide       | ENUM    | NO        | Default `BOTH` for One-way Mode; `LONG` or `SHORT` for Hedge Mode.                                                                                                                         |
| quantityUnit       | STRING  | YES       | `BASE` (qty in base asset, e.g. BTC) or `QUOTE` (qty in quote asset, e.g. USDT). For `QUOTE` orders the system converts to BASE using mark price.                                          |
| quantity           | DECIMAL | YES       | Order quantity in the unit specified by `quantityUnit`.                                                                                                                                    |
| reduceOnly         | STRING  | NO        | `"true"` or `"false"` (case-insensitive). Any other value is rejected. Default `"false"`.                                                                                                  |
| chaseOffset        | DECIMAL | NO        | Distance from BBO to peg the order. Default `"0"` (exact BBO peg). Must be ≥ 0 and a multiple of `tickSize`. BUY price = `bid1 − chaseOffset`; SELL price = `ask1 + chaseOffset`.           |
| chaseOffsetType    | STRING  | NO        | `ABSOLUTE` (default). only supports `ABSOLUTE` for now. Will support `PERCENTAGE` later.                                                                                                   |
| maxChaseOffset     | DECIMAL | NO        | Maximum tolerated distance from the original BBO before the chase auto-cancels. Must be `> 0`. If omitted, no distance-based auto-cancel is applied and any `maxChaseOffsetType` sent is ignored.        |
| maxChaseOffsetType | STRING  | NO        | `ABSOLUTE` or `PERCENTAGE` (default `ABSOLUTE` when `maxChaseOffset` is sent). `ABSOLUTE`: same unit as price, must be a multiple of `tickSize`. `PERCENTAGE`: ≤ 2 decimal places.          |
| timeInForce        | ENUM    | NO        | Default `GTX` (post-only). **`NO_FILL` is not allowed** and is rejected with `INVALID_TIF`.                                                                                                |
| clientStrategyId   | STRING  | NO        | User-defined strategy id. Auto-generated if not sent. **Length ≤ 28 characters** (DB column is `varchar(28)`). Must match `^[\.A-Z\:/a-z0-9_-]{1,28}$`.                                    |
| recvWindow         | LONG    | NO        |                                                                                                                                                                                            |
| timestamp          | LONG    | YES       |                                                                                                                                                                                            |

**Validation rules:**

* `side` is mandatory.
* `reduceOnly` accepts only `"true"` / `"false"` (case-insensitive). Any other string returns `INVALID_PARAMETER` with `reduceOnly` as the offending param name.
* `chaseOffset` must be ≥ 0 and a multiple of `tickSize`.
* `chaseOffsetType` / `maxChaseOffsetType` must be `ABSOLUTE` or `PERCENTAGE`; an invalid value returns `INVALID_PARAMETER` (not `INVALID_CHASE_OFFSET`).
* `chaseOffsetType` currently supports only `ABSOLUTE`. `PERCENTAGE` is a valid value but is not yet implemented for `chaseOffset` and returns `UNSUPPORTED_OPERATION`. (`maxChaseOffsetType` supports both.)
* Quantity / notional floor (entry orders only; `reduceOnly` orders are exempt from the notional minimum): for `quantityUnit = BASE`, `quantity` must be ≥ the symbol's `marketMinQty` (else `QTY_LESS_THAN_MIN_QTY`) and `quantity × markPrice` must be ≥ the symbol's `minNotional` (else `MIN_NOTIONAL`); for `quantityUnit = QUOTE`, the quote amount must be ≥ `minNotional` (else `MIN_NOTIONAL`) and `quoteQty / markPrice` must be ≥ `marketMinQty` (else `QTY_LESS_THAN_MIN_QTY`).
* When `maxChaseOffsetType = PERCENTAGE`, the input value must have ≤ 2 decimal places (it is stored at scale 2 on the wire, e.g. `"1"` → 1.00%, `"100"` → 100.00%).
* When `maxChaseOffsetType = ABSOLUTE`, `maxChaseOffset` must be a multiple of `tickSize`.
* `timeInForce` cannot be `NO_FILL`.
* `clientStrategyId` length must be ≤ 28 characters.
* OI cap check: for `quantityUnit = QUOTE`, the gateway converts to BASE quantity for the symbol-leverage OI bracket check using `qtyBase = qtyQuote × 10^quantityDecimal / markPrice`.

**Behavior:**

* The initial order is placed as a GTX (post-only) limit with `pegPriceType = QUEUE_1` and signed `pegOffset` (negative for BUY, positive for SELL).
* The strategy service polls every second and amends the order price as the BBO moves, keeping the order at `bid1 − chaseOffset` (BUY) or `ask1 + chaseOffset` (SELL).
* If the market moves beyond `maxChaseOffset` from the original BBO, the chase **auto-cancels** with reason `OFFSET_CANCELLED`.
* The chase terminates on FILL, user cancel (via standard `DELETE /fapi/v3/order`), or `maxChaseOffset` breach.

## Place Multiple Orders  (TRADE)

> **Response:**

```javascript
[
	{
	 	"clientOrderId": "testOrder",
	 	"cumQty": "0",
	 	"cumQuote": "0",
	 	"executedQty": "0",
	 	"orderId": 22542179,
	 	"avgPrice": "0.00000",
	 	"origQty": "10",
	 	"price": "0",
	  	"reduceOnly": false,
	  	"side": "BUY",
	  	"positionSide": "SHORT",
	  	"status": "NEW",
	  	"stopPrice": "9300",		// please ignore when order type is TRAILING_STOP_MARKET
	  	"symbol": "BTCUSDT",
	  	"timeInForce": "GTC",
	  	"type": "TRAILING_STOP_MARKET",
	  	"origType": "TRAILING_STOP_MARKET",
	  	"activatePrice": "9020",	// activation price, only return with TRAILING_STOP_MARKET order
	  	"priceRate": "0.3",			// callback rate, only return with TRAILING_STOP_MARKET order
	 	"updateTime": 1566818724722,
	 	"workingType": "CONTRACT_PRICE",
	 	"priceProtect": false            // if conditional order trigger is protected
	},
	{
		"code": -2022, 
		"msg": "ReduceOnly Order is rejected."
	}
]
```

``POST /fapi/v3/batchOrders  (HMAC SHA256)``

**Weight:**
5

**Parameters:**

| Name        | Type       | Mandatory | Description              |
| ----------- | ---------- | --------- | ------------------------ |
| batchOrders | LIST | YES       | order list. Max 5 orders |
| recvWindow  | LONG       | NO        |                          |
| timestamp   | LONG       | YES       |                          |

**Where ``batchOrders`` is the list of order parameters in JSON**

| Name             | Type    | Mandatory | Description                                                                                                                            |
| ---------------- | ------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| symbol           | STRING  | YES       |                                                                                                                                        |
| side             | ENUM    | YES       |                                                                                                                                        |
| positionSide     | ENUM    | NO        | Default`BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent with Hedge Mode.                                    |
| type             | ENUM    | YES       |                                                                                                                                        |
| timeInForce      | ENUM    | NO        |                                                                                                                                        |
| quantity         | DECIMAL | YES       |                                                                                                                                        |
| reduceOnly       | STRING  | NO        | "true" or "false". default "false".                                                                                                    |
| price            | DECIMAL | NO        |                                                                                                                                        |
| newClientOrderId | STRING  | NO        | A unique id among open orders. Automatically generated if not sent. Can only be string following the rule:`^[\.A-Z\:/a-z0-9_-]{1,36}$` |
| stopPrice        | DECIMAL | NO        | Used with`STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.                                                                |
| activationPrice  | DECIMAL | NO        | Used with`TRAILING_STOP_MARKET` orders, default as the latest price(supporting different `workingType`)                                |
| callbackRate     | DECIMAL | NO        | Used with`TRAILING_STOP_MARKET` orders, min 0.1, max 4 where 1 for 1%                                                                  |
| workingType      | ENUM    | NO        | stopPrice triggered by: "MARK_PRICE", "CONTRACT_PRICE". Default "CONTRACT_PRICE"                                                       |
| priceProtect     | STRING  | NO        | "TRUE" or "FALSE", default "FALSE". Used with`STOP/STOP_MARKET` or `TAKE_PROFIT/TAKE_PROFIT_MARKET` orders.                            |
| newOrderRespType | ENUM    | NO        | "ACK", "RESULT", default "ACK"                                                                                                         |

* Paremeter rules are same with `New Order`
* Batch orders are processed concurrently, and the order of matching is not guaranteed.
* The order of returned contents for batch orders is the same as the order of the order list.


## Transfer Between Futures And Spot (USER_DATA)

> **Response:**

```javascript
{
    "tranId": 21841, //transaction id
    "status": "SUCCESS" //status
}
```

``
POST /fapi/v3/asset/wallet/transfer  (HMAC SHA256)
``

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
---------------- | ------- | -------- | ----
amount |	DECIMAL | 	YES |	amount
asset |	STRING | 	YES |	asset
clientTranId |	STRING | 	YES |	transaction id 
kindType |	STRING | 	YES |	kindType
timestamp	| LONG | YES	|	timestamp

Notes:

* kindType can take the following values:
     FUTURE_SPOT  (futures converted to spot)
	 SPOT_FUTURE  (spot converted to futures)


## Query Order (USER_DATA)

> **Response:**

```javascript
{
  	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE",
  	"priceProtect": false            // if conditional order trigger is protected
}
```

``GET /fapi/v3/order (HMAC SHA256)``

Check an order's status.

**Weight:**
1

* These orders will not be found:
  * order status is `CANCELED` or `EXPIRED`, **AND**
  * order has NO filled trade, **AND**
  * created time + 7 days < current time

**Parameters:**

| Name              | Type   | Mandatory | Description |
| ----------------- | ------ | --------- | ----------- |
| symbol            | STRING | YES       |             |
| orderId           | LONG   | NO        |             |
| origClientOrderId | STRING | NO        |             |
| recvWindow        | LONG   | NO        |             |
| timestamp         | LONG   | YES       |             |

Notes:

* Either `orderId` or `origClientOrderId` must be sent.

## Cancel Order (TRADE)

> **Response:**

```javascript
{
 	"clientOrderId": "myOrder1",
 	"cumQty": "0",
 	"cumQuote": "0",
 	"executedQty": "0",
 	"orderId": 283194212,
 	"origQty": "11",
 	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "CANCELED",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
 	"updateTime": 1571110484038,
 	"workingType": "CONTRACT_PRICE",
 	"priceProtect": false            // if conditional order trigger is protected
}
```

``DELETE /fapi/v3/order  (HMAC SHA256)``

Cancel an active order.

**Weight:**
1

**Parameters:**

| Name              | Type   | Mandatory | Description |
| ----------------- | ------ | --------- | ----------- |
| symbol            | STRING | YES       |             |
| orderId           | LONG   | NO        |             |
| origClientOrderId | STRING | NO        |             |
| recvWindow        | LONG   | NO        |             |
| timestamp         | LONG   | YES       |             |

Either `orderId` or `origClientOrderId` must be sent.

## Cancel All Open Orders (TRADE)

> **Response:**

```javascript
{
	"code": "200", 
	"msg": "The operation of cancel all open order is done."
}
```

``DELETE /fapi/v3/allOpenOrders  (HMAC SHA256)``

**Weight:**
1

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | YES       |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |

## Cancel Multiple Orders (TRADE)

> **Response:**

```javascript
[
	{
	 	"clientOrderId": "myOrder1",
	 	"cumQty": "0",
	 	"cumQuote": "0",
	 	"executedQty": "0",
	 	"orderId": 283194212,
	 	"origQty": "11",
	 	"origType": "TRAILING_STOP_MARKET",
  		"price": "0",
  		"reduceOnly": false,
  		"side": "BUY",
  		"positionSide": "SHORT",
  		"status": "CANCELED",
  		"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  		"closePosition": false,   // if Close-All
  		"symbol": "BTCUSDT",
  		"timeInForce": "GTC",
  		"type": "TRAILING_STOP_MARKET",
  		"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  		"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
	 	"updateTime": 1571110484038,
	 	"workingType": "CONTRACT_PRICE",
	 	"priceProtect": false            // if conditional order trigger is protected
	},
	{
		"code": -2011,
		"msg": "Unknown order sent."
	}
]
```

``DELETE /fapi/v3/batchOrders  (HMAC SHA256)``

**Weight:**
1

**Parameters:**

| Name                  | Type           | Mandatory | Description                                                                                     |
| --------------------- | -------------- | --------- | ----------------------------------------------------------------------------------------------- |
| symbol                | STRING         | YES       |                                                                                                 |
| orderIdList           | LIST\   | NO        | max length 10 e.g. [1234567,2345678]                                                      |
| origClientOrderIdList | LIST\ | NO        | max length 10 e.g. ["my_id_1","my_id_2"], encode the double quotes. No space after comma. |
| recvWindow            | LONG           | NO        |                                                                                                 |
| timestamp             | LONG           | YES       |                                                                                                 |

Either `orderIdList` or `origClientOrderIdList ` must be sent.

## Auto-Cancel All Open Orders (TRADE)

> **Response:**

```javascript
{
	"symbol": "BTCUSDT", 
	"countdownTime": "100000"
}
```

Cancel all open orders of the specified symbol at the end of the specified countdown.

``POST /fapi/v3/countdownCancelAll  (HMAC SHA256)``

**Weight:**
10

**Parameters:**

| Name          | Type   | Mandatory | Description                                              |
| ------------- | ------ | --------- | -------------------------------------------------------- |
| symbol        | STRING | YES       |                                                          |
| countdownTime | LONG   | YES       | countdown time, 1000 for 1 second. 0 to cancel the timer |
| recvWindow    | LONG   | NO        |                                                          |
| timestamp     | LONG   | YES       |                                                          |

* The endpoint should be called repeatedly as heartbeats so that the existing countdown time can be canceled and replaced by a new one.
* Example usage:
  Call this endpoint at 30s intervals with an countdownTime of 120000 (120s).
  If this endpoint is not called within 120 seconds, all your orders of the specified symbol will be automatically canceled.
  If this endpoint is called with an countdownTime of 0, the countdown timer will be stopped.
* The system will check all countdowns **approximately every 10 milliseconds**, so please note that sufficient redundancy should be considered when using this function. We do not recommend setting the countdown time to be too precise or too small.

## Query Current Open Order (USER_DATA)

> **Response:**

```javascript
{
  	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",	
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",	
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   			// if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order	
  	"updateTime": 1579276756075,
  	"workingType": "CONTRACT_PRICE",
  	"priceProtect": false            // if conditional order trigger is protected
}
```

``GET /fapi/v3/openOrder  (HMAC SHA256)``

**Weight:** 1

**Parameters:**

| Name              | Type   | Mandatory | Description |
| ----------------- | ------ | --------- | ----------- |
| symbol            | STRING | YES       |             |
| orderId           | LONG   | NO        |             |
| origClientOrderId | STRING | NO        |             |
| recvWindow        | LONG   | NO        |             |
| timestamp         | LONG   | YES       |             |

* Either`orderId` or `origClientOrderId` must be sent
* If the queried order has been filled or cancelled, the error message "Order does not exist" will be returned.

## Current All Open Orders (USER_DATA)

> **Response:**

```javascript
[
  {
  	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE",
  	"priceProtect": false            // if conditional order trigger is protected
  }
]
```

``GET /fapi/v3/openOrders  (HMAC SHA256)``

Get all open orders on a symbol. **Careful** when accessing this with no symbol.

**Weight:**
1 for a single symbol; **40** when the symbol parameter is omitted

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | NO        |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |

* If the symbol is not sent, orders for all symbols will be returned in an array.

## All Orders (USER_DATA)

> **Response:**

```javascript
[
  {
   	"avgPrice": "0.00000",
  	"clientOrderId": "abc",
  	"cumQuote": "0",
  	"executedQty": "0",
  	"orderId": 1917641,
  	"origQty": "0.40",
  	"origType": "TRAILING_STOP_MARKET",
  	"price": "0",
  	"reduceOnly": false,
  	"side": "BUY",
  	"positionSide": "SHORT",
  	"status": "NEW",
  	"stopPrice": "9300",				// please ignore when order type is TRAILING_STOP_MARKET
  	"closePosition": false,   // if Close-All
  	"symbol": "BTCUSDT",
  	"time": 1579276756075,				// order time
  	"timeInForce": "GTC",
  	"type": "TRAILING_STOP_MARKET",
  	"activatePrice": "9020",			// activation price, only return with TRAILING_STOP_MARKET order
  	"priceRate": "0.3",					// callback rate, only return with TRAILING_STOP_MARKET order
  	"updateTime": 1579276756075,		// update time
  	"workingType": "CONTRACT_PRICE",
  	"priceProtect": false            // if conditional order trigger is protected
  }
]
```

``GET /fapi/v3/allOrders (HMAC SHA256)``

Get all account orders; active, canceled, or filled.

* These orders will not be found:
  * order status is `CANCELED` or `EXPIRED`, **AND**
  * order has NO filled trade, **AND**
  * created time + 7 days < current time

**Weight:**
5

**Parameters:**

| Name       | Type   | Mandatory | Description            |
| ---------- | ------ | --------- | ---------------------- |
| symbol     | STRING | YES       |                        |
| orderId    | LONG   | NO        |                        |
| startTime  | LONG   | NO        |                        |
| endTime    | LONG   | NO        |                        |
| limit      | INT    | NO        | Default 500; max 1000. |
| recvWindow | LONG   | NO        |                        |
| timestamp  | LONG   | YES       |                        |

**Notes:**

* If `orderId` is set, it will get orders >= that `orderId`. Otherwise most recent orders are returned.
* The query time period must be less then 7 days( default as the recent 7 days).

## Futures Account Balance v3 (USER_DATA)

> **Response:**

```javascript
[
 	{
 		"accountAlias": "SgsR",    // unique account code
 		"asset": "USDT",  	// asset name
 		"balance": "122607.35137903", // wallet balance
 		"crossWalletBalance": "23.72469206", // crossed wallet balance
  		"crossUnPnl": "0.00000000"  // unrealized profit of crossed positions
  		"availableBalance": "23.72469206",       // available balance
  		"maxWithdrawAmount": "23.72469206",     // maximum amount for transfer out
  		"marginAvailable": true,    // whether the asset can be used as margin in Multi-Assets mode
  		"updateTime": 1617939110373
	}
]
```

``GET /fapi/v3/balance (HMAC SHA256)``

**Weight:**
5

**Parameters:**

| Name       | Type | Mandatory | Description |
| ---------- | ---- | --------- | ----------- |
| recvWindow | LONG | NO        |             |
| timestamp  | LONG | YES       |             |

## Account Information v3 (USER_DATA)

> **Response:**

```javascript
{
	"feeTier": 0,  		// account commisssion tier 
 	"canTrade": true,  	// if can trade
 	"canDeposit": true,  	// if can transfer in asset
 	"canWithdraw": true, 	// if can transfer out asset
 	"updateTime": 0,
 	"totalInitialMargin": "0.00000000",    // total initial margin required with current mark price (useless with isolated positions), only for USDT asset
 	"totalMaintMargin": "0.00000000",  	  // total maintenance margin required, only for USDT asset
 	"totalWalletBalance": "23.72469206",     // total wallet balance, only for USDT asset
 	"totalUnrealizedProfit": "0.00000000",   // total unrealized profit, only for USDT asset
 	"totalMarginBalance": "23.72469206",     // total margin balance, only for USDT asset
 	"totalPositionInitialMargin": "0.00000000",    // initial margin required for positions with current mark price, only for USDT asset
 	"totalOpenOrderInitialMargin": "0.00000000",   // initial margin required for open orders with current mark price, only for USDT asset
 	"totalCrossWalletBalance": "23.72469206",      // crossed wallet balance, only for USDT asset
 	"totalCrossUnPnl": "0.00000000",	  // unrealized profit of crossed positions, only for USDT asset
 	"availableBalance": "23.72469206",       // available balance, only for USDT asset
 	"maxWithdrawAmount": "23.72469206"     // maximum amount for transfer out, only for USDT asset
 	"assets": [
 		{
 			"asset": "USDT",			// asset name
		   	"walletBalance": "23.72469206",      // wallet balance
		   	"unrealizedProfit": "0.00000000",    // unrealized profit
		   	"marginBalance": "23.72469206",      // margin balance
		   	"maintMargin": "0.00000000",	    // maintenance margin required
		   	"initialMargin": "0.00000000",    // total initial margin required with current mark price 
		   	"positionInitialMargin": "0.00000000",    //initial margin required for positions with current mark price
		   	"openOrderInitialMargin": "0.00000000",   // initial margin required for open orders with current mark price
		   	"crossWalletBalance": "23.72469206",      // crossed wallet balance
		   	"crossUnPnl": "0.00000000"       // unrealized profit of crossed positions
		   	"availableBalance": "23.72469206",       // available balance
		   	"maxWithdrawAmount": "23.72469206",     // maximum amount for transfer out
		   	"marginAvailable": true,    // whether the asset can be used as margin in Multi-Assets mode
		   	"updateTime": 1625474304765 // last update time 
		},
		{
 			"asset": "BUSD",			// asset name
		   	"walletBalance": "103.12345678",      // wallet balance
		   	"unrealizedProfit": "0.00000000",    // unrealized profit
		   	"marginBalance": "103.12345678",      // margin balance
		   	"maintMargin": "0.00000000",	    // maintenance margin required
		   	"initialMargin": "0.00000000",    // total initial margin required with current mark price 
		   	"positionInitialMargin": "0.00000000",    //initial margin required for positions with current mark price
		   	"openOrderInitialMargin": "0.00000000",   // initial margin required for open orders with current mark price
		   	"crossWalletBalance": "103.12345678",      // crossed wallet balance
		   	"crossUnPnl": "0.00000000"       // unrealized profit of crossed positions
		   	"availableBalance": "103.12345678",       // available balance
		   	"maxWithdrawAmount": "103.12345678",     // maximum amount for transfer out
		   	"marginAvailable": true,    // whether the asset can be used as margin in Multi-Assets mode
		   	"updateTime": 1625474304765 // last update time
		}
	],
 	"positions": [  // positions of all symbols in the market are returned
 		// only "BOTH" positions will be returned with One-way mode
 		// only "LONG" and "SHORT" positions will be returned with Hedge mode
 		{
		 	"symbol": "BTCUSDT",  	// symbol name
		   	"initialMargin": "0",	// initial margin required with current mark price 
		   	"maintMargin": "0",		// maintenance margin required
		   	"unrealizedProfit": "0.00000000",  // unrealized profit
		   	"positionInitialMargin": "0",      // initial margin required for positions with current mark price
		   	"openOrderInitialMargin": "0",     // initial margin required for open orders with current mark price
		   	"leverage": "100",		// current initial leverage
		   	"isolated": true,  		// if the position is isolated
		   	"entryPrice": "0.00000",  	// average entry price
		   	"maxNotional": "250000",  	// maximum available notional with current leverage
		   	"positionSide": "BOTH",  	// position side
		   	"positionAmt": "0",			// position amount
		   	"updateTime": 0           // last update time
		}
  	]
}
```

``GET /fapi/v3/accountWithJoinMargin (HMAC SHA256)``

Get current account information.

**Weight:**
5

**Parameters:**

| Name       | Type | Mandatory | Description |
| ---------- | ---- | --------- | ----------- |
| recvWindow | LONG | NO        |             |
| timestamp  | LONG | YES       |             |

## Change Initial Leverage (TRADE)

> **Response:**

```javascript
{
 	"leverage": 21,
 	"maxNotionalValue": "1000000",
 	"symbol": "BTCUSDT"
}
```

``POST /fapi/v3/leverage (HMAC SHA256)``

Change user's initial leverage of specific symbol market.

**Weight:**
1

**Parameters:**

| Name       | Type   | Mandatory | Description                                |
| ---------- | ------ | --------- | ------------------------------------------ |
| symbol     | STRING | YES       |                                            |
| leverage   | INT    | YES       | target initial leverage: int from 1 to 125 |
| recvWindow | LONG   | NO        |                                            |
| timestamp  | LONG   | YES       |                                            |

## Change Margin Type (TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /fapi/v3/marginType (HMAC SHA256)``

**Weight:**
1

**Parameters:**

| Name       | Type   | Mandatory | Description       |
| ---------- | ------ | --------- | ----------------- |
| symbol     | STRING | YES       |                   |
| marginType | ENUM   | YES       | ISOLATED, CROSSED |
| recvWindow | LONG   | NO        |                   |
| timestamp  | LONG   | YES       |                   |

## Modify Isolated Position Margin (TRADE)

> **Response:**

```javascript
{
	"amount": 100.0,
  	"code": 200,
  	"msg": "Successfully modify position margin.",
  	"type": 1
}
```

``POST /fapi/v3/positionMargin (HMAC SHA256)``

**Weight:**
1

**Parameters:**

| Name         | Type    | Mandatory | Description                                                                                         |
| ------------ | ------- | --------- | --------------------------------------------------------------------------------------------------- |
| symbol       | STRING  | YES       |                                                                                                     |
| positionSide | ENUM    | NO        | Default`BOTH` for One-way Mode ; `LONG` or `SHORT` for Hedge Mode. It must be sent with Hedge Mode. |
| amount       | DECIMAL | YES       |                                                                                                     |
| type         | INT     | YES       | 1: Add position margin，2: Reduce position margin                                                   |
| recvWindow   | LONG    | NO        |                                                                                                     |
| timestamp    | LONG    | YES       |                                                                                                     |

* Only for isolated symbol

## Get Position Margin Change History (TRADE)

> **Response:**

```javascript
[
	{
		"amount": "23.36332311",
	  	"asset": "USDT",
	  	"symbol": "BTCUSDT",
	  	"time": 1578047897183,
	  	"type": 1,
	  	"positionSide": "BOTH"
	},
	{
		"amount": "100",
	  	"asset": "USDT",
	  	"symbol": "BTCUSDT",
	  	"time": 1578047900425,
	  	"type": 1,
	  	"positionSide": "LONG"
	}
]
```

``GET /fapi/v3/positionMargin/history (HMAC SHA256)``

**Weight:**
1

**Parameters:**

| Name       | Type   | Mandatory | Description                                       |
| ---------- | ------ | --------- | ------------------------------------------------- |
| symbol     | STRING | YES       |                                                   |
| type       | INT    | NO        | 1: Add position margin，2: Reduce position margin |
| startTime  | LONG   | NO        |                                                   |
| endTime    | LONG   | NO        |                                                   |
| limit      | INT    | NO        | Default: 500                                      |
| recvWindow | LONG   | NO        |                                                   |
| timestamp  | LONG   | YES       |                                                   |

## Position Information v3 (USER_DATA)

> **Response:**

> For One-way position mode:

```javascript
[
  	{
  		"entryPrice": "0.00000",
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "0.00000000",
  		"leverage": "10", 
  		"liquidationPrice": "0", 
  		"markPrice": "6679.50671178",
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "0.000", 
  		"symbol": "BTCUSDT", 
  		"unRealizedProfit": "0.00000000", 
  		"positionSide": "BOTH",
  		"updateTime": 0
  	}
]
```

> For Hedge position mode:

```javascript
[
  	{
  		"entryPrice": "6563.66500", 
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "15517.54150468",
  		"leverage": "10",
  		"liquidationPrice": "5930.78",
  		"markPrice": "6679.50671178",
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "20.000", 
  		"symbol": "BTCUSDT", 
  		"unRealizedProfit": "2316.83423560"
  		"positionSide": "LONG", 
  		"updateTime": 1625474304765
  	},
  	{
  		"entryPrice": "0.00000",
  		"marginType": "isolated", 
  		"isAutoAddMargin": "false",
  		"isolatedMargin": "5413.95799991", 
  		"leverage": "10", 
  		"liquidationPrice": "7189.95", 
  		"markPrice": "6679.50671178",
  		"maxNotionalValue": "20000000", 
  		"positionAmt": "-10.000", 
  		"symbol": "BTCUSDT",
  		"unRealizedProfit": "-1156.46711780" 
  		"positionSide": "SHORT",
  		"updateTime": 0
  	}
]
```

``GET /fapi/v3/positionRisk (HMAC SHA256)``

Get current position information.

**Weight:**
5

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | NO        |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |

**Note**
Please use with user data stream `ACCOUNT_UPDATE` to meet your timeliness and accuracy needs.

## Account Trade List (USER_DATA)

> **Response:**

```javascript
[
  {
  	"buyer": false,
  	"commission": "-0.07819010",
  	"commissionAsset": "USDT",
  	"id": 698759,
  	"maker": false,
  	"orderId": 25851813,
  	"price": "7819.01",
  	"qty": "0.002",
  	"quoteQty": "15.63802",
  	"realizedPnl": "-0.91539999",
  	"side": "SELL",
  	"positionSide": "SHORT",
  	"symbol": "BTCUSDT",
  	"time": 1569514978020
  }
]
```

``GET /fapi/v3/userTrades  (HMAC SHA256)``

Get trades for a specific account and symbol.

**Weight:**
5

**Parameters:**

| Name       | Type   | Mandatory | Description                                              |
| ---------- | ------ | --------- | -------------------------------------------------------- |
| symbol     | STRING | YES       |                                                          |
| startTime  | LONG   | NO        |                                                          |
| endTime    | LONG   | NO        |                                                          |
| fromId     | LONG   | NO        | Trade id to fetch from. Default gets most recent trades. |
| limit      | INT    | NO        | Default 500; max 1000.                                   |
| recvWindow | LONG   | NO        |                                                          |
| timestamp  | LONG   | YES       |                                                          |

* If `startTime` and `endTime` are both not sent, then the last 7 days' data will be returned.
* The time between `startTime` and `endTime` cannot be longer than 7 days.
* The parameter `fromId` cannot be sent with `startTime` or `endTime`.

## Get Income History(USER_DATA)

> **Response:**

```javascript
[
	{
    	"symbol": "",					// trade symbol, if existing
    	"incomeType": "TRANSFER",	// income type
    	"income": "-0.37500000",  // income amount
    	"asset": "USDT",				// income asset
    	"info":"TRANSFER",			// extra information
    	"time": 1570608000000,
    	"tranId":"9689322392",		// transaction id
    	"tradeId":""					// trade id, if existing
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

``GET /fapi/v3/income (HMAC SHA256)``

**Weight:**
30

**Parameters:**

| Name       | Type   | Mandatory | Description                                                                                                                      |
| ---------- | ------ | --------- | -------------------------------------------------------------------------------------------------------------------------------- |
| symbol     | STRING | NO        |                                                                                                                                  |
| incomeType | STRING | NO        | "TRANSFER"，"WELCOME_BONUS", "REALIZED_PNL"，"FUNDING_FEE", "COMMISSION", "INSURANCE_CLEAR", and "MARKET_MERCHANT_RETURN_REWARD" |
| startTime  | LONG   | NO        | Timestamp in ms to get funding from INCLUSIVE.                                                                                   |
| endTime    | LONG   | NO        | Timestamp in ms to get funding until INCLUSIVE.                                                                                  |
| limit      | INT    | NO        | Default 100; max 1000                                                                                                            |
| recvWindow | LONG   | NO        |                                                                                                                                  |
| timestamp  | LONG   | YES       |                                                                                                                                  |

* If neither `startTime` nor `endTime` is sent, the recent 7-day data will be returned.
* If `incomeType ` is not sent, all kinds of flow will be returned
* "trandId" is unique in the same incomeType for a user

## Notional and Leverage Brackets (USER_DATA)

> **Response:**

```javascript
[
    {
        "symbol": "ETHUSDT",
        "brackets": [
            {
                "bracket": 1,   // Notional bracket
                "initialLeverage": 75,  // Max initial leverage for this bracket
                "notionalCap": 10000,  // Cap notional of this bracket
                "notionalFloor": 0,  // Notional threshold of this bracket 
                "maintMarginRatio": 0.0065, // Maintenance ratio for this bracket
                "cum":0 // Auxiliary number for quick calculation 
     
            },
        ]
    }
]
```

> **OR** (if symbol sent)

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

``GET /fapi/v3/leverageBracket``

**Weight:** 1

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | NO        |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |

## Position ADL Quantile Estimation (USER_DATA)

> **Response:**

```javascript
[
	{
		"symbol": "ETHUSDT", 
		"adlQuantile": 
			{
				// if the positions of the symbol are crossed margined in Hedge Mode, "LONG" and "SHORT" will be returned a same quantile value, and "HEDGE" will be returned instead of "BOTH".
				"LONG": 3,  
				"SHORT": 3, 
				"HEDGE": 0   // only a sign, ignore the value
			}
		},
 	{
 		"symbol": "BTCUSDT", 
 		"adlQuantile": 
 			{
 				// for positions of the symbol are in One-way Mode or isolated margined in Hedge Mode
 				"LONG": 1, 	// adl quantile for "LONG" position in hedge mode
 				"SHORT": 2, 	// adl qauntile for "SHORT" position in hedge mode
 				"BOTH": 0		// adl qunatile for position in one-way mode
 			}
 	}
 ]
```

``GET /fapi/v3/adlQuantile``

**Weight:** 5

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | NO        |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |

* Values update every 30s.
* Values 0, 1, 2, 3, 4 shows the queue position and possibility of ADL from low to high.
* For positions of the symbol are in One-way Mode or isolated margined in Hedge Mode, "LONG", "SHORT", and "BOTH" will be returned to show the positions' adl quantiles of different position sides.
* If the positions of the symbol are crossed margined in Hedge Mode:
  
  * "HEDGE" as a sign will be returned instead of "BOTH";
  * A same value caculated on unrealized pnls on long and short sides' positions will be shown for "LONG" and "SHORT" when there are positions in both of long and short sides.

## User's Force Orders (USER_DATA)

> **Response:**

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

``GET /fapi/v3/forceOrders``

**Weight:** 20 with symbol, 50 without symbol

**Parameters:**

| Name          | Type   | Mandatory | Description                                                 |
| ------------- | ------ | --------- | ----------------------------------------------------------- |
| symbol        | STRING | NO        |                                                             |
| autoCloseType | ENUM   | NO        | "LIQUIDATION" for liquidation orders, "ADL" for ADL orders. |
| startTime     | LONG   | NO        |                                                             |
| endTime       | LONG   | NO        |                                                             |
| limit         | INT    | NO        | Default 50; max 100.                                        |
| recvWindow    | LONG   | NO        |                                                             |
| timestamp     | LONG   | YES       |                                                             |

* If "autoCloseType" is not sent, orders with both of the types will be returned
* If "startTime" is not sent, data within 7 days before "endTime" can be queried

## User Commission Rate (USER_DATA)

> **Response:**

```javascript
{
	"symbol": "BTCUSDT",
  	"makerCommissionRate": "0.0002",  // 0.02%
  	"takerCommissionRate": "0.0004"   // 0.04%
}
```

``GET /fapi/v3/commissionRate (HMAC SHA256)``

**Weight:**
20

**Parameters:**

| Name       | Type   | Mandatory | Description |
| ---------- | ------ | --------- | ----------- |
| symbol     | STRING | YES       |             |
| recvWindow | LONG   | NO        |             |
| timestamp  | LONG   | YES       |             |



## **Bind Sub-Account (USER_DATA)**

> **Response:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/sub-accounts/bind`

**Weight:** 5

**Parameters:**

| Name           | Type   | Mandatory | Description |
| -------------- | ------ | --------- | ----------- |
| childAddress   | STRING | YES       | Sub-account wallet address |
| name           | STRING | YES       | Sub-account name |
| nonce          | LONG   | YES       | Microsecond-level timestamp, used for replay attack prevention |
| user           | STRING | YES       | Master account wallet address |
| childSignature | STRING | YES       | Sub-account's signature over the message body (see Signature Instructions below) |
| signature      | STRING | YES       | Master account's signature over the message body, **must be signed using the master account's wallet private key** (see Signature Instructions below) |

---

### Signature Instructions

This endpoint requires **two independent signatures**. The message body differs between the two:

#### Step 1: Sub-Account Signature (childSignature)

The sub-account signs the following message body using **its own wallet private key**:

```
childAddress={childAddress}&name={name}&nonce={nonce}&user={user}
```

#### Step 2: Master Account Signature (signature)

The master account signs the following message body using **its own wallet private key** (not the API Key). The message body **appends `childSignature`** to the one used in Step 1:

```
childAddress={childAddress}&name={name}&nonce={nonce}&user={user}&childSignature={childSignature}
```

> The key difference between the two message bodies: the master account's message body **includes** `childSignature`, while the sub-account's does not.

#### Supported Signing Algorithms

| Account Type   | Signing Algorithm                                          | Encoding Format |
| -------------- | ---------------------------------------------------------- | --------------- |
| EVM Address    | EIP-712 Typed Data (chainId=1666, message.msg=message body) | Hex             |
| Solana Address | Ed25519                                                    | Base58          |

> The address types of the master account and sub-account must match (EVM with EVM, Solana with Solana).

---

### Important Notes

* `signature` must be signed using the **master account's wallet private key** — the signer private key must not be used as a substitute
* `childSignature` and `signature` are not interchangeable
* The `user` field must match the authenticated master account address and cannot be forged
* Only **whitelisted addresses** are supported for creation. Please contact the **project team** for configuration.


## **Create Sub-Account (TRADE)**

> **Response:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/createSubAccount`

**Weight:** 5

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| subSourceAddr | STRING | YES | Sub-account wallet address |
| subAccountName | STRING | YES | Sub-account name |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| user | STRING | YES | Master account wallet address |
| signer | STRING | YES | Signer address associated with the master account |
| childSignature | STRING | YES | Sub-account's signature over the message body (see Signature Instructions below) |
| signature | STRING | YES | Master account's signature over the message body, **must be signed using the master account's wallet private key** (see Signature Instructions below) |

---

### Signature Instructions

This endpoint requires **two independent signatures**. The message body differs between the two:

#### Step 1: Sub-Account Signature (childSignature)

The sub-account signs the following message body using **its own wallet private key:**

```
subAccountName={subAccountName}&subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}
```

#### Step 2: Master Account Signature (signature)

The master account signs the following message body using **its own wallet private key** (not the signer private key). The message body **appends `childSignature`** to the one used in Step 1:

```
subAccountName={subAccountName}&subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}&childSignature={childSignature}
```

> The key difference between the two message bodies: the master account's message body **includes** `childSignature`, while the sub-account's does not.

#### Supported Signing Algorithms

| Account Type | Signing Algorithm | Encoding Format |
|---|---|---|
| EVM Address | EIP-712 Typed Data (chainId=1666, message.msg=message body) | Hex |
| Solana Address | Ed25519 | Base58 |

> The address types of the master account and sub-account must match (EVM with EVM, Solana with Solana).

---

### Important Notes

* `signature` **must be signed using the master account's wallet private key** — the signer private key must not be used as a substitute.
* `childSignature` and `signature` are not interchangeable.
* The `user` field must match the authenticated master account address and cannot be forged.



## **Get Sub-Account List (USER_DATA)**

> **Response:**

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

**Weight:** 5

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| user | STRING | YES | Master account wallet address |
| signer | STRING | YES | Signer address associated with the master account |
| signature | STRING | YES | Signature over the message body (see Signature Instructions below) |

**Response fields:**

| Name | Type | Description |
|------|------|-------------|
| accountId | LONG | Account ID |
| subAccountName | STRING | Sub-account name |
| parentAccount | BOOLEAN | Whether this entry is the master account (`true`) or a sub-account (`false`) |

---

### Signature Instructions

Sign the following message body using the **signer's private key** (EIP-712, always used regardless of whether the parent account is EVM or Solana):

```
nonce={nonce}&user={user}&signer={signer}
```

#### Signing Algorithm

| Algorithm | Encoding Format |
|---|---|
| EIP-712 Typed Data (chainId=1666, message.msg=message body) | Hex |

> Unlike other sub-account endpoints, `getSubAccountList` always uses the **signer private key** (EIP-712) for signing, even when the master account is a Solana address.



## **Update Sub-Account (TRADE)**

> **Response:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/updateSubAccount`

**Weight:** 5

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| subSourceAddr | STRING | YES | Sub-account wallet address to update |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| user | STRING | YES | Master account wallet address |
| signer | STRING | YES | Signer address associated with the master account |
| subAccountName | STRING | NO | New sub-account name (at least one of `subAccountName` or `status` must be provided) |
| status | STRING | NO | Sub-account status: `NORMAL` (active) or `FROZEN` (frozen) |
| signature | STRING | YES | Master account's signature over the message body, **must be signed using the master account's wallet private key** (see Signature Instructions below) |

---

### Signature Instructions

The master account signs the following message body using **its own wallet private key** (not the signer private key):

```
subSourceAddr={subSourceAddr}&nonce={nonce}&user={user}&signer={signer}[&subAccountName={subAccountName}][&status={status}]
```

> Only include optional fields (`subAccountName`, `status`) in the message body if they are included in the request.

#### Supported Signing Algorithms

| Account Type | Signing Algorithm | Encoding Format |
|---|---|---|
| EVM Address | EIP-712 Typed Data (chainId=1666, message.msg=message body) | Hex |
| Solana Address | Ed25519 | Base58 |

---

### Important Notes

* `signature` **must be signed using the master account's wallet private key** — the signer private key must not be used as a substitute.
* At least one of `subAccountName` or `status` must be provided.
* `status` accepted values: `NORMAL` (unfreeze / active), `FROZEN` (freeze).
* A frozen sub-account cannot initiate transfers.



## **Sub-Account Transfer (TRADE)**

> **Response:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/subAccountTransfer`

**Weight:** 5

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| toAccountAddress | STRING | YES | Destination wallet address |
| asset | STRING | YES | Asset name (e.g., `USDT`) |
| amount | STRING | YES | Transfer amount |
| kindType | ENUM | YES | Transfer direction (see table below) |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| user | STRING | YES | Signing account wallet address (master account address in most cases; sub-account address when the sub-account initiates a transfer to the master account) |
| signer | STRING | YES | Signer address associated with `user` |
| fromAccountAddress | STRING | NO | Source wallet address. Required when the source account differs from `user` (e.g., sub→sub transfers or master→sub transfers initiated by a third party) |
| signature | STRING | YES | Signature over the message body, **must be signed using the `user` account's wallet private key** (see Signature Instructions below) |

**`kindType` values:**

| Value | Description |
|-------|-------------|
| `FUTURE_FUTURE` | Perpetual account → Perpetual account |
| `FUTURE_SPOT` | Perpetual account → Spot account |
| `SPOT_FUTURE` | Spot account → Perpetual account |
| `SPOT_SPOT` | Spot account → Spot account |

---

### Signature Instructions

Sign the following message body using the **`user` account's wallet private key** (not the signer private key):

**Without `fromAccountAddress`:**
```
toAccountAddress={toAccountAddress}&asset={asset}&amount={amount}&kindType={kindType}&nonce={nonce}&user={user}&signer={signer}
```

**With `fromAccountAddress`:**
```
toAccountAddress={toAccountAddress}&asset={asset}&amount={amount}&kindType={kindType}&nonce={nonce}&user={user}&signer={signer}&fromAccountAddress={fromAccountAddress}
```

#### Supported Signing Algorithms

| Account Type | Signing Algorithm | Encoding Format |
|---|---|---|
| EVM Address | EIP-712 Typed Data (chainId=1666, message.msg=message body) | Hex |
| Solana Address | Ed25519 | Base58 |

---

### Transfer Scenarios

| Scenario | `user` | `fromAccountAddress` | Signing Key |
|----------|--------|----------------------|-------------|
| Master → Sub | Master account address | *(not required)* | Master account wallet private key |
| Sub → Master | Sub-account address | *(not required)* | Sub-account wallet private key |
| Sub A → Sub B | Master account address | Sub A address | Master account wallet private key |
| Sub A → Sub B | Sub-account address | *(not required)* | Sub-account wallet private key |

---

### Important Notes

* `signature` **must be signed using the `user` account's wallet private key** — the signer private key must not be used as a substitute.
* The `user` field must match the address corresponding to the private key used for signing.
* When signing with a **sub-account's private key**, transfers **to the master account** are supported. Sub→Sub transfers are also supported, with the source address being the signing **sub-account**.
* Transfers to or from a **frozen sub-account** will fail.
* Transfers to **external addresses** (addresses not within the sub-account relationship) are not supported.



## **Migrate User Assets (WITHDRAW)**

> **Response:**

```javascript
{
    "batchId": "a1B2c3D4e5F6g7H8i9J0k1"   // batch ID for querying migration history
}
```

`POST /fapi/v3/asset/migrateUser`

**Weight:** 50

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| user | STRING | YES | Source account wallet address — the account from which assets will be migrated |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| signature | STRING | YES | Signature over the request body, signed using the `user` account's wallet private key (EIP-712 for EVM addresses, Ed25519 for Solana addresses) |

**Notes:**

* The source account must have **no open positions** before migration can proceed; the request will be rejected otherwise.
* The source account must have **no open orders** before migration can proceed; the request will be rejected otherwise.
* All assets with a positive available balance are automatically included. Assets with zero balance are skipped.
* Up to **300 assets** are processed per migration batch.
* Assets are transferred to the **authenticated user's account** (the destination).
* If the source account has no assets with a positive balance, an empty response is returned and no transfer is initiated.
* Record the returned `batchId` to query migration status via the Migrate User Assets History endpoint below.

---

## **Migrate User Assets History (USER_DATA)**

> **Response:**

```javascript
{
    "batchId": "a1B2c3D4e5F6g7H8i9J0k1",
    "totalCount": 2,                    // total number of records
    "successCount": 1,                  // number of successful records (status=S)
    "processingCount": 0,               // number of processing records (status=P)
    "failCount": 0,                     // number of failed records (status=F)
    "initCount": 1,                     // number of pending records (status=I)
    "details": [
        {
            "id": 1001,
            "fromUserId": 12345678,
            "toUserId": 87654321,
            "asset": "USDT",
            "amount": "500.00000000",
            "tranId": 9876543210,
            "status": "S",              // I=pending, S=success, F=failed
            "fromStatus": "S",          // S=success, F=failed, P=processing
            "fromErrorCode": null,
            "fromResponse": null,
            "toStatus": "S",            // S=success, F=failed, P=processing
            "toErrorCode": null,
            "toResponse": null
        },
        {
            "id": 1002,
            "fromUserId": 12345678,
            "toUserId": 87654321,
            "asset": "BTC",
            "amount": "0.05000000",
            "tranId": null,             // null if not yet processed
            "status": "I",              // I=pending, S=success, F=failed
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

**Weight:** 50

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| batchId | STRING | YES | Batch ID returned from the Migrate User Assets endpoint |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| user | STRING | YES | Authenticated user's wallet address |
| signer | STRING | YES | Signer address associated with the `user` account |
| signature | STRING | YES | Signature over the request body |

**Notes:**

* `batchId` is mandatory. Each `batchId` corresponds to a single migration batch.
* The query is scoped to the authenticated user — only migrations initiated by or associated with the authenticated account are returned.

## Register and Approve Agent (PUBLIC)

> **Response:**

```javascript
{
    "code": 200,
    "msg": "success"
}
```

`POST /fapi/v3/registerAndApproveAgent`

Registers a new API agent account and grants it trading/withdrawal permissions in a single call. Once approved, the agent can use API Keys to act on behalf of the user within the specified permission scope.

**Weight:** 50

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| user | STRING | YES | User's wallet address |
| nonce | LONG | YES | Microsecond-level timestamp, used for replay attack prevention |
| agentName | STRING | YES | Display name for the agent |
| agentAddress | STRING | YES | Agent's wallet address |
| expired | LONG | YES | Agent expiration timestamp (milliseconds) |
| signatureChainId | LONG | YES | Chain ID used when generating the signature (`56` for EVM addresses, `101` for Solana addresses) |
| signature | STRING | YES | Signature over the message body, signed using the user's wallet private key (see Signature Instructions below) |
| canSpotTrade | BOOLEAN | YES | Whether the agent is permitted to place spot orders |
| canPerpTrade | BOOLEAN | YES | Whether the agent is permitted to place perpetual futures orders |
| canWithdraw | BOOLEAN | YES | Whether the agent is permitted to initiate withdrawals |
| ipWhitelist | STRING | NO | Space-separated list of permitted IP addresses or CIDR ranges (e.g. `192.168.1.1 10.0.0.0/24`). **Required when `canWithdraw` is `true`.** |
| agentCode | STRING | NO | Referral/invitation code for agent registration |

---

### Signature Instructions

Sign the following message body using the **user's wallet private key**:

```
msg: user={user}&nonce={nonce}&agentName={agentName}&agentAddress={agentAddress}&expired={expired}&signatureChainId={signatureChainId}&canSpotTrade={canSpotTrade}&canPerpTrade={canPerpTrade}&canWithdraw={canWithdraw}&ipWhitelist={ipWhitelist}
```

> **EVM addresses only:** wrap the `msg` string above as `message.msg` in the following EIP-712 typed data structure before signing:

> **⚠️ Note:** `domain.chainId` must be set to the value of the request parameter `signatureChainId` (`56` for EVM addresses, `101` for Solana addresses) — do **not** use the fixed value `714`.

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

#### Supported Signing Algorithms

| Account Type | Signing Algorithm | Encoding |
|---|---|---|
| EVM Address | EIP-712 Typed Data (chainId=signatureChainId, message.msg=message body) | Hex |
| Solana Address | Ed25519 | Base58 |

---

### Important Notes

* `nonce` must be a microsecond-precision timestamp. The difference from server time must not exceed **10 seconds**, and the same nonce cannot be reused.
* `expired` is the agent's validity deadline in **milliseconds**. The agent will automatically expire after this timestamp.
* `ipWhitelist` uses **spaces** as the delimiter and supports CIDR notation (e.g. `192.168.1.1 10.0.0.0/24`). **`ipWhitelist` is required and must not be empty when `canWithdraw` is `true`.**
* This endpoint is **unauthenticated** — no API Key or HMAC header is required. All authorization is verified through the on-chain `signature`.
* This endpoint combines agent registration and permission granting into a single call, equivalent to the standalone registration step followed by `approveAgent`.
