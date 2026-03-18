## **Error code**

error JSON payload:

```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

Errors consist of two parts: an error code and a message. The code is standardized, but the message may vary.

## **10xx \- General server or network issues**

### \-1000 UNKNOWN

* An unknown error occurred while processing the request.

### \-1001 DISCONNECTED

* Internal error; unable to process your request. Please try again.

### \-1002 UNAUTHORIZED

* You are not authorized to execute this request.

### \-1003 TOO\_MANY\_REQUESTS

* Too many requests queued.  
* Too many requests; please use the WebSocket for live updates.  
* Too many requests; current limit is %s requests per minute. Please use the WebSocket for live updates to avoid polling the API.  
* Too many request weights; IP banned until %s. Please use the WebSocket for live updates to avoid bans.

### \-1004 DUPLICATE\_IP

* This IP is already on the white list.

### \-1005 NO\_SUCH\_IP

* No such IP has been whitelisted.

### \-1006 UNEXPECTED\_RESP

* An unexpected response was received from the message bus. Execution status unknown.

### \-1007 TIMEOUT

* Timeout waiting for response from backend server. Send status unknown; execution status unknown.

### \-1014 UNKNOWN\_ORDER\_COMPOSITION

* The current order parameter combination is not supported.

### \-1015 TOO\_MANY\_ORDERS

* Too many new orders.  
* Too many new orders; the current limit is %s orders per %s.

### \-1016 SERVICE\_SHUTTING\_DOWN

* This service is no longer available.

### \-1020 UNSUPPORTED\_OPERATION

* This operation is not supported.

### \-1021 INVALID\_TIMESTAMP

* Timestamp for this request is outside of the recvWindow.  
* The timestamp for this request was 1000ms ahead of the server's time.

### \-1022 INVALID\_SIGNATURE

* The signature for this request is invalid.

### \-1023 START\_TIME\_GREATER\_THAN\_END\_TIME

* The start time in the parameters is after the end time.

## **11xx \- Request issues**

### \-1100 ILLEGAL\_CHARS

* Illegal characters found in a parameter.  
* Illegal characters found in parameter %s; legal range is %s.

### \-1101 TOO\_MANY\_PARAMETERS

* Too many parameters sent for this endpoint.  
* Too many parameters; expected %s and received %s.  
* Duplicate values for a parameter detected.

### \-1102 MANDATORY\_PARAM\_EMPTY\_OR\_MALFORMED

* A mandatory parameter was not sent, was empty/null, or malformed.  
* Mandatory parameter %s was not sent, was empty/null, or malformed.  
* Param %s or %s must be sent, but both were empty/null.

### \-1103 UNKNOWN\_PARAM

* An unknown parameter was sent.

### \-1104 UNREAD\_PARAMETERS

* Not all sent parameters were read.  
* Not all sent parameters were read; read %s parameter(s) but %s parameter(s) were sent.

### \-1105 PARAM\_EMPTY

* A parameter was empty.  
* Parameter %s was empty.

### \-1106 PARAM\_NOT\_REQUIRED

* A parameter was sent when not required. 

### \-1111 BAD\_PRECISION 

* The precision exceeds the maximum defined for this asset.

### \-1112 NO\_DEPTH

* No open orders for the trading pair.

### \-1114 TIF\_NOT\_REQUIRED

* TimeInForce parameter sent when not required.

### \-1115 INVALID\_TIF

* Invalid timeInForce.

### \-1116 INVALID\_ORDER\_TYPE

* Invalid orderType.

### \-1117 INVALID\_SIDE

* Invalid order side.

### \-1118 EMPTY\_NEW\_CL\_ORD\_ID

* New client order ID was empty.

### \-1119 EMPTY\_ORG\_CL\_ORD\_ID

* The client’s custom order ID is empty.

### \-1120 BAD\_INTERVAL

* Invalid time interval.

### \-1121 BAD\_SYMBOL

* Invalid trading pair.

### \-1125 INVALID\_LISTEN\_KEY

* This listenKey does not exist.

### \-1127 MORE\_THAN\_XX\_HOURS

* The query interval is too large.  
* More than %s hours between startTime and endTime.

### \-1128 OPTIONAL\_PARAMS\_BAD\_COMBO 

* Combination of optional parameters invalid. 

### \-1130 INVALID\_PARAMETER 

* The parameter sent contains invalid data.  
* Data sent for parameter %s is not valid. 

### \-1136 INVALID\_NEW\_ORDER\_RESP\_TYPE 

* Invalid newOrderRespType. 

## **20xx \- Processing Issues**

### \-2010 NEW\_ORDER\_REJECTED 

* New order rejected.

### \-2011 CANCEL\_REJECTED

* Order cancellation rejected.

### \-2013 NO\_SUCH\_ORDER

* Order does not exist.

### \-2014 BAD\_API\_KEY\_FMT

* API-key format invalid.

### \-2015 REJECTED\_MBX\_KEY

* Invalid API key, IP, or permissions for action.

### \-2016 NO\_TRADING\_WINDOW

* No trading window could be found for the symbol. Try ticker/24hrs instead.

### \-2018 BALANCE\_NOT\_SUFFICIENT

* Balance is insufficient.

### \-2020 UNABLE\_TO\_FILL

* Unable to fill.

### \-2021 ORDER\_WOULD\_IMMEDIATELY\_TRIGGER

* Order would immediately trigger.

### \-2022 REDUCE\_ONLY\_REJECT

* ReduceOnly Order is rejected.

### \-2024 POSITION\_NOT\_SUFFICIENT

* Position is not sufficient.

### \-2025 MAX\_OPEN\_ORDER\_EXCEEDED

* Reached max open order limit.

### \-2026 REDUCE\_ONLY\_ORDER\_TYPE\_NOT\_SUPPORTED

* This OrderType is not supported when reduceOnly.

## **40xx \- Filters and other Issues**

### \-4000 INVALID\_ORDER\_STATUS

* Invalid order status.

### \-4001 PRICE\_LESS\_THAN\_ZERO

* Price less than 0\.

### \-4002 PRICE\_GREATER\_THAN\_MAX\_PRICE

* Price greater than max price.

### \-4003 QTY\_LESS\_THAN\_ZERO

* Quantity less than zero.

### \-4004 QTY\_LESS\_THAN\_MIN\_QTY

* Quantity less than minimum quantity.

### \-4005 QTY\_GREATER\_THAN\_MAX\_QTY

* Quantity greater than maximum quantity.

### \-4006 STOP\_PRICE\_LESS\_THAN\_ZERO

* Stop price less than zero.

### \-4007 STOP\_PRICE\_GREATER\_THAN\_MAX\_PRICE

* Stop price greater than max price.

### \-4008 TICK\_SIZE\_LESS\_THAN\_ZERO

* Tick size less than zero.

### \-4009 MAX\_PRICE\_LESS\_THAN\_MIN\_PRICE

* Max price less than min price.

### \-4010 MAX\_QTY\_LESS\_THAN\_MIN\_QTY

* Maximum quantity less than minimum quantity.

### \-4011 STEP\_SIZE\_LESS\_THAN\_ZERO

* Step size less than zero.

### \-4012 MAX\_NUM\_ORDERS\_LESS\_THAN\_ZERO

* Maximum order quantity less than 0\.

### \-4013 PRICE\_LESS\_THAN\_MIN\_PRICE

* Price less than minimum price.

### \-4014 PRICE\_NOT\_INCREASED\_BY\_TICK\_SIZE

* Price not increased by tick size.

### \-4015 INVALID\_CL\_ORD\_ID\_LEN

* Client order ID is not valid.  
* Client order ID length should not be more than 36 characters.

### \-4016 PRICE\_HIGHTER\_THAN\_MULTIPLIER\_UP

* Price is higher than mark price multiplier cap.

### \-4017 MULTIPLIER\_UP\_LESS\_THAN\_ZERO

* Multiplier up less than zero.

### \-4018 MULTIPLIER\_DOWN\_LESS\_THAN\_ZERO

* Multiplier down less than zero.

### \-4019 COMPOSITE\_SCALE\_OVERFLOW

* Composite scale too large.

### \-4020 TARGET\_STRATEGY\_INVALID

* Target strategy invalid for orderType %s, reduceOnly %b'

### \-4021 INVALID\_DEPTH\_LIMIT

* Invalid depth limit.  
* %s is not a valid depth limit.

### \-4022 WRONG\_MARKET\_STATUS

* Market status sent is not valid.

### \-4023 QTY\_NOT\_INCREASED\_BY\_STEP\_SIZE

* The increment of the quantity is not a multiple of the step size.

### \-4024 PRICE\_LOWER\_THAN\_MULTIPLIER\_DOWN

* Price is lower than mark price multiplier floor.

### \-4025 MULTIPLIER\_DECIMAL\_LESS\_THAN\_ZERO

* Multiplier decimal less than zero.

### \-4026 COMMISSION\_INVALID

* Commission invalid.  
* Incorrect profit value.  
* `%s` less than zero.  
* `%s` absolute value greater than `%s`.

### \-4027 INVALID\_ACCOUNT\_TYPE

* Invalid account type.

### \-4029 INVALID\_TICK\_SIZE\_PRECISION

* Tick size precision is invalid.  
* Price decimal precision is incorrect.

### \-4030 INVALID\_STEP\_SIZE\_PRECISION

* The number of decimal places for the step size is incorrect.

### \-4031 INVALID\_WORKING\_TYPE

* Invalid parameter working type: `%s`

### \-4032 EXCEED\_MAX\_CANCEL\_ORDER\_SIZE

* Exceeds the maximum order quantity that can be canceled.  
* Invalid parameter working type: `%s`

### \-4044 INVALID\_BALANCE\_TYPE

* The balance type is incorrect.

### \-4045 MAX\_STOP\_ORDER\_EXCEEDED

* Reached the stop-loss order limit.

### \-4055 AMOUNT\_MUST\_BE\_POSITIVE

* The quantity must be a positive integer.

### \-4056 INVALID\_API\_KEY\_TYPE

* The API key type is invalid.

### \-4057 INVALID\_RSA\_PUBLIC\_KEY

* The API key is invalid.

### \-4058 MAX\_PRICE\_TOO\_LARGE

* maxPrice and priceDecimal too large, please check.

### \-4060 INVALID\_POSITION\_SIDE

* Invalid position side.

### \-4061 POSITION\_SIDE\_NOT\_MATCH

* The order's position direction does not match the user’s settings.

### \-4062 REDUCE\_ONLY\_CONFLICT

* Invalid or improper reduceOnly value.

### \-4084 UPCOMING\_METHOD

* Method is not allowed currently. Coming soon.

### \-4086 INVALID\_PRICE\_SPREAD\_THRESHOLD

* Invalid price spread threshold.

### \-4087 REDUCE\_ONLY\_ORDER\_PERMISSION

* Users can only place reduce-only orders.

### \-4088 NO\_PLACE\_ORDER\_PERMISSION

* User cannot place orders currently.

### \-4114 INVALID\_CLIENT\_TRAN\_ID\_LEN

* clientTranId is not valid.  
* The customer's tranId length should be less than 64 characters.

### \-4115 DUPLICATED\_CLIENT\_TRAN\_ID

* clientTranId is duplicated.  
* The client's tranId should be unique within 7 days.

### \-4118 REDUCE\_ONLY\_MARGIN\_CHECK\_FAILED

* ReduceOnly Order failed. Please check your existing position and open orders

### \-4131 MARKET\_ORDER\_REJECT

* The counterparty's best price does not meet the PERCENT\_PRICE filter limit.

### \-4135 INVALID\_ACTIVATION\_PRICE

* Invalid activation price.

### \-4137 QUANTITY\_EXISTS\_WITH\_CLOSE\_POSITION

* Quantity must be zero when closePosition is true.

### \-4138 REDUCE\_ONLY\_MUST\_BE\_TRUE

* Reduce only must be true when closePosition is true.

### \-4139 ORDER\_TYPE\_CANNOT\_BE\_MKT

* Order type cannot be a market order if it cannot be canceled.

### \-4140 INVALID\_OPENING\_POSITION\_STATUS

* Invalid symbol status for opening position.

### \-4141 SYMBOL\_ALREADY\_CLOSED

* Trading pair has been delisted.

### \-4142 STRATEGY\_INVALID\_TRIGGER\_PRICE

* Rejected: Take Profit or Stop order would be triggered immediately.

### \-4164 MIN\_NOTIONAL

* Order notional must be at least 5.0 (unless you select Reduce Only)  
* Order notional must be no smaller than %s (unless you choose Reduce Only)

### \-4165 INVALID\_TIME\_INTERVAL

* Invalid time interval  
* Maximum time interval is %s days

### \-4183 PRICE\_HIGHTER\_THAN\_STOP\_MULTIPLIER\_UP

* Limit price cannot be higher than the cap of %s.  
* Take-Profit/Stop-Loss price cannot be higher than the cap of %s.

### \-4184 PRICE\_LOWER\_THAN\_STOP\_MULTIPLIER\_DOWN

* Price is below the stop price limit.  
* Take-Profit/Stop-Loss price must be above the trigger price × multiplier floor.  
* Order price (limit or TP/SL) can’t be below %s.
