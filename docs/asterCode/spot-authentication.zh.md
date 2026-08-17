## **两套签名方式**

Spot Aster Code 根据接口类型使用两种不同的签名模式。

---

### 模式 1 — 主钱包签名（管理类接口）

**适用接口：**

- `POST /api/v3/approveBuilder`
- `POST /api/v3/updateBuilder`
- `DELETE /api/v3/delBuilder`

**签名方：** 用户主钱包（请求中不含 `signer` 字段）

**签名方案：** EIP-712，使用各接口专属的 `primaryType`（如 `ApproveBuilder`、`UpdateBuilder`、`DelBuilder`）。

**请求包含字段：** `user`、`nonce`、`signature`、`signatureChainId` 及各接口的业务参数。

**请求不包含：** `signer`

**EIP-712 Domain：**

```javascript
const domain = {
  name: "AsterSignTransaction",
  version: "1",
  chainId: signatureChainId,   // 与请求参数中的 signatureChainId 保持一致
  verifyingContract: "0x0000000000000000000000000000000000000000",
};
```

> **注意：** Domain 中的 `chainId` 必须等于请求参数中的 `signatureChainId`。除非恰好与目标环境所需的 `signatureChainId` 匹配，否则不要使用 `Message.msg` 的链 ID（1666 / 714）来签署管理类接口。

**Typed data 字段名使用首字母大写（Title Case）**，而请求参数名使用小驼峰（camelCase）。例如：

| 请求参数    | Typed data 字段 |
| ----------- | --------------- |
| `builder`   | `Builder`       |
| `maxFeeRate`| `MaxFeeRate`    |
| `builderName`| `BuilderName`  |
| `asterChain`| `AsterChain`    |
| `user`      | `User`          |
| `nonce`     | `Nonce`         |

---

### 模式 2 — Agent / API Wallet 签名（交易与查询类接口）

**适用接口：**

- `GET /api/v3/builder`
- `POST /api/v3/order`

**签名方：** 已授权的 API Wallet / Agent（Builder 后端持有私钥的 `signer` 地址）

**签名方案：** EIP-712 固定 `Message` 类型，仅签 `msg` 字段。

**EIP-712 Domain：**

```javascript
const domain = {
  name: "AsterSignTransaction",
  version: "1",
  chainId: 1666,   // 主网；测试网使用 714
  verifyingContract: "0x0000000000000000000000000000000000000000",
};
```

**Types：**

```javascript
const types = {
  Message: [
    { name: "msg", type: "string" }
  ]
};
```

**`msg` 的值**是请求的完整 query string（不含 `signature`），参数顺序须与实际 HTTP 请求完全一致。

```javascript
// 以 GET /api/v3/builder 为例：
const msg = `user=${user}&signer=${signer}&nonce=${nonce}`;

const value = { msg };
const signature = await signer.signTypedData(domain, types, value);
// 最终追加：&signature=<signature>
```

> **关键规则：** 签名串必须与实际请求的 query string 逐字节一致（仅排除 `signature` 参数本身）。参数顺序或编码的任何差异都会导致验签失败。

---

## **签名方式总览**

| 接口                            | 签名方              | PrimaryType     | Domain chainId              |
| ------------------------------- | ------------------- | --------------- | --------------------------- |
| `POST /api/v3/approveBuilder`   | 用户主钱包          | `ApproveBuilder`| `signatureChainId`          |
| `POST /api/v3/updateBuilder`    | 用户主钱包          | `UpdateBuilder` | `signatureChainId`          |
| `DELETE /api/v3/delBuilder`     | 用户主钱包          | `DelBuilder`    | `signatureChainId`          |
| `GET /api/v3/builder`           | API Wallet / Agent  | `Message`       | 1666（主网）/ 714（测试网） |
| `POST /api/v3/order`            | API Wallet / Agent  | `Message`       | 1666（主网）/ 714（测试网） |

---

## **EIP-712 Typed Data 结构**

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

### Message（用于 GET /api/v3/builder 和 POST /api/v3/order）

```javascript
const types = {
  Message: [
    { name: "msg", type: "string" }
  ]
};
```

---
