---
description: 在哪里查看 TronSave 服务状态、如何上报事故，以及关于正常运行时间预期的须知。
---

# 状态与 SLA

本页介绍在哪里查看 TronSave 是否正常运行、如何上报问题，以及在发生事故时应有的预期。内容涵盖**网站**和 **API**（主网和 Nile（测试网））。

## 服务状态

{% hint style="info" %}
TronSave 未发布专门的状态/正常运行时间页面。要查看服务是否正常运行，请直接测试相关环境（见下文），或在社区频道（[TronSave | Energy Service Group](https://t.me/tronsaveofficial)）询问。
{% endhint %}

在没有状态页的情况下，你可以通过对某个读取端点发起一个轻量级请求，来确认某个特定环境是否可达：

{% tabs %}
{% tab title="cURL" %}
```bash
# Mainnet
curl -s -o /dev/null -w "%{http_code}\n" https://api.tronsave.io/v2/user-info -H "apikey: YOUR_API_KEY"

# Nile testnet
curl -s -o /dev/null -w "%{http_code}\n" https://api-dev.tronsave.io/v2/user-info -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API = "https://api.tronsave.io"; // or https://api-dev.tronsave.io

const res = await fetch(`${TRONSAVE_API}/v2/user-info`, {
  headers: { apikey: "YOUR_API_KEY" },
});

console.log(res.status); // 200 means the API is reachable and your key is valid
```
{% endtab %}
{% endtabs %}

返回 `200` 表示 API 可达且你的密钥有效。返回 `5xx` 或连接超时则表明问题出在服务端，而非你的请求本身。关于各个状态码的含义，请参阅 [错误与速率限制](../developers/errors-and-rate-limits.md)。

## "状态"涵盖哪些内容

TronSave 有多个独立运营的服务面。其中一个出现问题并不一定意味着其他服务面也受影响：

<table><thead><tr><th width="170">服务面</th><th>端点 / URL</th><th>提供的服务</th></tr></thead><tbody><tr><td><strong>网站（主网）</strong></td><td><code>https://tronsave.io</code></td><td>买卖界面、账户面板</td></tr><tr><td><strong>网站（Nile）</strong></td><td><code>https://testnet.tronsave.io</code></td><td>测试网界面</td></tr><tr><td><strong>API（主网）</strong></td><td><code>https://api.tronsave.io</code></td><td>生产环境 API</td></tr><tr><td><strong>API（Nile）</strong></td><td><code>https://api-dev.tronsave.io</code></td><td>测试网 API</td></tr><tr><td><strong>TRON 网络</strong></td><td>主网 / Nile</td><td>链上代理（委托）与结算</td></tr></tbody></table>

{% hint style="warning" %}
订单的履约和代理（委托）最终依赖于 **TRON 网络**本身。如果底层链拥堵或出现问题，即使 TronSave 自身的服务完全正常运行，订单也可能结算缓慢。
{% endhint %}

有关每个环境的完整详情，请参阅 [环境](../developers/environments.md)。

## 上报事故

如果你认为 TronSave 已停机或性能下降：

1. **确认影响范围** — 使用上文的请求测试受影响的服务面，并记下 HTTP 状态码或错误信息。
2. **记录详情** — 记录时间戳、环境（主网或 Nile）、受影响的端点或页面，以及返回的任何 `requestId` / 订单 ID。
3. **通过官方支持渠道上报。** 支持 / 退款：[t.me/wantingtrx](https://t.me/wantingtrx)；社区：[TronSave | Energy Service Group](https://t.me/tronsaveofficial)。

对于非停机类问题（订单卡住、转账失败、意外错误），请先查阅 [故障排查](troubleshooting.md) 和 [转账恢复](transfer-recovery/)，再上报事故。

## 服务水平预期

TronSave 未发布正式的、具有合约效力的服务水平协议（SLA）（没有正常运行时间保证、支持响应承诺或服务赔付额度）。服务的使用受 [服务条款](terms-of-service.md) 约束。

{% hint style="info" %}
如果你的业务规模较大，需要正常运行时间保证或支持响应承诺，请通过 [t.me/wantingtrx](https://t.me/wantingtrx) 联系以讨论企业级条款。
{% endhint %}

## 构建具备韧性的系统

无论是否适用正式的 SLA，都应将 API 视为远程依赖，并进行防御性设计：

* 对 `5xx` 和超时响应**采用退避策略重试**；不要对 `4xx` 重试。请参阅 [错误与速率限制](../developers/errors-and-rate-limits.md)。
* 在可能的情况下**使订单创建具备幂等性**，以便重试的请求不会下重复订单。
* **轮询订单状态**，而不是假定请求已成功 — 在依赖代理（委托）之前，先确认 `fulfilledPercent`。
* 为时间敏感的交易**准备一个回退方案**（例如，如果订单无法及时成交，就让 TRX 直接燃烧）。

## 后续步骤

* [错误与速率限制](../developers/errors-and-rate-limits.md) — 状态码与重试指南
* [故障排查](troubleshooting.md) — 常见问题与解决办法
* [环境](../developers/environments.md) — 主网与 Nile 端点
* [服务条款](terms-of-service.md)
