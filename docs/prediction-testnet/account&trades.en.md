
## Place order (TRADE)

**Response ACK:**

```javascript
{
  "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT", 
  "orderId": 28, 
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP", 
  "updateTime": 1507725176595, 
  "price": "0.00000000", 
  "avgPrice": "0.0000000000000000", 
  "origQty": "10.00000000", 
  "cumQty": "0",          
  "executedQty": "10.00000000", 
  "cumQuote": "10.00000000",
  "status": "FILLED",
  "timeInForce": "GTC", 
  "stopPrice": "0",    
  "origType": "LIMIT",  
  "type": "LIMIT", 
  "side": "SELL", 
}
```

`POST /api/v3/order`

Send order

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| side | ENUM | YES | See enum definition: Order direction |
| type | ENUM | YES | See enumeration definition: Order type |
| timeInForce | ENUM | NO | See enum definition: Time in force |
| quantity | DECIMAL | NO |  |
| quoteOrderQty | DECIMAL | NO |  |
| price | DECIMAL | NO |  |
| newClientOrderId | STRING | NO | Client-customized unique order ID. If not provided, one will be generated automatically. |
| stopPrice | DECIMAL | NO | Only STOP, STOP\_MARKET, TAKE\_PROFIT, TAKE\_PROFIT\_MARKET require this parameter |

Depending on the order `type`, certain parameters are mandatory:

| Type | Mandatory parameters |
| :---- | :---- |
| LIMIT | timeInForce, quantity, price |
| MARKET | quantity or quoteOrderQty |
| STOP and TAKE\_PROFIT | quantity, price, stopPrice |
| STOP\_MARKET and TAKE\_PROFIT\_MARKET | quantity, stopPrice |

Other information:

* Place a `MARKET` `SELL` market order; the user controls the amount of base assets to sell with the market order via `QUANTITY`.  
  * For example, when placing a `MARKET` `SELL` market order on the `BTC_UP_DOWN_5M_1778483280_YUSDT` pair, use `QUANTITY` to let the user specify how much BTC they want to sell.  
* For a `MARKET` `BUY` market order, the user controls how much of the quote asset they want to spend with `quoteOrderQty`; `QUANTITY` will be calculated by the system based on market liquidity. For example, when placing a `MARKET` `BUY` market order on the `BTC_UP_DOWN_5M_1778483280_YUSDT` pair, use `quoteOrderQty` to let the user choose how much USDT to use to buy BTC.  
* A `MARKET` order using `quoteOrderQty` will not violate the `LOT_SIZE` limit rules; the order will be executed as closely as possible to the given `quoteOrderQty`.  
* Unless a previous order has already been filled, orders set with the same `newClientOrderId` will be rejected.

## Cancel order (TRADE)

**Response**

```javascript
{
  "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT", 
  "orderId": 28, 
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP", 
  "updateTime": 1507725176595, 
  "price": "0.00000000", 
  "avgPrice": "0.0000000000000000", 
  "origQty": "10.00000000", 
  "cumQty": "0",            
  "executedQty": "10.00000000", 
  "cumQuote": "10.00000000", 
  "status": "CANCELED", 
  "timeInForce": "GTC", 
  "stopPrice": "0",    
  "origType": "LIMIT",  
  "type": "LIMIT", 
  "side": "SELL",
}
```

`DELETE /api/v3/order`

Cancel active orders

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| orderId | LONG | NO |  |
| origClientOrderId | STRING | NO |  |

At least one of `orderId` or `origClientOrderId` must be sent.

## Query order (USER\_DATA)

**Response**

```javascript
{
    "orderId": 38,
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",
    "status": "FILLED",
    "clientOrderId": "afMd4GBQyHkHpGWdiy34Li",
    "price": "20",
    "avgPrice": "12.0000000000000000",
    "origQty": "10",
    "executedQty": "10",
    "cumQuote": "120",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0",
    "origType": "LIMIT",
    "time": 1649913186270,
    "updateTime": 1649913186297
} 
```

`GET /api/v3/order`

Query order status

* Please note that orders meeting the following conditions will not be returned:  
  * The final status of the order is `CANCELED` or `EXPIRED`, **and**  
  * The order has no trade records, **and**  
  * Order creation time \+ 7 days \< current time

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| orderId | LONG | NO |  |
| origClientOrderId | STRING | NO |  |

Note:

* You must send at least one of `orderId` or `origClientOrderId`.

## Query Current Open Order (USER\_DATA)

**Response**

```javascript
{
    "orderId": 38,
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",
    "status": "NEW",
    "clientOrderId": "afMd4GBQyHkHpGWdiy34Li",
    "price": "20",
    "avgPrice": "12.0000000000000000",
    "origQty": "10",
    "executedQty": "10",
    "cumQuote": "120",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0",
    "origType": "LIMIT",
    "time": 1649913186270,
    "updateTime": 1649913186297
}
```

`GET /api/v3/openOrder`

Query current open order status.

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| orderId | LONG | NO |  |
| origClientOrderId | STRING | NO |  |

Note:

* You must send at least one of `orderId` or `origClientOrderId`.

## Current open orders (USER\_DATA)

**Response**

```javascript
[
    {
        "orderId": 349661, 
        "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT", 
        "status": "NEW", 
        "clientOrderId": "LzypgiMwkf3TQ8wwvLo8RA", 
        "price": "1.10000000", 
        "avgPrice": "0.0000000000000000", 
        "origQty": "5",  
        "executedQty": "0", 
        "cumQuote": "0", 
        "timeInForce": "GTC",
        "type": "LIMIT", 
        "side": "BUY",   
        "stopPrice": "0", 
        "origType": "LIMIT", 
        "time": 1756252940207, 
        "updateTime": 1756252940207, 
    }
]
```

`GET /api/v3/openOrders`

Retrieve all current open orders for trading pairs. Use calls without a trading pair parameter with caution.

**Weight:**

* With symbol ***1***  
* Without ***40***  

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO |  |

* If the symbol parameter is not provided, it will return the order books for all trading pairs.

## Cancel All Open Orders (TRADE)

> **Response**

```javascript
{
    "code": 200,
    "msg": "The operation of cancel all open order is done."
}
```

``
DEL /api/v3/allOpenOrders ``

**Weight:**
- ***1***

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderIdList | STRING | NO |  orderid array string
origClientOrderIdList | STRING | NO | clientOrderId array string


## Query all orders (USER\_DATA)

**Response**

```javascript
[
    {
        "orderId": 349661, 
        "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT", 
        "status": "NEW", 
        "clientOrderId": "LzypgiMwkf3TQ8wwvLo8RA", 
        "price": "1.10000000", 
        "avgPrice": "0.0000000000000000", 
        "origQty": "5",  
        "executedQty": "0", 
        "cumQuote": "0", 
        "timeInForce": "GTC", 
        "type": "LIMIT", 
        "side": "BUY",   
        "stopPrice": "0", 
        "origType": "LIMIT", 
        "time": 1756252940207, 
        "updateTime": 1756252940207, 
    }
]
```

`GET /api/v3/allOrders`

Retrieve all account orders; active, canceled, or completed.

* Please note that orders meeting the following conditions will not be returned:  
  * Order creation time \+ 7 days \< current time

**Weight:** 5

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| orderId | LONG | NO |  |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| limit | INT | NO | Default 500; maximum 1000 |

* The maximum query time range must not exceed 7 days.  
* By default, query data is from the last 7 days.


``
GET /api/v3/prediction/transactionHistory``
> **Response**

```javascript
[
   {
    "tranId": 1759115482304540227, 
    "tradeId": null,              
    "asset": "ASTER",             
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",                 
    "balanceDelta": "-500.00000000", 
    "balanceInfo": "TRADE_SOURCE",    
    "time": 1759115482000,       
    "type": "TRADE_SOURCE"      
   }
]
```

Query transaction records

**Weight:**
30

**Parameters:**

| Name | Type | Is it required? | Description |
------------ | ------------ | ------------ | ------------
asset | STRING | NO | asset
type | STRING | NO |  type
startTime | LONG | NO | startTime
endTime | LONG | NO |  endTime
limit | LONG | NO | default:100 max:1000

**Note:** 

*  `type`: `TRADE_TARGET`,`TRADE_SOURCE`,`TRANSFER_SPOT_TO_FUTURE`,`TRANSFER_FUTURE_TO_SPOT`,`TRANSFER_SPOT_TO_SPOT`,`AIRDROP`,`DIVIDEND`,`TRANSFER_REFUND`,`INTERNAL_TRANSFER`,`TRANSFER`,`SWAP`,`COMMISSION_REBATE`,`CASH_BACK`,`STAKING_WITHDRAW`, `STAKING_CLAIM`, `STAKING_DELEGATE`,`PREDICTION_CLAIM`,`PREDICTION_USER_REBATE`,`PREDICTION_SETTLE`,`PREDICTION_SETTLE_FEE`,`PREDICTION_MINT`,`PREDICTION_BURN` 
*  If startTime and endTime are not provided, only data from the most recent 7 days will be returned.

## Perp-spot transfer (TRADE)

**Response:**

```javascript
{
    "tranId": 21841, //Tran Id
    "status": "SUCCESS" //Status
}
```

`POST /api/v3/asset/wallet/transfer `

**Weight:** 5

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| amount | DECIMAL | YES | Quantity |
| asset | STRING | YES | Asset |
| clientTranId | STRING | YES | Transaction ID |
| kindType | STRING | YES | Transaction type |

* kindType FUTURE_SPOT(future to spot)/SPOT_FUTURE(spot to future)

## Prediction market mint (TRADE)

**Response**

```javascript
{
    "clientOrderId": "xxx",        // Client order ID
    "quoteAmount": "10.00000000",  // Total quote asset spent
    "yesPrice": "0.55000000",      // YES token price at mint time
    "noPrice": "0.45000000",       // NO token price at mint time (yesPrice + noPrice = 1)
    "pairCount": "10.00000000"     // Number of YES+NO pairs minted
}
```

`POST /api/v3/prediction/mint`

Mint YES+NO token pairs for a prediction market. The system deducts an equivalent amount of quote asset at the current market price and credits equal quantities of YES and NO tokens to the account.

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES | The YES token trading pair of the prediction market |
| quantity | DECIMAL | YES | Number of YES+NO token pairs to mint |
| newClientOrderId | STRING | NO | Client-customized unique order ID. If not provided, one will be generated automatically. |

Notes:
* `symbol` must be a valid prediction market token trading pair with status `TRADING` or `PENDING_TRADING`
* `quantity` represents the number of pairs to mint simultaneously; the account will receive equal amounts of YES and NO tokens
* `yesPrice` + `noPrice` = 1 (in quote asset units)


## Prediction market burn (TRADE)

**Response**

```javascript
{
    "clientOrderId": "xxx",        // Client order ID
    "quoteAmount": "9.80000000",   // Total quote asset returned after burn
    "yesPrice": null,              // Not returned for burn operations
    "noPrice": null,               // Not returned for burn operations
    "pairCount": "10.00000000"     // Number of YES+NO pairs burned
}
```

`POST /api/v3/prediction/burn`

Burn equal quantities of YES+NO token pairs to redeem the corresponding quote asset. The account must hold equal amounts of YES and NO tokens to perform a burn.

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES | The YES token trading pair of the prediction market |
| quantity | DECIMAL | YES | Number of YES+NO token pairs to burn |
| newClientOrderId | STRING | NO | Client-customized unique order ID. If not provided, one will be generated automatically. |

Notes:
* `symbol` must be a valid prediction market token trading pair with status `TRADING`, `PENDING_TRADING`, or `CLOSED`
* `quantity` represents the number of pairs to burn simultaneously; the account must hold the corresponding amounts of both YES and NO tokens
* `yesPrice` and `noPrice` are `null` in the burn response
* Burn operations are still available after the market is `CLOSED`


## Query prediction market current positions (USER\_DATA)

**Response**

```javascript
[
  {
    "marketName": "BTC_UP_DOWN_20260512",      // Prediction market name
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",        // Trading pair
    "assetName": "USDT",                        // Settlement asset
    "type": "YES",                              // Position direction: "YES" or "NO"
    "openAvgPrice": "0.65000000",               // Average open price
    "cumQty": "100.00000000",                   // Total position quantity
    "closeAvgPrice": "0.00000000",              // Average close price (0 if not closed)
    "realizedPnl": "0.00000000",                // Realized PnL
    "closeQty": "0.00000000",                   // Closed quantity
    "insertTime": 1747000000000,                // Position open time (millisecond timestamp)
    "commissionFee": "0.10000000",              // Commission fee paid
    "commissionAsset": "USDT",                  // Commission fee asset
    "balance": "65.00000000",                   // Position value (quantity × average open price)
    "availableBalance": "65.00000000",          // Available balance
  }
]
```

`GET /api/v3/prediction/positions`

Query the current account's prediction market position list.

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO | Trading pair; if not provided, returns all positions |
| signer | STRING | YES | API wallet address |
| nonce | STRING | YES | Current time in microseconds |
| signature | STRING | YES | Signature |

## Query prediction market position history (USER\_DATA)

**Response**

```javascript
[
  {
    "marketName": "BTC_UP_DOWN_20260501",       // Prediction market name
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",        // Trading pair
    "assetName": "USDT",                        // Settlement asset
    "type": "YES",                              // Position direction: "YES" or "NO"
    "openAvgPrice": "0.72000000",               // Average open price
    "cumQty": "200.00000000",                   // Total position quantity
    "closeAvgPrice": "1.00000000",              // Average close price
    "realizedPnl": "56.00000000",               // Realized PnL
    "closeQty": "200.00000000",                 // Closed quantity
    "insertTime": 1746000000000,                // Position open time (millisecond timestamp)
    "closeTime": 1746086400000,                 // Close/settlement time (millisecond timestamp)
    "status": "settled",                        // Position status: "close" (manually closed) or "settled" (market settled)
    "commissionFee": "0.10000000",              // Commission fee paid
    "commissionAsset": "USDT",                  // Commission fee asset
    "settleFee": "2.00000000",                  // Settlement fee (only present when status is "settled")
    "settleAsset": "USDT",                      // Settlement fee asset
  }
]
```

`GET /api/v3/prediction/positionHistories`

Query the current account's prediction market historical position list (closed or settled).

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO | Trading pair; if not provided, returns all historical positions |
| startTime | LONG | NO | Start time (millisecond timestamp) |
| endTime | LONG | NO | End time (millisecond timestamp) |
| limit | INT | NO | Number of results; default 100, maximum 1000 |
| signer | STRING | YES | API wallet address |
| nonce | STRING | YES | Current time in microseconds |
| signature | STRING | YES | Signature |

Notes:
* When both `startTime` and `endTime` are provided, `startTime` must not be greater than `endTime`
* `limit` range is 1 to 1000, default is 100
* `status` field: `close` means the user manually closed the position; `settled` means the prediction market expired and settled

## Query prediction market settlement histories (USER\_DATA)

**Response**

```javascript
[
  {
    "marketName": "BTC_UP_DOWN_20260501",       // Prediction market name
    "asset": "BTC_UP_DOWN_20260501YES",          // Token asset (YES or NO token)
    "symbol": "BTC_UP_DOWN_20260501YES",         // Trading pair
    "marketStartTime": 1746000000000,            // Market start time (millisecond timestamp)
    "marketEndTime": 1746086400000,              // Market end time / settlement time (millisecond timestamp)
    "shareAmount": "200.00000000",               // Share quantity held at settlement
    "settleAsset": "USDT",                       // Settlement asset
    "settleAmount": "200.00000000",              // Settlement amount received
    "settleFeeAmount": "2.00000000",             // Settlement fee charged
    "entryPrice": "0.72000000",                  // Average entry price
    "settlePrice": "1.00000000",                 // Final settlement price
    "realizedPnl": "56.00000000",                // Realized PnL after settlement
    "status": "settled"                          // Settlement status
  }
]
```

`GET /api/v3/prediction/settlementHistories`

Query the current account's prediction market settlement history (markets that have expired and settled).

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO | Trading pair; if not provided, returns all settlement records |
| startTime | LONG | NO | Filter by market start time (millisecond timestamp) |
| endTime | LONG | NO | Filter by market end time (millisecond timestamp) |
| limit | INT | NO | Number of results; default 100, maximum 1000 |
| signer | STRING | YES | API wallet address |
| nonce | STRING | YES | Current time in microseconds |
| signature | STRING | YES | Signature |

Notes:
* When both `startTime` and `endTime` are provided, `startTime` must not be greater than `endTime`
* `limit` range is 1 to 1000, default is 100
* `settleAmount` is the gross payout; `realizedPnl` equals `settleAmount` minus the cost basis
* `settleFeeAmount` is deducted from the settlement payout

## Get withdraw fee (NONE)
> **Response:**
```javascript
{
  "tokenPrice": 1.00019000,
   "gasCost": 0.5000,
  "gasUsdValue": 0.5
}
```

``
GET /api/v3/aster/withdraw/estimateFee 
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
chainId | STRING | YES | 
asset | STRING | YES | 

**Notes:**
* chainId: 1(ETH),56(BSC),42161(Arbi)
* gasCost: The minimum fee required for a withdrawal

## Withdraw (USER_DATA)
> **Response:**
```javascript
{
  "withdrawId": "1014729574755487744",
  "hash":"0xa6d1e617a3f69211df276fdd8097ac8f12b6ad9c7a49ba75bbb24f002df0ebb"
}
```

``
POST /api/v3/aster/user-withdraw``

**Weight:**
5

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
chainId | STRING | YES | 1(ETH),56(BSC),42161(Arbi)
asset | STRING | YES |
amount | STRING | YES |
fee | STRING | YES |
receiver | STRING | YES |  The address of the current account
nonce | STRING | YES |  The current time in microseconds 
userSignature | STRING | YES | 


**Note:** 
* chainId: 1(ETH),56(BSC),42161(Arbi)
* receiver: The address of the current account
* If the futures account balance is insufficient, funds will be transferred from the spot account to the perp account for withdrawal.
* userSignature demo

```shell
const domain = {
    name: 'Aster',
    version: '1',
    chainId: 56,
    verifyingContract: ethers.ZeroAddress,
  }

const currentTime = Date.now() * 1000
 
const types = {
    Action: [
        {name: "type", type: "string"},
        {name: "destination", type: "address"},
        {name: "destination Chain", type: "string"},
        {name: "token", type: "string"},
        {name: "amount", type: "string"},
        {name: "fee", type: "string"},
        {name: "nonce", type: "uint256"},
        {name: "aster chain", type: "string"},
    ],
  }
  const value = {
    'type': 'Withdraw',
    'destination': '0xD9cA6952F1b1349d27f91E4fa6FB8ef67b89F02d',
    'destination Chain': 'BSC',
    'token': 'USDT',
    'amount': '10.123400',
    'fee': '1.234567891',
    'nonce': currentTime,
    'aster chain': 'Mainnet',
  }


const signature = await signer.signTypedData(domain, types, value)
```

## Account information (USER\_DATA)

**Response**

```javascript
{     
   "feeTier": 0,
   "canTrade": true,
   "canDeposit": true,
   "canWithdraw": true,
   "canBurnAsset": true,
   "updateTime": 0,
   "balances": [
    {
      "asset": "BTC",
      "free": "4723846.89208129",
      "locked": "0.00000000"
    },
    {
      "asset": "LTC",
      "free": "4763368.68006011",
      "locked": "0.00000000"
    }
  ]
}
```

`GET /api/v3/account`

Retrieve current account information

**Weight:** 5

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |

## Account trade history (USER\_DATA)

**Response**

```javascript
[ 
  {
    "symbol": "BTC_UP_DOWN_5M_1778483280_YUSDT",
    "id": 1002,
    "orderId": 266358,
    "side": "BUY",
    "price": "1",
    "qty": "2",
    "quoteQty": "2",
    "commission": "0.00105000",
    "commissionAsset": "BNB",
    "time": 1755656788798,
    "counterpartyId": 19,
    "createUpdateId": null,
    "maker": false,
    "buyer": true
  }
] 
```

`GET /api/v3/userTrades`

Retrieve the trade history for a specified trading pair of an account

**Weight:** 5

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO |  |
| orderId | LONG | NO | Must be used together with the parameter symbol |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| fromId | LONG | NO | Starting trade ID. Defaults to fetching the most recent trade. |
| limit | INT | NO | Default 500; maximum 1000 |

* If both `startTime` and `endTime` are not sent, only data from the last 7 days will be returned.  
* The maximum interval between startTime and endTime is 7 days.  
* `fromId` cannot be sent together with `startTime` or `endTime`.      

---

