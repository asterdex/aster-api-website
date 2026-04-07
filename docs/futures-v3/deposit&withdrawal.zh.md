## **查询所有充值资产**

请求：

```shell
curl 'https://www.asterdex.com/bapi/futures/v1/public/future/aster/deposit/assets?chainIds=56&networks=EVM&accountType=spot'
```

参数：

| 参数        | 类型   | 必填 | 说明                                       |
|-------------|--------|------|--------------------------------------------|
| chainIds    | string | 是   | 链 ID，多个 ID 用逗号分隔                  |
| networks    | string | 否   | 网络类型，如 EVM、SOLANA，多个用逗号分隔  |
| accountType | string | 是   | 账户类型，如 spot、perp                    |

响应：

```json
{
    "code": "000000",
    "message": null,
    "messageDetail": null,
    "data": [
        {
            "name": "ASTER",
            "displayName": "ASTER",
            "contractAddress": "0x000ae314e2a2172a039b26378814c252734f556a",
            "decimals": 18,
            "network": "EVM",
            "chainId": 56,
            "depositType": "normal",
            "rank": 10,
            "isNative": false,
            "admin": null,
            "bank": null,
            "tokenVaultAuthority": null,
            "tokenVault": null,
            "tokenMint": null,
            "associatedTokenProgram": null,
            "tokenProgram": null,
            "systemProgram": null,
            "ixSysvar": null,
            "priceFeed": null,
            "priceFeedProgram": null,
            "solVault": null
        }
    ],
    "success": true
}
```

## **查询所有提现资产**

请求：

```shell
curl 'https://www.asterdex.com/bapi/futures/v1/public/future/aster/withdraw/assets?chainIds=56&networks=EVM&accountType=spot'
```

参数：

| 参数        | 类型   | 必填 | 说明                                       |
|-------------|--------|------|--------------------------------------------|
| chainIds    | string | 是   | 链 ID，多个 ID 用逗号分隔                  |
| networks    | string | 否   | 网络类型，如 EVM、SOLANA，多个用逗号分隔  |
| accountType | string | 是   | 账户类型，如 spot、perp                    |

响应：

```json
{
    "code": "000000",
    "message": null,
    "messageDetail": null,
    "data": [
        {
            "name": "ASTER",
            "displayName": "ASTER",
            "contractAddress": "0x000ae314e2a2172a039b26378814c252734f556a",
            "decimals": 18,
            "network": "EVM",
            "chainId": 56,
            "withdrawType": "autoWithdraw",
            "rank": 10,
            "isNative": false,
            "isProfit": true,
            "admin": null,
            "bank": null,
            "tokenVaultAuthority": null,
            "tokenVault": null,
            "tokenMint": null,
            "associatedTokenProgram": null,
            "tokenProgram": null,
            "systemProgram": null,
            "ixSysvar": null,
            "priceFeed": null,
            "priceFeedProgram": null,
            "solVault": null
        }
    ],
    "success": true
}
```

## **估算提现手续费**

请求：

```shell
curl 'https://www.asterdex.com/bapi/futures/v1/public/future/aster/estimate-withdraw-fee?chainId=56&network=EVM&currency=ASTER&accountType=spot'
```

参数：

| 参数        | 类型   | 必填 | 说明                         |
|-------------|--------|------|------------------------------|
| chainId     | int    | 是   | 链 ID                        |
| network     | string | 是   | 网络类型，如 EVM、SOL        |
| currency    | string | 是   | 币种名称，如 ASTER           |
| accountType | string | 是   | 账户类型，如 spot、perp      |

响应：

```json
{
    "code": "000000",
    "message": null,
    "messageDetail": null,
    "data": {
        "gasPrice": null,
        "gasLimit": 200000,
        "nativePrice": null,
        "tokenPrice": 1.12357820,
        "gasCost": 0.0891,
        "gasUsdValue": 0.1
    },
    "success": true
}
```

| 字段    | 说明                     |
|---------|--------------------------|
| gasCost | 以代币单位计的预估提现手续费 |

## **获取服务器时间**

请求：

```shell
curl 'https://fapi5.asterdex.com/fapi/v3/time'
```

响应：

```json
{
    "serverTime": 1742198400000
}
```

| 字段       | 说明                            |
|------------|---------------------------------|
| serverTime | 当前服务器时间（Unix 毫秒时间戳）|

## **查询用户提现信息**

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#查询用户提现信息v3)。

* 注意：请参照 [API-KEY 签名（V1）](#api-key-签名v1) 说明生成请求签名。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/aster/user-withdraw-info?timestamp=1742198400000&recvWindow=5000&signature=<signature>' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

除标准 V1 签名参数（`timestamp`、`recvWindow`、`signature`）外，无需其他参数。

响应：

```json
{
    "userDailyLimit": "10000",
    "userRemainingDailyLimit": "9000",
    "totalDailyLimit": "1000000",
    "totalRemainingDailyLimit": "980000",
    "balances": {
        "USDT": {
            "currency": "USDT",
            "spotTotalWithdrawAmount": "500",
            "perpTotalWithdrawAmount": "300",
            "dailyLimit": "9000",
            "chainBalances": {
                "56": {
                    "chainId": 56,
                    "spotMaxWithdrawAmount": "500",
                    "perpMaxWithdrawAmount": "300",
                    "chainLimit": "800",
                    "withdrawFee": "0.5"
                }
            }
        }
    }
}
```

| 字段                                         | 说明                                         |
|----------------------------------------------|----------------------------------------------|
| userDailyLimit                               | 用户每日提现限额（以 USD 计）                |
| userRemainingDailyLimit                      | 用户今日剩余提现限额（以 USD 计）            |
| totalDailyLimit                              | 平台每日提现总限额（以 USD 计）              |
| totalRemainingDailyLimit                     | 平台今日剩余提现总限额（以 USD 计）          |
| balances                                     | 非零余额资产映射，以资产名称为键             |
| balances.currency                            | 资产名称                                     |
| balances.spotTotalWithdrawAmount             | 现货账户可提现总余额                         |
| balances.perpTotalWithdrawAmount             | 合约账户可提现总余额                         |
| balances.dailyLimit                          | 该资产今日剩余提现限额（以 USD 计）          |
| balances.chainBalances                       | 各链余额信息，以链 ID 为键                   |
| balances.chainBalances.chainId               | 链 ID                                        |
| balances.chainBalances.spotMaxWithdrawAmount | 该链现货账户最大可提现数量                   |
| balances.chainBalances.perpMaxWithdrawAmount | 该链合约账户最大可提现数量                   |
| balances.chainBalances.chainLimit            | 该链最大可提现数量                           |
| balances.chainBalances.withdrawFee           | 该链提现手续费                               |

## **查询充值提现历史**

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#查询充值提现历史v3)。

* 注意：请参照 [API-KEY 签名（V1）](#api-key-签名v1) 说明生成请求签名。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/aster/deposit-withdraw-history?timestamp=1742198400000&recvWindow=5000&signature=<signature>' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

除标准 V1 签名参数（`timestamp`、`recvWindow`、`signature`）外，无需其他参数。

响应：

```json
[
    {
        "id": "1234567",
        "type": "DEPOSIT",
        "asset": "USDT",
        "amount": "100",
        "state": "SUCCESS",
        "txHash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90",
        "time": 1742198400000,
        "chainId": 56,
        "accountType": "spot"
    }
]
```

| 字段        | 说明                                          |
|-------------|-----------------------------------------------|
| id          | 记录 ID                                       |
| type        | 记录类型：`DEPOSIT`（充值）或 `WITHDRAW`（提现）|
| asset       | 资产名称，如 USDT                             |
| amount      | 数量                                          |
| state       | 状态：`PROCESSING`、`SUCCESS` 或 `FAILED`     |
| txHash      | 链上交易哈希                                  |
| time        | 记录时间（Unix 毫秒时间戳）                   |
| chainId     | 链 ID                                         |
| accountType | 账户类型：`spot`（现货）或 `perp`（合约）     |

## **查询用户提现信息\[v3\]**

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/v3/aster/user-withdraw-info' \
  --header 'Content-Type: application/json'
```

参数：

除标准 V3 签名参数外，无需其他参数。

响应：

```json
{
    "userDailyLimit": "10000",
    "userRemainingDailyLimit": "9000",
    "totalDailyLimit": "1000000",
    "totalRemainingDailyLimit": "980000",
    "balances": {
        "USDT": {
            "currency": "USDT",
            "spotTotalWithdrawAmount": "500",
            "perpTotalWithdrawAmount": "300",
            "dailyLimit": "9000",
            "chainBalances": {
                "56": {
                    "chainId": 56,
                    "spotMaxWithdrawAmount": "500",
                    "perpMaxWithdrawAmount": "300",
                    "chainLimit": "800",
                    "withdrawFee": "0.5"
                }
            }
        }
    }
}
```

| 字段                                         | 说明                                         |
|----------------------------------------------|----------------------------------------------|
| userDailyLimit                               | 用户每日提现限额（以 USD 计）                |
| userRemainingDailyLimit                      | 用户今日剩余提现限额（以 USD 计）            |
| totalDailyLimit                              | 平台每日提现总限额（以 USD 计）              |
| totalRemainingDailyLimit                     | 平台今日剩余提现总限额（以 USD 计）          |
| balances                                     | 非零余额资产映射，以资产名称为键             |
| balances.currency                            | 资产名称                                     |
| balances.spotTotalWithdrawAmount             | 现货账户可提现总余额                         |
| balances.perpTotalWithdrawAmount             | 合约账户可提现总余额                         |
| balances.dailyLimit                          | 该资产今日剩余提现限额（以 USD 计）          |
| balances.chainBalances                       | 各链余额信息，以链 ID 为键                   |
| balances.chainBalances.chainId               | 链 ID                                        |
| balances.chainBalances.spotMaxWithdrawAmount | 该链现货账户最大可提现数量                   |
| balances.chainBalances.perpMaxWithdrawAmount | 该链合约账户最大可提现数量                   |
| balances.chainBalances.chainLimit            | 该链最大可提现数量                           |
| balances.chainBalances.withdrawFee           | 该链提现手续费                               |

## **查询充值提现历史\[v3\]**

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/v3/aster/deposit-withdraw-history' \
  --header 'Content-Type: application/json'
```

参数：

除标准 V3 签名参数外，无需其他参数。

响应：

```json
[
    {
        "id": "1234567",
        "type": "DEPOSIT",
        "asset": "USDT",
        "amount": "100",
        "state": "SUCCESS",
        "txHash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90",
        "time": 1742198400000,
        "chainId": 56,
        "accountType": "spot"
    }
]
```

| 字段        | 说明                                          |
|-------------|-----------------------------------------------|
| id          | 记录 ID                                       |
| type        | 记录类型：`DEPOSIT`（充值）或 `WITHDRAW`（提现）|
| asset       | 资产名称，如 USDT                             |
| amount      | 数量                                          |
| state       | 状态：`PROCESSING`、`SUCCESS` 或 `FAILED`     |
| txHash      | 链上交易哈希                                  |
| time        | 记录时间（Unix 毫秒时间戳）                   |
| chainId     | 链 ID                                         |
| accountType | 账户类型：`spot`（现货）或 `perp`（合约）     |

---

## **签名说明**

### API-KEY 签名（V1）

使用 V1 API Key 时，您需要自行生成 `API_KEY` 和 `API_SECRET`。每次请求必须包含以下三个参数：

| 参数       | 说明                                                                           |
|------------|--------------------------------------------------------------------------------|
| timestamp  | 当前时间的毫秒级 Unix 时间戳                                                   |
| recvWindow | 请求在 `timestamp` 之后保持有效的最大毫秒数（默认：`5000`）                   |
| signature  | 使用 `API_SECRET` 对完整请求查询字符串或请求体进行 HMAC SHA256 签名            |

此外，请在请求头中携带您的 `API_KEY`：

```
X-MBX-APIKEY: <your API_KEY>
```

#### 如何生成签名

将所有查询参数拼接为一个字符串，然后使用 HMAC SHA256 和您的 `API_SECRET` 进行签名，将结果作为 `signature` 参数附加在末尾。

```javascript
const queryString = 'asset=USDT&amount=10&timestamp=1742198400000&recvWindow=5000';
const signature = CryptoJS.HmacSHA256(queryString, API_SECRET).toString();
const finalUrl = `${baseUrl}?${queryString}&signature=${signature}`;
```

> `signature` 参数必须始终作为查询字符串中的**最后一个**参数。

### Pro API-KEY 签名（V3）

使用 Pro API Key（V3）时，您将获得一个专属 EOA 钱包地址及其对应私钥。每次 V3 请求必须包含以下参数：

| 参数      | 说明                                                                                         |
|-----------|----------------------------------------------------------------------------------------------|
| nonce     | 纳秒级时间戳，30 秒内有效。请使用[获取服务器时间](#获取服务器时间)接口获取当前服务器时间。  |
| user      | 用户自己的钱包地址                                                                           |
| signer    | 签发的 EOA 钱包地址                                                                          |
| signature | 使用签发的 EOA 钱包私钥对所有请求参数的签名                                                  |

#### 如何生成签名

V3 签名为 EIP712 typed data 签名。将所有查询参数拼接为一个字符串作为消息内容，然后使用签发的 EOA 私钥通过 `signTypedData` 进行签名。

**EIP712 Domain**

```json
{
  "name": "AsterSignTransaction",
  "version": "1",
  "chainId": "<API_CHAINID>",
  "verifyingContract": "0x0000000000000000000000000000000000000000"
}
```

**EIP712 Types**

```json
{
  "Message": [
    { "name": "msg", "type": "string" }
  ]
}
```

**Value**

```json
{
  "msg": "<所有请求参数的查询字符串>"
}
```

**示例（JavaScript / ethers.js）**

```javascript
const domain = {
    name: 'AsterSignTransaction',
    version: '1',
    chainId: 1666,
    verifyingContract: ethers.ZeroAddress,
};

const types = {
    Message: [
        { name: 'msg', type: 'string' },
    ],
};

const queryString = 'nonce=1742198400000000000&user=0xYourAddress&signer=0xSignerAddress';
const value = { msg: queryString };

const wallet = new ethers.Wallet(API_PRIVATEKEY);
const signature = await wallet.signTypedData(domain, types, value);
const finalUrl = `${baseUrl}?${queryString}&signature=${signature}`;
```

> `signature` 参数必须始终作为查询字符串中的**最后一个**参数。

## **EVM 提现签名**

* 提现时需提供 EIP712 签名，使用您的钱包对以下消息内容进行签名。

### EIP712 Domain

```json
{
  "name": "Aster",
  "version": "1",
  "chainId": 56,
  "verifyingContract": "0x0000000000000000000000000000000000000000"
}
```

| 字段              | 说明                      |
|-------------------|---------------------------|
| name              | 固定字符串：`Aster`       |
| version           | 固定字符串：`1`           |
| chainId           | 提现目标链的 chainId      |
| verifyingContract | 固定地址：零地址          |

### EIP712 Types

```json
{
  "Action": [
    {"name": "type", "type": "string"},
    {"name": "destination", "type": "address"},
    {"name": "destination Chain", "type": "string"},
    {"name": "token", "type": "string"},
    {"name": "amount", "type": "string"},
    {"name": "fee", "type": "string"},
    {"name": "nonce", "type": "uint256"},
    {"name": "aster chain", "type": "string"}
  ]
}
```

| 字段              | 说明                                                                              |
|-------------------|-----------------------------------------------------------------------------------|
| type              | 固定字符串：`Withdraw`                                                            |
| destination       | 收款地址，需为用户注册地址                                                        |
| destination Chain | 收款地址所在链名称，chainName 定义见下表                                          |
| token             | 提现币种名称，如 `ASTER`，可通过 withdraw/asset 接口获取                         |
| amount            | 提现数量（代币单位），如 `1.23`                                                   |
| fee               | 用户支付的手续费（代币单位），如 `0.01`，可通过 estimate-withdraw-fee 接口获取   |
| nonce             | 唯一数值，使用当前毫秒级时间戳乘以 `1000`                                        |
| aster chain       | 固定字符串：`Mainnet`                                                             |

### chainName 定义

| chainId | chainName |
|---------|-----------|
| 1       | ETH       |
| 56      | BSC       |
| 42161   | Arbitrum  |

## **Solana 提现签名（可选）**

提交 Solana 提现时，可选择性地提供有效签名。虽然目前签名并非必填，但后续版本将强制要求。强烈建议携带签名——只有包含有效签名的提现请求才会被记录在 L1 链上。

### 如何生成签名

Solana 提现签名为 **Ed25519 签名**，对结构化消息字符串进行签名，结果以 **Base58** 编码。

**消息格式**

按以下字段顺序用逗号拼接：

```
PrimaryType=Withdraw,AsterChain=Mainnet,Destination={destination},DestinationChain={destinationChain},Token={token},Amount={amount},Fee={fee},Nonce={nonce}
```

| 字段             | 说明                                           |
|------------------|------------------------------------------------|
| Destination      | 收款方 Solana 钱包地址                         |
| DestinationChain | 固定字符串：`Solana`                           |
| Token            | 币种名称，如 `USDT`                            |
| Amount           | 去除尾部零的提现数量，如 `1.2`（非 `1.20`）   |
| Fee              | 去除尾部零的手续费，如 `0.1`（非 `0.10`）     |
| Nonce            | 纳秒级时间戳，如 `1773741793787000`            |

**消息示例**

```
PrimaryType=Withdraw,AsterChain=Mainnet,Destination=H7LqU4p4f8LDddADXDH9oFeoh3r7vhfJFf3XCEot8pkd,DestinationChain=Solana,Token=USDT,Amount=1.2,Fee=0.1,Nonce=1773741793787000
```

**示例（Node.js）**

```javascript
import { Keypair } from '@solana/web3.js';
import nacl from 'tweetnacl';
import bs58 from 'bs58';

const destination = 'H7LqU4p4f8LDddADXDH9oFeoh3r7vhfJFf3XCEot8pkd';
const destinationChain = 'Solana';
const token = 'USDT';
const amount = '1.2';
const fee = '0.1';
const nonce = Date.now() * 1000; // 纳秒级时间戳

const message = `PrimaryType=Withdraw,AsterChain=Mainnet,Destination=${destination},DestinationChain=${destinationChain},Token=${token},Amount=${amount},Fee=${fee},Nonce=${nonce}`;

const messageBytes = Buffer.from(message, 'utf8');
const keypair = Keypair.fromSecretKey(bs58.decode(YOUR_PRIVATE_KEY));
const signatureBytes = nacl.sign.detached(messageBytes, keypair.secretKey);
const userSignature = bs58.encode(signatureBytes);
```

> `Amount` 和 `Fee` 中的尾部零必须去除（如 `1.20` → `1.2`），否则签名验证将失败。

## **提现接口列表**

### 通过 fapi 提现 \[evm\] \[futures\]

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#通过-fapiv3-提现-evm-futures)。

* 注意：请参照 [API-KEY 签名（V1）](#api-key-签名v1) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/aster/user-withdraw?chainId=56&asset=USDT&amount=31&fee=0.3&receiver=0x000ae314e2a2172a039b26378814c252734f556a&nonce=1761210000000000&userSignature=0xde4ca529eef20db136eed1daf1d072083431d5279e6d6e219600cf57161c5e6d1232af3c8a8ef37ba8b5963f439ef9cc2b475fe18dcc3732dda9fb93c94a3abd1c' \
  --header 'Content-Type: application/json' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

| 参数          | 类型   | 必填 | 说明                               |
|---------------|--------|------|------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）               |
| chainId       | int    | 是   | 链 ID                              |
| asset         | string | 是   | 币种名称，如 ASTER                 |
| fee           | string | 是   | 提现手续费（代币单位）             |
| nonce         | string | 是   | 唯一数值，需与签名中的值一致       |
| receiver      | string | 是   | 提现收款地址，需与签名中的值一致   |
| userSignature | string | 是   | EIP712 签名                        |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

| 字段       | 说明               |
|------------|--------------------|
| withdrawId | 提现请求 ID，唯一值 |
| hash       | 用户签名的摘要     |

### 通过 API 提现 \[evm\] \[spot\]

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#通过-fapiv3-提现-evm-spot)。

请求：

```shell
curl --location --request POST 'https://sapi.asterdex.com/api/v1/aster/user-withdraw?chainId=56&asset=ASTER&amount=1&fee=0.095&receiver=0x000ae314e2a2172a039b26378814c252734f556a&nonce=1761222960000000&userSignature=0x39051cc68de0fefb8e823259d3f7014fc787a8008b65d2a89d70defc48c3f91b35a4a819718c22ffcaeb143c8e1735621a0768d7c69e45ad8fbcf9bd315988423b' \
  --header 'Content-Type: application/json' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

| 参数          | 类型   | 必填 | 说明                               |
|---------------|--------|------|------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）               |
| chainId       | int    | 是   | 链 ID                              |
| asset         | string | 是   | 币种名称，如 ASTER                 |
| fee           | string | 是   | 提现手续费（代币单位）             |
| nonce         | string | 是   | 唯一数值，需与签名中的值一致       |
| receiver      | string | 是   | 提现收款地址，需与签名中的值一致   |
| userSignature | string | 是   | EIP712 签名                        |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

| 字段       | 说明               |
|------------|--------------------|
| withdrawId | 提现请求 ID，唯一值 |
| hash       | 用户签名的摘要     |

### 通过 fapi 提现 \[solana\] \[futures\]

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#通过-fapiv3-提现-solana-futures)。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/aster/user-solana-withdraw?chainId=101&asset=USDT&amount=3&fee=0.6&receiver=4wTV1YmiEkRvAtNtsSGPtUrqRYQMe5SKy2uB4Jjaxnjf&userNonce=1773741793787000&userSignature=51pM5A46n5NzHYTtuzB7gh8FFfbkh4Aij1fceCZV2NtkiVvE7DADMnSvXFiUJvauKawdWaCfPhzCTVfXYcf1iteQ' \
  --header 'Content-Type: application/json' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                 |
|---------------|--------|------|----------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                 |
| chainId       | int    | 是   | 固定值：`101`                                                        |
| asset         | string | 是   | 币种名称，如 USDT                                                    |
| fee           | string | 是   | 提现手续费（代币单位）                                               |
| receiver      | string | 是   | 提现收款地址                                                         |
| userNonce     | string | 否   | 纳秒级时间戳，需与签名中的值一致。目前非必填，但强烈建议携带         |
| userSignature | string | 否   | Base58 编码的 Ed25519 提现签名。目前非必填，但强烈建议携带           |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

> 注意：`hash` 不是交易哈希，仅为唯一标识符。

### 通过 API 提现 \[solana\] \[spot\]

> **已废弃：** 该 V1 接口将在未来停止服务，请迁移至 [V3 版本](#通过-fapiv3-提现-solana-spot)。

请求：

```shell
curl --location --request POST 'https://sapi.asterdex.com/api/v1/aster/user-solana-withdraw?chainId=101&asset=USDT&amount=0.97&fee=0.5&receiver=BzsJhmtg2UtQWNw6764DkK5Y4GPjc1XMzRqAGqSziymK&userNonce=1773741793787000&userSignature=51pM5A46n5NzHYTtuzB7gh8FFfbkh4Aij1fceCZV2NtkiVvE7DADMnSvXFiUJvauKawdWaCfPhzCTVfXYcf1iteQ' \
  --header 'Content-Type: application/json' \
  --header 'X-MBX-APIKEY: Your API KEY'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                 |
|---------------|--------|------|----------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                 |
| chainId       | int    | 是   | 固定值：`101`                                                        |
| asset         | string | 是   | 币种名称，如 USDT                                                    |
| fee           | string | 是   | 提现手续费（代币单位）                                               |
| receiver      | string | 是   | 提现收款地址                                                         |
| userNonce     | string | 否   | 纳秒级时间戳，需与签名中的值一致。目前非必填，但强烈建议携带         |
| userSignature | string | 否   | Base58 编码的 Ed25519 提现签名。目前非必填，但强烈建议携带           |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

> 注意：`hash` 不是交易哈希，仅为唯一标识符。

### 通过 fapi\[v3\] 提现 \[evm\] \[futures\]

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/v3/aster/user-withdraw?chainId=56&asset=USDT&amount=31&fee=0.3&receiver=0x000ae314e2a2172a039b26378814c252734f556a&userNonce=1761210000000000&userSignature=0xde4ca529eef20db136eed1daf1d072083431d5279e6d6e219600cf57161c5e6d1232af3c8a8ef37ba8b5963f439ef9cc2b475fe18dcc3732dda9fb93c94a3abd1c' \
  --header 'Content-Type: application/json'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                     |
|---------------|--------|------|--------------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                     |
| chainId       | int    | 是   | 链 ID                                                                    |
| asset         | string | 是   | 币种名称，如 ASTER                                                       |
| fee           | string | 是   | 提现手续费（代币单位）                                                   |
| userNonce     | string | 是   | EVM 提现签名的纳秒级时间戳；与 V3 API `nonce` 相互独立，允许相差 1 小时以内 |
| receiver      | string | 是   | 提现收款地址，需与签名中的值一致                                         |
| userSignature | string | 是   | EIP712 提现签名                                                          |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

| 字段       | 说明               |
|------------|--------------------|
| withdrawId | 提现请求 ID，唯一值 |
| hash       | 用户签名的摘要     |

### 通过 fapi\[v3\] 提现 \[solana\] \[futures\]

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://fapi.asterdex.com/fapi/v3/aster/user-solana-withdraw?chainId=101&asset=USDT&amount=3&fee=0.6&receiver=4wTV1YmiEkRvAtNtsSGPtUrqRYQMe5SKy2uB4Jjaxnjf&userNonce=1773741793787000&userSignature=51pM5A46n5NzHYTtuzB7gh8FFfbkh4Aij1fceCZV2NtkiVvE7DADMnSvXFiUJvauKawdWaCfPhzCTVfXYcf1iteQ' \
  --header 'Content-Type: application/json'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                              |
|---------------|--------|------|-----------------------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                              |
| chainId       | int    | 是   | 固定值：`101`                                                                     |
| asset         | string | 是   | 币种名称，如 USDT                                                                 |
| fee           | string | 是   | 提现手续费（代币单位）                                                            |
| receiver      | string | 是   | 提现收款地址                                                                      |
| userNonce     | string | 否   | Solana 提现签名的纳秒级时间戳；与 V3 API `nonce` 相互独立。目前非必填，但强烈建议携带 |
| userSignature | string | 否   | Base58 编码的 Ed25519 提现签名。目前非必填，但强烈建议携带                        |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

> 注意：`hash` 不是交易哈希，仅为唯一标识符。

### 通过 fapi\[v3\] 提现 \[evm\] \[spot\]

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://sapi.asterdex.com/api/v3/aster/user-withdraw?chainId=56&asset=USDT&amount=31&fee=0.3&receiver=0x000ae314e2a2172a039b26378814c252734f556a&userNonce=1761210000000000&userSignature=0xde4ca529eef20db136eed1daf1d072083431d5279e6d6e219600cf57161c5e6d1232af3c8a8ef37ba8b5963f439ef9cc2b475fe18dcc3732dda9fb93c94a3abd1c' \
  --header 'Content-Type: application/json'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                     |
|---------------|--------|------|--------------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                     |
| chainId       | int    | 是   | 链 ID                                                                    |
| asset         | string | 是   | 币种名称，如 ASTER                                                       |
| fee           | string | 是   | 提现手续费（代币单位）                                                   |
| userNonce     | string | 是   | EVM 提现签名的纳秒级时间戳；与 V3 API `nonce` 相互独立，允许相差 1 小时以内 |
| receiver      | string | 是   | 提现收款地址，需与签名中的值一致                                         |
| userSignature | string | 是   | EIP712 提现签名                                                          |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

| 字段       | 说明               |
|------------|--------------------|
| withdrawId | 提现请求 ID，唯一值 |
| hash       | 用户签名的摘要     |

### 通过 fapi\[v3\] 提现 \[solana\] \[spot\]

* 注意：请参照 [Pro API-KEY 签名（V3）](#pro-api-key-签名v3) 说明生成请求签名，以下示例仅包含该接口特有参数。

请求：

```shell
curl --location --request POST 'https://sapi.asterdex.com/api/v3/aster/user-solana-withdraw?chainId=101&asset=USDT&amount=0.97&fee=0.5&receiver=BzsJhmtg2UtQWNw6764DkK5Y4GPjc1XMzRqAGqSziymK&userNonce=1773741793787000&userSignature=51pM5A46n5NzHYTtuzB7gh8FFfbkh4Aij1fceCZV2NtkiVvE7DADMnSvXFiUJvauKawdWaCfPhzCTVfXYcf1iteQ' \
  --header 'Content-Type: application/json'
```

参数：

| 参数          | 类型   | 必填 | 说明                                                                              |
|---------------|--------|------|-----------------------------------------------------------------------------------|
| amount        | string | 是   | 提现数量（代币单位）                                                              |
| chainId       | int    | 是   | 固定值：`101`                                                                     |
| asset         | string | 是   | 币种名称，如 USDT                                                                 |
| fee           | string | 是   | 提现手续费（代币单位）                                                            |
| receiver      | string | 是   | 提现收款地址                                                                      |
| userNonce     | string | 否   | Solana 提现签名的纳秒级时间戳；与 V3 API `nonce` 相互独立。目前非必填，但强烈建议携带 |
| userSignature | string | 否   | Base58 编码的 Ed25519 提现签名。目前非必填，但强烈建议携带                        |

响应：

```json
{
    "withdrawId": "1234567",
    "hash": "0x9a40f0119b670fb6b155744b51981f91c4c4c8a20c333441a63853fe7d055c90"
}
```

> 注意：`hash` 不是交易哈希，仅为唯一标识符。
