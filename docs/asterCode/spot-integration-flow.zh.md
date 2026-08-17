## **概述**

Spot Aster Code 允许已注册的 Builder 代用户进行现货下单，并通过透明的费率归因机制收取 Builder 费用。

| 产品    | Base URL                    | 前缀     |
| ------- | --------------------------- | -------- |
| Spot    | https://sapi.asterdex.com   | /api/v3  |
| Futures | https://fapi.asterdex.com   | /fapi/v3 |

---

## **对接流程**

### 1. 准备 Spot Builder
- 在 Aster 注册 Builder 地址（需持有不低于最低要求数量的 ASTER 余额）。
- Builder 地址用于订单归因与费用收取。

### 2. 创建 API Wallet / Agent
- 为每个用户生成一套 `signer` 地址 + 私钥。
- 私钥由 Builder 后端安全保管（建议每用户独立一个 signer）。

### 3. 用户授权 Agent
- 调用 `POST /fapi/v3/approveAgent`（用户主钱包签名）。
- 配置权限（`canSpotTrade` 等）、IP 白名单和过期时间。

### 4. 用户授权 Spot Builder

有 **两种方式** 授权 Spot Builder，选择其一即可：

**方式 A — 在授权 Agent 时同时授权**

在 `POST /fapi/v3/approveAgent` 请求中加入 `spotBuilder`、`maxSpotFeeRate` 及可选的 `spotBuilderName`，即可在同一请求中完成 Spot Builder 的授权。

**方式 B — 在授权 Agent 后单独授权**

调用 `POST /api/v3/approveBuilder`（用户主钱包签名）。

> **说明：** 方式 A 和方式 B 是两种可选方案，不需要同时执行。

### 5. Builder 收到现货下单请求
- 用户发起下单操作（例如在 Builder 界面点击"下单"）。

### 6. Builder 后端使用 Agent 私钥签名
- 构造 query string，使用 EIP-712 `Message.msg` 方案（Agent / API Wallet 签名模式）进行签名。

### 7. POST /api/v3/order 携带 builder + feeRate
- Builder 后端将已签名的请求提交至 `POST /api/v3/order`。
- 请求中包含 `builder` 和 `feeRate` 参数。

### 8. Aster 校验授权与费率限制
- Aster 校验以下内容：
  - Agent 已为该用户授权。
  - Spot Builder 授权记录存在。
  - `feeRate` 不超过用户对该 Builder 授权的 `maxFeeRate` 上限。
  - `feeRate` 满足 Aster 全局费率约束。

### 9. 订单提交成功
- 所有校验通过后，订单正式提交。

---

## **后续维护**

| 操作               | 接口                             |
| ------------------ | -------------------------------- |
| 更新 Spot Builder 费率 | `POST /api/v3/updateBuilder` |
| 撤销 Spot Builder 授权 | `DELETE /api/v3/delBuilder`  |
| 更新 Agent         | `POST /fapi/v3/updateAgent`      |
| 撤销 Agent         | `DELETE /fapi/v3/agent`          |

> **重要：** Spot 和 Futures 使用**不同的** Builder 删除接口。
> - Spot：`DELETE /api/v3/delBuilder`
> - Futures：`DELETE /fapi/v3/builder`

---
