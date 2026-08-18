## **Builder 准备**
   - 准备 Builder 地址（用于归因与收取 Builder fee）。该地址必须在 Aster 注册，且 Builder 的 **Futures 账户**中至少持有 `100 ASTER`，才能开通 Futures Builder 功能。
   - 为每个用户生成一套 API Wallet / Agent（`signer` 地址 + 私钥），私钥由 Builder 后端安全保管（建议每用户独立 signer）。

**Futures Builder 前置条件：**
- Builder 必须在 Aster 注册。
- Builder 的 **Futures 账户**中至少持有 `100 ASTER`。
- `maxFeeRate` 最大允许值为 `0.001`。

> **说明：** ASTER 持仓要求按产品账户独立计算。Futures Builder 要求 Builder 的 Futures 账户至少持有 `100 ASTER`；Spot Builder 要求 Builder 的 Spot 账户至少持有 `100 ASTER`，两者独立判断，互不替代。
## **用户授权 Agent（API Wallet）**
   - 调用 `POST /fapi/v3/approveAgent`（用户主钱包签名）
   - 设置权限（现货/合约/提现）、IP 白名单、过期时间等。
## **用户授权 Builder（Builder地址和费率上限）**
   - 调用 `POST /fapi/v3/approveBuilder`（用户主钱包签名）
   - 设置 `maxFeeRate`（Builder 可收取的最大费率），最大允许值为 `0.001`。
   **注意：授权 Builder 可以在授权 Agent 时一起授权，也可以在授权 Agent 后单独授权。**
## **Builder 代用户下单**
   - 用户发起下单
   - Builder 后端调用 `POST /fapi/v3/order`
   - 订单参数中携带 `builder` + `feeRate`
   - 由 `signer` 私钥签名并发送请求。

## **后续维护**

   - 更新 / 撤销 Agent：`updateAgent` / `DELETE /agent`
   - 更新 / 撤销 Builder：`updateBuilder` / `DELETE /builder`

---
