## **Two Signing Models**

Spot Aster Code uses two distinct signing models depending on the endpoint type.

---

### Model 1 — Main Wallet Signing (Management Endpoints)

**Applies to:**

- `POST /api/v3/approveBuilder`
- `POST /api/v3/updateBuilder`
- `DELETE /api/v3/delBuilder`

**Signer:** User's main wallet (no `signer` field in request)

**Signature scheme:** EIP-712 with a dynamic `primaryType` specific to each operation (e.g., `ApproveBuilder`, `UpdateBuilder`, `DelBuilder`).

**Request includes:** `user`, `nonce`, `signature`, `signatureChainId`, and endpoint-specific business parameters.

**Request does NOT include:** `signer`

**EIP-712 Domain:**

```javascript
const domain = {
  name: "AsterSignTransaction",
  version: "1",
  chainId: signatureChainId,   // value from request parameter
  verifyingContract: "0x0000000000000000000000000000000000000000",
};
```

> **Warning:** The `chainId` in the domain must equal the `signatureChainId` parameter sent in the request. Do not use the `Message.msg` chain IDs (1666 / 714) for management requests unless they happen to match the required `signatureChainId` for your environment.

**Typed data field names use Title Case** (first letter uppercased), while request parameter names use camelCase. For example:

| Request parameter | Typed data field |
| ----------------- | ---------------- |
| `builder`         | `Builder`        |
| `maxFeeRate`      | `MaxFeeRate`     |
| `builderName`     | `BuilderName`    |
| `asterChain`      | `AsterChain`     |
| `user`            | `User`           |
| `nonce`           | `Nonce`          |

---

### Model 2 — Agent / API Wallet Signing (Trading & Query Endpoints)

**Applies to:**

- `GET /api/v3/builder`
- `POST /api/v3/order`

**Signer:** Authorized API Wallet / Agent (the `signer` address whose private key is held by the Builder backend)

**Signature scheme:** EIP-712 fixed `Message` type, signing only the `msg` field.

**EIP-712 Domain:**

```javascript
const domain = {
  name: "AsterSignTransaction",
  version: "1",
  chainId: 1666,   // mainnet; use 714 for testnet
  verifyingContract: "0x0000000000000000000000000000000000000000",
};
```

**Types:**

```javascript
const types = {
  Message: [
    { name: "msg", type: "string" }
  ]
};
```

**The `msg` value** is the exact query string of the request (excluding `signature`), assembled in the same parameter order as the actual HTTP request.

```javascript
// Example for GET /api/v3/builder:
const msg = `user=${user}&signer=${signer}&nonce=${nonce}`;

const value = { msg };
const signature = await signer.signTypedData(domain, types, value);
// Append: &signature=<signature>
```

> **Key rule:** The signed string must match the actual request query string byte-for-byte, excluding only the `signature` parameter. Any difference in parameter order or encoding will cause signature verification to fail.

---

## **Signing Model Summary**

| Endpoint                        | Signer              | PrimaryType     | chainId in domain     |
| ------------------------------- | ------------------- | --------------- | --------------------- |
| `POST /api/v3/approveBuilder`   | User main wallet    | `ApproveBuilder`| `signatureChainId`    |
| `POST /api/v3/updateBuilder`    | User main wallet    | `UpdateBuilder` | `signatureChainId`    |
| `DELETE /api/v3/delBuilder`     | User main wallet    | `DelBuilder`    | `signatureChainId`    |
| `GET /api/v3/builder`           | API Wallet / Agent  | `Message`       | 1666 (mainnet) / 714 (testnet) |
| `POST /api/v3/order`            | API Wallet / Agent  | `Message`       | 1666 (mainnet) / 714 (testnet) |

---

## **EIP-712 Typed Data Structures**

### ApproveBuilder

```javascript
const types = {
  ApproveBuilder: [
    { name: "Builder",     type: "string"  },
    { name: "MaxFeeRate",  type: "string"  },
    { name: "BuilderName", type: "string"  },
    { name: "AsterChain",  type: "string"  },
    { name: "User",        type: "string"  },
    { name: "Nonce",       type: "uint256" },
  ]
};
```

### UpdateBuilder

```javascript
const types = {
  UpdateBuilder: [
    { name: "Builder",    type: "string"  },
    { name: "MaxFeeRate", type: "string"  },
    { name: "AsterChain", type: "string"  },
    { name: "User",       type: "string"  },
    { name: "Nonce",      type: "uint256" },
  ]
};
```

### DelBuilder

```javascript
const types = {
  DelBuilder: [
    { name: "Builder",    type: "string"  },
    { name: "AsterChain", type: "string"  },
    { name: "User",       type: "string"  },
    { name: "Nonce",      type: "uint256" },
  ]
};
```

### Message (for GET /api/v3/builder and POST /api/v3/order)

```javascript
const types = {
  Message: [
    { name: "msg", type: "string" }
  ]
};
```

---
