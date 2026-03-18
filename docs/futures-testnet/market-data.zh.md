## **Noop**

> **Response:**

```javascript
{
 "code": 200,
 "msg": "success"
}
```

``POST /fapi/v3/noop``

通过此请求，可以高效取消已发送但仍在队列中且尚未完成链上操作的交易（Nonce需与该请求一致,不保证成功）

**Weight:**
1

#### 示例 : Noop (方法以python为例) 

```python
import time
import urllib

import aiohttp
from eth_account.messages import  encode_structured_data
from eth_account import Account
import asyncio

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
    "chainId": 1666,
    "verifyingContract": "0x0000000000000000000000000000000000000000"
  },
  "message": {
    "msg": "$msg"
  }
}

headers = {
    'Content-Type': 'application/x-www-form-urlencoded',
    'User-Agent': 'PythonApp/1.0'
}
host = 'https://fapi3.asterdex.com'

# config your user and agent info here
user = '*'
signer = '*'
private_key = "**"

noop = {'url':'/fapi/v3/noop','method':'POST','params':{}}
place_order = {"url":"/fapi/v3/order","method":"POST","params":{"symbol": "ASTERUSDT", "type": "LIMIT", "side": "BUY",
                  "timeInForce": "GTC", "quantity": "20", "price": "0.5"}}
_last_ms = 0
_i = 0

def get_nonce():
    global _last_ms, _i
    now_ms = int(time.time())

    if now_ms == _last_ms:
        _i += 1
    else:
        _last_ms = now_ms
        _i = 0

    return now_ms * 1_000_000 + _i

async def send_by_url(api,nonce) :
    my_dict = api['params']
    url = host + api['url']

    my_dict['nonce'] = nonce
    my_dict['user'] = user
    my_dict['signer'] = signer
    param = urllib.parse.urlencode(my_dict)

    typed_data['message']['msg'] = param
    message = encode_structured_data(typed_data)
    signed = Account.sign_message(message, private_key=private_key)

    url = url + '?' + param + '&signature=' + signed.signature.hex()
    print(url)
    async with aiohttp.ClientSession() as session:
        async with session.post(url, headers=headers) as resp:
            text = await resp.text()
            return {"status_code": resp.status, "text": text,'nonce': nonce}

async def main():
    _place_order_nonce = get_nonce()
    task_place_order =asyncio.create_task( send_by_url(place_order, _place_order_nonce))
    task_noop= asyncio.create_task( send_by_url(noop, _place_order_nonce))
    place_order_result, noop_result = await asyncio.gather(task_place_order, task_noop)
    print(f"place_order_result : {place_order_result['text']},nonce: {place_order_result['nonce']}")
    print(f"noop_result : {noop_result['text']},nonce: {noop_result['nonce']}")

if __name__ == '__main__':
     asyncio.run(main())

# This operation may not succeed every time. Please retry.
# place_order_result : {"code":-4225,"msg":"Nonce Expired"},nonce: 1773321233000000
# noop_result : {"code": 200,"msg": "success"},nonce: 1773321233000000

```

## **测试服务器连通性 PING**
``
GET /fapi/v3/ping
``

> **响应:**

```javascript
{}
```

测试能否联通

**权重:**
1

**参数:**
NONE



## **获取服务器时间**

> **响应:**

```javascript
{
  "serverTime": 1499827319559 // 当前的系统时间
}
```

``
GET /fapi/v3/time
``

获取服务器时间

**权重:**
1

**参数:**
NONE


## **获取交易规则和交易对**

> **响应:**

```javascript
{
	"exchangeFilters": [],
 	"rateLimits": [ // API访问的限制
 		{
 			"interval": "MINUTE", // 按照分钟计算
   			"intervalNum": 1, // 按照1分钟计算
   			"limit": 2400, // 上限次数
   			"rateLimitType": "REQUEST_WEIGHT" // 按照访问权重来计算
   		},
  		{
  			"interval": "MINUTE",
   			"intervalNum": 1,
   			"limit": 1200,
   			"rateLimitType": "ORDERS" // 按照订单数量来计算
   		}
   	],
 	"serverTime": 1565613908500, // 请忽略。如果需要获取当前系统时间，请查询接口 “GET /fapi/v3/time”
 	"assets": [ // 资产信息
 		{
 			"asset": "BUSD",
   			"marginAvailable": true, // 是否可用作保证金
   			"autoAssetExchange": 0 // 保证金资产自动兑换阈值
   		},
 		{
 			"asset": "USDT",
   			"marginAvailable": true, // 是否可用作保证金
   			"autoAssetExchange": 0 // 保证金资产自动兑换阈值
   		},
 		{
 			"asset": "BNB",
   			"marginAvailable": false, // 是否可用作保证金
   			"autoAssetExchange": null // 保证金资产自动兑换阈值
   		}
   	],
 	"symbols": [ // 交易对信息
 		{
 			"symbol": "BLZUSDT",  // 交易对
 			"pair": "BLZUSDT",  // 标的交易对
 			"contractType": "PERPETUAL",	// 合约类型
 			"deliveryDate": 4133404800000,  // 交割日期
 			"onboardDate": 1598252400000,	  // 上线日期
 			"status": "TRADING",  // 交易对状态
 			"maintMarginPercent": "2.5000",  // 请忽略
 			"requiredMarginPercent": "5.0000", // 请忽略
 			"baseAsset": "BLZ",  // 标的资产
 			"quoteAsset": "USDT", // 报价资产
 			"marginAsset": "USDT", // 保证金资产
 			"pricePrecision": 5,  // 价格小数点位数(仅作为系统精度使用，注意同tickSize 区分）
 			"quantityPrecision": 0,  // 数量小数点位数(仅作为系统精度使用，注意同stepSize 区分）
 			"baseAssetPrecision": 8,  // 标的资产精度
 			"quotePrecision": 8,  // 报价资产精度
 			"underlyingType": "COIN",
 			"underlyingSubType": ["STORAGE"],
 			"settlePlan": 0,
 			"triggerProtect": "0.15", // 开启"priceProtect"的条件订单的触发阈值
 			"filters": [
 				{
 					"filterType": "PRICE_FILTER", // 价格限制
     				"maxPrice": "300", // 价格上限, 最大价格
     				"minPrice": "0.0001", // 价格下限, 最小价格
     				"tickSize": "0.0001" // 订单最小价格间隔
     			},
    			{
    				"filterType": "LOT_SIZE", // 数量限制
     				"maxQty": "10000000", // 数量上限, 最大数量
     				"minQty": "1", // 数量下限, 最小数量
     				"stepSize": "1" // 订单最小数量间隔
     			},
    			{
    				"filterType": "MARKET_LOT_SIZE", // 市价订单数量限制
     				"maxQty": "590119", // 数量上限, 最大数量
     				"minQty": "1", // 数量下限, 最小数量
     				"stepSize": "1" // 允许的步进值
     			},
     			{
    				"filterType": "MAX_NUM_ORDERS", // 最多订单数限制
    				"limit": 200
  				},
  				{
    				"filterType": "MAX_NUM_ALGO_ORDERS", // 最多条件订单数限制
    				"limit": 100
  				},
  				{
  					"filterType": "MIN_NOTIONAL",  // 最小名义价值
  					"notional": "1", 
  				},
  				{
    				"filterType": "PERCENT_PRICE", // 价格比限制
    				"multiplierUp": "1.1500", // 价格上限百分比
    				"multiplierDown": "0.8500", // 价格下限百分比
    				"multiplierDecimal": 4
    			}
   			],
 			"OrderType": [ // 订单类型
   				"LIMIT",  // 限价单
   				"MARKET",  // 市价单
   				"STOP", // 止损单
   				"STOP_MARKET", // 止损市价单
   				"TAKE_PROFIT", // 止盈单
   				"TAKE_PROFIT_MARKET", // 止盈暑市价单
   				"TRAILING_STOP_MARKET" // 跟踪止损市价单
   			],
   			"timeInForce": [ // 有效方式
   				"GTC", // 成交为止, 一直有效
   				"IOC", // 无法立即成交(吃单)的部分就撤销
   				"FOK", // 无法全部立即成交就撤销
   				"GTX", // 无法成为挂单方就撤销
				"HIDDEN"
 			],
 			"liquidationFee": "0.010000",	// 强平费率
   			"marketTakeBound": "0.30",	// 市价吃单(相对于标记价格)允许可造成的最大价格偏离比例
 		}
   	],
	"timezone": "UTC" // 服务器所用的时间区域
}

```

``
GET /fapi/v3/exchangeInfo
``

获取交易规则和交易对

**权重:**
1

**参数:**
NONE



## **深度信息**

> **响应:**

```javascript
{
  "lastUpdateId": 1027024,
  "E": 1589436922972,   // 消息时间
  "T": 1589436922959,   // 撮合引擎时间
  "bids": [				// 买单
    [
      "4.00000000",     // 价格
      "431.00000000"    // 数量
    ]
  ],
  "asks": [				// 卖单
    [
      "4.00000200",		// 价格
      "12.00000000"		// 数量
    ]
  ]
}
```

``
GET /fapi/v3/depth
``

**权重:**

limit         | 权重
------------  | ------------
5, 10, 20, 50 | 2
100           | 5
500           | 10
1000          | 20

**参数:**

 名称  |  类型  | 是否必需 |                            描述
------ | ------ | -------- | -----------------------------------------------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认 500; 可选值:[5, 10, 20, 50, 100, 500, 1000]



## **近期成交**

> **响应:**

```javascript
[
  {
    "id": 28457,				// 成交ID
    "price": "4.00000100",		// 成交价格
    "qty": "12.00000000",		// 成交量
    "quoteQty": "48.00",		// 成交额
    "time": 1499865549590,		// 时间
    "isBuyerMaker": true		// 买方是否为挂单方
  }
]
```

``
GET /fapi/v3/trades
``

获取近期订单簿成交

**权重:**
1

**参数:**

 名称  |  类型  | 是否必需 |          描述
------ | ------ | -------- | ----------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认:500，最大1000 

* 仅返回订单簿成交，即不会返回保险基金和自动减仓(ADL)成交

## **查询历史成交(MARKET_DATA)**

> **响应:**

```javascript
[
  {
    "id": 28457,				// 成交ID
    "price": "4.00000100",		// 成交价格
    "qty": "12.00000000",		// 成交量
    "quoteQty": "48.00",		// 成交额
    "time": 1499865549590,		// 时间
    "isBuyerMaker": true		// 买方是否为挂单方
  }
]
```

``
GET /fapi/v3/historicalTrades
``

查询订单簿历史成交

**权重:**
20

**参数:**

 名称  |  类型  | 是否必需 |                      描述
------ | ------ | -------- | ----------------------------------------------
symbol | STRING | YES      | 交易对
limit  | INT    | NO       | 默认值:500 最大值:1000.
fromId | LONG   | NO       | 从哪一条成交id开始返回. 缺省返回最近的成交记录

* 仅返回订单簿成交，即不会返回保险基金和自动减仓(ADL)成交

## **近期成交(归集)**

> **响应:**

```javascript
[
  {
    "a": 26129,         // 归集成交ID
    "p": "0.01633102",  // 成交价
    "q": "4.70443515",  // 成交量
    "f": 27781,         // 被归集的首个成交ID
    "l": 27781,         // 被归集的末个成交ID
    "T": 1498793709153, // 成交时间
    "m": true,          // 是否为主动卖出单
  }
]
```

``
GET /fapi/v3/aggTrades
``

归集交易与逐笔交易的区别在于，同一价格、同一方向、同一时间(按秒计算)的订单簿trade会被聚合为一条

**权重:**
20

**参数:**

  名称    |  类型  | 是否必需 |                描述
--------- | ------ | -------- | ----------------------------------
symbol    | STRING | YES      | 交易对
fromId    | LONG   | NO       | 从包含fromID的成交开始返回结果
startTime | LONG   | NO       | 从该时刻之后的成交记录开始返回结果
endTime   | LONG   | NO       | 返回该时刻为止的成交记录
limit     | INT    | NO       | 默认 500; 最大 1000.

* 如果同时发送`startTime`和`endTime`，间隔必须小于一小时
* 如果没有发送任何筛选参数(`fromId`, `startTime`, `endTime`)，默认返回最近的成交记录
* 保险基金和自动减仓(ADL)成交不属于订单簿成交，故不会被归并聚合


## **K线数据**

> **响应:**

```javascript
[
  [
    1499040000000,      // 开盘时间
    "0.01634790",       // 开盘价
    "0.80000000",       // 最高价
    "0.01575800",       // 最低价
    "0.01577100",       // 收盘价(当前K线未结束的即为最新价)
    "148976.11427815",  // 成交量
    1499644799999,      // 收盘时间
    "2434.19055334",    // 成交额
    308,                // 成交笔数
    "1756.87402397",    // 主动买入成交量
    "28.46694368",      // 主动买入成交额
    "17928899.62484339" // 请忽略该参数
  ]
]
```

``
GET /fapi/v3/klines
``

每根K线的开盘时间可视为唯一ID

**权重:** 取决于请求中的LIMIT参数

LIMIT参数 | 权重
---|---
[1,100) | 1
[100, 500) | 2
[500, 1000] | 5
> 1000 | 10

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
symbol    | STRING | YES      | 交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500.

* 缺省返回最近的数据



## **价格指数K线数据**

> **响应:**

```javascript
[
  [
    1591256400000,      	// 开盘时间
    "9653.69440000",    	// 开盘价
    "9653.69640000",     	// 最高价
    "9651.38600000",     	// 最低价
    "9651.55200000",     	// 收盘价(当前K线未结束的即为最新价)
    "0	", 					// 请忽略
    1591256459999,      	// 收盘时间
    "0",    				// 请忽略
    60,                		// 构成记录数
    "0",    				// 请忽略
    "0",      				// 请忽略
    "0" 					// 请忽略
  ]
]
```

``
GET /fapi/v3/indexPriceKlines
``

每根K线的开盘时间可视为唯一ID

**权重:** 取决于请求中的LIMIT参数

LIMIT参数 | 权重
---|---
[1,100) | 1
[100, 500) | 2
[500, 1000] | 5
> 1000 | 10

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
pair    	| STRING | YES      | 标的交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 缺省返回最近的数据


## **标记价格K线数据**

> **响应:**

```javascript
[
  [
    1591256400000,      	// 开盘时间
    "9653.69440000",    	// 开盘价
    "9653.69640000",     	// 最高价
    "9651.38600000",     	// 最低价
    "9651.55200000",     	// 收盘价(当前K线未结束的即为最新价)
    "0	", 					// 请忽略
    1591256459999,      	// 收盘时间
    "0",    				// 请忽略
    60,                		// 构成记录数
    "0",    				// 请忽略
    "0",      				// 请忽略
    "0" 					// 请忽略
  ]
]
```

``
GET /fapi/v3/markPriceKlines
``
每根K线的开盘时间可视为唯一ID

**权重:** 取决于请求中的LIMIT参数

LIMIT参数 | 权重
---|---
[1,100) | 1
[100, 500) | 2
[500, 1000] | 5
> 1000 | 10

**参数:**

  名称    |  类型  | 是否必需 |          描述
--------- | ------ | -------- | ----------------------
symbol   	| STRING | YES      | 交易对
interval  | ENUM   | YES      | 时间间隔
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:500 最大值:1500

* 缺省返回最近的数据


## **最新标记价格和资金费率**

> **响应:**

```javascript
{
    "symbol": "BTCUSDT",				// 交易对
    "markPrice": "11793.63104562",		// 标记价格
    "indexPrice": "11781.80495970",		// 指数价格
    "estimatedSettlePrice": "11781.16138815",  // 预估结算价,仅在交割开始前最后一小时有意义
    "lastFundingRate": "0.00038246",	// 最近更新的资金费率
    "nextFundingTime": 1597392000000,	// 下次资金费时间
    "interestRate": "0.00010000",		// 标的资产基础利率
    "time": 1597370495002				// 更新时间
}
```

> **当不指定symbol时相应**

```javascript
[
	{
    	"symbol": "BTCUSDT",			// 交易对
    	"markPrice": "11793.63104562",	// 标记价格
    	"indexPrice": "11781.80495970",	// 指数价格
    	"estimatedSettlePrice": "11781.16138815",  // 预估结算价,仅在交割开始前最后一小时有意义
    	"lastFundingRate": "0.00038246",	// 最近更新的资金费率
    	"nextFundingTime": 1597392000000,	// 下次资金费时间
    	"interestRate": "0.00010000",		// 标的资产基础利率
    	"time": 1597370495002				// 更新时间
	}
]
```


``
GET /fapi/v3/premiumIndex
``

采集各大交易所数据加权平均

**权重:**
1

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对


## **查询资金费率历史**

> **响应:**

```javascript
[
	{
    	"symbol": "BTCUSDT",			// 交易对
    	"fundingRate": "-0.03750000",	// 资金费率
    	"fundingTime": 1570608000000,	// 资金费时间
	},
	{
   		"symbol": "BTCUSDT",
    	"fundingRate": "0.00010000",
    	"fundingTime": 1570636800000,
	}
]
```

``
GET /fapi/v3/fundingRate
``

**权重:**
1

**参数:**

  名称    |  类型  | 是否必需 |                         描述
--------- | ------ | -------- | -----------------------------------------------------
symbol    | STRING | NO      | 交易对
startTime | LONG   | NO       | 起始时间
endTime   | LONG   | NO       | 结束时间
limit     | INT    | NO       | 默认值:100 最大值:1000

* 如果 `startTime` 和 `endTime` 都未发送, 返回最近 `limit` 条数据.
* 如果 `startTime` 和 `endTime` 之间的数据量大于 `limit`, 返回 `startTime` + `limit`情况下的数据。


## **24hr价格变动情况**

> **响应:**

```javascript
{
  "symbol": "BTCUSDT",
  "priceChange": "-94.99999800",    //24小时价格变动
  "priceChangePercent": "-95.960",  //24小时价格变动百分比
  "weightedAvgPrice": "0.29628482", //加权平均价
  "lastPrice": "4.00000200",        //最近一次成交价
  "lastQty": "200.00000000",        //最近一次成交额
  "openPrice": "99.00000000",       //24小时内第一次成交的价格
  "highPrice": "100.00000000",      //24小时最高价
  "lowPrice": "0.10000000",         //24小时最低价
  "volume": "8913.30000000",        //24小时成交量
  "quoteVolume": "15.30000000",     //24小时成交额
  "openTime": 1499783499040,        //24小时内，第一笔交易的发生时间
  "closeTime": 1499869899040,       //24小时内，最后一笔交易的发生时间
  "firstId": 28385,   // 首笔成交id
  "lastId": 28460,    // 末笔成交id
  "count": 76         // 成交笔数
}
```

> 或(当不发送交易对信息)

```javascript
[
	{
  		"symbol": "BTCUSDT",
  		"priceChange": "-94.99999800",    //24小时价格变动
  		"priceChangePercent": "-95.960",  //24小时价格变动百分比
  		"weightedAvgPrice": "0.29628482", //加权平均价
  		"lastPrice": "4.00000200",        //最近一次成交价
  		"lastQty": "200.00000000",        //最近一次成交额
  		"openPrice": "99.00000000",       //24小时内第一次成交的价格
  		"highPrice": "100.00000000",      //24小时最高价
  		"lowPrice": "0.10000000",         //24小时最低价
  		"volume": "8913.30000000",        //24小时成交量
  		"quoteVolume": "15.30000000",     //24小时成交额
  		"openTime": 1499783499040,        //24小时内，第一笔交易的发生时间
  		"closeTime": 1499869899040,       //24小时内，最后一笔交易的发生时间
  		"firstId": 28385,   // 首笔成交id
  		"lastId": 28460,    // 末笔成交id
  		"count": 76         // 成交笔数
    }
]
```

``
GET /fapi/v3/ticker/24hr
``

请注意，不携带symbol参数会返回全部交易对数据，不仅数据庞大，而且权重极高

**权重:**
* 带symbol为`1`
* 不带为`40`

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对

* 不发送交易对参数，则会返回所有交易对信息


## **最新价格**

> **响应:**

```javascript
{
  "symbol": "LTCBTC",		// 交易对
  "price": "4.00000200",		// 价格
  "time": 1589437530011   // 撮合引擎时间
}
```

> 或(当不发送symbol)

```javascript
[
	{
  		"symbol": "BTCUSDT",	// 交易对
  		"price": "6000.01",		// 价格
  		"time": 1589437530011   // 撮合引擎时间
	}
]
```

``
GET /fapi/v3/ticker/price
``

返回最近价格

**权重:**
* 单交易对`1`
* 无交易对`2`

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对

* 不发送交易对参数，则会返回所有交易对信息


## **当前最优挂单**

> **响应:**

```javascript
{
  "symbol": "BTCUSDT", // 交易对
  "bidPrice": "4.00000000", //最优买单价
  "bidQty": "431.00000000", //挂单量
  "askPrice": "4.00000200", //最优卖单价
  "askQty": "9.00000000", //挂单量
  "time": 1589437530011   // 撮合引擎时间
}
```
> 或(当不发送symbol)

```javascript
[
	{
  		"symbol": "BTCUSDT", // 交易对
  		"bidPrice": "4.00000000", //最优买单价
  		"bidQty": "431.00000000", //挂单量
  		"askPrice": "4.00000200", //最优卖单价
  		"askQty": "9.00000000", //挂单量
  		"time": 1589437530011   // 撮合引擎时间
	}
]
```

``
GET /fapi/v3/ticker/bookTicker
``

返回当前最优的挂单(最高买单，最低卖单)

**权重:**
* 单交易对`1`   
* 无交易对`2`

**参数:**

 名称  |  类型  | 是否必需 |  描述
------ | ------ | -------- | ------
symbol | STRING | NO       | 交易对

* 不发送交易对参数，则会返回所有交易对信息



