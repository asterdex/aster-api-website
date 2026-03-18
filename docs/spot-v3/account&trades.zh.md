## 下单  (TRADE)

> **Response ACK:**

```javascript
{
  "symbol": "BTCUSDT", // 交易对
  "orderId": 28, // 系统的订单ID
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP", // 客户自己设置的ID
  "updateTime": 1507725176595, // 交易的时间戳
  "price": "0.00000000", // 订单价格
  "avgPrice": "0.0000000000000000", //平均价格
  "origQty": "10.00000000", // 用户设置的原始订单数量
  "cumQty": "0",            //累计数量
  "executedQty": "10.00000000", // 交易的订单数量
  "cumQuote": "10.00000000", // 累计交易的金额
  "status": "FILLED", // 订单状态
  "timeInForce": "GTC", // 订单的时效方式
  "stopPrice": "0",     //触发价格
  "origType": "LIMIT",  //触发前订单类型
  "type": "LIMIT", // 订单类型， 比如市价单，现价单等
  "side": "SELL", // 订单方向，买还是卖
}
```

``
POST /api/v3/order  
``

发送下单。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
side | ENUM | YES | 详见枚举定义：订单方向
type | ENUM | YES | 详见枚举定义：订单类型 
timeInForce | ENUM | NO | 详见枚举定义：有效方式 
quantity | DECIMAL | NO |
quoteOrderQty|DECIMAL|NO|
price | DECIMAL | NO |
newClientOrderId | STRING | NO | 客户自定义的唯一订单ID。 如果未发送，则自动生成
stopPrice | DECIMAL | NO | 仅 `STOP`, `STOP_MARKET` , `TAKE_PROFIT`,`TAKE_PROFIT_MARKET` 需要此参数。

基于订单 `type`不同，强制要求某些参数:

类型 | 强制要求的参数
------------ | ------------
`LIMIT` | `timeInForce`, `quantity`, `price`
`MARKET` | `quantity` 或 `quoteOrderQty` 
`STOP`和`TAKE_PROFIT` | `quantity`,  `price`, `stopPrice`
`STOP_MARKET`和`TAKE_PROFIT_MARKET` | `quantity`, `stopPrice`

其他信息:

* 下`MARKET` `SELL`市价单，用户通过`QUANTITY`控制想用市价单卖出的基础资产数量。
  * 比如在`BTCUSDT`交易对上下一个`MARKET` `SELL`市价单, 通过`QUANTITY`让用户指明想卖出多少BTC。
* 下`MARKET` `BUY`的市价单，用户使用 `quoteOrderQty` 控制想用市价单买入的报价资产数量，`QUANTITY`将由系统根据市场流动性计算出来。
  * 比如在`BTCUSDT`交易对上下一个`MARKET` `BUY`市价单, 通过`quoteOrderQty`让用户选择想使用多少USDT买入BTC。
* 使用 `quoteOrderQty` 的市价单`MARKET`不会突破`LOT_SIZE`的限制规则; 报单会按给定的`quoteOrderQty`尽可能接近地被执行。
* 除非之前的订单已经成交, 不然设置了相同的`newClientOrderId`订单会被拒绝。



## 撤销订单 (TRADE)

> **响应**

```javascript
{
  "symbol": "BTCUSDT", // 交易对
  "orderId": 28, // 系统的订单ID
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP", // 客户自己设置的ID
  "updateTime": 1507725176595, // 交易的时间戳
  "price": "0.00000000", // 订单价格
  "avgPrice": "0.0000000000000000", //平均价格
  "origQty": "10.00000000", // 用户设置的原始订单数量
  "cumQty": "0",            //累计数量
  "executedQty": "10.00000000", // 交易的订单数量
  "cumQuote": "10.00000000", // 累计交易的金额
  "status": "CANCELED", // 订单状态
  "timeInForce": "GTC", // 订单的时效方式
  "stopPrice": "0",     //触发价格
  "origType": "LIMIT",  //触发前订单类型
  "type": "LIMIT", // 订单类型， 比如市价单，现价单等
  "side": "SELL", // 订单方向，买还是卖
}
```

``
DELETE /api/v3/order  
``

取消有效订单。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |

`orderId` 或 `origClientOrderId` 必须至少发送一个

## 查询订单 (USER_DATA)

> **响应**
```javascript
{
    "orderId": 38,   // 系统订单号
    "symbol": "ADA25SLP25",  // 交易对
    "status": "FILLED",  // 订单状态
    "clientOrderId": "afMd4GBQyHkHpGWdiy34Li",  // 用户自定义的订单号
    "price": "20",  // 委托价格
    "avgPrice": "12.0000000000000000",  // 平均成交价
    "origQty": "10",  // 原始委托数量
    "executedQty": "10",  // 成交量
    "cumQuote": "120",  // 成交金额
    "timeInForce": "GTC",  // 有效方法
    "type": "LIMIT",  // 订单类型
    "side": "BUY",  // 买卖方向
    "stopPrice": "0",  // 触发价
    "origType": "LIMIT",  // 触发前订单类型
    "time": 1649913186270,  // 订单时间
    "updateTime": 1649913186297  // 更新时间
}
```

``
GET /api/v3/order 
``

查询订单状态。

* 请注意，如果订单满足如下条件，不会被查询到：
	* 订单的最终状态为 `CANCELED` 或者 `EXPIRED`, **并且** 
	* 订单没有任何的成交记录, **并且**
	* 订单生成时间 + 7天 < 当前时间

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |

注意:

* 至少需要发送 `orderId` 与 `origClientOrderId`中的一个


## 查询当前挂单 (USER_DATA)

> **响应**
```javascript
{
    "orderId": 38,   // 系统订单号
    "symbol": "ADA25SLP25",  // 交易对
    "status": "NEW",  // 订单状态
    "clientOrderId": "afMd4GBQyHkHpGWdiy34Li",  // 用户自定义的订单号
    "price": "20",  // 委托价格
    "avgPrice": "12.0000000000000000",  // 平均成交价
    "origQty": "10",  // 原始委托数量
    "executedQty": "10",  // 成交量
    "cumQuote": "120",  // 成交金额
    "timeInForce": "GTC",  // 有效方法
    "type": "LIMIT",  // 订单类型
    "side": "BUY",  // 买卖方向
    "stopPrice": "0",  // 触发价
    "origType": "LIMIT",  // 触发前订单类型
    "time": 1649913186270,  // 订单时间
    "updateTime": 1649913186297  // 更新时间
}
```

``
GET /api/v3/openOrder 
``

查询订单状态。

**权重:**
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
origClientOrderId | STRING | NO |

注意:

* 至少需要发送 `orderId` 与 `origClientOrderId`中的一个


## 当前所有挂单 (USER_DATA)

> **响应**

```javascript
[
    {
        "orderId": 349661, // 系统订单号
        "symbol": "BNBUSDT", // 交易对
        "status": "NEW", // 订单状态
        "clientOrderId": "LzypgiMwkf3TQ8wwvLo8RA", // 用户自定义的订单号
        "price": "1.10000000", // 委托价格
        "avgPrice": "0.0000000000000000", // 平均成交价
        "origQty": "5",  // 原始委托数量
        "executedQty": "0", // 成交量
        "cumQuote": "0", // 成交金额
        "timeInForce": "GTC", // 有效方法
        "type": "LIMIT", // 订单类型
        "side": "BUY",   // 买卖方向
        "stopPrice": "0", // 触发价
        "origType": "LIMIT", // 触发前订单类型
        "time": 1756252940207, // 订单时间
        "updateTime": 1756252940207, // 更新时间
    }
]
```

``
GET /api/v3/openOrders  
``

获取交易对的所有当前挂单， 请小心使用不带交易对参数的调用。

**权重:**
- 带symbol ***1***
- 不带 ***40***  

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* 不带symbol参数，会返回所有交易对的挂单



## 取消当前所有挂单 (USER_DATA)

> **响应**

```javascript
{
    "code": 200,
    "msg": "The operation of cancel all open order is done."
}
```

``
DEL /api/v3/allOpenOrders  
``

**权重:**
- ***1***

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderIdList | STRING | NO | id数组字符串
origClientOrderIdList | STRING | NO | clientOrderId数组字符串


## 查询所有订单 (USER_DATA)
> **响应**
```javascript
[
    {
        "orderId": 349661, // 系统订单号
        "symbol": "BNBUSDT", // 交易对
        "status": "NEW", // 订单状态
        "clientOrderId": "LzypgiMwkf3TQ8wwvLo8RA", // 用户自定义的订单号
        "price": "1.10000000", // 委托价格
        "avgPrice": "0.0000000000000000", // 平均成交价
        "origQty": "5",  // 原始委托数量
        "executedQty": "0", // 成交量
        "cumQuote": "0", // 成交金额
        "timeInForce": "GTC", // 有效方法
        "type": "LIMIT", // 订单类型
        "side": "BUY",   // 买卖方向
        "stopPrice": "0", // 触发价
        "origType": "LIMIT", // 触发前订单类型
        "time": 1756252940207, // 订单时间
        "updateTime": 1756252940207, // 更新时间
    }
]
```

``
GET /api/v3/allOrders 
``

获取所有帐户订单； 有效，已取消或已完成。

* 请注意，如果订单满足如下条件，不会被查询到：
	* 订单生成时间 + 7天 < 当前时间

**权重:** 
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
orderId | LONG | NO |
startTime | LONG | NO |
endTime | LONG | NO |
limit | INT | NO | 默认 500; 最大 1000.

* 查询时间范围最大不得超过7天
* 默认查询最近7天内的数据 



``
GET /api/v3/transactionHistory 
``
> **响应**

```javascript
[
   {
    "tranId": 1759115482304540227, //划转ID
    "tradeId": null,               //流水ID 
    "asset": "ASTER",              //资产
    "symbol": "",                  //交易对
    "balanceDelta": "-500.00000000", //资金流数量，正数代表流入，负数代表流出
    "balanceInfo": "TRADE_SOURCE",   //流水描述 
    "time": 1759115482000,       // 时间
    "type": "TRADE_SOURCE"      //资金流类型
   }
]
```

查询交易流水

**权重:**
30

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
asset | STRING | NO | 资产
type | STRING | NO |  类别
startTime | LONG | NO | 开始时间
endTime | LONG | NO |  结束时间
limit | LONG | NO | 返回的结果集数量 默认值:100 最大值:1000

注意:

*  `type` 取值 `TRADE_TARGET`,`TRADE_SOURCE`,`TRANSFER_SPOT_TO_FUTURE`,`TRANSFER_FUTURE_TO_SPOT`,`TRANSFER_SPOT_TO_SPOT`,`AIRDROP`,`DIVIDEND`,`TRANSFER_REFUND`,`INTERNAL_TRANSFER`,`TRANSFER`,`SWAP`,`COMMISSION_REBATE`,`CASH_BACK` 中的一种
*  如果`startTime` 和 `endTime` 均未发送, 只会返回最近7天的数据。

## 期货现货互转 (TRADE)

> **响应:**

```javascript
{
    "tranId": 21841, //交易id
    "status": "SUCCESS" //状态
}
```

``
POST /api/v3/asset/wallet/transfer  
``

**权重:**
5

**参数:**


名称              |  类型   | 是否必需   | 描述
---------------- | ------- | -------- | ----
amount |	DECIMAL | 	YES |	数量
asset |	STRING | 	YES |	资产
clientTranId |	STRING | 	YES |	交易id 
kindType |	STRING | 	YES |	交易类型

注意:
* kindType 取值为FUTURE_SPOT(期货转现货),SPOT_FUTURE(现货转期货)

## 现货提现手续费 (NONe)
> **响应**
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

**权重:** 
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
chainId | STRING | YES | 
asset | STRING | YES |

注意:
* chainId: 1(ETH),56(BSC),42161(Arbi)
* gasCost: 提现所需的最少数量的手续费

## 现货提现 (USER_DATA)
> **响应**
```javascript
{
  "withdrawId": "1014729574755487744",
  "hash":"0xa6d1e617a3f69211df276fdd8097ac8f12b6ad9c7a49ba75bbb24f002df0ebb"
}
```

``
POST /api/v3/aster/user-withdraw 
``

**权重:** 
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
chainId | STRING | YES | 
asset | STRING | YES |
amount | STRING | YES |
fee | STRING | YES |
receiver | STRING | YES | 
nonce | STRING | YES |  当前时间的微秒值 
userSignature | STRING | YES | 

注意:
* chainId: 1(ETH),56(BSC),42161(Arbi)
* receiver : 当前账户的地址
* 如果期货余额不足，会从spot账户划转到期货账户，进行提现
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

## 获取创建apikey nonce (NONE)
> **响应**
```javascript

111111

```

``
POST /api/v3/getNonce 
``

**权重:** 
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
address | STRING | YES |
userOperationType | STRING | YES | CREATE_API_KEY
network | STRING | NO | 

注意:
* userOperationType 仅可取值: CREATE_API_KEY
* network: sol网络必须传入SOL,其他忽略
* 限流单IP一分钟60次


## 创建apikey (NONE)
> **响应**
```javascript
{
    "apiKey": "bb3b24d0a3dec88cb06be58a257e4575cb0b1bb256ad6fd90ae8fd0ee1d102ae",
    "apiSecret": "9fe8f5642ae1961674ea0cb7f957fa99dc8e0421b607c985a963ad2ced90ae1c"
}
```

``
POST /api/v3/createApiKey
``

**权重:** 
1

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
address | STRING | YES |
userOperationType | STRING | YES | CREATE_API_KEY
network | STRING | NO | 
userSignature | STRING | YES | 
apikeyIP | STRING | NO | 
desc | STRING | YES | 

注意:
* userOperationType 仅可取值: CREATE_API_KEY
* network: sol网络必须传入SOL,其他忽略
* desc: 同一账户不能重复，长度不能超过20个字符
* apikeyIP ip数组以,分隔
* 限流单IP一分钟60次
* userSignature evm demo

```shell

const nonce = 111111
const message = 'You are signing into Astherus ${nonce}' ;

const signature = await signer.signMessage(message);
```


## 账户信息 (USER_DATA)
> **响应**
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

``
GET /api/v3/account 
``

获取当前账户信息。

**权重:**
5

**参数:**

名称 | 类型 | 是否必需| 描述
------------ | ------------ | ------------ | ------------


## 账户成交历史 (USER_DATA)
> **响应**
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

``
GET /api/v3/userTrades  
``

获取账户指定交易对的成交历史

**权重:**
5

**参数:**

名称 | 类型 | 是否必需 | 描述
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |
orderId|LONG|NO| 必须要和参数`symbol`一起使用.
startTime | LONG | NO |
endTime | LONG | NO |
fromId | LONG | NO | 起始Trade id。 默认获取最新交易。
limit | INT | NO | 默认 500; 最大 1000.

* 如果`startTime` 和 `endTime` 均未发送, 只会返回最近7天的数据。
* startTime 和 endTime 的最大间隔为7天
* 不能同时传`fromId`与`startTime` 或 `endTime`
       




---
