## **Overview**

* This API may require the user's AGENT. 
* To create an AGENT please [click here](https://www.asterdex.com/en/api-wallet) and switch to `Pro API` at the top
* The base endpoint is: **https://fapi.asterdex.com**
* All endpoints return either a JSON object or array.
* Data is returned in **ascending** order. Oldest first, newest last.
* All data types adopt definition in JAVA.

### HTTP Return Codes

* HTTP `4XX` return codes are used for for malformed requests;
  the issue is on the sender's side.
* HTTP `403` return code is used when the WAF Limit (Web Application Firewall) has been violated.
* HTTP `429` return code is used when breaking a request rate limit.
* HTTP `418` return code is used when an IP has been auto-banned for continuing to send requests after receiving `429` codes.
* HTTP `5XX` return codes are used for internal errors; the issue is on
  Aster's side.
* HTTP `503` return code is used when the API successfully sent the message but not get a response within the timeout period.
  It is important to **NOT** treat this as a failure operation; the execution status is
  **UNKNOWN** and could have been a success.

### Error Codes and Messages

* Any endpoint can return an ERROR

> ***The error payload is as follows:***

```javascript
{
  "code": -1121,
  "msg": "Invalid symbol."
}
```

* Specific error codes and messages defined in **Error Codes** Page.

### General Information on Endpoints

* For `GET` endpoints, parameters must be sent as a `query string`.
* For POST, PUT, and DELETE method APIs, send data in the request body (content type application/x-www-form-urlencoded)
* Parameters may be sent in any order.

### V3 Nonce Mechanism

* **Nonce** is used to validate the **validity, uniqueness, and replay-protection** of requests. Clients should use the **current timestamp (microsecond precision)** as the nonce, and the difference from server time must not exceed **10 seconds**.

* Request processing flow:

  1. If the nonce **has already been used** → rejected as a **duplicate request**
  2. Otherwise, the system checks whether it is **too old**

* To improve performance, each user maintains only the **most recent 100 nonces**:

  * If the list is full and the new nonce is **smaller than the current minimum** → rejected as **expired**
  * Otherwise, the **oldest nonce is removed** and the new one is added

* Overall effect: **prevents duplicates and stale requests, retaining only recent valid requests**
## **LIMITS**

* The `/fapi/v3/exchangeInfo` `rateLimits` array contains objects related to the exchange's `RAW_REQUEST`, `REQUEST_WEIGHT`, and `ORDER` rate limits. These are further defined in the `ENUM definitions` section under `Rate limiters (rateLimitType)`.
* A `429` will be returned when either rate limit is violated.

<aside class="notice">
Aster Finance has the right to further tighten the rate limits on users with intent to attack.
</aside>

### IP Limits

* Every request will contain `X-MBX-USED-WEIGHT-(intervalNum)(intervalLetter)` in the response headers which has the current used weight for the IP for all request rate limiters defined.
* Each route has a `weight` which determines for the number of requests each endpoint counts for. Heavier endpoints and endpoints that do operations on multiple symbols will have a heavier `weight`.
* When a 429 is received, it's your obligation as an API to back off and not spam the API.
* **Repeatedly violating rate limits and/or failing to back off after receiving 429s will result in an automated IP ban (HTTP status 418).**
* IP bans are tracked and **scale in duration** for repeat offenders, **from 2 minutes to 3 days**.
* **The limits on the API are based on the IPs, not the API keys.**

<aside class="notice">
It is strongly recommended to use websocket stream for getting data as much as possible, which can not only ensure the timeliness of the message, but also reduce the access restriction pressure caused by the request.
</aside>

### Order Rate Limits

* Every order response will contain a `X-MBX-ORDER-COUNT-(intervalNum)(intervalLetter)` header which has the current order count for the account for all order rate limiters defined.
* Rejected/unsuccessful orders are not guaranteed to have `X-MBX-ORDER-COUNT-**` headers in the response.
* **The order rate limit is counted against each account**.

## **API authentication type**

* Each API has its own authentication type, which determines what kind of authentication is required when accessing it.
* If API_WALLET authentication is required, a signer should be included in the request body.
* If master account authentication is required, a user should be included in the request body.

| Security Type | Description                               |
| ------------- | ----------------------------------------- |
| NONE          | API that does not require authentication  |
| TRADE         | A valid signer and signature are required |
| USER_DATA     | A valid signer and signature are required |
| USER_STREAM   | A valid signer and signature are required |
| MARKET_DATA   | API that does not require authentication |

## **Authentication signature payload**

| Parameter | Description                        |
| --------- | ---------------------------------- |
| user      | Main account wallet address        |
| signer    | API wallet address                 |
| nonce     | Current timestamp, in microseconds |
| signature | Signature                          |

## **Endpoints requiring signature**
* Security Type: TRADE, USER_DATA, USER_STREAM

## **Example of POST /fapi/v3/order**


#### The following parameters are API registration details. The values for user, signer, and privateKey are for demonstration purposes only (the privateKey corresponds to the signer).

| Key        | Value          | Desc                                                    
| ---------- | ------------------------------------------------------------------ |  ---------- |
| user       | 0x63DD5aCC6b1aa0f563956C0e534DD30B6dcF7C4e                         |Login wallet address |
| signer     | 0x21cF8Ae13Bb72632562c6Fff438652Ba1a151bb0                         |[Click Here](https://www.asterdex.com/en/api-wallet)         | 
| privateKey | 0x4fd0a42218f3eae43a6ce26d22544e986139a01e5b34a62db53757ffca81bae1 |[Click Here](https://www.asterdex.com/en/api-wallet)        | 

#### The nonce parameter is the current system time in microseconds. If it exceeds the system time or lags behind it by more than 10 seconds, the request is considered invalid.

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

#### Example: Post an order (using Python as an example).


```python
import time
import threading
import urllib

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
private_key = '*'

place_order = {"url":"/fapi/v3/order","method":"POST","params":{"symbol": "ASTERUSDT", "type": "LIMIT", "side": "BUY",
                  "timeInForce": "GTC", "quantity": "20", "price": "0.5"}}
batch_orders = {"url":"/fapi/v3/batchOrders","method":"POST","params":{
          "batchOrders":"[{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'},{'symbol':'ASTERUSDT','type':'LIMIT','side':'BUY','timeInForce':'GTC','quantity':'20','price':'0.5'}]" }}
batch_orders_delete = {"url": "/fapi/v3/batchOrders", "method": "DELETE",
                       "params": {"symbol": "BTCUSDT", "origClientOrderIdList": '["123aaaa","111ccc","321313"]'}}

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
    method = api['method']

    my_dict['nonce'] = str(get_nonce())
    my_dict['signer'] = signer

    param = urllib.parse.urlencode(my_dict)

    print(param)
    typed_data['message']['msg'] = param
    message = encode_structured_data(typed_data)
    signed = Account.sign_message(message, private_key=private_key)

    url = url + '?' + param + '&signature=' + signed.signature.hex()
    print(url)

    if method == 'DELETE':
        res = requests.delete(url, headers=headers)
        print(res.text)
    if method == 'POST':
        res = requests.post(url, headers=headers)
        print(res.text)

def send_by_body(api) :
       my_dict = api['params']
       url = host +api['url']
       method = api['method']

       my_dict['nonce'] = str(get_nonce())
       my_dict['signer'] = signer

       param = urllib.parse.urlencode(my_dict)
       typed_data['message']['msg'] = param
       message = encode_structured_data(typed_data)

       signed = Account.sign_message(message, private_key=private_key)
       print(signed.signature.hex())

       my_dict['signature'] = signed.signature.hex()

       print(my_dict)
       if method == 'DELETE':
           res = requests.delete(url, data=my_dict, headers=headers)
           print(res.text)
       if method == 'POST':
           res = requests.post(url, data=my_dict, headers=headers)
           print(res.text)

if __name__ == '__main__':
    send_by_url(place_order)
    # send_by_body(place_order)
    # send_by_body(batch_orders)
    # send_by_url(batch_orders_delete)
    # send_by_body(batch_orders_delete)
```
## **Public Endpoints Info**

### Terminology

* `base asset` refers to the asset that is the `quantity` of a symbol.
* `quote asset` refers to the asset that is the `price` of a symbol.

### ENUM definitions

**Symbol type:**

* FUTURE

**Contract type (contractType):**

* PERPETUAL

**Contract status(contractStatus，status):**

* PENDING_TRADING
* TRADING
* PRE_SETTLE
* SETTLING
* CLOSE

**Order status (status):**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* REJECTED
* EXPIRED

**Order types (orderTypes, type):**

* LIMIT
* MARKET
* STOP
* STOP_MARKET
* TAKE_PROFIT
* TAKE_PROFIT_MARKET
* TRAILING_STOP_MARKET

**Order side (side):**

* BUY
* SELL

**Position side (positionSide):**

* BOTH
* LONG
* SHORT

**Time in force (timeInForce):**

* GTC - Good Till Cancel
* IOC - Immediate or Cancel
* FOK - Fill or Kill
* GTX - Good Till Crossing	(Post Only)
* HIDDEN - HIDDEN This type of order is not visible in the order book

**Working Type (workingType)**

* MARK_PRICE
* CONTRACT_PRICE

**Response Type (newOrderRespType)**

* ACK
* RESULT

**Kline/Candlestick chart intervals:**

m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

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

**Rate limiters (rateLimitType)**

> REQUEST_WEIGHT

```javascript
{
  	"rateLimitType": "REQUEST_WEIGHT",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 2400
  }
```

> ORDERS

```javascript
{
  	"rateLimitType": "ORDERS",
  	"interval": "MINUTE",
  	"intervalNum": 1,
  	"limit": 1200
   }
```

* REQUEST_WEIGHT
* ORDERS

**Rate limit intervals (interval)**

* MINUTE

## **Filters**

Filters define trading rules on a symbol or an exchange.

### Symbol filters

#### PRICE_FILTER

> **/exchangeInfo format:**

```javascript
{
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
  }
```

The `PRICE_FILTER` defines the `price` rules for a symbol. There are 3 parts:

* `minPrice` defines the minimum `price`/`stopPrice` allowed; disabled on `minPrice` == 0.
* `maxPrice` defines the maximum `price`/`stopPrice` allowed; disabled on `maxPrice` == 0.
* `tickSize` defines the intervals that a `price`/`stopPrice` can be increased/decreased by; disabled on `tickSize` == 0.

Any of the above variables can be set to 0, which disables that rule in the `price filter`. In order to pass the `price filter`, the following must be true for `price`/`stopPrice` of the enabled rules:

* `price` >= `minPrice`
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0

#### LOT_SIZE

> **/exchangeInfo format:**

```javascript
{
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

The `LOT_SIZE` filter defines the `quantity` (aka "lots" in auction terms) rules for a symbol. There are 3 parts:

* `minQty` defines the minimum `quantity` allowed.
* `maxQty` defines the maximum `quantity` allowed.
* `stepSize` defines the intervals that a `quantity` can be increased/decreased by.

In order to pass the `lot size`, the following must be true for `quantity`:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0

#### MARKET_LOT_SIZE

> **/exchangeInfo format:**

```javascript
{
    "filterType": "MARKET_LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

The `MARKET_LOT_SIZE` filter defines the `quantity` (aka "lots" in auction terms) rules for `MARKET` orders on a symbol. There are 3 parts:

* `minQty` defines the minimum `quantity` allowed.
* `maxQty` defines the maximum `quantity` allowed.
* `stepSize` defines the intervals that a `quantity` can be increased/decreased by.

In order to pass the `market lot size`, the following must be true for `quantity`:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0

#### MAX_NUM_ORDERS

> **/exchangeInfo format:**

```javascript
{
    "filterType": "MAX_NUM_ORDERS",
    "limit": 200
  }
```

The `MAX_NUM_ORDERS` filter defines the maximum number of orders an account is allowed to have open on a symbol.

Note that both "algo" orders and normal orders are counted for this filter.

#### MAX_NUM_ALGO_ORDERS

> **/exchangeInfo format:**

```javascript
{
    "filterType": "MAX_NUM_ALGO_ORDERS",
    "limit": 100
  }
```

The `MAX_NUM_ALGO_ORDERS ` filter defines the maximum number of all kinds of algo orders an account is allowed to have open on a symbol.

The algo orders include `STOP`, `STOP_MARKET`, `TAKE_PROFIT`, `TAKE_PROFIT_MARKET`, and `TRAILING_STOP_MARKET` orders.

#### PERCENT_PRICE

> **/exchangeInfo format:**

```javascript
{
    "filterType": "PERCENT_PRICE",
    "multiplierUp": "1.1500",
    "multiplierDown": "0.8500",
    "multiplierDecimal": 4
  }
```

The `PERCENT_PRICE` filter defines valid range for a price based on the mark price.

In order to pass the `percent price`, the following must be true for `price`:

* BUY: `price` <= `markPrice` * `multiplierUp`
* SELL: `price` >= `markPrice` * `multiplierDown`

#### MIN_NOTIONAL

> **/exchangeInfo format:**

```javascript
{
    "filterType": "MIN_NOTIONAL",
    "notional": "1"
  }
```

The `MIN_NOTIONAL` filter defines the minimum notional value allowed for an order on a symbol.
An order's notional value is the `price` * `quantity`.
Since `MARKET` orders have no price, the mark price is used.

---## Endpoints requiring signature 
* Security Type: TRADE, USER_DATA, USER_STREAM

