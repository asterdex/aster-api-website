## **Create / Approve Agent (TRADE)**

> **Response:**
```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/approveAgent



Create/approve an Agent/API Wallet (signer) for programmatic trading, and configure permissions, IP whitelist, and expiry time.

Optionally, one or both Builder authorizations (Futures Builder and/or Spot Builder) can be set together with Agent approval in a single request, or they can be authorized separately after Agent approval.

**Weight:**

TBD

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                                                            |
| --------------- | -------- | ------------- | ------------------------------------------------------------------------------------------ |
| user            | STRING   | YES           | User main address                                                                          |
| nonce           | LONG     | YES           | Anti-replay nonce (microseconds-style)                                                     |
| signature       | STRING   | YES           | EIP-712 signature (primaryType=ApproveAgent)                                               |
| agentName       | STRING   | YES           | Agent name (label)                                                                         |
| agentAddress    | STRING   | YES           | Agent address (signer address)                                                             |
| ipWhitelist     | STRING   | NO            | IP whitelist (recommended: Builder backend egress IP)                                      |
| expired         | LONG     | YES           | Expiry timestamp (ms)                                                                      |
| canSpotTrade    | BOOLEAN  | YES           | Whether spot trading is allowed                                                            |
| canPerpTrade    | BOOLEAN  | YES           | Whether perp trading is allowed                                                            |
| canWithdraw     | BOOLEAN  | YES           | Whether withdrawals are allowed (recommended default false)                                 |
| builder         | STRING   | NO            | **Futures Builder** address. If provided, simultaneously authorizes this Futures Builder.  |
| maxFeeRate      | STRING   | NO            | **Futures Builder** maximum fee rate cap. Required when `builder` is provided.             |
| builderName     | STRING   | NO            | **Futures Builder** name.                                                                  |
| spotBuilder     | STRING   | NO            | **Spot Builder** address. If provided, simultaneously authorizes this Spot Builder.        |
| maxSpotFeeRate  | STRING   | NO            | **Spot Builder** maximum fee rate cap. Required when `spotBuilder` is provided.            |
| spotBuilderName | STRING   | NO            | **Spot Builder** name.                                                                     |

> **Note:** `builder` / `maxFeeRate` / `builderName` apply to the **Futures** Builder. `spotBuilder` / `maxSpotFeeRate` / `spotBuilderName` apply to the **Spot** Builder. They are independent and can be configured separately or together.




------





## **Update Agent (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/updateAgent



Update Agent permissions and IP whitelist.



**Weight:**

TBD



**Parameters:**

| **Name**     | **Type** | **Mandatory** | **Description**                             |
| ------------ | -------- | ------------- | ------------------------------------------- |
| user         | STRING   | YES           | User main address                           |
| nonce        | LONG     | YES           | Anti-replay nonce                           |
| signature    | STRING   | YES           | EIP-712 signature (primaryType=UpdateAgent) |
| agentAddress | STRING   | YES           | Agent address (signer address)              |
| ipWhitelist  | STRING   | NO            | IP whitelist (can be empty)                 |
| canSpotTrade | BOOLEAN  | YES           | Whether spot trading is allowed             |
| canPerpTrade | BOOLEAN  | YES           | Whether perp trading is allowed             |
| canWithdraw  | BOOLEAN  | YES           | Whether withdrawals are allowed             |



------





## **Delete Agent (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

DELETE /fapi/v3/agent



Revoke Agent authorization (the signer can no longer be used for API trading).



**Weight:**

TBD



**Parameters:**

| **Name**     | **Type** | **Mandatory** | **Description**                          |
| ------------ | -------- | ------------- | ---------------------------------------- |
| user         | STRING   | YES           | User main address                        |
| nonce        | LONG     | YES           | Anti-replay nonce                        |
| signature    | STRING   | YES           | EIP-712 signature (primaryType=DelAgent) |
| agentAddress | STRING   | YES           | Agent address (signer address)           |



------





## **Get Agents (USER_DATA)**





> **Response:**

```
[
  {
    "agentAddress": "0x2610D3935A008036AF0AE12D014C8904b75fC5E9",
    "agentName": "ross0001",
    "ipWhitelist": "",
    "expired": 1867945395040,
    "source": "builder",
    "canRead": true,
    "canSpotTrade": true,
    "canPerpTrade": true,
    "canWithdraw": false
  }
]
```

GET /fapi/v3/agent



Query the list of Agents authorized by the user.



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                                         |
| --------- | -------- | ------------- | ------------------------------------------------------- |
| user      | STRING   | YES           | User main address                                       |
| signer    | STRING   | YES           | Any authorized signer (used to sign this query request) |
| nonce     | LONG     | YES           | Anti-replay nonce                                       |
| signature | STRING   | YES           | EIP-712 signature (sign querystring, Message.msg)       |



------





## **Approve Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/approveBuilder



User approves a builder and sets the maximum fee rate cap maxFeeRate.



**Weight:**

TBD



**Parameters:**

| **Name**   | **Type** | **Mandatory** | **Description**                                           |
| ---------- | -------- | ------------- | --------------------------------------------------------- |
| user       | STRING   | YES           | User main address                                         |
| nonce      | LONG     | YES           | Anti-replay nonce                                         |
| signature  | STRING   | YES           | EIP-712 signature (primaryType=ApproveBuilder)            |
| builder    | STRING   | YES           | Builder address (attribution/fee recipient)               |
| maxFeeRate | STRING   | YES           | Max fee rate cap (recommended to pass decimals as string) |



------





## **Update Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/updateBuilder



Update the builder’s maximum fee rate cap.



**Weight:**

TBD



**Parameters:**

| **Name**   | **Type** | **Mandatory** | **Description**                               |
| ---------- | -------- | ------------- | --------------------------------------------- |
| user       | STRING   | YES           | User main address                             |
| nonce      | LONG     | YES           | Anti-replay nonce                             |
| signature  | STRING   | YES           | EIP-712 signature (primaryType=UpdateBuilder) |
| builder    | STRING   | YES           | Builder address                               |
| maxFeeRate | STRING   | YES           | New max fee rate cap                          |



------





## **Delete Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

DELETE /fapi/v3/builder



Revoke authorization for a builder.



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                            |
| --------- | -------- | ------------- | ------------------------------------------ |
| user      | STRING   | YES           | User main address                          |
| nonce     | LONG     | YES           | Anti-replay nonce                          |
| signature | STRING   | YES           | EIP-712 signature (primaryType=DelBuilder) |
| builder   | STRING   | YES           | Builder address                            |



------





## **Get Builders (USER_DATA)**





> **Response:**

```
[
  {
    "userAddress": "0x6b5B34BB0B4Fe40bc38B2460376ADDdD36B30D47",
    "builderAddress": "0xc2af13e1B1de3A015252A115309A0F9DEEDCFa0A",
    "maxFeeRate": 0.00001,
    "builderName": "ivan3"
  }
]
```

GET /fapi/v3/builder



Query the list of approved builders and their maxFeeRate.



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                                         |
| --------- | -------- | ------------- | ------------------------------------------------------- |
| user      | STRING   | YES           | User main address                                       |
| signer    | STRING   | YES           | Any authorized signer (used to sign this query request) |
| nonce     | LONG     | YES           | Anti-replay nonce                                       |
| signature | STRING   | YES           | EIP-712 signature (sign querystring, Message.msg)       |



------





## **Place Order with Builder Code (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success",
  "data": {
    "orderId": "<TBD>",
    "status": "<TBD>"
  }
}
```

POST /fapi/v3/order 



Place an order where the signer represents the user, and attach builder + feeRate for attribution and builder fee charging.



**Rules:**



- feeRate must be <= maxFeeRate (the user-approved cap for that builder), otherwise it should be rejected.
- This request must be signed using the signer private key (trading signature mode).





**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                                   |
| --------- | -------- | ------------- | ------------------------------------------------- |
| user      | STRING   | YES           | User main address                                 |
| signer    | STRING   | YES           | Agent address (signer)                            |
| nonce     | LONG     | YES           | Anti-replay nonce                                 |
| signature | STRING   | YES           | EIP-712 signature (sign querystring, Message.msg) |
| symbol    | STRING   | YES           | Symbol (e.g., BTCUSDT)                            |
| type      | STRING   | YES           | Order type (e.g., MARKET/LIMIT)                   |
| side      | STRING   | YES           | BUY / SELL                                        |
| quantity  | STRING   | YES           | Quantity                                          |
| builder   | STRING   | YES           | Builder address (attribution/fee recipient)       |
| feeRate   | STRING   | YES           | Fee rate for this order (recommended as string)   |

