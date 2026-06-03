---
description: 完成你的第一次 TronSave API 调用 —— 获取密钥、预估、购买并确认一笔能量订单，大约只需 10 分钟。
---

# 开发者快速开始

本指南将带你完成第一次 TronSave API 调用 —— 从获取 API 密钥到成功成交你的第一笔能量订单 —— 大约只需 **10 分钟**。

## 开始之前

TronSave 为 API 调用支持**两种认证方式**。请选择适合你使用场景的一种：

|                  | API 密钥（内部账户）                                                          | 签名交易                                                     |
| ---------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **最适合**       | 自动化机器人、后端服务                                                        | 当你不希望在 TronSave 中持有 TRX 时                          |
| **工作原理**     | 将 TRX 充值到你的内部账户；TronSave 会自动扣除费用                            | 每次购买时从你自己的钱包签名一笔 TRX 交易                    |
| **复杂度**       | 更简单                                                                        | 需要集成 TronWeb                                            |
| **推荐**         | 适用于大多数使用场景                                                          | 当你需要完全自托管时                                        |

{% hint style="info" %}
**本指南使用 API 密钥方式。** 如果你想改用签名交易，请参阅[购买资源 → 签名交易](api-reference/buy-resources/signed-tx/README.md)。如需更深入的对比，请参阅[认证](authentication.md)。
{% endhint %}

## 第 1 步 —— 获取 API 密钥并充值 TRX

### 1.1 获取你的 API 密钥

1. 前往 [tronsave.io/market](https://tronsave.io/market)，点击 **Connect** 连接你的钱包。
2. 连接后，点击 **Address** 按钮 → 选择 **Account Info** → 点击 **Login TronSave** 并签名登录以确认。
3. 你的 API 密钥和充值地址将会显示出来 —— 请复制并保存它们。

{% hint style="info" %}
**正在测试？** 请使用位于 [testnet.tronsave.io](https://testnet.tronsave.io/) 的 Nile（测试网）—— 一切运作方式完全相同，但不会消耗真实的 TRX。请参阅下方的[测试环境](#test-environment-nile-testnet)表格。
{% endhint %}

### 1.2 将 TRX 充值到你的内部账户

点击 **Top Up** 获取你的充值地址，然后从任意钱包向该地址发送 TRX。

需要注意的几点：

* 每笔交易的最低充值额为 **10 TRX**。
* 你的首次充值需要额外约 1 TRX 来激活新地址。
* 你每天可获得 2 次免费充值；之后每次额外充值需花费 0.3 TRX。
* 你的余额会在约 3 秒内自动更新。

**通过 API 验证你的余额：**

```http
GET https://api.tronsave.io/v2/user-info
Headers: { "apikey": "YOUR_API_KEY" }
```

```json
{
  "error": false,
  "data": {
    "balance": "50000000",         // 50 TRX (unit: SUN — 1 TRX = 1,000,000 SUN)
    "depositAddress": "TKVSa...",  // Send TRX to this address to top up
    "representAddress": "TKVSa..." // Used as the "requester" field when placing orders
  }
}
```

## 第 2 步 —— 预估费用

在下单之前，调用预估接口查看该订单将花费多少 TRX。

```http
POST https://api.tronsave.io/v2/estimate-buy-resource
Content-Type: application/json
```

```json
{
  "receiver": "YOUR_TRON_RECEIVER_ADDRESS",
  "resourceType": "ENERGY",
  "resourceAmount": 65000,
  "durationSec": 900,
  "unitPrice": "MEDIUM"
}
```

**请求字段：**

| 字段             | 示例          | 说明                                                                          |
| ---------------- | ------------- | ----------------------------------------------------------------------------- |
| `receiver`       | `"TFwUFW..."` | 将接收能量的 TRON 地址                                                         |
| `resourceAmount` | `32000`       | 要购买的能量数量（单次 USDT TRC-20 转账约消耗 32,000 能量）                    |
| `durationSec`    | `259200`      | 租赁时长（秒）—— `259200` = 3 天                                              |
| `unitPrice`      | `"MEDIUM"`    | 参见下方的价格表                                                              |

**选择 `unitPrice`：**

| 取值           | 何时使用                               | 价格等级    |
| -------------- | -------------------------------------- | ----------- |
| `"MEDIUM"`     | 默认 —— 适合大多数情况                 | 适中        |
| `"FAST"`       | 需要订单立即成交                       | 较高        |
| `"SLOW"`       | 对时间不敏感，优先考虑省钱             | 最低        |
| `number` (SUN) | 完全控制价格，例如 `80`                | 自定义      |

**响应：**

```json
{
  "error": false,
  "data": {
    "unitPrice": 64,
    "durationSec": 900,
    "estimateTrx": 4230000,    // Estimated cost: 4.23 TRX
    "availableResource": 65000 // Market currently has enough Energy available
  }
}
```

{% hint style="warning" %}
如果 `availableResource` 小于你请求的 `resourceAmount`，说明市场当前没有足够的能量来完全成交该订单。请参阅下一步中的 `options.allowPartialFill`。
{% endhint %}

## 第 3 步 —— 创建购买订单

确认费用看起来合理后，即可创建订单：

```http
POST https://api.tronsave.io/v2/buy-resource
Headers: { "apikey": "YOUR_API_KEY" }
Content-Type: application/json
```

```json
{
  "receiver": "YOUR_TRON_RECEIVER_ADDRESS",
  "resourceType": "ENERGY",
  "resourceAmount": 65000,
  "durationSec": 900,
  "unitPrice": "MEDIUM",
  "options": {
    "allowPartialFill": true,
    "preventDuplicateIncompleteOrders": true
  }
}
```

**推荐的 `options` 配置：**

```jsonc
// For production bots (most common setup)
{
  "allowPartialFill": true,                 // Accept partial fill if market supply is low
  "preventDuplicateIncompleteOrders": true, // Prevent duplicate orders on retry
  "maxPriceAccepted": 100                   // Reject if unit price exceeds 100 SUN
}

// For urgent / must-fill-now orders
{
  "allowPartialFill": true,
  "onlyCreateWhenFulfilled": true
}
```

**成功响应：**

```json
{
  "error": false,
  "data": {
    "orderId": "6818426a65fa8ea36d119999"
  }
}
```

请保存 `orderId` —— 下一步会用到它。

## 第 4 步 —— 查询订单状态

```http
GET https://api.tronsave.io/v2/order/6818426a65fa8ea36d119d2c
Headers: { "apikey": "YOUR_API_KEY" }
```

```json
{
  "error": false,
  "data": {
    "id": "6818426a65fa8ea36d119d2c",
    "resourceAmount": 65000,
    "resourceType": "ENERGY",
    "fulfilledPercent": 100,   // 100 = order fully matched
    "remainAmount": 0,
    "price": 64,               // Actual unit price (SUN)
    "payoutAmount": 4230000,   // Total charged (SUN)
    "durationSec": 900,
    "delegates": [
      {
        "delegator": "THnnMC...", // Provider who delegated the Energy
        "amount": 65000,
        "txid": "19d3fa..."       // On-chain transaction ID
      }
    ]
  }
}
```

**解读 `fulfilledPercent`：**

| 取值   | 含义                                                              |
| ------ | ----------------------------------------------------------------- |
| `100`  | 订单已完全成交 —— 能量已被代理（委托）                           |
| `1–99` | 部分成交（仅当 `allowPartialFill: true` 时可能出现）             |
| `0`    | 待处理 —— 等待供应商匹配                                         |

{% hint style="success" %}
能量通常会在订单匹配后的**几秒到几分钟内**完成代理（委托）。
{% endhint %}

## 完整示例（JavaScript）

从预估到订单验证的完整流程：

```javascript
const TRONSAVE_API = "https://api.tronsave.io";
const API_KEY = "YOUR_API_KEY";
const RECEIVER = "YOUR_TRON_RECEIVER_ADDRESS";

async function buyEnergy(amount = 32000, durationSec = 259200) {
  // Step 1: Estimate cost
  const estimateRes = await fetch(`${TRONSAVE_API}/v2/estimate-buy-resource`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      receiver: RECEIVER,
      resourceType: "ENERGY",
      resourceAmount: amount,
      durationSec,
      unitPrice: "MEDIUM",
    }),
  });
  const { data: estimate } = await estimateRes.json();
  console.log(`Estimated cost: ${estimate.estimateTrx / 1e6} TRX`);

  // Step 2: Create a buy order
  const buyRes = await fetch(`${TRONSAVE_API}/v2/buy-resource`, {
    method: "POST",
    headers: {
      "apikey": API_KEY,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      receiver: RECEIVER,
      resourceType: "ENERGY",
      resourceAmount: amount,
      durationSec,
      unitPrice: "MEDIUM",
      options: {
        allowPartialFill: true,
        preventDuplicateIncompleteOrders: true,
        maxPriceAccepted: 100,
      },
    }),
  });
  const { data: order } = await buyRes.json();
  console.log(`Order created: ${order.orderId}`);

  // Step 3: Wait and check order status
  await new Promise((r) => setTimeout(r, 5000)); // Wait 5 seconds

  const statusRes = await fetch(`${TRONSAVE_API}/v2/order/${order.orderId}`, {
    headers: { "apikey": API_KEY },
  });
  const { data: status } = await statusRes.json();

  if (status.fulfilledPercent === 100) {
    console.log(`Successfully delegated ${status.resourceAmount} Energy to ${RECEIVER}`);
  } else {
    console.log(`Order pending... ${status.fulfilledPercent}% filled`);
  }

  return status;
}

buyEnergy();
```

{% hint style="info" %}
**更喜欢用 SDK？** [SDK](sdk.md)（TypeScript、Rust、Python、Java 和 PHP）能让你更快上手：

```bash
npm install tronsave-sdk
```

这会安装 TypeScript 包；其他语言请参阅 [SDK](sdk.md) 页面。
{% endhint %}

## 测试环境（Nile 测试网）

测试时请替换基础 URL：

<table><thead><tr><th width="124"></th><th width="268">生产环境</th><th>测试网</th></tr></thead><tbody><tr><td><strong>网站</strong></td><td><code>tronsave.io</code></td><td><code>https://testnet.tronsave.io</code></td></tr><tr><td><strong>API</strong></td><td><code>https://api.tronsave.io</code></td><td><code>https://api-dev.tronsave.io</code></td></tr></tbody></table>

所有接口路径保持不变 —— 只需更改域名。详情请参阅[环境与网络](environments.md)。

## 后续步骤

现在你已经下了第一笔订单，接下来你可以：

* [**延长订单**](api-reference/extend-orders/README.md) —— 在租赁到期前续期时长，无需创建新订单。
* [**查看订单历史**](api-reference/buy-resources/api-key/order-history.md) —— 跟踪你账户下的所有订单。
* [**使用签名交易购买**](api-reference/buy-resources/signed-tx/README.md) —— 如果你不希望在 TronSave 内部持有 TRX。
* [**完整代码示例**](code-examples/README.md) —— JS、PHP 和 Python 的完整可运行示例。

***

_需要帮助？请通过_ [_Telegram_](https://t.me/tronsave) _联系我们，或查看_ [_常见问题_](../resources/faq.md)_。_
