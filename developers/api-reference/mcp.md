---
description: TronSave MCP 服务器 —— 通过 Streamable HTTP 让 AI 智能体驱动 TronSave 的身份验证、订单、定价和代理(委托)工作流。
---

# MCP 服务器

`tron-save-mcp-server` 是面向生产环境的 TronSave 生态系统 [Model Context Protocol](https://modelcontextprotocol.io) 服务器。它通过 **Streamable HTTP** 传输将 TronSave 业务操作作为 MCP 工具暴露出来，并采用基于 Redis 的会话以及强类型的 TypeScript + Zod 契约。

**核心能力：**

* 位于 `/mcp` 的 Streamable MCP 端点（`POST`、`GET`、`DELETE`）
* 双重身份验证模型（`ApiKey` 和 `Signature`）
* 借助 Redis TTL 实现的会话与 nonce 安全机制
* 类型化的 GraphQL 与 REST 集成
* 所有工具均采用严格的输入/输出 schema

## 使命

**TronSave 统一资源操作**

该服务器为平台级和内部 TronSave 操作提供统一的 MCP 接口 —— 涵盖身份验证、账户数据、订单生命周期、定价/预估，以及代理(委托)扩展工作流。

## 身份验证模型

该服务器支持两种凭据类型：

* **`ApiKey`** —— 使用你的 TronSave API 密钥进行身份验证。所有**内部操作**工具均需此凭据。
* **`Signature`** —— 使用钱包签名的消息进行身份验证。部分平台工具需此凭据。

调用 `tronsave_get_sign_message` 获取一次性消息/nonce，用你的钱包对其签名，然后调用 `tronsave_login` 创建服务器会话。

{% hint style="info" %}
内部工具始终需要 **API 密钥会话**。部分平台工具需要 **Signature 会话**。在 **Requires Login** 列中标记为 `No` 的工具可在 MCP 初始化后立即调用，无需任何登录步骤。
{% endhint %}

参阅 [Authentication](../authentication.md) 了解如何获取 API 密钥，以及如何在 API 密钥方式和签名交易方式之间进行选择。

## 工具分类

### 平台身份验证与身份

<table><thead><tr><th width="287">工具</th><th width="146">需要登录</th><th>描述</th></tr></thead><tbody><tr><td><code>tronsave_get_sign_message</code></td><td>否</td><td>返回供钱包签名以进行 Signature 登录的一次性消息/nonce。</td></tr><tr><td><code>tronsave_login</code></td><td>否</td><td>使用 ApiKey 或 Signature 凭据创建服务器会话。</td></tr><tr><td><code>tronsave_user_info_get</code></td><td>是</td><td>获取已验证平台用户的个人资料级信息。</td></tr><tr><td><code>tronsave_user_permissions_get</code></td><td>是</td><td>返回当前平台会话已授予的权限/范围。</td></tr><tr><td><code>tronsave_user_auto_setting_get</code></td><td>是</td><td>获取与平台用户行为相关的当前自动设置。</td></tr></tbody></table>

### 平台市场、订单与资源操作

<table><thead><tr><th width="218">工具</th><th width="151">需要登录</th><th>描述</th></tr></thead><tbody><tr><td><code>tronsave_order_book</code></td><td>否</td><td>返回公开的买/卖市场深度和当前订单簿数据。</td></tr><tr><td><code>tronsave_orders_list</code></td><td>否</td><td>列出当前用户/会话上下文的平台订单。</td></tr><tr><td><code>tronsave_order_detail</code></td><td>是</td><td>获取某个特定平台订单的详细信息。</td></tr><tr><td><code>tronsave_order_create</code></td><td>否</td><td>使用旧版嵌套载荷格式创建订单。</td></tr><tr><td><code>tronsave_order_create_onchain</code></td><td>否</td><td>使用 ONCHAIN 支付模式和扁平输入创建新订单。</td></tr><tr><td><code>tronsave_order_create_internal</code></td><td>否</td><td>使用 INTERNAL 余额和扁平输入创建新订单。</td></tr><tr><td><code>tronsave_order_create_pending</code></td><td>否</td><td>创建一个等待充值/结算流程的待处理订单。</td></tr><tr><td><code>tronsave_order_update</code></td><td>否</td><td>更新现有订单的可编辑字段。</td></tr><tr><td><code>tronsave_order_cancel</code></td><td>否</td><td>在取消规则允许时取消一个开放订单。</td></tr><tr><td><code>tronsave_order_sell_manual</code></td><td>否</td><td>为符合条件的资源持仓/订单触发手动卖出流程。</td></tr><tr><td><code>tronsave_estimate_buy_resource</code></td><td>否</td><td>在创建买入订单之前预估预期数量/成本。</td></tr><tr><td><code>tronsave_energy_price_table_get</code></td><td>是</td><td>返回能量/资源市场的定价表快照。</td></tr><tr><td><code>tronsave_user_seller_energy_stats_get</code></td><td>是</td><td>返回卖家侧的业绩和能量统计数据。</td></tr><tr><td><code>tronsave_extendable_delegates_get</code></td><td>否</td><td>列出可被扩展的代理(委托)条目。</td></tr></tbody></table>

### 内部操作

<table><thead><tr><th width="292">工具</th><th width="144">需要登录</th><th>描述</th></tr></thead><tbody><tr><td><code>tronsave_internal_account_get</code></td><td>是</td><td>获取 API 密钥会话的内部账户/余额详情。</td></tr><tr><td><code>tronsave_get_deposit_address</code></td><td>是</td><td>返回内部充值工作流的充值地址。</td></tr><tr><td><code>tronsave_internal_order_history</code></td><td>是</td><td>列出内部订单历史记录，支持筛选。</td></tr><tr><td><code>tronsave_internal_order_details</code></td><td>是</td><td>返回某条内部订单记录的完整详情。</td></tr><tr><td><code>tronsave_internal_order_estimate</code></td><td>是</td><td>在提交前为内部订单计算预估。</td></tr><tr><td><code>tronsave_internal_order_create</code></td><td>是</td><td>使用内部工作流参数创建新的内部订单。</td></tr><tr><td><code>tronsave_internal_extend_delegates</code></td><td>是</td><td>在内部工作流中扩展代理(委托)的时长/条款。</td></tr><tr><td><code>tronsave_internal_extend_request</code></td><td>是</td><td>为内部代理(委托)/订单流程提交扩展请求。</td></tr></tbody></table>

## 连接

MCP 服务器托管于 `https://mcp.tronsave.io/mcp`。该软件包以 `tron-save-mcp-server` 名称发布。

该端点使用 Streamable HTTP 传输，接受 `POST`、`GET` 和 `DELETE` 请求。将任意兼容 MCP 的客户端（例如 AI 智能体运行时）指向该主机端点：

```
https://mcp.tronsave.io/mcp
```

## 后续步骤

* [API 参考](README.md) · [Authentication](../authentication.md)
* [购买资源](buy-resources/README.md) · [扩展订单](extend-orders/README.md)
