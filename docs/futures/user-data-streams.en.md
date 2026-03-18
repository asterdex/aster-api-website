## **Overview**

* The base API endpoint is: **https://fapi.asterdex.com**
* A User Data Stream `listenKey` is valid for 60 minutes after creation.
* Doing a `PUT` on a `listenKey` will extend its validity for 60 minutes.
* Doing a `DELETE` on a `listenKey` will close the stream and invalidate the `listenKey`.
* Doing a `POST` on an account with an active `listenKey` will return the currently active `listenKey` and extend its validity for 60 minutes.
* The baseurl for websocket is **wss://fstream.asterdex.com**
* User Data Streams are accessed at **/ws/\<listenKey\>**
* User data stream payloads are **not guaranteed** to be in order during heavy periods; **make sure to order your updates using E**
* A single connection to **fstream.asterdex.com** is only valid for 24 hours; expect to be disconnected at the 24 hour mark


## **Start User Data Stream (USER_STREAM)**


> **Response:**

```javascript
{
  "listenKey": "pqia91ma19a5s61cv6a81va65sdf19v8a65a1a5s61cv6a81va65sdf19v8a65a1"
}
```

``
POST /fapi/v1/listenKey
``

Start a new user data stream. The stream will close after 60 minutes unless a keepalive is sent. If the account has an active `listenKey`, that `listenKey` will be returned and its validity will be extended for 60 minutes.

**Weight:**
1

**Parameters:**

None



## **Keepalive User Data Stream (USER_STREAM)**

> **Response:**

```javascript
{}
```

``
PUT /fapi/v1/listenKey
``

Keepalive a user data stream to prevent a time out. User data streams will close after 60 minutes. It's recommended to send a ping about every 60 minutes.

**Weight:**
1

**Parameters:**

None



## **Close User Data Stream (USER_STREAM)**


> **Response:**

```javascript
{}
```

``
DELETE /fapi/v1/listenKey
``

Close out a user data stream.

**Weight:**
1

**Parameters:**

None


## **Event: User Data Stream Expired**

> **Payload:**

```javascript
{
	'e': 'listenKeyExpired',      // event type
	'E': 1576653824250				// event time
}
```

When the `listenKey` used for the user data stream turns expired, this event will be pushed.

**Notice:**

* This event is not related to the websocket disconnection.
* This event will be received only when a valid `listenKey` in connection got expired.
* No more user data event will be updated after this event received until a new valid `listenKey` used.





## **Event: Margin Call**

> **Payload:**

```javascript
{
    "e":"MARGIN_CALL",    	// Event Type
    "E":1587727187525,		// Event Time
    "cw":"3.16812045",		// Cross Wallet Balance. Only pushed with crossed position margin call
    "p":[					// Position(s) of Margin Call
      {
        "s":"ETHUSDT",		// Symbol
        "ps":"LONG",		// Position Side
        "pa":"1.327",		// Position Amount
        "mt":"CROSSED",		// Margin Type
        "iw":"0",			// Isolated Wallet (if isolated position)
        "mp":"187.17127",	// Mark Price
        "up":"-1.166074",	// Unrealized PnL
        "mm":"1.614445"		// Maintenance Margin Required
      }
    ]
}  
 
```


* When the user's position risk ratio is too high, this stream will be pushed.
* This message is only used as risk guidance information and is not recommended for investment strategies.
* In the case of a highly volatile market, there may be the possibility that the user's position has been liquidated at the same time when this stream is pushed out.





## **Event: Balance and Position Update**


> **Payload:**

```javascript
{
  "e": "ACCOUNT_UPDATE",				// Event Type
  "E": 1564745798939,            		// Event Time
  "T": 1564745798938 ,           		// Transaction
  "a":                          		// Update Data
    {
      "m":"ORDER",						// Event reason type
      "B":[                     		// Balances
        {
          "a":"USDT",           		// Asset
          "wb":"122624.12345678",    	// Wallet Balance
          "cw":"100.12345678",			// Cross Wallet Balance
          "bc":"50.12345678"			// Balance Change except PnL and Commission
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
          "s":"BTCUSDT",          	// Symbol
          "pa":"0",               	// Position Amount
          "ep":"0.00000",            // Entry Price
          "cr":"200",             	// (Pre-fee) Accumulated Realized
          "up":"0",						// Unrealized PnL
          "mt":"isolated",				// Margin Type
          "iw":"0.00000000",			// Isolated Wallet (if isolated position)
          "ps":"BOTH"					// Position Side
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

Event type is `ACCOUNT_UPDATE`.   

* When balance or position get updated, this event will be pushed.
	* `ACCOUNT_UPDATE` will be pushed only when update happens on user's account, including changes on balances, positions, or margin type.
	* Unfilled orders or cancelled orders will not make the event `ACCOUNT_UPDATE` pushed, since there's no change on positions.
	* Only positions of symbols with non-zero isolatd wallet or non-zero position amount will be pushed in the "position" part of the event `ACCOUNT_UPDATE` when any position changes.

* When "FUNDING FEE" changes to the user's balance, the event will be pushed with the brief message:
	* When "FUNDING FEE" occurs in a **crossed position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only), without any position `P` message. 
	* When "FUNDING FEE" occurs in an **isolated position**, `ACCOUNT_UPDATE` will be pushed with only the balance `B`(including the "FUNDING FEE" asset only) and the relative position message `P`( including the isolated position on which the "FUNDING FEE" occurs only, without any other position message).  

* The field "m" represents the reason type for the event and may shows the following possible types:
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

* The field "bc" represents the balance change except for PnL and commission.

## **Event: Order Update**


> **Payload:**

```javascript
{
  
  "e":"ORDER_TRADE_UPDATE",		// Event Type
  "E":1568879465651,			// Event Time
  "T":1568879465650,			// Transaction Time
  "o":{								
    "s":"BTCUSDT",				// Symbol
    "c":"TEST",					// Client Order Id
      // special client order id:
      // starts with "autoclose-": liquidation order
      // "adl_autoclose": ADL auto close order
    "S":"SELL",					// Side
    "o":"TRAILING_STOP_MARKET",	// Order Type
    "f":"GTC",					// Time in Force
    "q":"0.001",				// Original Quantity
    "p":"0",					// Original Price
    "ap":"0",					// Average Price
    "sp":"7103.04",				// Stop Price. Please ignore with TRAILING_STOP_MARKET order
    "x":"NEW",					// Execution Type
    "X":"NEW",					// Order Status
    "i":8886774,				// Order Id
    "l":"0",					// Order Last Filled Quantity
    "z":"0",					// Order Filled Accumulated Quantity
    "L":"0",					// Last Filled Price
    "N":"USDT",            	// Commission Asset, will not push if no commission
    "n":"0",               	// Commission, will not push if no commission
    "T":1568879465651,			// Order Trade Time
    "t":0,			        	// Trade Id
    "b":"0",			    	// Bids Notional
    "a":"9.91",					// Ask Notional
    "m":false,					// Is this trade the maker side?
    "R":false,					// Is this reduce only
    "wt":"CONTRACT_PRICE", 		// Stop Price Working Type
    "ot":"TRAILING_STOP_MARKET",	// Original Order Type
    "ps":"LONG",						// Position Side
    "cp":false,						// If Close-All, pushed with conditional order
    "AP":"7476.89",				// Activation Price, only puhed with TRAILING_STOP_MARKET order
    "cr":"5.0",					// Callback Rate, only puhed with TRAILING_STOP_MARKET order
    "rp":"0"							// Realized Profit of the trade
  }
  
}
```


When new order created, order status changed will push such event.
event type is `ORDER_TRADE_UPDATE`.





**Side**

* BUY 
* SELL 

**Order Type**

* MARKET 
* LIMIT
* STOP
* TAKE_PROFIT
* LIQUIDATION

**Execution Type**

* NEW
* CANCELED
* CALCULATED		 - Liquidation Execution
* EXPIRED
* TRADE

**Order Status**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* EXPIRED
* NEW_INSURANCE     - Liquidation with Insurance Fund
* NEW_ADL				- Counterparty Liquidation`

**Time in force**

* GTC
* IOC
* FOK
* GTX
* HIDDEN

**Working Type**

* MARK_PRICE
* CONTRACT_PRICE 



## **Event: Account Configuration Update previous Leverage Update**

> **Payload:**

```javascript
{
    "e":"ACCOUNT_CONFIG_UPDATE",       // Event Type
    "E":1611646737479,		           // Event Time
    "T":1611646737476,		           // Transaction Time
    "ac":{								
    "s":"BTCUSDT",					   // symbol
    "l":25						       // leverage
     
    }
}  
 
```

> **Or**

```javascript
{
    "e":"ACCOUNT_CONFIG_UPDATE",       // Event Type
    "E":1611646737479,		           // Event Time
    "T":1611646737476,		           // Transaction Time
    "ai":{							   // User's Account Configuration
    "j":true,						   // Multi-Assets Mode
    "f":true,                          // Specified token fee deduction
    "d":true                           // Position mode: true for dual-side (hedge) mode, false for single-side (one-way) mode
    }
}  
```

When the account configuration is changed, the event type will be pushed as `ACCOUNT_CONFIG_UPDATE`

When the leverage of a trade pair changes, the payload will contain the object `ac` to represent the account configuration of the trade pair, where `s` represents the specific trade pair and `l` represents the leverage

When the user Multi-Assets margin mode changes the payload will contain the object `ai` representing the user account configuration, where `j` represents the user Multi-Assets margin mode


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

* h: Transaction hash of the trade.

* m: Array containing the participant addresses:

    * m[0]: Taker address

    * m[1]: Maker address

