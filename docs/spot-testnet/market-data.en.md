## Noop(TRADE)

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /api/v3/noop``

By using this request, it is possible to efficiently cancel previously sent transactions that are still in the queue and have not completed the on chain operation (Nonce should be equal to this request)

**Weight:**
1

**Parameters:**

## Test server connectivity

**Response**

```javascript
{}
```

`GET /api/v3/ping`

Test whether the REST API can be reached.

**Weight:** 1

**Parameters:** NONE

## Get server time

**Response**

```javascript
{
  "serverTime": 1499827319559
}
```

`GET /api/v3/time`

Test if the REST API can be reached and retrieve the server time.

**Weight:** 1

**Parameters:** NONE

## Trading specification information

**Response**

```javascript
{
	"timezone": "UTC",
	"serverTime": 1756197279679,
	"rateLimits": [{
			"rateLimitType": "REQUEST_WEIGHT",
			"interval": "MINUTE",
			"intervalNum": 1,
			"limit": 6000
		},
		{
			"rateLimitType": "ORDERS",
			"interval": "MINUTE",
			"intervalNum": 1,
			"limit": 6000
		},
		{
			"rateLimitType": "ORDERS",
			"interval": "SECOND",
			"intervalNum": 10,
			"limit": 300
		}
	],
	"exchangeFilters": [],
	"assets": [{
			"asset": "USD"
		}, {
			"asset": "USDT"
		},
		{
			"asset": "BNB"
		}
	],
	"symbols": [{
		"status": "TRADING",
		"baseAsset": "BNB",
		"quoteAsset": "USDT",
		"pricePrecision": 8,
		"quantityPrecision": 8,
		"baseAssetPrecision": 8,
		"quotePrecision": 8,
		"filters": [{
				"minPrice": "0.01000000",
				"maxPrice": "100000",
				"filterType": "PRICE_FILTER",
				"tickSize": "0.01000000"
			},
			{
				"stepSize": "0.00100000",
				"filterType": "LOT_SIZE",
				"maxQty": "1000",
				"minQty": "1"
			},
			{
				"stepSize": "0.00100000",
				"filterType": "MARKET_LOT_SIZE",
				"maxQty": "900000",
				"minQty": "0.00100000"
			},
			{
				"limit": 200,
				"filterType": "MAX_NUM_ORDERS"
			},
			{
				"minNotional": "5",
				"filterType": "MIN_NOTIONAL"
			},
			{
				"maxNotional": "100",
				"filterType": "MAX_NOTIONAL"
			},
			{
				"maxNotional": "100",
				"minNotional": "5",
				"avgPriceMins": 5,
				"applyMinToMarket": true,
				"filterType": "NOTIONAL",
				"applyMaxToMarket": true
			},
			{
				"multiplierDown": "0",
				"multiplierUp": "5",
				"multiplierDecimal": "0",
				"filterType": "PERCENT_PRICE"
			},
			{
				"bidMultiplierUp": "5",
				"askMultiplierUp": "5",
				"bidMultiplierDown": "0",
				"avgPriceMins": 5,
				"multiplierDecimal": "0",
				"filterType": "PERCENT_PRICE_BY_SIDE",
				"askMultiplierDown": "0"
			}
		],
		"orderTypes": [
			"LIMIT",
			"MARKET",
			"STOP",
			"STOP_MARKET",
			"TAKE_PROFIT",
			"TAKE_PROFIT_MARKET"
		],
		"timeInForce": [
			"GTC",
			"IOC",
			"FOK",
			"GTX",
      "HIDDEN"
		],
		"symbol": "BNBUSDT",
		"ocoAllowed": false
	}]
}
```

`GET /api/v3/exchangeInfo`

Retrieve trading rules and trading pair information.

**Weight:** 1

**Parameters:** None

## Depth information

**Response**

```javascript
{
  "lastUpdateId": 1027024,
  "E":1589436922972, //  Message output time
  "T":1589436922959, //  Transaction time
  "bids": [
    [
      "4.00000000", // PRICE
      "431.00000000" // QTY
    ]
  ],
  "asks": [
    [
      "4.00000200",
      "12.00000000"
    ]
  ]
}
```

`GET /api/v3/depth`

**Weight:**

Based on limit adjustments:

| Limitations | Weight |
| :---- | :---- |
| 5, 10, 20, 50 | 2 |
| 100 | 5 |
| 500 | 10 |
| 1000 | 20 |

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| limit | INT | NO | Default 100\. Optional values: \[5, 10, 20, 50, 100, 500, 1000\] |

## Recent trades list

**Response**

```javascript
[
 {
    "id": 657,
    "price": "1.01000000",
    "qty": "5.00000000",
    "baseQty": "4.95049505",
    "time": 1755156533943,
    "isBuyerMaker": false
  }
]
```

`GET /api/v3/trades`

Get recent trades

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| limit | INT | NO | Default 500; maximum 1000 |

## Query historical trades (MARKET\_DATA)

**Response**

```javascript
[
 {
    "id": 1140,
    "price": "1.10000000",
    "qty": "7.27200000",
    "baseQty": "6.61090909",
    "time": 1756094288700,
    "isBuyerMaker": false
 }
]
```

`GET /api/v3/historicalTrades`

Retrieve historical trades

**Weight:** 20

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| limit | INT | NO | Default 500; maximum 1000\. |
| fromId | LONG | NO | Return starting from which trade id. Defaults to returning the most recent trade records. |

## Recent trades (aggregated)

**Response**

```javascript
[
  {
    "a": 26129, // Aggregate tradeId
    "p": "0.01633102", // Price
    "q": "4.70443515", // Quantity
    "f": 27781, // First tradeId
    "l": 27781, // Last tradeId
    "T": 1498793709153, // Timestamp
    "m": true, // Was the buyer the maker?
  }
]
```

`GET /api/v3/aggTrades`

The difference between aggregated trades and individual trades is that trades with the same price, same side, and same time are combined into a single entry.

**Weight:** 20

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| fromId | LONG | NO | Return results starting from the trade ID that includes fromId |
| startTime | LONG | NO | Return results starting from trades after that time |
| endTime | LONG | NO | Return the trade records up to that moment |
| limit | INT | NO | Default 500; maximum 1000\. |

* If you send startTime and endTime, the interval must be less than one hour.  
* If no filter parameters (fromId, startTime, endTime) are sent, the most recent trade records are returned by default

## K-line data

**Response**

```javascript
[
  [
    1499040000000, // Open time
    "0.01634790", // Open
    "0.80000000", // High
    "0.01575800", // Low
    "0.01577100", // Close
    "148976.11427815", // Volume
    1499644799999, // Close time
    "2434.19055334", // Quote asset volume
    308, // Number of trades
    "1756.87402397", // Taker buy base asset volume
    "28.46694368", // Taker buy quote asset volume
  ]
]
```

`GET /api/v3/klines`

Each K-line represents a trading pair. The open time of each K-line can be regarded as a unique ID.

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| interval | ENUM | YES | See the enumeration definition: K-line interval |
| startTime | LONG | NO |  |
| endTime | LONG | NO |  |
| limit | INT | NO | Default 500; maximum 1500\. |

* If startTime and endTime are not sent, the most recent trades are returned by default

## 24h price change

**Response**

```javascript
{
  "symbol": "BTCUSDT",              //symbol
  "priceChange": "-94.99999800",    //price change
  "priceChangePercent": "-95.960",  //price change percent
  "weightedAvgPrice": "0.29628482", //weighted avgPrice
  "prevClosePrice": "3.89000000",   //prev close price
  "lastPrice": "4.00000200",        //last price
  "lastQty": "200.00000000",        //last qty
  "bidPrice": "866.66000000",       //first bid price
  "bidQty": "72.05100000",          //first bid qty
  "askPrice": "866.73000000",       //first ask price
  "askQty": "1.21700000",           //first ask qty
  "openPrice": "99.00000000",       //open price
  "highPrice": "100.00000000",      //high price
  "lowPrice": "0.10000000",         //low price
  "volume": "8913.30000000",        //volume
  "quoteVolume": "15.30000000",     //quote volume
  "openTime": 1499783499040,        //open time
  "closeTime": 1499869899040,       //close time
  "firstId": 28385,   // first id
  "lastId": 28460,    // last id
  "count": 76,         // count
  "baseAsset": "BTC",   //base asset
  "quoteAsset": "USDT"  //quote asset
}
```

`GET /api/v3/ticker/24hr`

24-hour rolling window price change data. Please note that omitting the symbol parameter will return data for all trading pairs; in that case the returned data is an example array for the respective pairs, which is not only large in volume but also has a very high weight.

**Weight:** 1 \= single trading pair; **40** \= When the trading pair parameter is missing (returns all trading pairs)

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO |  |

* Please note that omitting the symbol parameter will return data for all trading pairs

## Latest price

**Response**

```javascript
{
   "symbol": "ADAUSDT",
   "price": "1.30000000",
   "time": 1649666690902
}  
```

OR

```javascript
[     
  {
     "symbol": "ADAUSDT",
     "price": "1.30000000",
     "time": 1649666690902
  }
]
```

`GET /api/v3/ticker/price`

Get the latest price for a trading pair

**Weight:** 1 \= Single trading pair; **2** \= No symbol parameter (returns all pairs)

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO |  |

* If no trading pair parameter is sent, information for all trading pairs will be returned

## Current best order

**Response**

```javascript
{
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
  "time": 1589437530011   // Timestamp
}
```

OR

```javascript
[
  {
    "symbol": "LTCBTC",
    "bidPrice": "4.00000000",
    "bidQty": "431.00000000",
    "askPrice": "4.00000200",
    "askQty": "9.00000000",
    "time": 1589437530011   // Timestamp
  }
]
```

`GET /api/v3/ticker/bookTicker`

Return the current best orders (highest bid, lowest ask)

**Weight:** 1 \= Single trading pair; **2** \= No symbol parameter (returns all pairs)

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | NO |  |

* If no trading pair parameter is sent, information for all trading pairs will be returned

## Get symbol fees

**Response**

```javascript
{
   "symbol": "APXUSDT",
   "makerCommissionRate": "0.000200",    
   "takerCommissionRate": "0.000700"
}
```

`GET /api/v3/commissionRate`

Get symbol fees

**Weight:** 20

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| symbol | STRING | YES |  |
| recvWindow | LONG | NO | The assigned value cannot be greater than 60000 |
| timestamp | LONG | YES |  |

