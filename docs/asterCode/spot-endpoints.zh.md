## **授权 Spot Builder (SPOT_TRADE)**

`POST /api/v3/approveBuilder`

授权某个 Spot Builder，并设置该 Builder 被允许收取的最大费率上限（`maxFeeRate`）。

> **说明：** 本接口仅用于授权 Spot Builder。如需授权 Futures Builder，请使用 `POST /fapi/v3/approveBuilder`。

**Weight:** 5

**签名方：** 用户主钱包 — 本接口为管理类接口，请求中**不含** `signer` 字段。

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                             |
| --------------- | -------- | ------------- | ----------------------------------------------------------- |
| builder         | STRING   | YES           | Spot Builder 钱包地址                                       |
| maxFeeRate      | STRING   | YES           | 用户授权的最大费率上限（小数字符串）                        |
| builderName     | STRING   | NO            | Builder 名称                                                |
| asterChain      | STRING   | YES           | Aster 环境标识符                                            |
| user            | STRING   | YES           | 用户主钱包地址                                              |
| nonce           | LONG     | YES           | 防重放 nonce                                                |
| signature       | STRING   | YES           | EIP-712 签名（primaryType=ApproveBuilder）                  |
| signatureChainId| LONG     | YES           | EIP-712 domain 使用的 Chain ID                              |

**EIP-712 typed data 字段顺序：**

```
Builder, MaxFeeRate, BuilderName, AsterChain, User, Nonce
```

（除 `Nonce` 为 `uint256` 外，其余均为 `string`。）

**Response：**

成功时返回 **HTTP 200**，**响应体为空**（无 JSON）。

**常见错误：**

| 错误信息                                             | 含义                                           |
| ---------------------------------------------------- | ---------------------------------------------- |
| `Exceed max builder fee rate: <value>`               | `maxFeeRate` 超过 Aster 允许的 Spot Builder 费率上限 |
| `Builder address:<addr> is in black list`            | Builder 地址不被允许                           |
| `User address and builder address can not be the same` | 用户地址与 Builder 地址不能相同              |
| `Builder address:<addr> not registered in Aster`    | Builder 地址未在 Aster 注册                    |
| `User address:<addr> not registered in Aster`       | 用户地址未在 Aster 注册                        |
| `Builder address:<addr> ASTER balance less than <n>` | Builder 不满足最低 ASTER 余额要求             |
| `Exceed max builders per user: <n>`                  | 用户已达到可授权 Builder 数量上限              |
| `Builder name length exceed max length: <n>`         | Builder 名称超过最大长度限制                   |
| `Exist builder name:<name>`                          | Builder 名称已被使用                           |
| `User address:<addr> has no spot account`            | 相关账户不存在现货账户                         |
| `Signature check failed`                             | EIP-712 签名内容或签名参数不匹配               |

```json
// 错误示例：
{
  "code": -1130,
  "msg": "Exceed max builder fee rate: 0.005"
}
```

**Builder 前置条件：**
- 必须在 Aster 注册
- 必须拥有现货账户
- 必须满足 ASTER 最低余额要求
- 不能与用户地址相同
- 不能在黑名单中

------

## **更新 Spot Builder (SPOT_TRADE)**

`POST /api/v3/updateBuilder`

更新已有 Spot Builder 授权的最大费率上限。

> **说明：** 本接口不支持修改 `builderName`。如需管理 Futures Builder 授权，请使用 `POST /fapi/v3/updateBuilder`。

**Weight:** 5

**签名方：** 用户主钱包

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                  |
| --------------- | -------- | ------------- | ------------------------------------------------ |
| builder         | STRING   | YES           | Spot Builder 地址                                |
| maxFeeRate      | STRING   | YES           | 新的最大费率上限（小数字符串）                   |
| asterChain      | STRING   | YES           | Aster 环境标识符                                 |
| user            | STRING   | YES           | 用户主钱包地址                                   |
| nonce           | LONG     | YES           | 防重放 nonce                                     |
| signature       | STRING   | YES           | EIP-712 签名（primaryType=UpdateBuilder）        |
| signatureChainId| LONG     | YES           | EIP-712 domain 使用的 Chain ID                   |

**EIP-712 typed data 字段顺序：**

```
Builder, MaxFeeRate, AsterChain, User, Nonce
```

（除 `Nonce` 为 `uint256` 外，其余均为 `string`。）

**Response：**

成功时返回 **HTTP 200**，**响应体为空**。

**错误示例：**

```json
{
  "code": -1130,
  "msg": "No builder found"
}
```

------

## **删除 Spot Builder (SPOT_TRADE)**

`DELETE /api/v3/delBuilder`

撤销指定 Spot Builder 的授权。

> **重要：** Spot 和 Futures 使用**不同的** Builder 删除接口。
> - Spot：`DELETE /api/v3/delBuilder`
> - Futures：`DELETE /fapi/v3/builder`

**Weight:** 5

**签名方：** 用户主钱包

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                   |
| --------------- | -------- | ------------- | ------------------------------------------------- |
| builder         | STRING   | YES           | 待撤销的 Spot Builder 地址                        |
| asterChain      | STRING   | YES           | Aster 环境标识符                                  |
| user            | STRING   | YES           | 用户主钱包地址                                    |
| nonce           | LONG     | YES           | 防重放 nonce                                      |
| signature       | STRING   | YES           | EIP-712 签名（primaryType=DelBuilder）            |
| signatureChainId| LONG     | YES           | EIP-712 domain 使用的 Chain ID                    |

**EIP-712 typed data 字段顺序：**

```
Builder, AsterChain, User, Nonce
```

（除 `Nonce` 为 `uint256` 外，其余均为 `string`。）

**Response：**

成功时返回 **HTTP 200**，**响应体为空**。

**错误示例：**

```json
{
  "code": -1130,
  "msg": "No builder found"
}
```

------

## **查询 Spot Builders (USER_DATA)**

`GET /api/v3/builder`

返回用户已授权的 Spot Builder 列表及各 Builder 的 `maxFeeRate`。

**Weight:** 5

**签名方：** 已授权的 API Wallet / Agent（使用 Agent 私钥，请求中包含 `signer`）

> **说明：** GET 请求不要设置 `Content-Type: application/x-www-form-urlencoded`，否则可能返回 HTTP 500 错误。

**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                                       |
| --------- | -------- | ------------- | ----------------------------------------------------- |
| user      | STRING   | YES           | 用户主钱包地址                                        |
| signer    | STRING   | YES           | 已授权的 API Wallet / Agent 地址                      |
| nonce     | LONG     | YES           | 防重放 nonce                                          |
| signature | STRING   | YES           | EIP-712 签名（Message.msg — 签 query string）         |

**签名方式：** 按参数顺序构造完整 query string，作为 `Message.msg` 签名：

```javascript
const msg = `user=${user}&signer=${signer}&nonce=${nonce}`;
// 使用 EIP-712 Message 类型签名，chainId 主网 1666 / 测试网 714
// 最终追加：&signature=<signature>
```

**Response：**

返回**裸 JSON 数组**（无包装对象）。若用户未授权任何 Spot Builder，返回空数组 `[]`。

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

**响应字段：**

| 字段           | JSON 类型  | 描述                                                         |
| -------------- | ---------- | ------------------------------------------------------------ |
| userAddress    | string     | 用户主钱包地址                                               |
| builderAddress | string     | 已授权的 Spot Builder 地址                                   |
| maxFeeRate     | **number** | 最大授权费率（**JSON number**，非 string）                   |
| builderName    | string     | Builder 名称；未设置名称时返回 `""`                          |

> **重要：** `maxFeeRate` 在响应中是 **JSON number**，而在请求/签名中是 **string**，两者类型不同，请注意区分。

------

## **Spot 带 Builder 下单 (SPOT_TRADE)**

`POST /api/v3/order`

代用户下现货单，可附带 Builder 归因和费率参数。

**Weight:** 1

**签名方：** 已授权的 API Wallet / Agent

**Parameters：**

标准现货下单参数（参见 Spot V3 — Place Order）基础上，以下参数用于 Builder 归因：

| **Name** | **Type** | **Mandatory**               | **Description**                                                      |
| -------- | -------- | --------------------------- | -------------------------------------------------------------------- |
| builder  | STRING   | NO                          | 已授权的 Spot Builder 地址                                           |
| feeRate  | STRING   | 传入 `builder` 时必须提供   | 本单适用的 Builder 费率（小数字符串）                                |

**携带 Builder 时的校验规则：**

1. 该 `builder` 与 `user` 之间必须存在有效的 Spot Builder 授权记录。
2. 必须提供 `feeRate`。
3. `feeRate` 不得超过用户对该 Builder 授权的 `maxFeeRate` 上限。
4. `feeRate` 必须满足 Aster 的全局费率范围和精度要求。

**签名方式：** EIP-712 `Message.msg` 方案（Agent 签名）。构造完整 query string（所有参数，除 `signature` 外），对其进行签名：

```javascript
// 示例（简化）：
const msg = `user=${user}&signer=${signer}&nonce=${nonce}&symbol=ASTERUSDT&side=SELL&type=LIMIT&...&builder=${builder}&feeRate=${feeRate}`;
```

> **关键规则：** 签名串必须与实际请求的 query string 逐字节一致（仅排除 `signature` 参数本身），参数顺序必须保持一致。

**Response：**

直接返回订单结果。LIMIT 与 MARKET 订单使用相同的响应结构。

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

**响应字段：**

| 字段          | JSON 类型  | 是否必定存在                      | 描述                                                       |
| ------------- | ---------- | --------------------------------- | ---------------------------------------------------------- |
| orderId       | **number** | YES                               | 订单 ID                                                    |
| symbol        | string     | YES                               | 交易对                                                     |
| status        | string     | YES                               | 订单状态                                                   |
| clientOrderId | string     | YES                               | 客户端订单 ID                                              |
| price         | string     | YES                               | 订单价格                                                   |
| avgPrice      | string     | YES                               | 平均成交价                                                 |
| origQty       | string     | YES                               | 原始数量                                                   |
| executedQty   | string     | YES                               | 已成交数量                                                 |
| cumQty        | string     | 正常下单路径通常存在              | 累计成交数量                                               |
| cumQuote      | string     | YES                               | 累计成交金额（计价货币）                                   |
| timeInForce   | string     | YES                               | 有效时间类型                                               |
| type          | string     | YES                               | 订单类型                                                   |
| side          | string     | YES                               | 买卖方向                                                   |
| stopPrice     | string     | YES                               | 止损价格                                                   |
| origType      | string     | YES                               | 原始订单类型                                               |
| activatePrice | string     | NO（仅追踪止损订单）              | 追踪止损激活价格                                           |
| priceRate     | string     | NO（仅追踪止损订单）              | 追踪止损回调比率                                           |
| time          | **number** | NO（正常下单路径通常不返回）      | 订单创建时间                                               |
| updateTime    | **number** | YES                               | 最后更新时间（毫秒）                                       |
| orderListId   | **number** | YES                               | 订单列表 ID；不属于任何订单列表时返回 `-1`                 |

> **说明：**
> - `orderId`、`time`、`updateTime`、`orderListId` 为 **JSON number**；其余数值型字段（如 `price`、`avgPrice`、`origQty` 等）均为 **JSON string**。
> - 响应中**不包含** `fills` 数组，无论 `newOrderRespType` 如何设置。这与 Binance Spot API 行为不同。
> - `builder` 和 `feeRate` 仅为请求参数，**不会**出现在订单响应中。

**错误示例：**

```json
// 传入 builder 但未提供 feeRate：
{
  "code": -1102,
  "msg": "Mandatory parameter 'feeRate' was not sent, was empty/null, or malformed."
}
```

**Builder 相关常见错误：**

| 场景                                  | 错误 / 含义                             |
| ------------------------------------- | --------------------------------------- |
| 传入 `builder` 但未提供 `feeRate`     | feeRate 缺失或无效（`-1102`）           |
| `feeRate` 超出 Aster 允许范围         | feeRate 无效                            |
| `feeRate` 精度超出允许范围            | feeRate 无效                            |
| Builder 授权记录不存在                | Builder not found                       |
| `feeRate` > `maxFeeRate`              | 费率超过用户授权上限                    |
| Agent 未授权或无效                    | No agent found                          |
| Nonce 与服务器时间偏差过大            | `NONCE_FAR_AWAY_FROM_NOW`               |

------
