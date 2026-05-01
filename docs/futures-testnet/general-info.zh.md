## 接口基本信息

* 本接口可能需要用户的 AGENT。
* 如需创建 AGENT，请[点击此处](https://www.asterdex-testnet.com/en/api-wallet)，并在顶部切换至 `Pro API`
* 接口 baseurl：**https://fapi.asterdex-testnet.com**
* 所有接口的响应都是 JSON 格式。
* 响应中如有数组，数组元素以时间升序排列，越早的数据越提前。
* 所有时间、时间戳均为 UNIX 时间，单位为毫秒。
* 所有数据类型采用 JAVA 的数据类型定义。

### HTTP 返回代码

* HTTP `4XX` 错误码用于指示错误的请求内容、行为、格式。
* HTTP `403` 错误码表示违反 WAF 限制（Web 应用程序防火墙）。
* HTTP `429` 错误码表示警告访问频次超限，即将被封 IP。
* HTTP `418` 表示收到 429 后继续访问，于是被封了。
* HTTP `5XX` 错误码用于指示 Aster Finance 服务侧的问题。
* HTTP `503` 表示 API 服务端已经向业务核心提交了请求但未能获取响应，特别需要注意的是其不代表请求失败，而是未知。很可能已经得到了执行，也有可能执行失败，需要做进一步确认。

### 接口错误代码

* 每个接口都有可能抛出异常

> ***异常响应格式如下：***

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* 具体的错误码及其解释在**错误代码**页面。

### 接口的基本信息

* `GET` 方法的接口，参数必须在 `query string` 中发送。
* `POST`、`PUT` 和 `DELETE` 方法的接口，在 `request body` 中发送（content type `application/x-www-form-urlencoded`）。
* 对参数的顺序不做要求。
### V3 Nonce 机制

* **Nonce** 用于校验请求的**有效性、唯一性和防重放**。客户端应使用**当前时间戳（微秒级）**作为 nonce，且与服务端时间误差不超过 **10 秒**。

* 请求处理流程：

  1. 若 nonce **已使用过** → 判定为**重复请求并拒绝**
  2. 否则判断是否**过旧**

* 为提升性能，每个用户仅维护**最近 100 个 nonce**：

  * 若已满且新 nonce **小于当前最小值** → 判定为**过期并拒绝**
  * 否则**移除最旧的 nonce**，加入新的

* 总体效果：**防重复、防过期，仅保留近期有效请求**

## 访问限制

* `/fapi/v3/exchangeInfo` 接口中 `rateLimits` 数组里包含有 REST 接口的访问限制，包括带权重的访问频次限制、下单速率限制。本篇`枚举定义`章节有限制类型的进一步说明。
* 违反上述任何一个访问限制都会收到 HTTP 429，这是一个警告。

<aside class="notice">
请注意，若用户被认定利用频繁挂撤单且故意低效交易意图发起攻击行为，Aster Finance 有权视具体情况进一步加强对其访问限制。
</aside>

### IP 访问限制

* 每个请求将包含一个 `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` 的响应头，其中包含当前 IP 所有请求的已使用权重。
* 每个路由都有一个"权重"，该权重确定每个接口计数的请求数。较重的接口和对多个交易对进行操作的接口将具有较重的"权重"。
* 收到 429 时，您有责任停止发送请求，不得持续请求 API。
* **重复违反访问限制或收到 429 后未停止请求，将导致 IP 被自动封禁（HTTP 418）。**
* IP 封禁时间会随违规次数累计增长，**最短 2 分钟，最长 3 天**。
* **访问限制基于 IP，而非 API Key。**

<aside class="notice">
强烈建议尽可能使用 WebSocket 数据流获取数据，既能保证消息的实时性，又能减少请求带来的访问限制压力。
</aside>

### 下单频率限制

* 每次下单响应将包含 `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` 响应头，其中包含该账户当前已使用的下单频率限制数量。
* 被拒绝或未成功的订单不保证包含上述响应头。
* **下单频率限制基于账户计算。**

**严肃的交易关乎时效性。** 网络可能不稳定，导致请求到达服务器所需的时间有所波动。通过使用 `recvWindow`，您可以指定请求必须在一定毫秒数内被处理，否则将被服务器拒绝。

<aside class="notice">
建议使用 5000 毫秒以内的 recvWindow！
</aside>

## 接口鉴权类型

* 每个接口都有自己的鉴权类型，决定了访问时应当进行何种鉴权。
* 如果需要使用API_WALLET鉴权，应当在请求体中添加signer
* 如果需要使用主地址鉴权，应当在请求体中添加user

| 鉴权类型 | 描述 |
| ------------- | ----------------------------------------- |
| NONE | 不需要鉴权的接口 |
| TRADE | 需要有效的 signer 和 signature |
| USER_DATA | 需要有效的 signer 和 signature |
| USER_STREAM | 需要有效的 signer 和 signature |
| MARKET_DATA | 不需要鉴权的接口 |

## 鉴权签名载荷

| 参数 | 描述 |
| --------- | ---------------------------------- |
| user | 主账户钱包地址 |
| signer | API 钱包地址 |
| nonce | 当前时间戳，单位为微秒 |
| signature | 签名 |

## 需要签名的接口

* 鉴权类型：TRADE、USER_DATA、USER_STREAM

## POST /fapi/v3/order 的示例

#### 所有参数均通过 request body 发送（Python 3.9.6）

#### 示例：以下参数为 API 注册信息，user、signer、privateKey 仅供示范（privateKey 为 signer 的私钥）

| Key | Value | 描述 |
| ---------- | ------------------------------------------------------------------ | ---------- |
| user | 0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e | 登录钱包地址 |
| signer | 0x21cF8Ae13Bb72632562c6Fff438652Ba1a151bb0 | [点击此处](https://www.asterdex-testnet.com/en/api-wallet) |
| privateKey | 0x4fd0a42218f3eae43a6ce26d22544e986139a01e5b34a62db53757ffca81bae1 | [点击此处](https://www.asterdex-testnet.com/en/api-wallet) |

#### 示例：nonce 参数为当前系统微秒值，超过系统时间或落后系统时间超过 10 秒为非法请求。

```python
#python
nonce = math.trunc(time.time()*1000000)
print(nonce)
#1748310859508867
```

```java
//java
Instant now = Instant.now();
long microsecond = now.getEpochSecond() * 1000000 + now.getNano() / 1000;
```

#### 示例：以下为业务请求参数

```python
import json
import time
import urllib
import threading

import requests
from eth_account.messages import  encode_structured_data
from eth_account import Account

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
    "chainId": 714,
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
host = 'https://fapi.asterdex-testnet.com'

# 在此配置您的用户和代理信息
user = '*'
signer = '*'
private_key = '*'

place_order = {"url":"/fapi/v3/order","method":"POST","params":{"symbol": "ASTERUSDT", "type": "LIMIT", "side": "BUY",
                  "timeInForce": "GTC", "quantity": "20", "price": "0.5"}}
batch_orders = {"url":"/fapi/v3/batchOrders","method":"POST","params":{
          "batchOrders":"[{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'},{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'}]" }}
listen_key = {"url":"/fapi/v3/listenKey","method":"POST","params":{}}

_last_ms = 0
_i = 0
_nonce_lock = threading.Lock()

def get_nonce():
    global _last_ms, _i
    with _nonce_lock:
        now_ms = int(time.time())

        if now_ms == _last_ms:
            _i += 1
        else:
            _last_ms = now_ms
            _i = 0

        return now_ms * 1_000_000 + _i

def send_by_url(api) :
    my_dict = api['params']
    url = host + api['url']

    my_dict['nonce'] = str(get_nonce())
    my_dict['signer'] = signer

    param = urllib.parse.urlencode(my_dict)

    print(param)
    typed_data['message']['msg'] = param
    message = encode_structured_data(typed_data)
    signed = Account.sign_message(message, private_key=private_key)

    url = url + '?' + param + '&signature=' + signed.signature.hex()
    print(url)
    res = requests.post(url, headers=headers)
    print(res.text)

def send_by_body(api) :
       my_dict = api['params']
       url = host +api['url']
       my_dict['nonce'] = str(get_nonce())
       my_dict['signer'] = signer

       param = urllib.parse.urlencode(my_dict)
       typed_data['message']['msg'] = param
       message = encode_structured_data(typed_data)

       signed = Account.sign_message(message, private_key=private_key)
       print(signed.signature.hex())

       my_dict['signature'] = signed.signature.hex()

       print(my_dict)
       res = requests.post(url, data=my_dict, headers=headers)
       # print(res.headers)
       print(res.text)

if __name__ == '__main__':
    send_by_url(place_order)
    # send_by_url(listen_key)
    # send_by_url(batch_orders)
    # send_by_body(place_order)
    # send_by_body(batch_orders)

```


