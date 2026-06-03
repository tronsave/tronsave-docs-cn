---
description: TronSave REST API — 基础 URL、身份验证、版本管理、响应封装，以及指向每个端点分组的链接。
---

# API 参考

TronSave API 让你能够以编程方式在 TRON 网络上购买、续期和出售能量与带宽。每个端点都是通过 HTTPS 发起的 JSON REST 调用。

## 基础 URL

| 环境 | API 基础 URL |
| --- | --- |
| 生产环境 | `https://api.tronsave.io` |
| 测试网 (Nile) | `https://api-dev.tronsave.io` |

端点通过路径前缀进行版本管理 —— 当前版本为 `v2`，例如 `https://api.tronsave.io/v2/buy-resource`。完整的主机和链列表请参阅 [环境与网络](../environments.md)。

## 身份验证

大多数写入端点都要求在 `apikey` 请求头中传入 API 密钥：

```bash
curl -X POST https://api.tronsave.io/v2/buy-resource \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ ... }'
```

支付和授权订单有两种方式：

* **使用 API 密钥** —— 为 TronSave 内部账户充值，然后用你的 API 密钥对每个请求进行身份验证。快速、易于集成，并能节省交易费用，但资金会保存在由 TronSave 托管的内部账户中。
* **使用签名交易** —— 构建一笔交易，用你的私钥对其签名，然后提交。这能让你完全掌控资金并拥有更高的安全标准，但代价是链上交易费用和更复杂的集成。

关于如何获取密钥、为账户充值以及在两种方式之间做出选择，请参阅 [身份验证](../authentication.md)。

## 版本管理

`v2` 是当前 API，也是本参考文档全篇所记录的版本。此前的 `v0` API 仍可供现有集成使用。

{% hint style="warning" %}
新集成应使用 `v2`。旧版 `v0` API 仅出于向后兼容的目的进行维护 —— 参见 [旧版 API (v0)](../legacy-api-v0/README.md)。
{% endhint %}

## 响应封装

每个 API 响应都被包装在一致的 JSON 封装中：

```json
{
  "error": false,
  "message": "OK",
  "data": { }
}
```

<table>
  <thead>
    <tr><th width="160">字段</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr><td><code>error</code></td><td>布尔标志 —— 成功时为 <code>false</code>，失败时为 <code>true</code>。</td></tr>
    <tr><td><code>message</code></td><td>人类可读的状态或错误消息。</td></tr>
    <tr><td><code>data</code></td><td>响应载荷（对象）—— 其结构取决于具体端点。</td></tr>
  </tbody>
</table>

关于错误代码、HTTP 状态映射和速率限制行为，请参阅 [错误与速率限制](../errors-and-rate-limits.md)。

## 端点分组

| 分组 | 功能 |
| --- | --- |
| [购买资源](buy-resources/README.md) | 预估成本并创建订单以购买能量或带宽，可通过签名交易或 API 密钥进行。 |
| [续期订单](extend-orders/README.md) | 查找可续期的代理（委托）并延长现有订单的时长。 |
| [出售资源](sell-resources.md) | 列出并管理你向市场提供的资源。 |
| [快速充值](fast-charge/README.md) | 快速补充资源 —— 预估、创建、跟踪、确认和取消订单。 |
| [MCP 服务器](mcp.md) | 使用 TronSave 的 Model Context Protocol 服务器从 AI 智能体驱动 API。 |

## 后续步骤

* [开发者快速开始](../quickstart.md) · [身份验证](../authentication.md) · [环境与网络](../environments.md)
* [购买资源](buy-resources/README.md) · [错误与速率限制](../errors-and-rate-limits.md)
