## **WebSocket account information push**

* The base URL for the API endpoints listed in this document is: [**https://sapi.asterdex-testnet.com**](https://sapi.asterdex-testnet.com)  
* The `listenKey` used to subscribe to account data is valid for 60 minutes from the time of creation  
* You can extend the 60-minute validity of a `listenKey` by sending a `PUT` request  
* You can immediately close the current data stream and invalidate the `listenKey` by sending a `DELETE` for a `listenKey`  
* Sending a `POST` on an account with a valid `listenKey` will return the currently valid `listenKey` and extend its validity by 60 minutes  
* The WebSocket interface baseurl: **wss://sstream.asterdex-testnet.com**  
* The stream name for subscribing to the user account data stream is \*\*/ws/\*\*  
* Each connection is valid for no more than 24 hours; please handle disconnections and reconnections appropriately

## Listen Key (spot account)

### Generate Listen Key (USER\_STREAM)

**Response**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

`POST /api/v3/listenKey`

Start a new data stream. The data stream will be closed after 60 minutes unless a keepalive is sent. If the account already has a valid `listenKey`, that `listenKey` will be returned and its validity extended by 60 minutes.

**Weight:** 1

**Parameters:** NONE

### Extend Listen Key validity period (USER\_STREAM)

**Response**

```javascript
{}
```

`PUT /api/v3/listenKey`

Validity extended to 60 minutes after this call. It is recommended to send a ping every 30 minutes.

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| listenKey | STRING | YES |  |

### Close Listen Key (USER\_STREAM)

**Response**

```javascript
{}
```

`DELETE /api/v3/listenKey`

Close user data stream

**Weight:** 1

**Parameters:**

| Name | Type | Is it required? | Description |
| :---- | :---- | :---- | :---- |
| listenKey | STRING | YES |  |

## Payload: ACCOUNT\_UPDATE

An `outboundAccountPosition` event is sent whenever an account balance changes; it contains the assets that may have changed due to the event that generated the balance update.

**Payload**

```javascript
{
  "B":[  //Balance
    {
      "a":"SLP25",   //Asset
      "f":"10282.42029415",   //Free
      "l":"653.00000001"   //Locked
    },
    {
      "a":"ADA25",
      "f":"9916.96229880",
      "l":"34.00510000"
    }
  ],
  "e":"outboundAccountPosition",   //Event type
  "T":1649926447190,   //Time of last account update
  "E":1649926447205   //Event Time
  "m":"WITHDRAW" // Event reason type
}
```

## Payload: Order Update

Orders are updated via the `executionReport` event

**Payload**

```javascript
{
 "s":"ADA25SLP25",   // symbol
  "c":"Xzh0gnxT41PStbwqOtXnjD",  // client order id
  "S":"SELL",   // order direction
  "o":"LIMIT",   // order type
  "f":"GTC",   // Time in force
  "q":"10.001000",   // Order quantity
  "p":"19.1000000000",   // Order price
  "ap":"19.0999999955550656",  //average price
  "P":"0",  //stop price
  "x":"TRADE",   // Current execution type
  "X":"PARTIALLY_FILLED",   // Current order status
  "i":27,   // Order ID
  "l":"1",    // Last executed quantity   
  "z":"8.999000",   // Cumulative filled quantity
  "L":"19.1000000000",   // Last executed price
  "n":"0.00382000",   // Commission amount
  "N":"SLP25",   // Commission asset
  "T":1649926447190,   //Trasanction Time
  "t":18,   // transaction id
  "m":true,   // is this trade the maker side?
  "ot":"LIMIT", //original order type
  "O":0,   // Order creation time
  "Z":"171.88089996",   // Cumulative quote asset transacted quantity
  "Y":"19.1000000000000000",   // Last quote asset transacted quantity (i.e. lastPrice * lastQty)
  "Q":"0",   // Quote Order Qty
  "e":"executionReport",   // event
  "E":1649926447209  // event time
}  
```

**Execution type:**

* NEW \- New Order  
* CANCELED \- Order canceled  
* REJECTED \- New order was rejected  
* TRADE \- Order had a new fill  
* EXPIRED \- Order expired (based on the order's Time In Force parameter)
