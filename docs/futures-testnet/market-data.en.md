## Noop

> **Response:**

```javascript
{
	"code": 200,
	"msg": "success"
}
```

``POST /fapi/v3/noop``

By using this request, it is possible to efficiently cancel previously sent transactions that are still in the queue and have not completed the on chain operation (Nonce should be equal to this request)

**Weight:**
1

**Parameters:**

## Test Connectivity

> **Response:**

```javascript
{}
```

``GET /fapi/v3/ping``

Test connectivity to the Rest API.

**Weight:**
1

**Parameters:**
NONE

## Check Server Time

> **Response:**

```javascript
{
  "serverTime": 1499827319559
}
```

``GET /fapi/v3/time``

Test connectivity to the Rest API and get the current server time.

**Weight:**
1

**Parameters:**
NONE

## Exchange Information

> **Response:**

```javascript
{
	"exchangeFilters": [],
 	"rateLimits": [
 		{
 			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 2400,
   			"rateLimitType": "REQUEST_WEIGHT" 
   		},
  		{
  			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 1200,
   			"rateLimitType": "ORDERS"
   		}
   	],
 	"serverTime": 1565613908500,    // Ignore please. If you want to check current server time, please check via "GET /fapi/v3/time"
 	"assets": [ // assets information
 		{
 			"asset": "BUSD",
   			"marginAvailable": true, // whether the asset can be used as margin in Multi-Assets mode
   			"autoAssetExchange": 0 // auto-exchange threshold in Multi-Assets margin mode
   		},
 		{
 			"asset": "USDT",
   			"marginAvailable": true,
   			"autoAssetExchange": 0
   		},
 		{
 			"asset": "BTC",
   			"marginAvailable": false,
   			"autoAssetExchange": null
   		}
   	],
 	"symbols": [
 		{
 			"symbol": "DOGEUSDT",
 			"pair": "DOGEUSDT",
 			"contractType": "PERPETUAL",
 			"deliveryDate": 4133404800000,
 			"onboardDate": 1598252400000,
 			"status": "TRADING",
 			"maintMarginPercent": "2.5000",   // ignore
 			"requiredMarginPercent": "5.0000",  // ignore
 			"baseAsset": "BLZ", 
 			"quoteAsset": "USDT",
 			"marginAsset": "USDT",
 			"pricePrecision": 5,	// please do not use it as tickSize
 			"quantityPrecision": 0, // please do not use it as stepSize
 			"baseAssetPrecision": 8,
 			"quotePrecision": 8, 
 			"underlyingType": "COIN",
 			"underlyingSubType": ["STORAGE"],
 			"settlePlan": 0,
 			"triggerProtect": "0.15", // threshold for algo order with "priceProtect"
 			"filters": [
 				{
 					"filterType": "PRICE_FILTER",
     				"maxPrice": "300",
     				"minPrice": "0.0001", 
     				"tickSize": "0.0001"
     			},
    			{
    				"filterType": "LOT_SIZE", 
     				"maxQty": "10000000",
     				"minQty": "1",
     				"stepSize": "1"
     			},
    			{
    				"filterType": "MARKET_LOT_SIZE",
     				"maxQty": "590119",
     				"minQty": "1",
     				"stepSize": "1"
     			},
     			{
    				"filterType": "MAX_NUM_ORDERS",
    				"limit": 200
  				},
  				{
    				"filterType": "MAX_NUM_ALGO_ORDERS",
    				"limit": 100
  				},
  				{
  					"filterType": "MIN_NOTIONAL",
  					"notional": "1", 
  				},
  				{
    				"filterType": "PERCENT_PRICE",
    				"multiplierUp": "1.1500",
    				"multiplierDown": "0.8500",
    				"multiplierDecimal": 4
    			}
   			],
 			"OrderType": [
   				"LIMIT",
   				"MARKET",
   				"STOP",
   				"STOP_MARKET",
   				"TAKE_PROFIT",
   				"TAKE_PROFIT_MARKET",
   				"TRAILING_STOP_MARKET" 
   			],
   			"timeInForce": [
   				"GTC", 
   				"IOC", 
   				"FOK", 
   				"GTX",
          "HIDDEN" 
 			],
 			"liquidationFee": "0.010000",	// liquidation fee rate
   			"marketTakeBound": "0.30",	// the max price difference rate( from mark price) a market order can make
 		}
   	],
	"timezone": "UTC" 
}
```

``GET /fapi/v3/exchangeInfo``

Current exchange trading rules and symbol information

**Weight:**
1

**Parameters:**
NONE

## Order Book

> **Response:**

```javascript
{
  "lastUpdateId": 1027024,
  "E": 1589436922972,   // Message output time
  "T": 1589436922959,   // Transaction time
  "bids": [
    [
      "4.00000000",     // PRICE
      "431.00000000"    // QTY
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

``GET /fapi/v3/depth``

**Weight:**

Adjusted based on the limit:

| Limit         | Weight |
| ------------- | ------ |
| 5, 10, 20, 50 | 2      |
| 100           | 5      |
| 500           | 10     |
| 1000          | 20     |

**Parameters:**

| Name   | Type   | Mandatory | Description                                               |
| ------ | ------ | --------- | --------------------------------------------------------- |
| symbol | STRING | YES       |                                                           |
| limit  | INT    | NO        | Default 500; Valid limits:[5, 10, 20, 50, 100, 500, 1000] |

## Recent Trades List

> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "48.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

``GET /fapi/v3/trades``

Get recent market trades

**Weight:**
1

**Parameters:**

| Name   | Type   | Mandatory | Description            |
| ------ | ------ | --------- | ---------------------- |
| symbol | STRING | YES       |                        |
| limit  | INT    | NO        | Default 500; max 1000. |

* Market trades means trades filled in the order book. Only market trades will be returned, which means the insurance fund trades and ADL trades won't be returned.

## Old Trades Lookup (MARKET_DATA)

> **Response:**

```javascript
[
  {
    "id": 28457,
    "price": "4.00000100",
    "qty": "12.00000000",
    "quoteQty": "8000.00",
    "time": 1499865549590,
    "isBuyerMaker": true,
  }
]
```

``GET /fapi/v3/historicalTrades``

Get older market historical trades.

**Weight:**
20

**Parameters:**

| Name   | Type   | Mandatory | Description                                             |
| ------ | ------ | --------- | ------------------------------------------------------- |
| symbol | STRING | YES       |                                                         |
| limit  | INT    | NO        | Default 500; max 1000.                                  |
| fromId | LONG   | NO        | TradeId to fetch from. Default gets most recent trades. |

* Market trades means trades filled in the order book. Only market trades will be returned, which means the insurance fund trades and ADL trades won't be returned.

## Compressed/Aggregate Trades List

> **Response:**

```javascript
[
  {
    "a": 26129,         // Aggregate tradeId
    "p": "0.01633102",  // Price
    "q": "4.70443515",  // Quantity
    "f": 27781,         // First tradeId
    "l": 27781,         // Last tradeId
    "T": 1498793709153, // Timestamp
    "m": true,          // Was the buyer the maker?
  }
]
```

``GET /fapi/v3/aggTrades``

Get compressed, aggregate market trades. Market trades that fill at the time, from the same order, with the same price will have the quantity aggregated.

**Weight:**
20

**Parameters:**

| Name      | Type   | Mandatory | Description                                              |
| --------- | ------ | --------- | -------------------------------------------------------- |
| symbol    | STRING | YES       |                                                          |
| fromId    | LONG   | NO        | ID to get aggregate trades from INCLUSIVE.               |
| startTime | LONG   | NO        | Timestamp in ms to get aggregate trades from INCLUSIVE.  |
| endTime   | LONG   | NO        | Timestamp in ms to get aggregate trades until INCLUSIVE. |
| limit     | INT    | NO        | Default 500; max 1000.                                   |

* If both startTime and endTime are sent, time between startTime and endTime must be less than 1 hour.
* If fromId, startTime, and endTime are not sent, the most recent aggregate trades will be returned.
* Only market trades will be aggregated and returned, which means the insurance fund trades and ADL trades won't be aggregated.

## Kline/Candlestick Data

> **Response:**

```javascript
[
  [
    1499040000000,      // Open time
    "0.01634790",       // Open
    "0.80000000",       // High
    "0.01575800",       // Low
    "0.01577100",       // Close
    "148976.11427815",  // Volume
    1499644799999,      // Close time
    "2434.19055334",    // Quote asset volume
    308,                // Number of trades
    "1756.87402397",    // Taker buy base asset volume
    "28.46694368",      // Taker buy quote asset volume
    "17928899.62484339" // Ignore.
  ]
]
```

``GET /fapi/v3/klines``

Kline/candlestick bars for a symbol.
Klines are uniquely identified by their open time.

**Weight:** based on parameter `LIMIT`

| LIMIT       | weight |
| ----------- | ------ |
| [1,100)     | 1      |
| [100, 500)  | 2      |
| [500, 1000] | 5      |

> 1000 | 10

**Parameters:**

| Name      | Type   | Mandatory | Description            |
| --------- | ------ | --------- | ---------------------- |
| symbol    | STRING | YES       |                        |
| interval  | ENUM   | YES       |                        |
| startTime | LONG   | NO        |                        |
| endTime   | LONG   | NO        |                        |
| limit     | INT    | NO        | Default 500; max 1500. |

* If startTime and endTime are not sent, the most recent klines are returned.

## Index Price Kline/Candlestick Data

> **Response:**

```javascript
[
  [
    1591256400000,      	// Open time
    "9653.69440000",    	// Open
    "9653.69640000",     	// High
    "9651.38600000",     	// Low
    "9651.55200000",     	// Close (or latest price)
    "0	", 					// Ignore
    1591256459999,      	// Close time
    "0",    				// Ignore
    60,                		// Number of Data Points
    "0",    				// Ignore
    "0",      				// Ignore
    "0" 					// Ignore
  ]
]
```

``GET /fapi/v3/indexPriceKlines``

Kline/candlestick bars for the index price of a pair.

Klines are uniquely identified by their open time.

**Weight:** based on parameter `LIMIT`

| LIMIT       | weight |
| ----------- | ------ |
| [1,100)     | 1      |
| [100, 500)  | 2      |
| [500, 1000] | 5      |

> 1000 | 10

**Parameters:**

| Name      | Type   | Mandatory | Description            |
| --------- | ------ | --------- | ---------------------- |
| pair      | STRING | YES       |                        |
| interval  | ENUM   | YES       |                        |
| startTime | LONG   | NO        |                        |
| endTime   | LONG   | NO        |                        |
| limit     | INT    | NO        | Default 500; max 1500. |

* If startTime and endTime are not sent, the most recent klines are returned.

## Mark Price Kline/Candlestick Data

> **Response:**

```javascript
[
  [
    1591256460000,     		// Open time
    "9653.29201333",    	// Open
    "9654.56401333",     	// High
    "9653.07367333",     	// Low
    "9653.07367333",     	// Close (or latest price)
    "0	", 					// Ignore
    1591256519999,      	// Close time
    "0",    				// Ignore
    60,                	 	// Number of Data Points
    "0",    				// Ignore
    "0",      			 	// Ignore
    "0" 					// Ignore
  ]
]
```

``GET /fapi/v3/markPriceKlines``

Kline/candlestick bars for the mark price of a symbol.

Klines are uniquely identified by their open time.

**Weight:** based on parameter `LIMIT`

| LIMIT       | weight |
| ----------- | ------ |
| [1,100)     | 1      |
| [100, 500)  | 2      |
| [500, 1000] | 5      |

> 1000 | 10

**Parameters:**

| Name      | Type   | Mandatory | Description            |
| --------- | ------ | --------- | ---------------------- |
| symbol    | STRING | YES       |                        |
| interval  | ENUM   | YES       |                        |
| startTime | LONG   | NO        |                        |
| endTime   | LONG   | NO        |                        |
| limit     | INT    | NO        | Default 500; max 1500. |

* If startTime and endTime are not sent, the most recent klines are returned.

## Mark Price

> **Response:**

```javascript
{
	"symbol": "BTCUSDT",
	"markPrice": "11793.63104562",	// mark price
	"indexPrice": "11781.80495970",	// index price
	"estimatedSettlePrice": "11781.16138815", // Estimated Settle Price, only useful in the last hour before the settlement starts.
	"lastFundingRate": "0.00038246",  // This is the lasted funding rate
	"nextFundingTime": 1597392000000,
	"interestRate": "0.00010000",
	"time": 1597370495002
}
```

> **OR (when symbol not sent)**

```javascript
[
	{
	    "symbol": "BTCUSDT",
	    "markPrice": "11793.63104562",	// mark price
	    "indexPrice": "11781.80495970",	// index price
	    "estimatedSettlePrice": "11781.16138815", // Estimated Settle Price, only useful in the last hour before the settlement starts.
	    "lastFundingRate": "0.00038246",  // This is the lasted funding rate
	    "nextFundingTime": 1597392000000,
	    "interestRate": "0.00010000",
	    "time": 1597370495002
	}
]
```

``GET /fapi/v3/premiumIndex``

Mark Price and Funding Rate

**Weight:**
1

**Parameters:**

| Name   | Type   | Mandatory | Description |
| ------ | ------ | --------- | ----------- |
| symbol | STRING | NO        |             |

## Get Funding Rate History

> **Response:**

```javascript
[
	{
    	"symbol": "BTCUSDT",
    	"fundingRate": "-0.03750000",
    	"fundingTime": 1570608000000,
	},
	{
   		"symbol": "BTCUSDT",
    	"fundingRate": "0.00010000",
    	"fundingTime": 1570636800000,
	}
]
```

``GET /fapi/v3/fundingRate``

**Weight:**
1

**Parameters:**

| Name      | Type   | Mandatory | Description                                           |
| --------- | ------ | --------- | ----------------------------------------------------- |
| symbol    | STRING | NO        |                                                       |
| startTime | LONG   | NO        | Timestamp in ms to get funding rate from INCLUSIVE.   |
| endTime   | LONG   | NO        | Timestamp in ms to get funding rate  until INCLUSIVE. |
| limit     | INT    | NO        | Default 100; max 1000                                 |

* If `startTime` and `endTime` are not sent, the most recent `limit` datas are returned.
* If the number of data between `startTime` and `endTime` is larger than `limit`, return as `startTime` + `limit`.
* In ascending order.

## 24hr Ticker Price Change Statistics

> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "priceChange": "-94.99999800",
  "priceChangePercent": "-95.960",
  "weightedAvgPrice": "0.29628482",
  "prevClosePrice": "0.10002000",
  "lastPrice": "4.00000200",
  "lastQty": "200.00000000",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000",
  "quoteVolume": "15.30000000",
  "openTime": 1499783499040,
  "closeTime": 1499869899040,
  "firstId": 28385,   // First tradeId
  "lastId": 28460,    // Last tradeId
  "count": 76         // Trade count
}
```

> OR

```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"priceChange": "-94.99999800",
  		"priceChangePercent": "-95.960",
  		"weightedAvgPrice": "0.29628482",
  		"prevClosePrice": "0.10002000",
  		"lastPrice": "4.00000200",
  		"lastQty": "200.00000000",
  		"openPrice": "99.00000000",
  		"highPrice": "100.00000000",
  		"lowPrice": "0.10000000",
  		"volume": "8913.30000000",
  		"quoteVolume": "15.30000000",
  		"openTime": 1499783499040,
  		"closeTime": 1499869899040,
  		"firstId": 28385,   // First tradeId
  		"lastId": 28460,    // Last tradeId
  		"count": 76         // Trade count
	}
]
```

``GET /fapi/v3/ticker/24hr``

24 hour rolling window price change statistics.
**Careful** when accessing this with no symbol.

**Weight:**
1 for a single symbol;
**40** when the symbol parameter is omitted

**Parameters:**

| Name   | Type   | Mandatory | Description |
| ------ | ------ | --------- | ----------- |
| symbol | STRING | NO        |             |

* If the symbol is not sent, tickers for all symbols will be returned in an array.

## Symbol Price Ticker

> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "price": "6000.01",
  "time": 1589437530011   // Transaction time
}
```

> OR

```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"price": "6000.01",
  		"time": 1589437530011
	}
]
```

``GET /fapi/v3/ticker/price``

Latest price for a symbol or symbols.

**Weight:**
1 for a single symbol;
**2** when the symbol parameter is omitted

**Parameters:**

| Name   | Type   | Mandatory | Description |
| ------ | ------ | --------- | ----------- |
| symbol | STRING | NO        |             |

* If the symbol is not sent, prices for all symbols will be returned in an array.

## Symbol Order Book Ticker

> **Response:**

```javascript
{
  "symbol": "BTCUSDT",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000",
  "time": 1589437530011   // Transaction time
}
```

> OR

```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"bidPrice": "4.00000000",
  		"bidQty": "431.00000000",
  		"askPrice": "4.00000200",
  		"askQty": "9.00000000",
  		"time": 1589437530011
	}
]
```

``GET /fapi/v3/ticker/bookTicker``

Best price/qty on the order book for a symbol or symbols.

**Weight:**
1 for a single symbol;
**2** when the symbol parameter is omitted

**Parameters:**

| Name   | Type   | Mandatory | Description |
| ------ | ------ | --------- | ----------- |
| symbol | STRING | NO        |             |

* If the symbol is not sent, bookTickers for all symbols will be returned in an array.

