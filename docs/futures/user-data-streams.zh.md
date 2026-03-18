## **概览**


* 本篇所列出REST接口的baseurl **https://fapi.asterdex.com**
* 用于订阅账户数据的 `listenKey` 从创建时刻起有效期为60分钟
* 可以通过`PUT`一个`listenKey`延长60分钟有效期
* 可以通过`DELETE`一个 `listenKey` 立即关闭当前数据流，并使该`listenKey` 无效
* 在具有有效`listenKey`的帐户上执行`POST`将返回当前有效的`listenKey`并将其有效期延长60分钟
* 本篇所列出的websocket接口baseurl: **wss://fstream.asterdex.com**
* 订阅账户数据流的stream名称为 **/ws/\<listenKey\>**
* 每个链接有效期不超过24小时，请妥善处理断线重连。
* 账户数据流的消息**不保证**严格时间序; **请使用 E 字段进行排序**
* 考虑到剧烈行情下, RESTful接口可能存在查询延迟，我们强烈建议您优先从Websocket user data stream推送的消息来获取订单，仓位等信息。


## **生成listenKey (USER_STREAM)**


> **响应:**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

``
POST /fapi/v1/listenKey
``

创建一个新的user data stream，返回值为一个listenKey，即websocket订阅的stream名称。如果该帐户具有有效的`listenKey`，则将返回该`listenKey`并将其有效期延长60分钟。

**权重:**
1

**参数:**

None


## **延长listenKey有效期 (USER_STREAM)**


> **响应:**

```javascript
{}
```

``
PUT /fapi/v1/listenKey
``

有效期延长至本次调用后60分钟

**权重:**
1

**参数:**

None



## **关闭listenKey (USER_STREAM)**

> **响应:**

```javascript
{}
```

``
DELETE /fapi/v1/listenKey
``

关闭某账户数据流

**权重:**
1

**参数:**

None



## **listenKey 过期推送**

> **Payload:**

```javascript
{
	'e': 'listenKeyExpired',      // 事件类型
	'E': 1576653824250				// 事件时间
}
```

当前连接使用的有效listenKey过期时，user data stream 将会推送此事件。

**注意:**

* 此事件与websocket连接中断没有必然联系
* 只有正在连接中的有效`listenKey`过期时才会收到此消息
* 收到此消息后user data stream将不再更新，直到用户使用新的有效的`listenKey`




## **追加保证金通知**

> **Payload:**

```javascript
{
    "e":"MARGIN_CALL",    	// 事件类型
    "E":1587727187525,		// 事件时间
    "cw":"3.16812045",		// 除去逐仓仓位保证金的钱包余额, 仅在全仓 margin call 情况下推送此字段
    "p":[					// 涉及持仓
      {
        "s":"ETHUSDT",		// symbol
        "ps":"LONG",		// 持仓方向
        "pa":"1.327",		// 仓位
        "mt":"CROSSED",		// 保证金模式
        "iw":"0",			// 若为逐仓，仓位保证金
        "mp":"187.17127",	// 标记价格
        "up":"-1.166074",	// 未实现盈亏
        "mm":"1.614445"		// 持仓需要的维持保证金
      }
    ]
}  
 
```


* 当用户持仓风险过高，会推送此消息。
* 此消息仅作为风险指导信息，不建议用于投资策略。
* 在大波动市场行情下,不排除此消息发出的同时用户仓位已被强平的可能。




## **Balance和Position更新推送**

> **Payload:**

```javascript
{
  "e": "ACCOUNT_UPDATE",				// 事件类型
  "E": 1564745798939,            		// 事件时间
  "T": 1564745798938 ,           		// 撮合时间
  "a":                          		// 账户更新事件
    {
      "m":"ORDER",						// 事件推出原因 
      "B":[                     		// 余额信息
        {
          "a":"USDT",           		// 资产名称
          "wb":"122624.12345678",    	// 钱包余额
          "cw":"100.12345678",			// 除去逐仓仓位保证金的钱包余额
          "bc":"50.12345678"			// 除去盈亏与交易手续费以外的钱包余额改变量
        },
        {
          "a":"BUSD",           
          "wb":"1.00000000",
          "cw":"0.00000000",         
          "bc":"-49.12345678"
        }
      ],
      "P":[
       {
          "s":"BTCUSDT",          	// 交易对
          "pa":"0",               	// 仓位
          "ep":"0.00000",            // 入仓价格
          "cr":"200",             	// (费前)累计实现损益
          "up":"0",						// 持仓未实现盈亏
          "mt":"isolated",				// 保证金模式
          "iw":"0.00000000",			// 若为逐仓，仓位保证金
          "ps":"BOTH"					// 持仓方向
       }，
       {
        	"s":"BTCUSDT",
        	"pa":"20",
        	"ep":"6563.66500",
        	"cr":"0",
        	"up":"2850.21200",
        	"mt":"isolated",
        	"iw":"13200.70726908",
        	"ps":"LONG"
      	 },
       {
        	"s":"BTCUSDT",
        	"pa":"-10",
        	"ep":"6563.86000",
        	"cr":"-45.04000000",
        	"up":"-1423.15600",
        	"mt":"isolated",
        	"iw":"6570.42511771",
        	"ps":"SHORT"
       }
      ]
    }
}
```

账户更新事件的 event type 固定为 `ACCOUNT_UPDATE`

* 当账户信息有变动时，会推送此事件：
	* 仅当账户信息有变动时(包括资金、仓位、保证金模式等发生变化)，才会推送此事件；
	* 订单状态变化没有引起账户和持仓变化的，不会推送此事件；
	* 每次因持仓变动推送的position 信息，仅包含当前持仓不为0或逐仓仓位保证金不为0的symbol position。

* "FUNDING FEE" 引起的资金余额变化，仅推送简略事件：
	* 当用户某**全仓**持仓发生"FUNDING FEE"时，事件`ACCOUNT_UPDATE`将只会推送相关的用户资产余额信息`B`(仅推送FUNDING FEE 发生相关的资产余额信息)，而不会推送任何持仓信息`P`。
	* 当用户某**逐仓**仓持仓发生"FUNDING FEE"时，事件`ACCOUNT_UPDATE`将只会推送相关的用户资产余额信息`B`(仅推送"FUNDING FEE"所使用的资产余额信息)，和相关的持仓信息`P`(仅推送这笔"FUNDING FEE"发生所在的持仓信息)，其余持仓信息不会被推送。

* 字段"m"代表了事件推出的原因，包含了以下可能类型:
	* DEPOSIT
	* WITHDRAW
	* ORDER
	* FUNDING_FEE
	* WITHDRAW_REJECT
	* ADJUSTMENT
	* INSURANCE_CLEAR
	* ADMIN_DEPOSIT
	* ADMIN_WITHDRAW
	* MARGIN_TRANSFER
	* MARGIN_TYPE_CHANGE
	* ASSET_TRANSFER
	* OPTIONS_PREMIUM_FEE
	* OPTIONS_SETTLE_PROFIT
	* AUTO_EXCHANGE

* 字段"bc"代表了钱包余额的改变量，即 balance change，但注意其不包含仓位盈亏及交易手续费。

## **订单/交易 更新推送**

> **Payload:**

```javascript
{
  
  "e":"ORDER_TRADE_UPDATE",			// 事件类型
  "E":1568879465651,				// 事件时间
  "T":1568879465650,				// 撮合时间
  "o":{								
    "s":"BTCUSDT",					// 交易对
    "c":"TEST",						// 客户端自定订单ID
      // 特殊的自定义订单ID:
      // "autoclose-"开头的字符串: 系统强平订单
      // "adl_autoclose": ADL自动减仓订单
    "S":"SELL",						// 订单方向
    "o":"TRAILING_STOP_MARKET",	// 订单类型
    "f":"GTC",						// 有效方式
    "q":"0.001",					// 订单原始数量
    "p":"0",						// 订单原始价格
    "ap":"0",						// 订单平均价格
    "sp":"7103.04",					// 条件订单触发价格，对追踪止损单无效
    "x":"NEW",						// 本次事件的具体执行类型
    "X":"NEW",						// 订单的当前状态
    "i":8886774,					// 订单ID
    "l":"0",						// 订单末次成交量
    "z":"0",						// 订单累计已成交量
    "L":"0",						// 订单末次成交价格
    "N": "USDT",                 	// 手续费资产类型
    "n": "0",                    	// 手续费数量
    "T":1568879465651,				// 成交时间
    "t":0,							// 成交ID
    "b":"0",						// 买单净值
    "a":"9.91",						// 卖单净值
    "m": false,					    // 该成交是作为挂单成交吗？
    "R":false	,				    // 是否是只减仓单
    "wt": "CONTRACT_PRICE",	        // 触发价类型
    "ot": "TRAILING_STOP_MARKET",	// 原始订单类型
    "ps":"LONG"						// 持仓方向
    "cp":false,						// 是否为触发平仓单; 仅在条件订单情况下会推送此字段
    "AP":"7476.89",					// 追踪止损激活价格, 仅在追踪止损单时会推送此字段
    "cr":"5.0",						// 追踪止损回调比例, 仅在追踪止损单时会推送此字段
    "rp":"0"							// 该交易实现盈亏
    
  }
  
}
```


当有新订单创建、订单有新成交或者新的状态变化时会推送此类事件
事件类型统一为 `ORDER_TRADE_UPDATE`

**订单方向**

* BUY 买入
* SELL 卖出

**订单类型**

* MARKET  市价单
* LIMIT	限价单
* STOP		止损单
* TAKE_PROFIT 止盈单
* LIQUIDATION 强平单

**本次事件的具体执行类型**

* NEW
* CANCELED		已撤
* CALCULATED		
* EXPIRED			订单失效
* TRADE			交易
	

**订单状态**

* NEW
* PARTIALLY_FILLED    
* FILLED
* CANCELED
* EXPIRED
* NEW_INSURANCE		风险保障基金(强平)
* NEW_ADL				自动减仓序列(强平)

**有效方式:**

* GTC 
* IOC
* FOK
* GTX
* HIDDEN


## **杠杆倍数等账户配置 更新推送**

> **Payload:**

```javascript
{
    "e":"ACCOUNT_CONFIG_UPDATE",       // 事件类型
    "E":1611646737479,		           // 事件时间
    "T":1611646737476,		           // 撮合时间
    "ac":{								
    "s":"BTCUSDT",					   // 交易对
    "l":25						       // 杠杆倍数
     
    }
}  
 
```

> **Or**

```javascript
{
    "e":"ACCOUNT_CONFIG_UPDATE",       // 事件类型
    "E":1611646737479,		           // 事件时间
    "T":1611646737476,		           // 撮合时间
    "ai":{							   // 用户账户配置
    "j":true,						   // 联合保证金状态
    "f":true,                          // 指定币种抵扣手续费
    "d":true                           // 持仓模式 true 为双向持仓模式， false为单向持仓模式
    }
}  
```

当账户配置发生变化时会推送此类事件类型统一为`ACCOUNT_CONFIG_UPDATE `

当交易对杠杆倍数发生变化时推送消息体会包含对象`ac`表示交易对账户配置，其中`s`代表具体的交易对，`l`代表杠杆倍数

当用户联合保证金状态发生变化时推送消息体会包含对象`ai`表示用户账户配置，其中`j`代表用户联合保证金状态


## **Event: TradePro**

> **Topic Subscribe:**

```javascript
{
  "method": "SUBSCRIBE",
  "params": [
    "btcusdt@tradepro"
  ],
  "id": 3
}
```

> **Payload:**

```javascript
{
    "stream": "btcusdt@tradepro",
    "data": {
        "e": "tradepro",
        "E": 1773751963081,
        "T": 1773751963079,
        "s": "BTCUSDT",
        "t": 128884613,
        "p": "73685.5",
        "q": "0.297",
        "h": "0X0000000000000000000000000000000000000000000000000000000000000000",
        "m": [
            "hidden",
            "hidden"
        ]
    }
}
```

* h: 该笔交易的交易哈希（Transaction hash）

* m: 包含交易参与方地址的数组：

    * m[0]: Taker 地址（主动成交方）

    * m[1]: Maker 地址（挂单方）

