## **创建/授权 Agent (TRADE)**

> **Response:**
```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/approveAgent 



创建/授权一个 Agent/API Wallet（signer）用于程序化交易，并配置权限、IP 白名单、过期时间。

可选地，Futures Builder 和/或 Spot Builder 的授权可以在授权 Agent 时一并完成，也可以在 Agent 授权后单独进行。

**Weight:**

TBD

**Parameters:**

| **Name**        | **Type** | **Mandatory** | **Description**                                                                               |
| --------------- | -------- | ------------- | --------------------------------------------------------------------------------------------- |
| user            | STRING   | YES           | 用户主地址                                                                                    |
| nonce           | LONG     | YES           | 防重放 nonce（微秒格式）                                                                      |
| signature       | STRING   | YES           | EIP-712 签名（primaryType=ApproveAgent）                                                      |
| agentName       | STRING   | YES           | Agent 名称（label）                                                                           |
| agentAddress    | STRING   | YES           | Agent 地址（signer 地址）                                                                     |
| ipWhitelist     | STRING   | NO            | 允许调用的 IP 白名单（建议配置 Builder 后端出口 IP）                                          |
| expired         | LONG     | YES           | 过期时间戳（毫秒）                                                                            |
| canSpotTrade    | BOOLEAN  | YES           | 是否允许现货交易                                                                              |
| canPerpTrade    | BOOLEAN  | YES           | 是否允许合约交易                                                                              |
| canWithdraw     | BOOLEAN  | YES           | 是否允许提现（建议默认 false）                                                                |
| builder         | STRING   | NO            | **Futures Builder** 地址。若传入，则同时授权该 Futures Builder。                              |
| maxFeeRate      | STRING   | NO            | **Futures Builder** 最大费率上限。传入 `builder` 时必须提供。                                 |
| builderName     | STRING   | NO            | **Futures Builder** 名称。                                                                    |
| spotBuilder     | STRING   | NO            | **Spot Builder** 地址。若传入，则同时授权该 Spot Builder。                                    |
| maxSpotFeeRate  | STRING   | NO            | **Spot Builder** 最大费率上限。传入 `spotBuilder` 时必须提供。                                |
| spotBuilderName | STRING   | NO            | **Spot Builder** 名称。                                                                       |

> **说明：** `builder` / `maxFeeRate` / `builderName` 适用于 **Futures Builder**；`spotBuilder` / `maxSpotFeeRate` / `spotBuilderName` 适用于 **Spot Builder**，二者相互独立，可分别或同时配置。



------





## **更新 Agent (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/updateAgent 



更新 Agent 的权限与 IP 白名单。



**Weight:**

TBD



**Parameters:**

| **Name**     | **Type** | **Mandatory** | **Description**                         |
| ------------ | -------- | ------------- | --------------------------------------- |
| user         | STRING   | YES           | 用户主地址                              |
| nonce        | LONG     | YES           | 防重放 nonce                            |
| signature    | STRING   | YES           | EIP-712 签名（primaryType=UpdateAgent） |
| agentAddress | STRING   | YES           | Agent 地址（signer 地址）               |
| ipWhitelist  | STRING   | NO            | IP 白名单（可置空）                     |
| canSpotTrade | BOOLEAN  | YES           | 是否允许现货交易                        |
| canPerpTrade | BOOLEAN  | YES           | 是否允许合约交易                        |
| canWithdraw  | BOOLEAN  | YES           | 是否允许提现                            |



------





## **删除 Agent (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

DELETE /fapi/v3/agent 



撤销 Agent 授权（使该 signer 不再可用于 API 交易）。



**Weight:**

TBD



**Parameters:**

| **Name**     | **Type** | **Mandatory** | **Description**                      |
| ------------ | -------- | ------------- | ------------------------------------ |
| user         | STRING   | YES           | 用户主地址                           |
| nonce        | LONG     | YES           | 防重放 nonce                         |
| signature    | STRING   | YES           | EIP-712 签名（primaryType=DelAgent） |
| agentAddress | STRING   | YES           | Agent 地址（signer 地址）            |



------





## **查询 Agents (USER_DATA)**





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



查询用户已授权的 Agent 列表。



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                             |
| --------- | -------- | ------------- | ------------------------------------------- |
| user      | STRING   | YES           | 用户主地址                                  |
| signer    | STRING   | YES           | 任一已授权 signer（用于签名本次查询请求）   |
| nonce     | LONG     | YES           | 防重放 nonce                                |
| signature | STRING   | YES           | EIP-712 签名（签 querystring，Message.msg） |



------





## **授权 Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/approveBuilder 



用户授权某个 builder 并设置最大费率上限 maxFeeRate。



**Weight:**

TBD



**Parameters:**

| **Name**   | **Type** | **Mandatory** | **Description**                            |
| ---------- | -------- | ------------- | ------------------------------------------ |
| user       | STRING   | YES           | 用户主地址                                 |
| nonce      | LONG     | YES           | 防重放 nonce                               |
| signature  | STRING   | YES           | EIP-712 签名（primaryType=ApproveBuilder） |
| builderName | STRING   | YES           | Builder 名称（label）                            |
| builder    | STRING   | YES           | Builder 地址（归因/收款地址）              |
| maxFeeRate | STRING   | YES           | 最大费率上限（建议使用字符串格式传小数）   |



------





## **更新 Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

POST /fapi/v3/updateBuilder 



更新 builder 的最大费率上限。



**Weight:**

TBD



**Parameters:**

| **Name**   | **Type** | **Mandatory** | **Description**                           |
| ---------- | -------- | ------------- | ----------------------------------------- |
| user       | STRING   | YES           | 用户主地址                                |
| nonce      | LONG     | YES           | 防重放 nonce                              |
| signature  | STRING   | YES           | EIP-712 签名（primaryType=UpdateBuilder） |
| builder    | STRING   | YES           | Builder 地址                              |
| maxFeeRate | STRING   | YES           | 新的最大费率上限                          |



------





## **删除 Builder (TRADE)**





> **Response:**

```
{
  "code": 200,
  "msg": "success"
}
```

DELETE /fapi/v3/builder 



撤销对某 builder 的授权。



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                        |
| --------- | -------- | ------------- | -------------------------------------- |
| user      | STRING   | YES           | 用户主地址                             |
| nonce     | LONG     | YES           | 防重放 nonce                           |
| signature | STRING   | YES           | EIP-712 签名（primaryType=DelBuilder） |
| builder   | STRING   | YES           | Builder 地址                           |



------





## **查询 Builders (USER_DATA)**





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



查询用户已授权的 builder 列表与 maxFeeRate。



**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                             |
| --------- | -------- | ------------- | ------------------------------------------- |
| user      | STRING   | YES           | 用户主地址                                  |
| signer    | STRING   | YES           | 任一已授权 signer（用于签名本次查询请求）   |
| nonce     | LONG     | YES           | 防重放 nonce                                |
| signature | STRING   | YES           | EIP-712 签名（签 querystring，Message.msg） |



------





## **带Builder Code下单 (TRADE)**





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



由 signer 代表 user 下单，并在订单中携带 builder 与 feeRate 实现归因与收费。



**规则：**



- feeRate 必须 <= maxFeeRate（用户对该 builder 的授权上限），否则应被拒绝。
- 该请求必须由 signer 私钥签名（交易类签名模式）。





**Weight:**

TBD



**Parameters:**

| **Name**  | **Type** | **Mandatory** | **Description**                             |
| --------- | -------- | ------------- | ------------------------------------------- |
| user      | STRING   | YES           | 用户主地址                                  |
| signer    | STRING   | YES           | Agent 地址（signer）                        |
| nonce     | LONG     | YES           | 防重放 nonce                                |
| signature | STRING   | YES           | EIP-712 签名（签 querystring，Message.msg） |
| symbol    | STRING   | YES           | 交易对（如 BTCUSDT）                        |
| type      | STRING   | YES           | 订单类型（如 MARKET/LIMIT）                 |
| side      | STRING   | YES           | BUY / SELL                                  |
| quantity  | STRING   | YES           | 数量                                        |
| builder   | STRING   | YES           | Builder 地址（归因/收款）                   |
| feeRate   | STRING   | YES           | 本单费率（建议字符串格式）                  |

