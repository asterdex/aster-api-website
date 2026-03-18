## **Spot API Overview**

* This document lists the base URL for the API endpoints: [**https://sapi.asterdex.com**](https://sapi.asterdex.com)  
* All API responses are in JSON format.  
* All times and timestamps are in UNIX time, in **milliseconds**.

## **API Key settings**

* Many endpoints require an API Key to access.  
* When setting the API Key, for security reasons it is recommended to set an IP access whitelist.  
* **Never reveal your API key/secret to anyone.**

If an API Key is accidentally exposed, immediately delete that Key and generate a new one.

### Attention
* TESTUSDT or any other symbols starting with TEST are symbols used for Aster’s INTERNAL TESTING ONLY. Please DO NOT trade on these symbols starting with TEST. Aster does not hold any accountability for loss of funds due to trading on these symbols. However, if you run into issues, you may contact support about this any time, we will try to help you recover your funds.

### HTTP return codes

* HTTP `4XX` status codes are used to indicate errors in the request content, behavior, or format. The problem lies with the requester.  
* HTTP `403` status code indicates a violation of WAF restrictions (Web Application Firewall).  
* HTTP `429` error code indicates a warning that the access frequency limit has been exceeded and the IP is about to be blocked.  
* HTTP `418` indicates that after receiving a 429 you continued to access, so the IP has been blocked.  
* HTTP `5XX` error codes are used to indicate issues on the Aster service side.

### API error codes

* When using the endpoint `/api/v1`, any endpoint may throw exceptions;

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

## **Access restrictions**

### Basic information on access restrictions

* The `rateLimits` array in `/api/v1/exchangeInfo` contains objects related to REQUEST\_WEIGHT and ORDERS rate limits for trading. These are further defined in the `enum definitions` section under `rateLimitType`.  
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
* When the number of orders exceeds the limit, you will receive a response with status 429 but without the `Retry-After` header. Please check the order rate limits in `GET api/v1/exchangeInfo` (rateLimitType \= ORDERS) and wait until the ban period ends.  
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

## **API authentication types**

* Each API has its own authentication type, which determines what kind of authentication should be performed when accessing it.  
* The authentication type will be indicated next to each endpoint name in this document; if not specifically stated, it defaults to `NONE`.  
* If API keys are required, they should be passed in the HTTP header using the `X-MBX-APIKEY` field.  
* API keys and secret keys are **case-sensitive**.   
* By default, API keys have access to all authenticated routes.

| Authentication type | Description |
| :---- | :---- |
| NONE | APIs that do not require authentication |
| TRADE | A valid API-Key and signature are required |
| USER\_DATA | A valid API-Key and signature are required |
| USER\_STREAM | A valid API-Key is required |
| MARKET\_DATA | A valid API-Key is required |

* The `TRADE` and `USER_DATA` endpoints are signed (SIGNED) endpoints.

---

## **SIGNED (TRADE AND USER\_DATA) Endpoint security**

* When calling a `SIGNED` endpoint, in addition to the parameters required by the endpoint itself, you must also pass a `signature` parameter in the `query string` or `request body`.  
* The signature uses the `HMAC SHA256` algorithm. The API-Secret corresponding to the API-KEY is used as the key for `HMAC SHA256`, and all other parameters are used as the data for the `HMAC SHA256` operation; the output is the signature.  
* The `signature` is **case-insensitive**.  
* "totalParams" is defined as the "query string" concatenated with the "request body".

### Time synchronization safety

* Signed endpoints must include the `timestamp` parameter, whose value should be the unix timestamp (milliseconds) at the moment the request is sent.  
* When the server receives a request it will check the timestamp; if it was sent more than 5,000 milliseconds earlier, the request will be considered invalid. This time window value can be defined by sending the optional `recvWindow` parameter.

The logical pseudocode is as follows:

```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow)
  {
    // process request
  } 
  else 
  {
    // reject request
  }
```

**About trade timeliness** Internet conditions are not completely stable or reliable, so the latency from your client to Aster's servers will experience jitter. This is why we provide `recvWindow`; if you engage in high-frequency trading and have strict requirements for timeliness, you can adjust `recvWindow` flexibly to meet your needs.

It is recommended to use a recvWindow of under 5 seconds. It must not exceed 60 seconds.

### Example of POST /api/v1/order

Below is an example of placing an order by calling the API using echo, openssl, and curl tools in a Linux bash environment. The apiKey and secretKey are for demonstration only.

| Key | Value |
| :---- | :---- |
| apiKey | 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795 |
| secretKey | fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313 |

| Parameters | Values |
| :---- | :---- |
| symbol | BNBUSDT |
| side | BUY |
| type | LIMIT |
| timeInForce | GTC |
| quantity | 5 |
| price | 1.1 |
| recvWindow | 5000 |
| timestamp | 1756187806000 |

#### Example 1: All parameters are sent through the request body

**Example 1** **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000" | openssl dgst -sha256 -hmac "fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313"
    (stdin)= e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0
```

**curl command:**

```shell
    (HMAC SHA256)
    $ curl -H "X-MBX-APIKEY: 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795" -X POST 'https://sapi.asterdex.com/api/v1/order' -d 'symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000&signature=e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0'
```

* **requestBody:**

symbol=BNBUSDT \&side=BUY \&type=LIMIT \&timeInForce=GTC \&quantity=5 \&price=1.1 \&recvWindow=5000 \&timestamp=1756187806000

#### Example 2: All parameters sent through the query string

**Example 2** **HMAC SHA256 signature:**

```shell
    $ echo -n "symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000" | openssl dgst -sha256 -hmac "fdde510a2b71fa43a43bff3e3cf7819c8c66df34633d338050f4f59664b3b313"
    (stdin)= e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0 
```

**curl command:**

```shell
    (HMAC SHA256)
   $ curl -H "X-MBX-APIKEY: 4452d7e2ed4da80b74105e02d06328c71a34488c9fdd60a5a0900d42d584b795" -X POST 'https://sapi.asterdex.com/api/v1/order?symbol=BNBUSDT&side=BUY&type=LIMIT&timeInForce=GTC&quantity=5&price=1.1&recvWindow=5000&timestamp=1756187806000&signature=e09169bf6c02ec4b29fa1bdc3a967f92c8c6cfcde0551ba1d477b2d3cf4c51b0'
```

* **queryString:**

symbol=BNBUSDT \&side=BUY \&type=LIMIT \&timeInForce=GTC \&quantity=5 \&price=1.1 \&recvWindow=5000 \&timestamp=1756187806000

---

## **Public API parameters**

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
| HIDDEN | This type of order is not visible in the order book |

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

## **Filters**

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
