## **Overview**

Spot Aster Code allows a registered Builder to place Spot orders on behalf of users, with transparent fee attribution.

| Product | Base URL                    | Prefix   |
| ------- | --------------------------- | -------- |
| Spot    | https://sapi.asterdex.com   | /api/v3  |
| Futures | https://fapi.asterdex.com   | /fapi/v3 |

---

## **Integration Flow**

### 1. Prepare a Spot Builder
- Register a Builder address on Aster. The Builder must hold at least `100 ASTER` in its **Spot account** to enable Spot Builder functionality.
- The Builder address is used for order attribution and fee collection.

**Spot Builder requirements:**
- The Builder must be registered on Aster.
- The Builder must hold at least `100 ASTER` in its **Spot account**.
- The maximum allowed `maxFeeRate` is `0.01`.

> **Note:** The ASTER balance requirement is product-specific. To enable a Futures Builder, at least `100 ASTER` must be held in the Builder's Futures account. To enable a Spot Builder, at least `100 ASTER` must be held in the Builder's Spot account. These two requirements are evaluated independently.

### 2. Create an API Wallet / Agent
- Generate a `signer` address + private key for each user.
- Store the private key securely on the Builder backend (one signer per user is recommended).

### 3. User authorizes Agent
- Call `POST /fapi/v3/approveAgent` (signed by the user's main wallet).
- Configure permissions (`canSpotTrade`, etc.), IP whitelist, and expiry time.

### 4. User authorizes Spot Builder

There are **two ways** to authorize a Spot Builder — choose one:

**Option A — Authorize together with Agent approval**

Include `spotBuilder`, `maxFeeRate` (maximum value: `0.01`), and optionally `spotBuilderName` in the `POST /fapi/v3/approveAgent` request. The Spot Builder is authorized in the same transaction.

**Option B — Authorize separately after Agent approval**

Call `POST /api/v3/approveBuilder` (signed by the user's main wallet).

> **Note:** Options A and B are alternatives, not both required. Do not combine them unnecessarily.

### 5. Builder receives a Spot order request
- User initiates an order action (e.g., clicks "Place Order" in the Builder UI).

### 6. Builder backend signs using Agent private key
- Construct the query string and sign it using the EIP-712 `Message.msg` scheme (Agent / API Wallet signing).

### 7. POST /api/v3/order with builder + feeRate
- Builder backend submits the signed request to `POST /api/v3/order`.
- Include `builder` and `feeRate` parameters.

### 8. Aster verifies authorization and fee limits
- Aster verifies:
  - The Agent is authorized for the user.
  - The Spot Builder authorization exists.
  - `feeRate` does not exceed the user's authorized `maxFeeRate` for this Builder.
  - `feeRate` satisfies Aster's global fee-rate constraints.

### 9. Order is submitted
- If all checks pass, the order is placed.

---

## **Ongoing Maintenance**

| Action                  | Endpoint                          |
| ----------------------- | --------------------------------- |
| Update Spot Builder fee | `POST /api/v3/updateBuilder`      |
| Revoke Spot Builder     | `DELETE /api/v3/delBuilder`       |
| Update Agent            | `POST /fapi/v3/updateAgent`       |
| Revoke Agent            | `DELETE /fapi/v3/agent`           |

> **Important:** Spot and Futures use **different** Builder deletion endpoints.
> - Spot: `DELETE /api/v3/delBuilder`
> - Futures: `DELETE /fapi/v3/builder`

---
