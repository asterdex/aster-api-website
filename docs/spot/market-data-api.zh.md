## **测试服务器连通性**
> **响应**
```javascript
{}
```
``
GET /api/v1/ping
``

测试能否联通 Rest API。

**权重:**
1

**参数:**
NONE


## **获取服务器时间**
> **响应**
```javascript
{
  "serverTime": 1499827319559
}
```
``
GET /api/v1/time
``

测试能否联通 Rest API 并获取服务器时间。

**权重:**
1

**参数:**
NONE


## **交易规范信息**

> **响应** 

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

``
GET /api/v1/exchangeInfo
``

获取交易规则和交易对信息。

**权重:**
1

**参数:**
无


## **深度信息**

> **响应**

```javascript
{
  "lastUpdateId": 1027024,
  "E":1589436922972, // 消息时间
  "T":1589436922959, // 撮合引擎时间
  "bids": [
    [
      "4.00000000", // 价位
      "431.00000000" // 挂单量
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
``
GET /api/v1/depth
``

**权重:**

基于限制调整:

限制 | 权重
------------ | ------------
5, 10, 20, 50 | 2
100 | 5
500 | 10
1000 | 20

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 100. 可选值:[5, 10, 20, 50, 100, 500, 1000]


## **近期成交列表**

> **响应**

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
``
GET /api/v1/trades
``

获取近期成交

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 500；最大1000


## **查询历史成交 (MARKET_DATA)**

> **响应**

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
``
GET /api/v1/historicalTrades
``

获取历史成交。

**权重:**
20

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | 默认 500; 最大值 1000.
fromId | LONG | NO | 从哪一条成交id开始返回. 缺省返回最近的成交记录。


## **近期成交(归集)**

> **响应**

```javascript
[
  {
    "a": 26129, // 归集成交ID
    "p": "0.01633102", // 成交价
    "q": "4.70443515", // 成交量
    "f": 27781, // 被归集的首个成交ID
    "l": 27781, // 被归集的末个成交ID
    "T": 1498793709153, // 成交时间
    "m": true, // 是否为主动卖出单
  }
]
```
``
GET /api/v1/aggTrades
``

归集交易与逐笔交易的区别在于，同一价格、同一方向、同一时间的trade会被聚合为一条

**权重:**
20

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
fromId | LONG | NO | 从包含fromId的成交id开始返回结果
startTime | LONG | NO | 从该时刻之后的成交记录开始返回结果
endTime | LONG | NO | 返回该时刻为止的成交记录
limit | INT | NO | 默认 500; 最大 1000.
* 如果发送startTime和endTime，间隔必须小于一小时。
* 如果没有发送任何筛选参数(fromId, startTime,endTime)，默认返回最近的成交记录


## **K线数据**

> **响应**
```javascript
[
  [
    1499040000000, // 开盘时间
    "0.01634790", // 开盘价
    "0.80000000", // 最高价
    "0.01575800", // 最低价
    "0.01577100", // 收盘价(当前K线未结束的即为最新价)
    "148976.11427815", // 成交量
    1499644799999, // 收盘时间
    "2434.19055334", // 成交额
    308, // 成交笔数
    "1756.87402397", // 主动买入成交量
    "28.46694368", // 主动买入成交额
  ]
]
```
``
GET /api/v1/klines
``

每根K线代表一个交易对。 
每根K线的开盘时间可视为唯一ID


**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
interval | ENUM | YES | 详见枚举定义：K线间隔
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | 默认 500; 最大 1500.
* 如果未发送 startTime 和 endTime ，默认返回最近的交易。



## **24hr 价格变动情况**

> **响应**

```javascript 
{
  "symbol": "BTCUSDT",
  "priceChange": "-94.99999800",    //24小时价格变动
  "priceChangePercent": "-95.960",  //24小时价格变动百分比
  "weightedAvgPrice": "0.29628482", //加权平均价
  "prevClosePrice": "3.89000000",   //上一次结束价格
  "lastPrice": "4.00000200",        //最近一次成交价
  "lastQty": "200.00000000",        //最近一次成交额
  "bidPrice": "866.66000000",       //最高的买入价格
  "bidQty": "72.05100000",          //最高的买入价格的数量
  "askPrice": "866.73000000",       //最低的卖出价
  "askQty": "1.21700000",           //最低的卖出价格的数量
  "openPrice": "99.00000000",       //24小时内第一次成交的价格
  "highPrice": "100.00000000",      //24小时最高价
  "lowPrice": "0.10000000",         //24小时最低价
  "volume": "8913.30000000",        //24小时成交量
  "quoteVolume": "15.30000000",     //24小时成交额
  "openTime": 1499783499040,        //24小时内，第一笔交易的发生时间
  "closeTime": 1499869899040,       //24小时内，最后一笔交易的发生时间
  "firstId": 28385,   // 首笔成交id
  "lastId": 28460,    // 末笔成交id
  "count": 76,         // 成交笔数
  "baseAsset": "BTC",   //基础代币
  "quoteAsset": "USDT"  //报价代币
}
```

``
GET /api/v1/ticker/24hr
``

24 小时滚动窗口价格变动数据。 请注意，不携带symbol参数会返回全部交易对数据，此时返回的数据为示例相应的数组，不仅数据庞大，而且权重极高

**权重:**
1 单一交易对; 
**40** 交易对参数缺失;

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
* 请注意，不携带symbol参数会返回全部交易对数据

## **最新价格**

> **响应**

```javascript    
{
   "symbol": "ADAUSDT",
   "price": "1.30000000",
   "time": 1649666690902
}  
```

> OR

```javascript
[     
  {
     "symbol": "ADAUSDT",
     "price": "1.30000000",
     "time": 1649666690902
  }
]
```

``
GET /api/v1/ticker/price
``

获取交易对最新价格

**权重:**
1 单一交易对; 
**2** 交易对参数缺失;

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
* 不发送交易对参数，则会返回所有交易对信息


## **当前最优挂单**
> **响应**
```javascript
{
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
  "time": 1589437530011   // 交易时间
}
```
> OR
```javascript
[
  {
    "symbol": "LTCBTC",
    "bidPrice": "4.00000000",
    "bidQty": "431.00000000",
    "askPrice": "4.00000200",
    "askQty": "9.00000000",
    "time": 1589437530011   // 交易时间
  }
]
```

``
GET /api/v1/ticker/bookTicker
``

返回当前最优的挂单(最高买单，最低卖单)

**权重:**
1 单一交易对; 
**2** 交易对参数缺失;

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
* 不发送交易对参数，则会返回所有交易对信息

## **获取Symbol手续费**

> **响应**

```javascript
{
   "symbol": "APXUSDT",
   "makerCommissionRate": "0.000200",    
   "takerCommissionRate": "0.000700"
}
```
``
GET /api/v1/commissionRate
``

获取Symbol手续费。

**权重:**
20

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
recvWindow | LONG | NO |赋值不能大于 ```60000```
timestamp | LONG | YES |




