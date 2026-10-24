---
description: >-
  TronSave MCP 服务器 —— 让 AI 智能体通过 Streamable HTTP 查询价格、购买、延长和出售
  TRON 能量与带宽。包含端点、登录方式、全部 53 个工具及主要工作流。
---

# MCP 服务器

TronSave [Model Context Protocol](https://modelcontextprotocol.io) 服务器将 TronSave 市场以类型化 MCP 工具的形式提供给 AI 智能体。每个工具都有严格的输入**和**输出 schema，并带有 `readOnlyHint` / `destructiveHint` 注解，智能体和 MCP 客户端可以据此区分读取与写入操作。服务器还提供一个指南资源（`tronsave://guide/agent`）和两个提示词（`buy_resource`、`account_overview`）。

| | |
|---|---|
| **主网** | `https://mcp.tronsave.io/mcp` |
| **测试网（Nile）** | `https://mcp.tronsave.io/testnet/mcp` |
| **传输** | Streamable HTTP —— `POST` 用于 JSON-RPC，`DELETE` 用于结束会话。`GET` 返回 `405`（无服务器主动推送流）。 |
| **协议版本** | `2025-06-18` |
| **单位** | 价格以 **SUN** 计，按每资源单位每天（1 TRX = 1,000,000 SUN）。时长以**秒**计；`extendTo` 为 Unix 时间（**秒**）。 |

网络由 URL 决定。请使用测试网端点（Nile TRX，无实际价值）试用写入类工具。

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

市场数据、报价、订单列表和链上支付的订单**无需登录**。其他工具需要 API 密钥或钱包签名，下表的**登录**列注明了具体要求。MCP 客户端也可以从 `tools/list` 的 `_meta["io.tronsave/auth"]` 读取同一信息（`public` = 无需登录，`session` = API 密钥，`signature-session` = 钱包签名）。随时可调用 `tronsave_get_session_status` 查看当前登录状态。

{% tabs %}
{% tab title="API 密钥" %}
可使用内部余额工具以及 `paymentMethod: "internal"`。

* 调用 `tronsave_login`，参数为 `{ "apiKey": "YOUR_API_KEY" }`（原始密钥，不加前缀），**或**
* 让客户端在每个请求中发送 `Authorization: ApiKey YOUR_API_KEY`（或 `x-api-key: YOUR_API_KEY`），此时无需调用登录工具。

获取密钥请参阅[身份验证](../authentication.md)。使用签名登录后，`tronsave_generate_api_key` 可签发密钥。
{% endtab %}

{% tab title="钱包签名" %}
可使用用户资料、自己的订单、自动购买和自动出售规则、卖家数据、通知、推荐、Telegram 通知、批量购买以及 API 密钥管理。

1. 调用 `tronsave_get_sign_message`，返回 `{ message, timestamp }`。
2. 在用户的 TRON 钱包中以 `signMessageV2` 方式对 `message` 签名（例如 `tronWeb.trx.signMessageV2(message)`、TronLink 或 WalletConnect）。
3. 调用 `tronsave_login`，参数为 `{ "signature": "<signature>_<timestamp>" }`。

如果该钱包有内部账户，其 API 密钥也会一并加载，同一会话中即可使用 API 密钥类工具。
{% endtab %}
{% endtabs %}

登录绑定到当前的 `mcp-session-id`，有效期 24 小时（`expiresAt`）；`tronsave_logout` 可移除登录。

{% hint style="warning" %}
切勿向服务器发送私钥或助记词：所有签名都在用户的钱包中完成。请像对待凭据一样对待 `mcp-session-id` 和 API 密钥；会话泄露后，在过期前他人都可以以你的身份操作。
{% endhint %}

## 工具

**登录**：“无需” —— 无需登录；“API 密钥” —— 内部 API 密钥（登录、请求头，或拥有内部账户的钱包的签名登录）；“签名” —— 钱包签名登录。**影响**来自工具的注解。

### 会话

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_sign_message` | 无需 | 只读 | 返回用于签名登录、需由钱包签名的消息。 |
| `tronsave_login` | 无需 | 写入 | 使用 API 密钥或钱包签名为当前 MCP 会话登录。 |
| `tronsave_logout` | 无需 | 写入 | 移除当前 MCP 会话的登录。 |
| `tronsave_get_session_status` | 无需 | 只读 | 显示当前登录状态（无、API 密钥或签名）、到期时间以及可用的工具组。 |

### 市场数据与报价（无需登录）

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_min_price` | 无需 | 只读 | 指定数量和时长的最低单价（SUN）。 |
| `tronsave_estimate_buy_resource` | 无需 | 只读 | 一笔购买的单价、总价（SUN）和可用量。 |
| `tronsave_get_price_table` | 无需 | 只读 | 价格表：每个数量 × 时长组合的单价。 |
| `tronsave_list_order_books` | 无需 | 只读 | 按价格档位显示的市场深度。 |
| `tronsave_suggest_create_order` | 无需 | 只读 | 完成一笔购买的最便宜方式：新订单、追加购买或延长现有代理。 |
| `tronsave_get_market_overview` | 无需 | 只读 | 可用及挂单的能量/带宽、资金池卖家数量、卖家 APY。 |
| `tronsave_get_system_info` | 无需 | 只读 | TronSave 与 TRON 参数：最小购买量、燃烧费率、系统充值地址。 |
| `tronsave_get_account_resources` | 无需 | 只读 | 任意地址已质押和可代理的能量/带宽及 TRX 余额。 |
| `tronsave_list_orders` | 无需 | 只读 | 分页列出市场订单。 |
| `tronsave_list_extendable_delegates` | 无需 | 只读 | 某接收地址可延长的代理（用于规划）。 |
| `tronsave_list_extendable_receivers` | 无需 | 只读 | 某地址所购、仍可延长代理的接收地址。 |
| `tronsave_list_flash_unstake_orders` | 无需 | 只读 | 闪电解押市场：可接单、进行中或已完成的订单。 |
| `tronsave_estimate_flash_unstake` | 无需 | 只读 | 某地址现在闪电解押可获得的金额。 |

### 订单

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_create_order` | 无需 | 写入 | 创建购买订单，链上支付（无需登录）或从内部余额支付（API 密钥）。 |
| `tronsave_get_order` | 签名 | 只读 | 单个订单的完整信息。 |
| `tronsave_update_order` | 签名 | 写入 | 修改未成交订单的接收地址或价格。 |
| `tronsave_cancel_order` | 签名 | 破坏性 | 取消未成交订单。 |
| `tronsave_sell_order_manual` | 签名 | 破坏性 | 以卖家身份用已签名的代理交易成交订单。 |
| `tronsave_bulk_create_internal_orders` | 签名 | 写入 | 从内部余额为多个接收地址购买相同数量的资源。 |

### 内部余额（API 密钥）

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_internal_account` | API 密钥 | 只读 | 内部账户：余额（SUN）、代表地址和充值地址。 |
| `tronsave_get_deposit_address` | API 密钥 | 只读 | 为内部余额充值的地址。 |
| `tronsave_internal_order_book` | API 密钥 | 只读 | 通过 REST API 获取市场深度。 |
| `tronsave_internal_order_estimate` | API 密钥 | 只读 | 从内部余额支付的购买的成本和可用量。 |
| `tronsave_internal_order_create` | API 密钥 | 写入 | 从内部余额支付购买能量/带宽。 |
| `tronsave_internal_order_history` | API 密钥 | 只读 | 内部账户的订单，按时间倒序。 |
| `tronsave_internal_order_details` | API 密钥 | 只读 | 单个内部账户订单。 |
| `tronsave_internal_extend_delegates` | API 密钥 | 只读 | 可延长的代理以及延长请求所需的 `extendData`。 |
| `tronsave_internal_create_extend_request` | API 密钥 | 写入 | 延长代理，从内部余额支付。 |
| `tronsave_list_internal_transactions` | 签名 | 只读 | 内部余额流水：充值、支付、退款。 |

### 自动购买

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_list_auto_buy_settings` | 签名 | 只读 | 自动购买规则列表。 |
| `tronsave_create_auto_buy_setting` | 签名 | 写入 | 创建在接收地址资源不足时自动购买的规则。 |
| `tronsave_update_auto_buy_setting` | 签名 | 写入 | 暂停、恢复或修改自动购买规则。 |
| `tronsave_delete_auto_buy_setting` | 签名 | 破坏性 | 删除自动购买规则。 |

### 卖家与自动出售

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_seller_stats` | 签名 | 只读 | 卖家累计数据和每日收益。 |
| `tronsave_list_delegations` | 签名 | 只读 | 卖家向买家发出的代理记录。 |
| `tronsave_get_user_auto_setting` | 签名 | 只读 | 自动出售配置。 |
| `tronsave_register_auto_sell` | 签名 | 写入 | 创建自动出售配置。 |
| `tronsave_update_auto_sell_setting` | 签名 | 写入 | 修改自动出售配置。 |
| `tronsave_get_user_permissions` | 签名 | 只读 | 自动出售已启用的权限操作。 |

### 账户、通知与 API 密钥

| 工具 | 登录 | 影响 | 功能 |
|---|---|---|---|
| `tronsave_get_user_info` | 签名 | 只读 | 用户资料及关联的内部账户。 |
| `tronsave_list_notifications` | 签名 | 只读 | 通知列表（按时间倒序）及未读数量。 |
| `tronsave_mark_notifications_read` | 签名 | 写入 | 将一条或全部通知标为已读。 |
| `tronsave_get_referral` | 签名 | 只读 | 推荐码、推荐人、推荐列表和奖励。 |
| `tronsave_get_telegram_notify_setting` | 签名 | 只读 | Telegram 通知筛选条件。 |
| `tronsave_update_telegram_notify_setting` | 签名 | 写入 | 替换 Telegram 通知筛选条件（需已绑定 Telegram 账户）。 |
| `tronsave_get_agency_bot` | 签名 | 只读 | 用户的 Telegram 代理（转售）机器人及其奖励。 |
| `tronsave_get_flash_unstake_request` | 签名 | 只读 | 用户的闪电解押请求及支付进度。 |
| `tronsave_generate_api_key` | 签名 | 破坏性 | 签发新的内部 API 密钥。 |
| `tronsave_revoke_api_key` | 签名 | 破坏性 | 撤销当前的内部 API 密钥。 |

`tronsave_*` 平台工具使用 TronSave GraphQL 市场。内部余额工具通过 [REST API](README.md) 使用你充值到内部账户中的 TRX。

## 工作流

**查询价格**（无需登录）。`tronsave_get_min_price` 返回最低单价；`tronsave_estimate_buy_resource` 返回单价、总价 `estimateTrx`（SUN）和可用量。`tronsave_get_price_table` 给出数量 × 时长的价格表，`tronsave_list_order_books` 按价格档位显示市场深度，`tronsave_suggest_create_order` 说明新订单、追加购买或延长现有代理中哪种最便宜。

**从内部账户购买**（API 密钥）：

1. 用 `tronsave_get_internal_account` 查看余额。如需充值，调用 `tronsave_get_deposit_address`（`amountTrx` ≥ 10）并转入相应 TRX。
2. 下单前立即调用 `tronsave_internal_order_estimate`。
3. 用预估得到的 `unitPrice`（或 `FAST` / `MEDIUM` / `SLOW`）调用 `tronsave_internal_order_create`，返回 `{ orderId }`。
4. 用 `tronsave_internal_order_details` 或 `tronsave_internal_order_history` 跟踪订单。

**在市场购买**：对相同的接收地址、数量和时长调用 `tronsave_estimate_buy_resource`，然后调用 `tronsave_create_order` 并指定 `paymentMethod`：

* `onchain`（无需登录）—— 让用户签名一笔向返回的 `systemDepositAddress` 转账 `estimateTrx` 的 TRX 交易，并作为 `paymentSignedTx` 传入；
* `internal`（API 密钥）—— 从内部账户支付。

**为多个地址购买**（签名）：`tronsave_bulk_create_internal_orders` 从内部账户为每个接收地址购买相同数量的资源。`paymentAmount` 必须等于总支付额（±2%）。

**自动补充资源**（签名）：`tronsave_create_auto_buy_setting` 在接收地址的资源低于 `thresholdResourceAmount`（至少 100,000）时购买 `resourceAmount`，最多花费 `limitBudgetInSun`（需大于 10 TRX，且内部账户余额足够）。每个接收地址只能有一条启用中的规则。使用 `tronsave_list_auto_buy_settings`、`tronsave_update_auto_buy_setting`（`isActive` 暂停或恢复）和 `tronsave_delete_auto_buy_setting` 管理规则。

**延长代理**（API 密钥，从内部账户支付）：

1. 用接收地址、`extendTo`（Unix **秒**；传毫秒会被拒绝并返回 `INVALID_EXTEND_TIME`）和 `maxPriceAccepted` 调用 `tronsave_internal_extend_delegates`，返回可延长的记录和 `extendData`。
2. 将该 `extendData` 原样传给 `tronsave_internal_create_extend_request`。

**出售**（签名）：用 `tronsave_get_seller_stats` 和 `tronsave_list_delegations` 查看收益；用 `tronsave_get_user_auto_setting`，再用 `tronsave_register_auto_sell` 或 `tronsave_update_auto_sell_setting` 设置自动出售。`tronsave_get_account_resources`（无需登录）显示某地址可代理的资源。

{% hint style="info" %}
价格和可用量随每个 TRON 区块（约 3 秒）变化。创建或更新订单前请立即重新预估，切勿复用较早的报价。在调用任何写入或破坏性工具前，请向用户展示接收地址、数量、时长和 TRX 成本，并获得确认。
{% endhint %}

## 错误

调用失败时返回 `isError: true`，文本为 `{"error":{"code","message","retryable","hint"}}`。请按照 `hint` 处理。`retryable` 为 `false` 时切勿重复写入操作；写入操作出现 `UPSTREAM_TIMEOUT` 后，请先检查状态（例如用 `tronsave_get_order`）再重试。

| 代码 | 含义 | 处理方式 |
|---|---|---|
| `SESSION_REQUIRED` | 该工具需要登录 | 按提示登录后重试 |
| `INVALID_SESSION_TYPE` | 该工具需要钱包签名，当前为仅 API 密钥会话 | 使用钱包签名登录 |
| `API_KEY_SESSION_REQUIRED` | 该工具需要内部 API 密钥 | 使用 API 密钥登录或发送请求头 |
| `INVALID_API_KEY`、`INVALID_SIGNATURE`、`SIGNATURE_EXPIRED` | 凭据被拒绝 | 修正密钥或重新签名 |
| `UPSTREAM_UNAUTHORIZED` | TronSave 拒绝了该会话 | 重新登录 |
| `GRAPHQL_ERROR`、`REST_ERROR` | 请求被业务规则拒绝，例如 `TSAS:220 ORDER_NOT_FOUND` | 阅读消息并修正输入 |
| `UPSTREAM_TIMEOUT`、`UPSTREAM_UNREACHABLE`、`UPSTREAM_UNAVAILABLE` | TronSave 响应慢或不可用 | 读取：稍后重试。写入：先检查状态 |

“Input validation error” 表示参数不符合工具的输入 schema；消息中会指出具体字段。

## 面向智能体

* 智能体技能（连接、登录、工具、工作流）：`https://tronsave.io/.well-known/agent-skills/tronsave-api/SKILL.md`
* MCP 服务器卡片：`https://tronsave.io/.well-known/mcp/server-card.json`
* 智能体身份验证指南：`https://tronsave.io/auth.md`
* 服务器托管的快速入门：`https://mcp.tronsave.io/quickstart`
* 服务器发现文档：`https://mcp.tronsave.io/.well-known/mcp.json`、`https://mcp.tronsave.io/llms.txt`

## 后续步骤

* [API 参考](README.md) · [身份验证](../authentication.md)
* [购买资源](buy-resources/README.md) · [延长订单](extend-orders/README.md)
