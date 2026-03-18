## Place order (TRADE)

**Response ACK:**

```javascript
{
  "symbol": "BTCUSDT", 
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

`POST /api/v3/order `

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
  * For example, when placing a `MARKET` `SELL` market order on the `BTCUSDT` pair, use `QUANTITY` to let the user specify how much BTC they want to sell.  
* For a `MARKET` `BUY` market order, the user controls how much of the quote asset they want to spend with `quoteOrderQty`; `QUANTITY` will be calculated by the system based on market liquidity. For example, when placing a `MARKET` `BUY` market order on the `BTCUSDT` pair, use `quoteOrderQty` to let the user choose how much USDT to use to buy BTC.  
* A `MARKET` order using `quoteOrderQty` will not violate the `LOT_SIZE` limit rules; the order will be executed as closely as possible to the given `quoteOrderQty`.  
* Unless a previous order has already been filled, orders set with the same `newClientOrderId` will be rejected.

## Cancel order (TRADE)

**Response**

```javascript
{
  "symbol": "BTCUSDT", 
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

`DELETE /api/v3/order `

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
    "symbol": "ADA25SLP25",
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

`GET /api/v3/order `

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

## Current open orders (USER\_DATA)

**Response**

```javascript
[
    {
        "orderId": 349661, 
        "symbol": "BNBUSDT", 
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

`GET /api/v3/openOrders `

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
DEL /api/v3/allOpenOrders  
``

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
        "symbol": "BNBUSDT", 
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

`GET /api/v3/allOrders `

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


## Perp-spot transfer (TRADE)

**Response:**

```javascript
{
    "tranId": 21841, //Tran Id
    "status": "SUCCESS" //Status
}
```

`POST /api/v3/asset/wallet/transfer  `

**Weight:** 5

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| amount | DECIMAL | YES | Quantity |
| asset | STRING | YES | Asset |
| clientTranId | STRING | YES | Transaction ID |
| kindType | STRING | YES | Transaction type |

* kindType FUTURE_SPOT(future to spot)/SPOT_FUTURE(spot to future)

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
POST /api/v3/aster/user-withdraw 
``

**Weight:**
1

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

## Get User Create Apikey nonce (NONE)

> **Response:**
```javascript

111111

```

``
POST /api/v3/getNonce 
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
address | STRING | YES |
userOperationType | STRING | YES | CREATE_API_KEY
network | STRING | NO | 

**Notes:**
* userOperationType: CREATE_API_KEY
* network: For the Solana network, SOL must be provided; otherwise, this field can be ignored.

## Create Apikey (NONE)

> **Response:**
```javascript
{
    "apiKey": "bb3b24d0a3dec88cb06be58a257e4575cb0b1bb256ad6fd90ae8fd0ee1d102ae",
    "apiSecret": "9fe8f5642ae1961674ea0cb7f957fa99dc8e0421b607c985a963ad2ced90ae1c"
}
```

``
POST /api/v3/createApiKey
``

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
address | STRING | YES |
userOperationType | STRING | YES | CREATE_API_KEY
network | STRING | NO | 
userSignature | STRING | YES | 
apikeyIP | STRING | NO | 
desc | STRING | YES | 

**Note:**
* userOperationType: CREATE_API_KEY
* network: For the Solana network, SOL must be provided; otherwise, this field can be ignored.
* desc: The same account cannot be duplicated, and the length must not exceed 20 characters.
* apikeyIP: An array of IP addresses, separated by commas.
* Rate limit: 60 requests per minute per IP.
* userSignature: EVM demo

```shell
const nonce = 111111
const message = 'You are signing into Astherus ${nonce}';
const signature = await signer.signMessage(message);
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

`GET /api/v3/account `

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
    "symbol": "BNBUSDT",
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

`GET /api/v3/userTrades `

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

