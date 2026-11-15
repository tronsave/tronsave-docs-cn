---
description: TronSave MCP 服务器 —— 53 个工具，涵盖报价、订单、自动购买和账户操作，通过 Streamable HTTP 提供，支持 OAuth 2.1 或 API 密钥身份验证。
---

# MCP 服务器

TronSave 提供托管的 [Model Context Protocol](https://modelcontextprotocol.io) 服务器，把 TronSave 市场开放给 AI 智能体：报价、创建和管理订单、自动购买与自动出售、账户及内部余额操作。它已以 `io.github.tronsave/mcp` 的名称收录在官方 MCP Registry 中。源代码：[github.com/tronsave/tronsave-mcp](https://github.com/tronsave/tronsave-mcp)（MIT）。

{% hint style="info" %}
想以普通用户身份在 Claude Code、Cursor 或 Claude 中使用？请参阅 [Use TronSave with AI Assistants](https://docs.tronsave.io/guides/ai-assistants)（英文）。
{% endhint %}

## 端点

所有端点均使用 **Streamable HTTP**（`POST`、`GET`、`DELETE`）。网络由 URL 决定。

<table><thead><tr><th width="330">URL</th><th width="110">网络</th><th>身份验证</th></tr></thead><tbody><tr><td><code>https://mcp.tronsave.io/mcp</code></td><td>主网</td><td>可选：公开工具无需验证；可发送 API 密钥请求头，或通过工具登录。也接受 OAuth 令牌。</td></tr><tr><td><code>https://mcp.tronsave.io/oauth/mcp</code></td><td>主网</td><td>必须使用 OAuth 2.1（用户登录前返回带 <code>WWW-Authenticate</code> 的 401）。</td></tr><tr><td><code>https://mcp.tronsave.io/testnet/mcp</code></td><td>Nile</td><td>同 <code>/mcp</code>。</td></tr><tr><td><code>https://mcp.tronsave.io/oauth/testnet/mcp</code></td><td>Nile</td><td>同 <code>/oauth/mcp</code>。</td></tr></tbody></table>

发现：`/.well-known/mcp.json`、`/llms.txt`、`/.well-known/oauth-authorization-server`，以及每个端点的受保护资源元数据（RFC 9728）。

## 身份验证

<table><thead><tr><th width="190">方式</th><th>做法</th><th>可用功能</th></tr></thead><tbody><tr><td><strong>无</strong></td><td>连接 <code>/mcp</code></td><td>公开工具：价格、报价、订单簿、市场数据，以及由钱包签名交易支付的链上订单。</td></tr><tr><td><strong>OAuth 2.1</strong></td><td>连接 <code>/oauth/mcp</code>。支持动态客户端注册和 PKCE S256；用户在 TronSave 页面上使用 API 密钥或钱包签名登录。访问令牌有效期 1 小时；刷新令牌会轮换，API 密钥授权为 30 天，钱包授权为 24 小时。</td><td>根据用户的登录方式，可使用 API 密钥工具和/或钱包工具。</td></tr><tr><td><strong>API 密钥</strong></td><td>请求头 <code>Authorization: ApiKey &#x3C;key></code>，或使用 <code>tronsave_login</code> 工具并传入 <code>apiKey</code></td><td>内部余额工具。参阅<a href="../authentication.md">身份验证 → 获取 API 密钥</a>。</td></tr><tr><td><strong>钱包签名</strong></td><td><code>tronsave_get_sign_message</code> → 在钱包中签署消息 → 用 <code>&#x3C;signature>_&#x3C;timestamp></code> 调用 <code>tronsave_login</code>。有效期 24 小时。</td><td>钱包账户工具：订单、自动出售、自动购买、通知、API 密钥。</td></tr></tbody></table>

`tronsave_get_session_status` 会报告当前会话可执行的操作。每个工具的要求也在 `tools/list` 中以 `_meta["io.tronsave/auth"]`（`public` | `session` | `signature-session`）标出。

## 工具

每个工具都带有 MCP 注解（`readOnlyHint`、`destructiveHint`、`idempotentHint`），因此客户端会在写操作前征求确认。错误返回 `{"error":{"code","message","retryable","hint"}}`。涉及花费资金的写操作绝不会自动重试。

### 会话

`tronsave_get_session_status`、`tronsave_get_sign_message`、`tronsave_login`、`tronsave_logout` —— 公开。

### 市场与报价（公开）

`tronsave_get_market_overview`、`tronsave_get_price_table`、`tronsave_get_system_info`、`tronsave_get_min_price`、`tronsave_list_order_books`、`tronsave_estimate_buy_resource`、`tronsave_suggest_create_order`、`tronsave_get_account_resources`、`tronsave_list_orders`、`tronsave_list_extendable_delegates`、`tronsave_list_extendable_receivers`、`tronsave_list_flash_unstake_orders`、`tronsave_estimate_flash_unstake`。

### 购买

`tronsave_create_order` —— 链上支付时为公开工具（由用户钱包签署支付交易）；使用 API 密钥登录时从内部余额扣款。

### 订单、账户与卖家（钱包签名）

`tronsave_get_order`、`tronsave_update_order`、`tronsave_cancel_order`、`tronsave_sell_order_manual`、`tronsave_get_user_info`、`tronsave_get_user_permissions`、`tronsave_get_user_auto_setting`、`tronsave_register_auto_sell`、`tronsave_update_auto_sell_setting`、`tronsave_get_seller_stats`、`tronsave_list_delegations`、`tronsave_list_internal_transactions`、`tronsave_get_flash_unstake_request`、`tronsave_generate_api_key`、`tronsave_revoke_api_key`。

### 自动购买与批量（钱包签名）

`tronsave_list_auto_buy_settings`、`tronsave_create_auto_buy_setting`、`tronsave_update_auto_buy_setting`、`tronsave_delete_auto_buy_setting`、`tronsave_bulk_create_internal_orders`。

### 通知与推荐（钱包签名）

`tronsave_list_notifications`、`tronsave_mark_notifications_read`、`tronsave_get_referral`、`tronsave_get_telegram_notify_setting`、`tronsave_update_telegram_notify_setting`、`tronsave_get_agency_bot`。

### 内部余额（API 密钥）

`tronsave_get_internal_account`、`tronsave_get_deposit_address`、`tronsave_internal_order_book`、`tronsave_internal_order_estimate`、`tronsave_internal_order_create`、`tronsave_internal_order_history`、`tronsave_internal_order_details`、`tronsave_internal_extend_delegates`、`tronsave_internal_create_extend_request`。

每个工具完整的输入和输出 schema：[TOOLS.md](https://github.com/tronsave/tronsave-mcp/blob/main/docs/TOOLS.md)。

## 单位

单价以每资源单位的 SUN 计，支付金额以 SUN 计（1 TRX = 1,000,000 SUN）。`durationSec` 以秒为单位。租期满一天起按每个开始的天数计费；精确总额请使用 `tronsave_estimate_buy_resource` 返回的 `estimateTrx`。内部延期工具中的 `extendTo` 为以秒为单位的 Unix 时间戳。

## 通过代码连接

```bash
# Claude Code
claude mcp add --transport http tronsave https://mcp.tronsave.io/oauth/mcp
```

```json
{ "mcpServers": { "tronsave": { "url": "https://mcp.tronsave.io/oauth/mcp" } } }
```

JSON 形式适用于 Cursor 及其他支持 `mcpServers` 的客户端。Anthropic 和 OpenAI API 的代码片段、原始 HTTP 调用以及完整的 OAuth 细节，请参阅[集成快速入门](https://mcp.tronsave.io/quickstart)。

## 后续步骤

* [Use TronSave with AI Assistants](https://docs.tronsave.io/guides/ai-assistants)（英文） · [身份验证](../authentication.md)
* [购买资源](buy-resources/README.md) · [扩展订单](extend-orders/README.md)
