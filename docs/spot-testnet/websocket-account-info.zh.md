## **Websocket 账户推送**

* 本篇所列出API接口的base url : **https://sapi.asterdex-testnet.com**
* 用于订阅账户数据的 `listenKey` 从创建时刻起有效期为60分钟
* 可以通过 `PUT` 一个 `listenKey` 延长60分钟有效期
* 可以通过`DELETE`一个 `listenKey` 立即关闭当前数据流，并使该`listenKey` 无效
* 在具有有效`listenKey`的帐户上执行`POST`将返回当前有效的`listenKey`并将其有效期延长60分钟
* websocket接口的baseurl: **wss://sstream.asterdex.com**
* U订阅账户数据流的stream名称为 **/ws/\<listenKey\>**
* 每个链接有效期不超过24小时，请妥善处理断线重连。


## Listen Key(现货账户)

### 生成 Listen Key (USER_STREAM)

> **响应**
```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

``
POST /api/v3/listenKey
``

开始一个新的数据流。除非发送 keepalive，否则数据流于60分钟后关闭。如果该帐户具有有效的`listenKey`，则将返回该`listenKey`并将其有效期延长60分钟。

**权重:**
1

**参数:**
NONE

### 延长 Listen Key 有效期 (USER_STREAM)

> **响应**
```javascript
{}
```

``
PUT /api/v3/listenKey 
``

有效期延长至本次调用后60分钟,建议每30分钟发送一个 ping 。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES


### 关闭 Listen Key (USER_STREAM)

> **响应**
```javascript
{}
```

``
DELETE /api/v3/listenKey
``

关闭用户数据流。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
listenKey | STRING | YES


## Payload: 账户更新

每当帐户余额发生更改时，都会发送一个事件`outboundAccountPosition`，其中包含可能由生成余额变动的事件而变动的资产。

> **Payload**
```javascript
{
  "B":[// 余额
    {
      "a":"SLP25",   // 资产名称
      "f":"10282.42029415",   // 可用余额
      "l":"653.00000001"   // 冻结余额
    },
    {
      "a":"ADA25",
      "f":"9916.96229880",
      "l":"34.00510000"
    }
  ],
  "e":"outboundAccountPosition",   // 事件类型
  "T":1649926447190,     // 账户末次更新时间戳 
  "E":1649926447205   // 事件时间
  "m":"WITHDRAW" // 事件推出原因
}
```


## Payload: 订单更新

订单通过`executionReport`事件进行更新。 

> **Payload**

```javascript
{
  "s":"ADA25SLP25",   // 交易对
  "c":"Xzh0gnxT41PStbwqOtXnjD",  // 客户端自定订单ID
  "S":"SELL",   // 订单方向
  "o":"LIMIT",   // 订单类型
  "f":"GTC",   // 有效方式
  "q":"10.001000",   // 订单原始数量
  "p":"19.1000000000",   // 订单原始价格
  "ap":"19.0999999955550656",  //平均价格
  "P":"0",  // 条件订单触发价格
  "x":"TRADE",   // 本次事件的具体执行类型
  "X":"PARTIALLY_FILLED",   // 订单的当前状态
  "i":27,   // 订单ID
  "l":"1",      // 订单末次成交量 
  "z":"8.999000",   // 订单累计已成交量
  "L":"19.1000000000",   // 订单末次成交价格
  "n":"0.00382000",    // 手续费数量    
  "N":"SLP25",   // 手续费资产类型
  "T":1649926447190,   // 成交时间
  "t":18,   // 成交ID
  "m":true,   // 该成交是作为挂单成交吗？
  "ot":"LIMIT",  //初始订单类型
  "O":0,   // 订单时间
  "Z":"171.88089996",   // 累计报价资产交易数量
  "Y":"19.1000000000000000",   // 最近报价交易数量
  "Q":"0",   // 报价数量
  "e":"executionReport",   // 事件类型
  "E":1649926447209  // 事件时间
}  
```


**执行类型:**
* NEW 新订单
* CANCELED 订单被取消
* REJECTED 新订单被拒绝
* TRADE 订单有新成交
* EXPIRED 订单失效(根据订单的Time In Force参数)



## Event: TradePro

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
