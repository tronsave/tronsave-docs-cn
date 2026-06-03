---
description: 通过 TronSave API 购买和延长能量与带宽的端到端、可复制粘贴的代码示例。
---

# 代码示例

这些页面包含完整、可运行的脚本，端到端地演示了完整的 TronSave 集成流程——从构建并签名交易，到下单并确认订单已成交。每个示例都是自包含的：复制它，设置好你的凭证，然后运行即可。

示例分为两种类型：

* **API 密钥** — 使用 TronSave API 密钥进行身份验证。最适合从预付的 TronSave 余额中付款的后端服务。参见[身份验证](../authentication.md)。
* **私钥** — 直接使用 TRON 账户的私钥进行签名和付款，无需预付余额。

{% hint style="info" %}
所有示例均使用 [TronWeb](https://tronweb.network/) `5.3.2`。通过以下命令安装：

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

更多内容请阅读 [TronWeb 5.3.2 发布说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)。
{% endhint %}

{% hint style="warning" %}
大多数示例引用的是主网 TronSave 接收地址 `TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S`。在针对测试网（Nile）运行时，请将其更改为 `TATT1UzHRikft98bRFqApFTsaSw73ycfoS`。参见[环境与网络](../environments.md)。
{% endhint %}

## v2（当前）

新集成请使用这些示例。它们针对的是当前的 TronSave API。

<table data-header-hidden><thead><tr><th></th><th></th><th></th></tr></thead><tbody>
<tr><td><strong>示例</strong></td><td><strong>身份验证</strong></td><td><strong>说明</strong></td></tr>
<tr><td><a href="v2/buy-with-api-key.md">使用 API 密钥购买资源</a></td><td>API 密钥</td><td>预估成本并下单购买资源，从你的 TronSave 余额中付款。</td></tr>
<tr><td><a href="v2/buy-with-private-key.md">使用私钥购买资源</a></td><td>私钥</td><td>直接从 TRON 账户签名并支付资源订单。</td></tr>
<tr><td><a href="v2/extend-with-api-key.md">使用 API 密钥延长订单</a></td><td>API 密钥</td><td>延长一个活跃的代理（委托），从你的 TronSave 余额中付款。</td></tr>
<tr><td><a href="v2/extend-with-private-key.md">使用私钥延长订单</a></td><td>私钥</td><td>延长一个活跃的代理（委托），从 TRON 账户签名并付款。</td></tr>
</tbody></table>

## v0（旧版）

{% hint style="warning" %}
这些是旧版 v0 示例，仅供参考保留。新集成请使用上面的 v2 示例。
{% endhint %}

<table data-header-hidden><thead><tr><th></th><th></th><th></th></tr></thead><tbody>
<tr><td><strong>示例</strong></td><td><strong>身份验证</strong></td><td><strong>说明</strong></td></tr>
<tr><td><a href="v0/buy-with-api-key.md">使用 API 密钥购买能量</a></td><td>API 密钥</td><td>从你的 TronSave 余额中付款购买能量的旧版流程。</td></tr>
<tr><td><a href="v0/buy-with-private-key.md">使用私钥购买能量</a></td><td>私钥</td><td>从 TRON 账户签名购买能量的旧版流程。</td></tr>
<tr><td><a href="v0/extend-with-api-key.md">使用 API 密钥延长订单</a></td><td>API 密钥</td><td>使用 API 密钥延长一个活跃代理（委托）的旧版流程。</td></tr>
</tbody></table>

## 后续步骤

* [快速开始](../quickstart.md) — 通往你第一笔订单的最短路径。
* [API 参考](../api-reference/README.md) — 这些示例调用的每个端点。
* SDK — 如果你不想直接调用 HTTP API，这是一个带类型的 TypeScript 封装。
