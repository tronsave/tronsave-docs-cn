---
description: >-
  TronSave MCP 服务器 —— 让 AI 智能体通过 Streamable HTTP 查询价格、购买、延长和出售
  TRON 能量与带宽。包含端点、登录方式、全部 29 个工具及主要工作流。
---

# MCP 服务器

TronSave [Model Context Protocol](https://modelcontextprotocol.io) 服务器将 TronSave 市场以类型化 MCP 工具的形式提供给 AI 智能体。每个工具都有严格的输入**和**输出 schema，并带有 `readOnlyHint` / `destructiveHint` 注解，智能体和 MCP 客户端可以据此区分读取与写入操作。

| | |
|---|---|
| **主网** | `https://mcp.tronsave.io/mcp` |
| **测试网（Nile）** | `https://mcp.tronsave.io/testnet/mcp` |
| **传输** | Streamable HTTP —— `POST` 用于 JSON-RPC，`DELETE` 用于结束会话。`GET` 返回 `405`（无服务器主动推送流）。 |
| **协议版本** | `2025-06-18` |
| **单位** | 价格以 **SUN** 计，按每资源单位每天（1 TRX = 1,000,000 SUN）。时长以**秒**计。 |

## 连接

将任意 MCP 客户端指向该端点即可，连接时无需任何请求头：

```json
{
  "mcpServers": {
    "tronsave": { "url": "https://mcp.tronsave.io/mcp" }
  }
}
```

`initialize` 响应会携带 `mcp-session-id` 请求头，MCP 客户端会在之后的每个请求中自动带上它。

## 登录

除 `tronsave_get_sign_message` 和 `tronsave_login` 外，所有工具都需要已登录的会话；未登录时返回 `SESSION_REQUIRED`。登录会为**当前**的 `mcp-session-id`（不会改变）完成身份验证，有效期 24 小时 —— `tronsave_login` 会返回 `expiresAt`。

{% tabs %}
{% tab title="钱包签名" %}
适用于**所有**工具，账户、订单和 API 密钥管理必须使用此方式（下表中的“签名”）。

1. 调用 `tronsave_get_sign_message`，返回 `{ message, timestamp }`；`message` 是时间戳的十六进制编码。
2. 在客户端用 TRON 钱包以 `signMessageV2` 方式对 `message` 签名（例如 `tronWeb.trx.signMessageV2(message)`、TronLink 或 WalletConnect）。
3. 调用 `tronsave_login`，参数为 `{ "signature": "<signature>_<timestamp>" }`。
{% endtab %}

{% tab title="API 密钥" %}
适用于内部账户余额工具和市场数据（下表中的“任意”），不适用于“签名”工具。

* 调用 `tronsave_login`，参数为 `{ "apiKey": "YOUR_API_KEY" }` —— 原始密钥，不加前缀。
* 获取密钥请参阅[身份验证](../authentication.md)。在签名会话中，`tronsave_generate_api_key` 可签发密钥，`tronsave_get_user_info` 会在 `internalAccount.apiKey` 中返回该密钥。
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
`authorization: ApiKey …` HTTP 请求头只会在会话开始时被校验 —— 它**不会**让会话登录。请始终调用 `tronsave_login`。切勿向服务器发送私钥或助记词：签名始终在客户端完成。请像对待凭据一样对待 `mcp-session-id`；会话泄露后，在过期前他人都可以以你的身份操作。
{% endhint %}

## 工具

**会话**：“无需” —— 无需登录；“任意” —— API 密钥会话或钱包签名会话均可；“签名” —— 仅限钱包签名会话。**影响**来自工具的注解。

### 登录与账户

| 工具 | 会话 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_sign_message` | 无需 | 只读 | 签发用于签名登录的、可由钱包签名的时间戳消息。 |
| `tronsave_login` | 无需 | 写入 | 向 TronSave 进行身份验证，为当前会话登录。 |
| `tronsave_get_user_info` | 签名 | 只读 | 读取当前已登录用户的资料及其关联的 TronSave 内部账户。 |
| `tronsave_get_user_permissions` | 签名 | 只读 | 读取当前用户已启用的权限操作（`autoSettings.permitOperations`）。 |
| `tronsave_get_user_auto_setting` | 签名 | 只读 | 读取当前用户的自动出售配置（`autoSettings`）。 |

### 市场数据与定价

| 工具 | 会话 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_min_price` | 任意 | 只读 | 查询购买的最低单价。 |
| `tronsave_estimate_buy_resource` | 任意 | 只读 | 在下单前，为接收地址报价购买 ENERGY 或 BANDWIDTH 的价格与可用量。 |
| `tronsave_list_order_books` | 任意 | 只读 | 读取 `ENERGY` 或 `BANDWIDTH` 的市场深度分档。 |
| `tronsave_list_orders` | 任意 | 只读 | 分页查询订单列表。 |
| `tronsave_get_order` | 签名 | 只读 | 按 `id` 读取单个订单的完整快照（NORMAL、FAST 或 EXTEND 类型）。 |
| `tronsave_list_extendable_delegates` | 任意 | 只读 | 列出某个 `receiver` 与 `resourceType` 的可延长代理候选（仅用于规划）。 |

### 订单（钱包签名）

| 工具 | 会话 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_create_order` | 签名 | 写入 | 在 TronSave 市场创建新的购买订单。 |
| `tronsave_update_order` | 签名 | 写入 | 按 `orderId` 部分更新未完成订单（`receiver`、`newPrice`）。 |
| `tronsave_cancel_order` | 签名 | 破坏性 | 按 `orderId` 取消未完成订单。 |
| `tronsave_sell_order_manual` | 签名 | 破坏性 | 使用钱包 `signedTx` 手动完成现有订单的卖方成交。 |

### 卖家自动化与 API 密钥

| 工具 | 会话 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_register_auto_sell` | 签名 | 写入 | 为当前用户创建初始自动出售配置。 |
| `tronsave_update_auto_sell_setting` | 签名 | 写入 | 部分更新现有的自动出售配置。 |
| `tronsave_delete_auto_buy_setting` | 签名 | 破坏性 | 按 `id` 永久删除一条自动购买规则。 |
| `tronsave_generate_api_key` | 签名 | 破坏性 | 为当前用户生成新的内部 API 密钥。 |
| `tronsave_revoke_api_key` | 签名 | 破坏性 | 撤销调用者当前的内部 API 密钥。 |

### 内部账户余额

| 工具 | 会话 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_internal_account` | 任意 | 只读 | 获取当前会话的内部账户信息：代表地址、充值地址和余额（SUN）。 |
| `tronsave_get_deposit_address` | 任意 | 只读 | 获取内部账户的专属充值地址。 |
| `tronsave_internal_order_book` | 任意 | 只读 | 通过 API 密钥 REST 端点返回 ENERGY 或 BANDWIDTH 的当前市场深度/价格档位。 |
| `tronsave_internal_order_estimate` | 任意 | 只读 | 在提交前预估购买订单的 TRX 成本与可用量（内部账户）。 |
| `tronsave_internal_order_create` | 任意 | 写入 | 创建 ENERGY 或 BANDWIDTH 购买订单，从内部账户余额支付。 |
| `tronsave_internal_order_history` | 任意 | 只读 | 分页列出内部账户的订单历史，最新在前。 |
| `tronsave_internal_order_details` | 任意 | 只读 | 按订单 ID 获取单个内部账户订单的完整详情。 |
| `tronsave_internal_extend_delegates` | 任意 | 只读 | 返回某接收地址可延长的代理，以及用于延长流程的 `extendData`。 |
| `tronsave_internal_create_extend_request` | 任意 | 写入 | 为已代理的资源提交延长请求，从内部账户支付。 |

`tronsave_*` 平台工具使用 TronSave GraphQL 市场。内部账户余额工具通过 [REST API](README.md) 使用你充值到内部账户中的 TRX。

## 工作流

**查询价格。** `tronsave_get_min_price` 返回最低单价；`tronsave_estimate_buy_resource` 返回单价、总价 `estimateTrx`（SUN）和可用量；`tronsave_list_order_books` 按价格档位显示市场深度。

**从内部账户购买**（API 密钥或签名会话）：

1. 用 `tronsave_get_internal_account` 查看余额。如需充值，调用 `tronsave_get_deposit_address`（`amountTrx` ≥ 10）并转入相应 TRX。
2. 下单前立即调用 `tronsave_internal_order_estimate`。
3. 用预估得到的 `unitPrice`（或 `FAST` / `MEDIUM` / `SLOW`）调用 `tronsave_internal_order_create`，返回 `{ orderId }`。
4. 用 `tronsave_internal_order_details` 或 `tronsave_internal_order_history` 跟踪订单。

**在市场购买**（签名会话）：对相同的接收地址、数量和时长调用 `tronsave_estimate_buy_resource`，然后调用 `tronsave_create_order` 并指定 `paymentMethod`：

* `internal` —— 从内部账户支付；
* `onchain` —— 在客户端签名一笔向返回的 `systemDepositAddress` 转账 `estimateTrx` 的 TRX 交易，并作为 `paymentSignedTx` 传入。

**延长代理**（内部账户）：

1. 用接收地址、`extendTo`（Unix **毫秒**）和 `maxPriceAccepted` 调用 `tronsave_internal_extend_delegates`，返回可延长的记录和 `extendData`。
2. 将该 `extendData` 原样传给 `tronsave_internal_create_extend_request`。

{% hint style="info" %}
价格和可用量随每个 TRON 区块（约 3 秒）变化。创建或更新订单前请立即重新预估，切勿复用较早的报价。在调用任何写入或破坏性工具前，请向用户展示接收地址、数量、时长和 TRX 成本，并获得确认。
{% endhint %}

## 错误

调用失败时返回 `isError: true`，内容为 `{ error, message, details: { statusCode, code } }`：

| 错误 | 含义 |
|---|---|
| `SESSION_REQUIRED`（401） | 未登录或会话已过期 —— 请重新调用 `tronsave_login`。 |
| “Platform tools require a signature session” | 当前为仅 API 密钥会话 —— 请使用钱包签名登录。 |
| `REST_ERROR`，带 `TSAS:<n> <NAME>` | 由 TronSave API 透传，例如 `TSAS:107 INVALID_API_KEY`、`TSAS:220 ORDER_NOT_FOUND`。 |
| MCP `-32602` Input validation error | 参数不符合工具的输入 schema；消息中会指出具体字段。 |

## 面向智能体

* 智能体技能（连接、登录、工具、工作流）：`https://tronsave.io/.well-known/agent-skills/tronsave-api/SKILL.md`
* MCP 服务器卡片：`https://tronsave.io/.well-known/mcp/server-card.json`
* 智能体身份验证指南：`https://tronsave.io/auth.md`
* 服务器托管的快速入门：`https://mcp.tronsave.io/quickstart`

## 后续步骤

* [API 参考](README.md) · [身份验证](../authentication.md)
* [购买资源](buy-resources/README.md) · [延长订单](extend-orders/README.md)
