## **Spot API Overview**

* This document lists the base URL for the API endpoints: [**https://sapi.asterdex-testnet.com**](https://sapi.asterdex-testnet.com)  
* All API responses are in JSON format.  

### Attention
* TESTUSDT or any other symbols starting with TEST are symbols used for Aster’s INTERNAL TESTING ONLY. Please DO NOT trade on these symbols starting with TEST. Aster does not hold any accountability for loss of funds due to trading on these symbols. However, if you run into issues, you may contact support about this any time, we will try to help you recover your funds.

### V3 Nonce Mechanism

* **Nonce** is used to validate the **validity, uniqueness, and replay-protection** of requests. Clients should use the **current timestamp (microsecond precision)** as the nonce, and the difference from server time must not exceed **10 seconds**.

* Request processing flow:

  1. If the nonce **has already been used** → rejected as a **duplicate request**
  2. Otherwise, the system checks whether it is **too old**

* To improve performance, each user maintains only the **most recent 100 nonces**:

  * If the list is full and the new nonce is **smaller than the current minimum** → rejected as **expired**
  * Otherwise, the **oldest nonce is removed** and the new one is added

* Overall effect: **prevents duplicates and stale requests, retaining only recent valid requests**

### HTTP return codes

* HTTP `4XX` status codes are used to indicate errors in the request content, behavior, or format. The problem lies with the requester.  
* HTTP `403` status code indicates a violation of WAF restrictions (Web Application Firewall).  
* HTTP `429` error code indicates a warning that the access frequency limit has been exceeded and the IP is about to be blocked.  
* HTTP `418` indicates that after receiving a 429 you continued to access, so the IP has been blocked.  
* HTTP `5XX` error codes are used to indicate issues on the Aster service side.

### API error codes

* When using the endpoint `/api/v3`, any endpoint may throw exceptions;

The API error codes are returned in the following format:

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

### Basic information about the endpoint

* Endpoints with the `GET` method must send parameters in the `query string`.  
* For `POST`, `PUT`, and `DELETE` endpoints, parameters can be sent in the `query string` with content type `application/x-www-form-urlencoded` , or in the `request body`.  
* The order of parameters is not required.

---

## Access restrictions

### Basic information on access restrictions

* The `rateLimits` array in `/api/v3/exchangeInfo` contains objects related to REQUEST\_WEIGHT and ORDERS rate limits for trading. These are further defined in the `enum definitions` section under `rateLimitType`.  
* A 429 will be returned when any of the rate limits are violated.

### IP access limits

* Each request will include a header named `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` that contains the used weight of all requests from the current IP.  
* Each endpoint has a corresponding weight, and some endpoints may have different weights depending on their parameters. The more resources an endpoint consumes, the higher its weight will be.  
* Upon receiving a 429, you are responsible for stopping requests and must not abuse the API.  
* **If you continue to violate access limits after receiving a 429, your IP will be banned and you will receive a 418 error code.**  
* Repeated violations of the limits will result in progressively longer bans, **from a minimum of 2 minutes up to a maximum of 3 days**.  
* The `Retry-After` header will be sent with responses bearing 418 or 429, and will give the wait time **in seconds** (if 429\) to avoid the ban, or, if 418, until the ban ends.  
* **Access restrictions are based on IP, not API Key**

You are advised to use WebSocket messages to obtain the corresponding data as much as possible to reduce the load and rate-limit pressure from requests.

### Order rate limits

* Each successful order response will include a `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` header containing the number of order limit units currently used by the account.  
* When the number of orders exceeds the limit, you will receive a response with status 429 but without the `Retry-After` header. Please check the order rate limits in `GET api/v3/exchangeInfo` (rateLimitType \= ORDERS) and wait until the ban period ends.  
* Rejected or unsuccessful orders are not guaranteed to include the above header in the response.  
* **Order placement rate limits are counted per account.**

### WebSocket connection limits

* The WebSocket server accepts a maximum of 5 messages per second. Messages include:  
  * PING frame  
  * PONG frame  
  * Messages in JSON format, such as subscribe and unsubscribe.  
* If a user sends messages that exceed the limit, the connection will be terminated. IPs that are repeatedly disconnected may be blocked by the server.  
* A single connection can subscribe to up to **1024** Streams.

---

## API authentication types

* Each API has its own authentication type, which determines what kind of authentication is required when accessing it.
* If API_WALLET authentication is required, a signer should be included in the request body.
* If master account authentication is required, a user should be included in the request body.

| Security Type | Description                               |
| ------------- | ----------------------------------------- |
| NONE          | API that does not require authentication  |
| TRADE         | A valid signer and signature are required |
| USER_DATA     | A valid signer and signature are required |
| USER_STREAM   | A valid signer and signature are required |
| MARKET_DATA   | A valid signer and signature are required |

---

## SIGNED (TRADE AND USER\_DATA) Endpoint security


## Authentication signature payload

| Parameter | Description                        |
| --------- | ---------------------------------- |
| user      | Main account wallet address        |
| signer    | API wallet address                 |
| nonce     | Current timestamp, in microseconds |
| signature | Signature                          |

## Endpoints requiring signature 
* Security Type: SPOT_TRADE, USER_DATA, USER_STREAM

## Example of POST /api/v3/order

#### All parameters are passed through the request body (Python 3.9.6)

#### The following parameters are API registration details. The values for user, signer, and privateKey are for demonstration purposes only (the privateKey corresponds to the signer).

| Key        | Value          | Desc                                                    
| ---------- | ------------------------------------------------------------------ |  ---------- |
| user       | 0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e                         |Login wallet address |
| signer     | 0x21cF8Ae13Bb72632562c6Fff438652Ba1a151bb0                         |[Click Here](https://www.asterdex-testnet.com/en/api-wallet)         | 
| privateKey | 0x4fd0a42218f3eae43a6ce26d22544e986139a01e5b34a62db53757ffca81bae1 |[Click Here](https://www.asterdex-testnet.com/en/api-wallet)        | 

#### The nonce parameter is the current system time in microseconds. If it exceeds the system time or lags behind it by more than 5 seconds, the request is considered invalid.

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
#### Example: The following parameters are business request parameters.

```python
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
host = 'https://sapi.asterdex-testnet.com'

# config your user and agent info here
user = '*'
signer = '*'
private_key = '*'

place_order = {"url":"/api/v3/order","method":"POST","params":{"symbol": "ASTERUSDT", "type": "LIMIT", "side": "BUY",
                  "timeInForce": "GTC", "quantity": "20", "price": "0.5"}}
batch_orders = {"url":"/api/v3/batchOrders","method":"POST","params":{
          "batchOrders":"[{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'},{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'}]" }}
listen_key = {"url":"/api/v3/listenKey","method":"POST","params":{}}
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
       print(res.text)

if __name__ == '__main__':
    send_by_url(place_order)
    # send_by_url(listen_key)
    # send_by_url(batch_orders)
    # send_by_body(place_order)
    # send_by_body(batch_orders)
```


## Public Endpoints Info

### Terminology

* `base asset` refers to the asset that is the `quantity` of a symbol.
* `quote asset` refers 

## Public API parameters

### Terminology

The terminology in this section applies throughout the document. New users are encouraged to read it carefully for better understanding.

* `base asset` refers to the asset being traded in a trading pair, i.e., the asset name written first; for example, in `BTCUSDT`, `BTC` is the `base asset`.  
* `quote asset` refers to the pricing asset of a trading pair, i.e., the asset name written at the latter part; for example, in `BTCUSDT`, `USDT` is the `quote asset`.

### Enumeration definition

**Trading pair status (status):**

* TRADING \- after trade

**Trading pair type:**

* SPOT \- spot

**Order status (status):**

| Status | Description |
| :---- | :---- |
| NEW | Order accepted by the matching engine |
| PARTIALLY\_FILLED | Part of the order was filled |
| FILLED | The order was fully filled |
| CANCELED | The user canceled the order |
| REJECTED | The order was not accepted by the matching engine and was not processed |
| EXPIRED | Order canceled by the trading engine, for example: Limit FOK order not filled, Market order not fully filled, orders canceled during exchange maintenance |

**Order types (orderTypes, type):**

* LIMIT \- Limit Order  
* MARKET \- Market Order  
* STOP \- Limit Stop Order  
* TAKE\_PROFIT \- Limit Take-Profit Order  
* STOP\_MARKET \- Market Stop Order  
* TAKE\_PROFIT\_MARKET \- Market Take-Profit Order

**Order response type (newOrderRespType):**

* ACK  
* RESULT  
* FULL

**Order direction (direction side):**

* BUY \- Buy  
* SELL \- Sell

**Valid types (timeInForce):**

This defines how long an order can remain valid before expiring.

| Status | Description |
| :---- | :---- |
| GTC (Good ‘Til Canceled) | The order remains active until it is fully executed or manually canceled. |
| IOC (Immediate or Cancel) | The order will execute immediately for any amount available. Any unfilled portion is automatically canceled. |
| FOK (Fill or Kill) | The order must be fully executed immediately. If it cannot be filled in full, it is canceled right away. |
| GTX (Good till crossing, Post only) | The post-only limit order will only be placed if it can be added as a maker order and not as a taker order.  |

**K-line interval:**

m (minutes), h (hours), d (days), w (weeks), M (months)

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

**Rate limit type (rateLimitType)**

REQUEST\_WEIGHT

```json
    {
      "rateLimitType": "REQUEST_WEIGHT",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 1200
    }
```

ORDERS

```json
    {
      "rateLimitType": "ORDERS",
      "interval": "MINUTE",
      "intervalNum": 1,
      "limit": 100
    }
```

* REQUEST\_WEIGHT \- The maximum sum of request weights allowed within a unit time  
    
* ORDERS \- Order placement frequency limit per time unit

**Interval restriction (interval)**

* MINUTE \- Minute

---

## Filters

Filters, i.e. Filter, define a set of trading rules. There are two types: filters for trading pairs `symbol filters`, and filters for the entire exchange `exchange filters` (not supported yet)

### Trading pair filters

#### PRICE\_FILTER Price filter

**Format in the /exchangeInfo response:**

```javascript
  {                     
    "minPrice": "556.72",
    "maxPrice": "4529764",
    "filterType": "PRICE_FILTER",
    "tickSize": "0.01"   
  }
```

The `Price Filter` checks the validity of the `price` parameter in an order. It consists of the following three parts:

* `minPrice` defines the minimum allowed value for `price`/`stopPrice`.  
* `maxPrice` defines the maximum allowed value for `price`/`stopPrice`.  
* `tickSize` defines the step interval for `price`/`stopPrice`, meaning the price must equal minPrice plus an integer multiple of tickSize.

Each of the above items can be 0; when 0 it means that item is not constrained.

The logical pseudocode is as follows:

* `price` \>= `minPrice`  
* `price` \<= `maxPrice`  
* (`price`\-`minPrice`) % `tickSize` \== 0

#### PERCENT\_PRICE price amplitude filter

**Format in the /exchangeInfo response:**

```javascript
  {                    
	"multiplierDown": "0.9500",
	"multiplierUp": "1.0500",
	"multiplierDecimal": "4",
	"filterType": "PERCENT_PRICE"
  }
```

The `PERCENT_PRICE` filter defines the valid range of prices based on the index price.

For the "price percentage" to apply, the "price" must meet the following conditions:

* `price` \<=`indexPrice` \*`multiplierUp`  
* `price`\> \=`indexPrice` \*`multiplierDown`

#### LOT\_SIZE order size

**Format in the /exchangeInfo response:**

```javascript
  {
    "stepSize": "0.00100000",
    "filterType": "LOT_SIZE",
    "maxQty": "100000.00000000",
    "minQty": "0.00100000"
  }
```

Lots is an auction term. The `LOT_SIZE` filter validates the `quantity` (i.e., the amount) parameter in orders. It consists of three parts:

* `minQty` indicates the minimum allowed value for `quantity`.  
* `maxQty` denotes the maximum allowed value for `quantity`.  
* `stepSize` denotes the allowed step increment for `quantity`.

The logical pseudocode is as follows:

* `quantity` \>= `minQty`  
* `quantity` \<= `maxQty`  
* (`quantity`\-`minQty`) % `stepSize` \== 0

#### MARKET\_LOT\_SIZE \- Market order size

\***/exchangeInfo response format:**

```javascript
  {
    "stepSize": "0.00100000",
    "filterType": "MARKET_LOT_SIZE"
	"maxQty": "100000.00000000",
	"minQty": "0.00100000"
  }
```

The `MARKET_LOT_SIZE` filter defines the `quantity` (i.e., the "lots" in an auction) rules for `MARKET` orders on a trading pair. There are three parts:

* `minQty` defines the minimum allowed `quantity`.  
* `maxQty` defines the maximum allowed quantity.  
* `stepSize` defines the increments by which the quantity can be increased or decreased.

In order to comply with the `market lot size`, the `quantity` must satisfy the following conditions:

* `quantity` \>= `minQty`  
* `quantity` \<= `maxQty`  
* (`quantity`\-`minQty`) % `stepSize` \== 0

