## **WebSocket market data feed**

* The base URL for all wss endpoints listed in this document is: **wss://sstream.asterdex.com**  
* Streams have either a single raw stream or a combined stream  
* Single raw streams format is \*\*/ws/\*\*  
* The URL format for combined streams is \*\*/stream?streams=//\*\*  
* When subscribing to combined streams, the event payload is wrapped in this format: \*\*{"stream":"","data":}\*\*  
* All trading pairs in stream names are **lowercase**  
* Each link to **sstream.asterdex.com** is valid for no more than 24 hours; please handle reconnections appropriately  
* Every 3 minutes the server sends a ping frame; the client must reply with a pong frame within 10 minutes, otherwise the server will close the connection. The client is allowed to send unpaired pong frames (i.e., the client may send pong frames at a frequency higher than once every 10 minutes to keep the connection alive).

## Real-time subscribe/unsubscribe data streams

* The following messages can be sent via WebSocket to subscribe or unsubscribe to data streams. Examples are shown below.  
* The `id` in the response content is an unsigned integer that serves as the unique identifier for exchanges of information.  
* If the `result` in the response content is `null`, it indicates the request was sent successfully.

### Subscribe to a stream

**Response**

```javascript
{
  "result": null,
  "id": 1
}
```

* **Request** { "method": "SUBSCRIBE", "params": \[ "btcusdt@aggTrade", "btcusdt@depth" \], "id": 1 }

### Unsubscribe from a stream

**Response**

```javascript
{
  "result": null,
  "id": 312
}
```

* **Request** { "method": "UNSUBSCRIBE", "params": \[ "btcusdt@depth" \], "id": 312 }

### Subscribed to the feed

**Response**

```javascript
{
  "result": [
    "btcusdt@aggTrade"
  ],
  "id": 3
}
```

* **Request**  
    
  { "method": "LIST\_SUBSCRIPTIONS", "id": 3 }

### Set properties

Currently, the only configurable property is whether to enable the `combined` ("combined") stream. When connecting using `/ws/` ("raw stream"), the combined property is set to `false`, while connecting using `/stream/` sets the property to `true`.

**Response**

```javascript
{
"result": null,
"id": 5
}
```

* **Request** { "method": "SET\_PROPERTY" "params": \[ "combined", true \], "id": 5 }

### Retrieve properties

**Response**

```javascript
{
  "result": true, // Indicates that combined is set to true.
  "id": 2
}
```

* **Request**  
    
  { "method": "GET\_PROPERTY", "params": \[ "combined" \], "id": 2 }

\#\#\# Error message

| Error message | Description |
| :---- | :---- |
| {"code": 0, "msg": "Unknown property"} | Parameters applied in SET\_PROPERTY or GET\_PROPERTY are invalid |
| {"code": 1, "msg": "Invalid value type: expected Boolean", "id": '%s'} | Only true or false are accepted |
| {"code": 2, "msg": "Invalid request: property name must be a string"} | The provided attribute name is invalid |
| {"code": 2, "msg": "Invalid request: request ID must be an unsigned integer"} | Parameter ID not provided or ID has an invalid type |
| {"code": 2, "msg": "Invalid request: unknown variant %s, expected one of SUBSCRIBE, UNSUBSCRIBE, LIST\_SUBSCRIPTIONS, SET\_PROPERTY, GET\_PROPERTY at line 1 column 28"} | Typo warning, or the provided value is not of the expected type |
| {"code": 2, "msg": "Invalid request: too many parameters"} | Unnecessary parameters were provided in the data |
| {"code": 2, "msg": "Invalid request: property name must be a string"} | Property name not provided |
| {"code": 2, "msg": "Invalid request: missing field method at line 1 column 73"} | Data did not provide method |
| {"code":3,"msg":"Invalid JSON: expected value at line %s column %s"} | JSON syntax error |

## Collection transaction flow

**Payload:**

```javascript
{
  "e": "aggTrade",  // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "a": 12345,       // Aggregate trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "f": 100,         // First trade ID
  "l": 105,         // Last trade ID
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
  "M": true         // Ignore
}
```

The collection transaction stream pushes transaction information and is an aggregation of a single order.

**Stream name:** `<symbol>@aggTrade`

**Update speed:** real-time

## Tick-by-tick trades

**Payload:**

```javascript
{
  "e": "trade",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "t": 12345,       // Trade ID
  "p": "0.001",     // Price
  "q": "100",       // Quantity
  "T": 123456785,   // Trade time
  "m": true,        // Is the buyer the market maker?
}
```

**Stream name:** `<symbol>@trade`

Each trade stream pushes the details of every individual trade. A **trade**, also called a transaction, is defined as a match between exactly one taker and one maker.

## K-line streams

**Payload:**

```javascript
{
  "e": "kline",     // Event type
  "E": 123456789,   // Event time
  "s": "BNBBTC",    // Symbol
  "k": {
    "t": 123400000, // Kline start time
    "T": 123460000, // Kline close time
    "s": "BNBBTC",  // Symbol
    "i": "1m",      // Interval
    "f": 100,       // First trade ID
    "L": 200,       // Last trade ID
    "o": "0.0010",  // Open price
    "c": "0.0020",  // Close price
    "h": "0.0025",  // High price
    "l": "0.0015",  // Low price
    "v": "1000",    // Base asset volume
    "n": 100,       // Number of trades
    "x": false,     // Is this kline closed?
    "q": "1.0000",  // Quote asset volume
    "V": "500",     // Taker buy base asset volume
    "Q": "0.500",   // Taker buy quote asset volume
    "B": "123456"   // Ignore
  }
}
```

The K-line stream pushes per-second updates for the requested type of K-line (the latest candle).

**Stream name:** `<symbol>@kline_<interval>`

**Update speed:** 2000ms

**K-line interval parameter:**

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

## Simplified ticker by symbol

**Payload:**

```javascript
  {
    "e": "24hrMiniTicker",  // Event type
    "E": 123456789,         // Event time
    "s": "BNBBTC",          // Symbol
    "c": "0.0025",          // Close price
    "o": "0.0010",          // Open price
    "h": "0.0025",          // High price
    "l": "0.0010",          // Low price
    "v": "10000",           // Total traded base asset volume
    "q": "18"               // Total traded quote asset volume
  }
```

Refreshed simplified 24-hour ticker information by symbol

**Stream name:** `<symbol>@miniTicker`

**Update speed:** 1000ms

## Compact tickers for all symbols in the entire market

**Payload:**

```javascript
[
  {
    // Same as <symbol>@miniTicker payload
  }
]
```

Same as above, but pushes all trading pairs. Note that only updated tickers will be pushed.

**Stream name:** \!miniTicker@arr

**Update speed:** 1000ms

## Full ticker per symbol

**Payload:**

```javascript
{
  "e": "24hrTicker",  // Event type
  "E": 123456789,     // Event time
  "s": "BNBBTC",      // Symbol
  "p": "0.0015",      // Price change
  "P": "250.00",      // Price change percent
  "w": "0.0018",      // Weighted average price
  "c": "0.0025",      // Last price
  "Q": "10",          // Last quantity
  "o": "0.0010",      // Open price
  "h": "0.0025",      // High price
  "l": "0.0010",      // Low price
  "v": "10000",       // Total traded base asset volume
  "q": "18",          // Total traded quote asset volume
  "O": 0,             // Statistics open time
  "C": 86400000,      // Statistics close time
  "F": 0,             // First trade ID
  "L": 18150,         // Last trade Id
  "n": 18151          // Total number of trades
}
```

Pushes per-second tag statistics for a single trading pair over a rolling 24-hour window.

**Stream name:** `<symbol>@ticker`

**Update speed:** 1000ms

## Complete ticker for all trading pairs on the entire market

**Payload:**

```javascript
[
  {
    // Same as <symbol>@ticker payload
  }
]
```

Pushes the full 24-hour refreshed ticker information for all trading pairs across the entire market. Note that tickers without updates will not be pushed.

**Stream name:** `!ticker@arr`

**Update speed:** 1000ms

## Best order book information by symbol

**Payload:**

```javascript
{
  "u":400900217,     // order book updateId
  "s":"BNBUSDT",     // symbol
  "b":"25.35190000", // best bid price
  "B":"31.21000000", // best bid qty
  "a":"25.36520000", // best ask price
  "A":"40.66000000"  // best ask qty
}
```

Real-time push of best order book information for the specified trading pair

**Stream name:** `<symbol>@bookTicker`

**Update speed:** Real-time

## Best order book information across the entire market

**Payload:**

```javascript
{
  // 同 <symbol>@bookTicker payload
}
```

Real-time push of the best order information for all trading pairs

**Stream name:** `!bookTicker`

**Update speed:** Real-time

## Limited depth information

**Payload:**

```javascript
{ 
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "T": 123456788,     // Transaction time 
  "s": "BTCUSDT",     // Symbol
  "U": 100,           // First update ID in event
  "u": 120,           // Final update ID in event
  "pu": 99,          // Final update Id in last stream(ie `u` in last stream) 
  "bids": [             // Bids to be updated
    [
      "0.0024",         // Price level to be updated
      "10"              // Quantity
    ]
  ],
  "asks": [             // Asks to be updated
    [
      "0.0026",         // Price level to be updated
      "100"             // Quantity
    ]
  ]
} 
```

Limited depth information pushed every second or every 100 milliseconds. Levels indicate how many levels of bid/ask information, optional 5/10/20 levels.

**Stream names:** `<symbol>@depth<levels>` or `<symbol>@depth<levels>@100ms`.

**Update speed:** 1000ms or 100ms

## Incremental depth information

**Payload:**

```javascript
{
  "e": "depthUpdate", // Event type
  "E": 123456789,     // Event time
  "T": 123456788,     // Transaction time 
  "s": "BTCUSDT",     // Symbol
  "U": 100,           // First update ID in event
  "u": 120,           // Final update ID in event
  "pu": 99,          // Final update Id in last stream(ie `u` in last stream)
  "b": [              // Bids to be updated
    [
      "5.4",       // Price level to be updated
      "10"            // Quantity
    ]
  ],
  "a": [              // Asks to be updated
    [
      "5.6",       // Price level to be updated
      "100"          // Quantity
    ]
  ]
}   
```

Pushes the changed parts of the orderbook (if any) every second or every 100 milliseconds

**Stream name:** `<symbol>@depth` or `<symbol>@depth@100ms`

**Update speed:** 1000ms or 100ms

## How to correctly maintain a local copy of an order book

1. Subscribe to **wss://sstream.asterdex.com/ws/bnbbtc@depth**  
2. Start caching the received updates. For the same price level, later updates overwrite earlier ones.  
3. Fetch the REST endpoint [**https://sapi.asterdex.com/api/v3/depth?symbol=BNBBTC\&limit=1000**](https://sapi.asterdex.com/api/v3/depth?symbol=BNBBTC&limit=1000) to obtain a 1000-level depth snapshot  
4. Discard from the currently cached messages those with `u` \<= the `lastUpdateId` obtained in step 3 (drop older, expired information)  
5. Apply the depth snapshot to your local order book copy, and resume updating the local copy from the first WebSocket event whose `U` \<= `lastUpdateId`\+1 **and** `u` \>= `lastUpdateId`\+1  
6. Each new event’s `U` should equal exactly the previous event’s `u`\+1; otherwise packets may have been lost \- restart initialization from step 3  
7. The order quantity in each event represents the current order quantity at that price as an **absolute value**, not a relative change  
8. If the order quantity at a given price is 0, it means the orders at that price have been canceled or filled, and that price level should be removed

