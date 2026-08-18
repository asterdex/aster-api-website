## **Builder preparation**
   - Prepare the Builder address (used for attribution and collecting the Builder fee). The address must be registered on Aster, and the Builder must hold at least `100 ASTER` in its **Futures account** to enable Futures Builder functionality.
   - Generate an API Wallet / Agent for each user (`signer` address + private key). The private key must be securely stored on the Builder backend (recommended: one signer per user).

**Futures Builder requirements:**
- The Builder must be registered on Aster.
- The Builder must hold at least `100 ASTER` in its **Futures account**.
- The maximum allowed `maxFeeRate` is `0.001`.

> **Note:** The ASTER balance requirement is product-specific. To enable a Futures Builder, at least `100 ASTER` must be held in the Builder's Futures account. To enable a Spot Builder, at least `100 ASTER` must be held in the Builder's Spot account. These two requirements are evaluated independently.

## **User authorizes the Agent (API Wallet)**
   - Call `POST /fapi/v3/approveAgent` (signed by the user’s main wallet)
   - Configure permissions (spot/perp/withdraw), IP whitelist, expiry, etc.

## **User authorizes the Builder (builder address and fee cap)**
   - Call `POST /fapi/v3/approveBuilder` (signed by the user’s main wallet)
   - Set `maxFeeRate` (maximum fee rate the Builder is allowed to charge). The maximum allowed value is `0.001`.
   - **Note: Builder authorization can be done together during Agent approval, or separately after Agent approval.**

## **Builder places orders on behalf of the user**
   - The user initiates the order action (e.g., clicks “Place Order” in the Builder UI)
   - Builder backend calls `POST /fapi/v3/order`
   - Include `builder` + `feeRate` in the order parameters
   - Sign and send the request using the `signer` private key.

## **Ongoing maintenance**
   - Update / revoke Agent: `updateAgent` / `DELETE /agent`
   - Update / revoke Builder: `updateBuilder` / `DELETE /builder`

---
