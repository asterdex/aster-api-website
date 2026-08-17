## **Approve Spot Builder (SPOT_TRADE)**

`POST /api/v3/approveBuilder`

Authorizes a Spot Builder and sets the maximum fee rate cap (`maxFeeRate`) that the Builder is allowed to charge on Spot orders placed on behalf of the user.

> **Note:** This endpoint is for Spot Builder authorization only. To authorize a Futures Builder, use `POST /fapi/v3/approveBuilder`.

**Weight:** 5

**Signer:** User main wallet — this is a management request. Do **not** include `signer` in the request.

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                                       |
| --------------- | -------- | ------------- | --------------------------------------------------------------------- |
| builder         | STRING   | YES           | Spot Builder wallet address                                           |
| maxFeeRate      | STRING   | YES           | Maximum fee rate the Builder is authorized to charge (decimal string) |
| builderName     | STRING   | NO            | Builder name                                                          |
| asterChain      | STRING   | YES           | Aster environment identifier                                          |
| user            | STRING   | YES           | User main wallet address                                              |
| nonce           | LONG     | YES           | Anti-replay nonce                                                     |
| signature       | STRING   | YES           | EIP-712 signature (primaryType=ApproveBuilder)                        |
| signatureChainId| LONG     | YES           | Chain ID used in the EIP-712 domain                                   |

**EIP-712 typed data field order:**

```
Builder, MaxFeeRate, BuilderName, AsterChain, User, Nonce
```

(All `string` except `Nonce` which is `uint256`.)

**Response:**

A successful request returns **HTTP 200** with a **zero-byte response body** (no JSON).

**Common errors:**

| Error message                                        | Meaning                                                               |
| ---------------------------------------------------- | --------------------------------------------------------------------- |
| `Exceed max builder fee rate: <value>`               | Requested `maxFeeRate` exceeds Aster's applicable Spot Builder fee limit |
| `Builder address:<addr> is in black list`            | Builder address is not allowed                                        |
| `User address and builder address can not be the same` | User and Builder addresses must be different                        |
| `Builder address:<addr> not registered in Aster`    | Builder address is not registered                                     |
| `User address:<addr> not registered in Aster`       | User address is not registered                                        |
| `Builder address:<addr> ASTER balance less than <n>` | Builder does not meet the minimum ASTER balance requirement           |
| `Exceed max builders per user: <n>`                  | User has reached the maximum number of authorized Builders            |
| `Builder name length exceed max length: <n>`         | Builder name exceeds the allowed length                               |
| `Exist builder name:<name>`                          | Builder name is already in use                                        |
| `User address:<addr> has no spot account`            | The relevant account does not have a Spot account                     |
| `Signature check failed`                             | EIP-712 payload or signing parameters do not match                    |

```json
// Error example:
{
  "code": -1130,
  "msg": "Exceed max builder fee rate: 0.005"
}
```

**Builder preconditions:**
- Must be registered in Aster
- Must have a Spot account
- Must satisfy the applicable ASTER balance requirement
- Must not equal the user address
- Must not be blacklisted

------

## **Update Spot Builder (SPOT_TRADE)**

`POST /api/v3/updateBuilder`

Updates the maximum fee rate cap for an existing Spot Builder authorization.

> **Note:** This endpoint does not support changing `builderName`. To manage Futures Builder authorizations, use `POST /fapi/v3/updateBuilder`.

**Weight:** 5

**Signer:** User main wallet

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                              |
| --------------- | -------- | ------------- | ------------------------------------------------------------ |
| builder         | STRING   | YES           | Spot Builder address                                         |
| maxFeeRate      | STRING   | YES           | New maximum fee rate cap (decimal string)                    |
| asterChain      | STRING   | YES           | Aster environment identifier                                 |
| user            | STRING   | YES           | User main wallet address                                     |
| nonce           | LONG     | YES           | Anti-replay nonce                                            |
| signature       | STRING   | YES           | EIP-712 signature (primaryType=UpdateBuilder)                |
| signatureChainId| LONG     | YES           | Chain ID used in the EIP-712 domain                          |

**EIP-712 typed data field order:**

```
Builder, MaxFeeRate, AsterChain, User, Nonce
```

(All `string` except `Nonce` which is `uint256`.)

**Response:**

A successful request returns **HTTP 200** with a **zero-byte response body**.

**Error example:**

```json
{
  "code": -1130,
  "msg": "No builder found"
}
```

------

## **Delete Spot Builder (SPOT_TRADE)**

`DELETE /api/v3/delBuilder`

Revokes the specified Spot Builder authorization.

> **Important:** Spot and Futures use **different** Builder deletion endpoints.
> - Spot: `DELETE /api/v3/delBuilder`
> - Futures: `DELETE /fapi/v3/builder`

**Weight:** 5

**Signer:** User main wallet

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                        |
| --------------- | -------- | ------------- | ------------------------------------------------------ |
| builder         | STRING   | YES           | Spot Builder address to revoke                         |
| asterChain      | STRING   | YES           | Aster environment identifier                           |
| user            | STRING   | YES           | User main wallet address                               |
| nonce           | LONG     | YES           | Anti-replay nonce                                      |
| signature       | STRING   | YES           | EIP-712 signature (primaryType=DelBuilder)             |
| signatureChainId| LONG     | YES           | Chain ID used in the EIP-712 domain                    |

**EIP-712 typed data field order:**

```
Builder, AsterChain, User, Nonce
```

(All `string` except `Nonce` which is `uint256`.)

**Response:**

A successful request returns **HTTP 200** with a **zero-byte response body**.

**Error example:**

```json
{
  "code": -1130,
  "msg": "No builder found"
}
```

------

## **Get Spot Builders (USER_DATA)**

`GET /api/v3/builder`

Returns the list of Spot Builders authorized by the user, along with each Builder's `maxFeeRate`.

**Weight:** 5

**Signer:** Authorized API Wallet / Agent (uses Agent private key, includes `signer` in request)

> **Note:** For GET requests, do not set `Content-Type: application/x-www-form-urlencoded`. Doing so may result in an HTTP 500 error.

**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                                       |
| --------- | -------- | ------------- | ----------------------------------------------------- |
| user      | STRING   | YES           | User main wallet address                              |
| signer    | STRING   | YES           | Authorized API Wallet / Agent address                 |
| nonce     | LONG     | YES           | Anti-replay nonce                                     |
| signature | STRING   | YES           | EIP-712 signature (Message.msg — sign the query string) |

**Signing:** Construct the query string in the exact parameter order, sign it as `Message.msg`:

```javascript
const msg = `user=${user}&signer=${signer}&nonce=${nonce}`;
// Sign using EIP-712 Message type with chainId 1666 (mainnet) or 714 (testnet)
// Append: &signature=<signature>
```

**Response:**

Returns a **bare JSON array** (no wrapper object). Returns an empty array `[]` if no Spot Builder has been authorized.

```json
[
  {
    "userAddress": "0xYourUserAddress",
    "builderAddress": "0xYourBuilderAddress",
    "maxFeeRate": 0.00001,
    "builderName": "myBuilder"
  }
]
```

**Response fields:**

| Field          | JSON Type | Description                                                        |
| -------------- | --------- | ------------------------------------------------------------------ |
| userAddress    | string    | User main wallet address                                           |
| builderAddress | string    | Authorized Spot Builder address                                    |
| maxFeeRate     | **number**| Maximum authorized Builder fee rate (**JSON number**, not string)  |
| builderName    | string    | Builder name; returns `""` when no name was provided               |

> **Important:** `maxFeeRate` is a **JSON number** in the response, even though it is passed as a **string** in the request/signature.

------

## **Place Spot Order with Builder (SPOT_TRADE)**

`POST /api/v3/order`

Places a Spot order on behalf of the user, with optional Builder attribution and fee parameters.

**Weight:** 1

**Signer:** Authorized API Wallet / Agent

**Parameters:**

Standard Spot order parameters apply (see Spot V3 — Place Order). The following additional parameters enable Builder attribution:

| **Name** | **Type** | **Mandatory**               | **Description**                                                               |
| -------- | -------- | --------------------------- | ----------------------------------------------------------------------------- |
| builder  | STRING   | NO                          | Authorized Spot Builder address                                                |
| feeRate  | STRING   | Required when `builder` is provided | Builder fee rate applied to this order (decimal string)            |

**Builder validation rules (when `builder` is provided):**

1. A Spot Builder authorization must exist for this `builder` and `user` pair.
2. `feeRate` must be provided.
3. `feeRate` must not exceed the user's authorized `maxFeeRate` for this Builder.
4. `feeRate` must be within Aster's allowed fee-rate range and decimal precision.

**Signing:** EIP-712 `Message.msg` scheme (Agent signing). Construct the full query string (all parameters excluding `signature`) and sign it:

```javascript
// Example (simplified):
const msg = `user=${user}&signer=${signer}&nonce=${nonce}&symbol=ASTERUSDT&side=SELL&type=LIMIT&...&builder=${builder}&feeRate=${feeRate}`;
```

> **Key rule:** The signed string must match the actual request query string byte-for-byte, excluding only `signature`. Parameter order must be preserved.

**Response:**

Returns the order result directly. LIMIT and MARKET orders use the same response structure.

```json
{
  "orderId": 123456789,
  "symbol": "ASTERUSDT",
  "status": "NEW",
  "clientOrderId": "web_xxxxxxxx",
  "price": "0.6",
  "avgPrice": "0",
  "origQty": "20",
  "executedQty": "0",
  "cumQty": "0",
  "cumQuote": "0",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "SELL",
  "stopPrice": "0",
  "origType": "LIMIT",
  "updateTime": 1787000000123,
  "orderListId": -1
}
```

**Response fields:**

| Field         | JSON Type | Always Present                        | Description                                              |
| ------------- | --------- | ------------------------------------- | -------------------------------------------------------- |
| orderId       | **number**| YES                                   | Order ID                                                 |
| symbol        | string    | YES                                   | Trading symbol                                           |
| status        | string    | YES                                   | Order status                                             |
| clientOrderId | string    | YES                                   | Client order ID                                          |
| price         | string    | YES                                   | Order price                                              |
| avgPrice      | string    | YES                                   | Average execution price                                  |
| origQty       | string    | YES                                   | Original quantity                                        |
| executedQty   | string    | YES                                   | Executed quantity                                        |
| cumQty        | string    | Usually present on the order path     | Cumulative executed quantity                             |
| cumQuote      | string    | YES                                   | Cumulative quote quantity                                |
| timeInForce   | string    | YES                                   | Time in force                                            |
| type          | string    | YES                                   | Order type                                               |
| side          | string    | YES                                   | Order side                                               |
| stopPrice     | string    | YES                                   | Stop price                                               |
| origType      | string    | YES                                   | Original order type                                      |
| activatePrice | string    | NO (trailing-stop orders only)        | Trailing-stop activation price                           |
| priceRate     | string    | NO (trailing-stop orders only)        | Trailing-stop callback rate                              |
| time          | **number**| NO (not present on normal new-order path) | Order creation time                                  |
| updateTime    | **number**| YES                                   | Last update timestamp (ms)                               |
| orderListId   | **number**| YES                                   | Order list ID; `-1` when not associated with a list      |

> **Notes:**
> - `orderId`, `time`, `updateTime`, and `orderListId` are **JSON numbers**. All other numeric-valued fields (e.g., `price`, `avgPrice`, `origQty`) are **JSON strings**.
> - The response does **not** include a `fills` array, regardless of `newOrderRespType`. This is different from Binance Spot API behavior.
> - `builder` and `feeRate` are request-only parameters and are **not** included in the order response.

**Error examples:**

```json
// builder provided but feeRate missing:
{
  "code": -1102,
  "msg": "Mandatory parameter 'feeRate' was not sent, was empty/null, or malformed."
}
```

**Common Builder-related errors:**

| Condition                                    | Error / Meaning                              |
| -------------------------------------------- | -------------------------------------------- |
| `builder` provided without `feeRate`         | Invalid/missing feeRate (`-1102`)            |
| `feeRate` outside Aster's allowed range      | Invalid feeRate                              |
| `feeRate` precision exceeds allowed scale    | Invalid feeRate                              |
| Builder authorization does not exist         | Builder not found                            |
| `feeRate` > `maxFeeRate`                     | Fee rate exceeds user's authorization        |
| Agent is not valid/authorized                | No agent found                               |
| Nonce is too far from server time            | `NONCE_FAR_AWAY_FROM_NOW`                    |

------
