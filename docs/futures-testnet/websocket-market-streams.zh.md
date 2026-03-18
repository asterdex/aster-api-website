## **概览**

* 本篇所列出的所有wss接口baseurl: **wss://fstream.asterdex.com**
* 订阅单一stream格式为 **/ws/\<streamName\>**
* 组合streams的URL格式为 **/stream?streams=\<streamName1\>/\<streamName2\>/\<streamName3\>**
* 订阅组合streams时，事件payload会以这样的格式封装 **{"stream":"\<streamName\>","data":\<rawPayload\>}**
* stream名称中所有交易对均为**小写**
* 每个链接有效期不超过24小时，请妥善处理断线重连。
* 服务端每5分钟会发送ping帧，客户端应当在15分钟内回复pong帧，否则服务端会主动断开链接。允许客户端发送不成对的pong帧(即客户端可以以高于15分钟每次的频率发送pong帧保持链接)。
* Websocket服务器每秒最多接受10个订阅消息。
* 如果用户发送的消息超过限制，连接会被断开连接。反复被断开连接的IP有可能被服务器屏蔽。
* 单个连接最多可以订阅 **200** 个Streams。




## **实时订阅/取消数据流**

* 以下数据可以通过websocket发送以实现订阅或取消订阅数据流。示例如下。
* 响应内容中的`id`是无符号整数，作为往来信息的唯一标识。

### 订阅一个信息流

> **响应**

  ```javascript
  {
    "result": null,
    "id": 1
  }
  ```

* **请求**

  	{    
    	"method": "SUBSCRIBE",    
    	"params":     
    	[   
      	"btcusdt@aggTrade",    
      	"btcusdt@depth"     
    	],    
    	"id": 1   
  	}



### 取消订阅一个信息流

> **响应**
  
  ```javascript
  {
    "result": null,
    "id": 312
  }
  ```

* **请求**

  {   
    "method": "UNSUBSCRIBE",    
    "params":     
    [    
      "btcusdt@depth"   
    ],    
    "id": 312   
  }



### 已订阅信息流

> **响应**
  
  ```javascript
  {
    "result": [
      "btcusdt@aggTrade"
    ],
    "id": 3
  }
  ```


* **请求**

  {   
    "method": "LIST_SUBSCRIPTIONS",    
    "id": 3   
  }     
 


### 设定属性
当前，唯一可以设置的属性是设置是否启用`combined`("组合")信息流。   
当使用`/ws/`("原始信息流")进行连接时，combined属性设置为`false`，而使用 `/stream/`进行连接时则将属性设置为`true`。


> **响应**
  
  ```javascript
  {
    "result": null
    "id": 5
  }
  ```

* **请求**

  {    
    "method": "SET_PROPERTY",    
    "params":     
    [   
      "combined",    
      true   
    ],    
    "id": 5   
  }




### 检索属性

> **响应**

  ```javascript
  {
    "result": true, // Indicates that combined is set to true.
    "id": 2
  }
  ```
  
* **请求**
  
  {   
    "method": "GET_PROPERTY",    
    "params":     
    [   
      "combined"   
    ],    
    "id": 2   
  }   
 



### 错误信息

错误信息 | 描述
---|---
{"code": 0, "msg": "Unknown property"} |  `SET_PROPERTY` 或 `GET_PROPERTY`中应用的参数无效
{"code": 1, "msg": "Invalid value type: expected Boolean"} | 仅接受`true`或`false`
{"code": 2, "msg": "Invalid request: property name must be a string"}| 提供的属性名无效
{"code": 2, "msg": "Invalid request: request ID must be an unsigned integer"}| 参数`id`未提供或`id`值是无效类型
{"code": 2, "msg": "Invalid request: unknown variant %s, expected one of `SUBSCRIBE`, `UNSUBSCRIBE`, `LIST_SUBSCRIPTIONS`, `SET_PROPERTY`, `GET_PROPERTY` at line 1 column 28"} | 错字提醒，或提供的值不是预期类型
{"code": 2, "msg": "Invalid request: too many parameters"}| 数据中提供了不必要参数
{"code": 2, "msg": "Invalid request: property name must be a string"} | 未提供属性名
{"code": 2, "msg": "Invalid request: missing field `method` at line 1 column 73"} | 数据未提供`method`
{"code":3,"msg":"Invalid JSON: expected value at line %s column %s"} | JSON 语法有误.




## **最新合约价格**
aggTrade中的价格'p'或ticker/miniTicker中的价格'c'均可以作为最新成交价。

## **归集交易**

> **Payload:**

```javascript
{
  "e": "aggTrade",  // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BNBUSDT",    // 交易对
  "a": 5933014,		// 归集成交 ID
  "p": "0.001",     // 成交价格
  "q": "100",       // 成交量
  "f": 100,         // 被归集的首个交易ID
  "l": 105,         // 被归集的末次交易ID
  "T": 123456785,   // 成交时间
  "m": true         // 买方是否是做市方。如true，则此次成交是一个主动卖出单，否则是一个主动买入单。
}
```

同一价格、同一方向、同一时间(100ms计算)的trade会被聚合为一条.

**Stream Name:**       
``<symbol>@aggTrade``

**Update Speed:** 100ms





## **最新标记价格**

> **Payload:**

```javascript
  {
    "e": "markPriceUpdate",  	// 事件类型
    "E": 1562305380000,      	// 事件时间
    "s": "BTCUSDT",          	// 交易对
    "p": "11794.15000000",   	// 标记价格
    "i": "11784.62659091",		// 现货指数价格
    "P": "11784.25641265",		// 预估结算价,仅在结算前最后一小时有参考价值
    "r": "0.00038167",       	// 资金费率
    "T": 1562306400000       	// 下次资金时间
  }
```


**Stream Name:**    
``<symbol>@markPrice`` 或 ``<symbol>@markPrice@1s``

**Update Speed:** 3000ms 或 1000ms






## **全市场最新标记价格**

> **Payload:**

```javascript
[
  {
    "e": "markPriceUpdate",  	// 事件类型
    "E": 1562305380000,      	// 事件时间
    "s": "BTCUSDT",          	// 交易对
    "p": "11185.87786614",   	// 标记价格
    "i": "11784.62659091"		// 现货指数价格
    "P": "11784.25641265",		// 预估结算价,仅在结算前最后一小时有参考价值
    "r": "0.00030000",       	// 资金费率
    "T": 1562306400000       	// 下个资金时间
  }
]
```


**Stream Name:**    
``!markPrice@arr`` 或 ``!markPrice@arr@1s``

**Update Speed:** 3000ms 或 1000ms





## **K线**

> **Payload:**

```javascript
{
  "e": "kline",     // 事件类型
  "E": 123456789,   // 事件时间
  "s": "BNBUSDT",    // 交易对
  "k": {
    "t": 123400000, // 这根K线的起始时间
    "T": 123460000, // 这根K线的结束时间
    "s": "BNBUSDT",  // 交易对
    "i": "1m",      // K线间隔
    "f": 100,       // 这根K线期间第一笔成交ID
    "L": 200,       // 这根K线期间末一笔成交ID
    "o": "0.0010",  // 这根K线期间第一笔成交价
    "c": "0.0020",  // 这根K线期间末一笔成交价
    "h": "0.0025",  // 这根K线期间最高成交价
    "l": "0.0015",  // 这根K线期间最低成交价
    "v": "1000",    // 这根K线期间成交量
    "n": 100,       // 这根K线期间成交笔数
    "x": false,     // 这根K线是否完结(是否已经开始下一根K线)
    "q": "1.0000",  // 这根K线期间成交额
    "V": "500",     // 主动买入的成交量
    "Q": "0.500",   // 主动买入的成交额
    "B": "123456"   // 忽略此参数
  }
}
```

K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。推送间隔250毫秒(如有刷新)

**订阅Kline需要提供间隔参数，最短为分钟线，最长为月线。支持以下间隔:**

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

**Stream Name:**    
``<symbol>@kline_<interval>``

**Update Speed:** 250ms




## **按Symbol的精简Ticker**

> **Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // 事件类型
    "E": 123456789,         // 事件时间(毫秒)
    "s": "BNBUSDT",          // 交易对
    "c": "0.0025",          // 最新成交价格
    "o": "0.0010",          // 24小时前开始第一笔成交价格
    "h": "0.0025",          // 24小时内最高成交价
    "l": "0.0010",          // 24小时内最低成交价
    "v": "10000",           // 成交量
    "q": "18"               // 成交额
  }
```

按Symbol刷新的24小时精简ticker信息.

**Stream Name:**     
``<symbol>@miniTicker`

**Update Speed:** 500ms



## **全市场的精简Ticker**

> **Payload:**

```javascript
[  
  {
    "e": "24hrMiniTicker",  // 事件类型
    "E": 123456789,         // 事件时间(毫秒)
    "s": "BNBUSDT",          // 交易对
    "c": "0.0025",          // 最新成交价格
    "o": "0.0010",          // 24小时前开始第一笔成交价格
    "h": "0.0025",          // 24小时内最高成交价
    "l": "0.0010",          // 24小时内最低成交价
    "v": "10000",           // 成交量
    "q": "18"               // 成交额
  }
]
```

所有symbol24小时精简ticker信息.需要注意的是，只有发生变化的ticker更新才会被推送。

**Stream Name:**     
`!miniTicker@arr`

**Update Speed:** 1000ms




## **按Symbol的完整Ticker**


> **Payload:**

```javascript
{
  "e": "24hrTicker",  // 事件类型
  "E": 123456789,     // 事件时间
  "s": "BNBUSDT",      // 交易对
  "p": "0.0015",      // 24小时价格变化
  "P": "250.00",      // 24小时价格变化(百分比)
  "w": "0.0018",      // 平均价格
  "c": "0.0025",      // 最新成交价格
  "Q": "10",          // 最新成交价格上的成交量
  "o": "0.0010",      // 24小时内第一比成交的价格
  "h": "0.0025",      // 24小时内最高成交价
  "l": "0.0010",      // 24小时内最低成交价
  "v": "10000",       // 24小时内成交量
  "q": "18",          // 24小时内成交额
  "O": 0,             // 统计开始时间
  "C": 86400000,      // 统计关闭时间
  "F": 0,             // 24小时内第一笔成交交易ID
  "L": 18150,         // 24小时内最后一笔成交交易ID
  "n": 18151          // 24小时内成交数
}
```

按Symbol刷新的24小时完整ticker信息

**Stream Name:**     
``<symbol>@ticker``

**Update Speed:** 500ms



## **全市场的完整Ticker**


> **Payload:**

```javascript
[
	{
	  "e": "24hrTicker",  // 事件类型
	  "E": 123456789,     // 事件时间
	  "s": "BNBUSDT",      // 交易对
	  "p": "0.0015",      // 24小时价格变化
	  "P": "250.00",      // 24小时价格变化(百分比)
	  "w": "0.0018",      // 平均价格
	  "c": "0.0025",      // 最新成交价格
	  "Q": "10",          // 最新成交价格上的成交量
	  "o": "0.0010",      // 24小时内第一比成交的价格
	  "h": "0.0025",      // 24小时内最高成交价
	  "l": "0.0010",      // 24小时内最低成交价
	  "v": "10000",       // 24小时内成交量
	  "q": "18",          // 24小时内成交额
	  "O": 0,             // 统计开始时间
	  "C": 86400000,      // 统计结束时间
	  "F": 0,             // 24小时内第一笔成交交易ID
	  "L": 18150,         // 24小时内最后一笔成交交易ID
	  "n": 18151          // 24小时内成交数
	}
]	
```

所有symbol 24小时完整ticker信息.需要注意的是，只有发生变化的ticker更新才会被推送。

**Stream Name:**     
``!ticker@arr``

**Update Speed:** 1000ms


## **按Symbol的最优挂单信息**

> **Payload:**

```javascript
{
  "e":"bookTicker",		// 事件类型
  "u":400900217,     	// 更新ID
  "E": 1568014460893,	// 事件推送时间
  "T": 1568014460891,	// 撮合时间
  "s":"BNBUSDT",     	// 交易对
  "b":"25.35190000", 	// 买单最优挂单价格
  "B":"31.21000000", 	// 买单最优挂单数量
  "a":"25.36520000", 	// 卖单最优挂单价格
  "A":"40.66000000"  	// 卖单最优挂单数量
}
```


实时推送指定交易对最优挂单信息

**Stream Name:** `<symbol>@bookTicker`

**Update Speed:** 实时





## **全市场最优挂单信息**

> **Payload:**

```javascript
{
  // Same as <symbol>@bookTicker payload
}
```

所有交易对交易对最优挂单信息

**Stream Name:** `!bookTicker`

**Update Speed:** 实时



##强平订单

> **Payload:**

```javascript
{

	"e":"forceOrder",                   // 事件类型
	"E":1568014460893,                  // 事件时间
	"o":{
	
		"s":"BTCUSDT",                   // 交易对
		"S":"SELL",                      // 订单方向
		"o":"LIMIT",                     // 订单类型
		"f":"IOC",                       // 有效方式
		"q":"0.014",                     // 订单数量
		"p":"9910",                      // 订单价格
		"ap":"9910",                     // 平均价格
		"X":"FILLED",                    // 订单状态
		"l":"0.014",                     // 订单最近成交量
		"z":"0.014",                     // 订单累计成交量
		"T":1568014460893,          	 // 交易时间
	
	}

}
```

推送特定`symbol`的强平订单快照信息。

1000ms内至多仅推送一条最近的强平订单作为快照

**Stream Name:**  ``<symbol>@forceOrder``

**Update Speed:** 1000ms





## **有限档深度信息**

> **Payload:**

```javascript
{
  "e": "depthUpdate", 			// 事件类型
  "E": 1571889248277, 			// 事件时间
  "T": 1571889248276, 			// 交易时间
  "s": "BTCUSDT",
  "U": 390497796,
  "u": 390497878,
  "pu": 390497794,
  "b": [          				// 买方
    [
      "7403.89",  				// 价格
      "0.002"     				// 数量
    ],
    [
      "7403.90",
      "3.906"
    ],
    [
      "7404.00",
      "1.428"
    ],
    [
      "7404.85",
      "5.239"
    ],
    [
      "7405.43",
      "2.562"
    ]
  ],
  "a": [          				// 卖方
    [
      "7405.96",  				// 价格
      "3.340"     				// 数量
    ],
    [
      "7406.63",
      "4.525"
    ],
    [
      "7407.08",
      "2.475"
    ],
    [
      "7407.15",
      "4.800"
    ],
    [
      "7407.20",
      "0.175"
    ]
  ]
}
```

推送有限档深度信息。levels表示几档买卖单信息, 可选 5/10/20档

**Stream Names:** `<symbol>@depth<levels>` 或 `<symbol>@depth<levels>@500ms` 或 `<symbol>@depth<levels>@100ms`.  

**Update Speed:** 250ms 或 500ms 或 100ms




## **增量深度信息**

> **Payload:**

```javascript
{
  "e": "depthUpdate", 	// 事件类型
  "E": 123456789,     	// 事件时间
  "T": 123456788,     	// 撮合时间
  "s": "BNBUSDT",      	// 交易对
  "U": 157,           	// 从上次推送至今新增的第一个 update Id
  "u": 160,           	// 从上次推送至今新增的最后一个 update Id
  "pu": 149,          	// 上次推送的最后一个update Id(即上条消息的‘u’)
  "b": [              	// 变动的买单深度
    [
      "0.0024",       	// 价格
      "10"           	// 数量
    ]
  ],
  "a": [              	// 变动的卖单深度
    [
      "0.0026",       	// 价格
      "100"          	// 数量
    ]
  ]
}
```

orderbook的变化部分，推送间隔250毫秒,500毫秒，100毫秒(如有刷新)

**Stream 名称:**     
``<symbol>@depth`` OR ``<symbol>@depth@500ms`` OR ``<symbol>@depth@100ms``

**Update Speed:** 250ms 或 500ms 或 100ms


## **如何正确在本地维护一个orderbook副本**
1. 订阅 **wss://fstream.asterdex.com/stream?streams=btcusdt@depth**
2. 开始缓存收到的更新。同一个价位，后收到的更新覆盖前面的。
3. 访问Rest接口 **https://fapi.asterdex.com/fapi/v3/depth?symbol=BTCUSDT&limit=1000**获得一个1000档的深度快照
4. 将目前缓存到的信息中`u`< 步骤3中获取到的快照中的`lastUpdateId`的部分丢弃(丢弃更早的信息，已经过期)。
5. 将深度快照中的内容更新到本地orderbook副本中，并从websocket接收到的第一个`U` <= `lastUpdateId` **且** `u` >= `lastUpdateId` 的event开始继续更新本地副本。
6. 每一个新event的`pu`应该等于上一个event的`u`，否则可能出现了丢包，请从step3重新进行初始化。
7. 每一个event中的挂单量代表这个价格目前的挂单量**绝对值**，而不是相对变化。
8. 如果某个价格对应的挂单量为0，表示该价位的挂单已经撤单或者被吃，应该移除这个价位。



