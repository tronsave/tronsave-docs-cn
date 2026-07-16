---
description: 介绍 TronSave 的两个 API 环境：主网与 Nile 测试网。两者端点路径完全相同，只需替换基础域名即可切换，让你在不花费真实 TRX 的情况下测试能量租赁集成。
---

# 环境

TronSave 运行在两个网络上：用于真实订单的**主网**，以及用于开发和测试的 **Nile 测试网**。两者的 API 接口完全相同——只有基础域名不同。

## 主网与 Nile 测试网对比

<table><thead><tr><th width="124"></th><th>主网（生产环境）</th><th>Nile（测试网）</th></tr></thead><tbody><tr><td><strong>网站</strong></td><td><code>https://tronsave.io</code></td><td><code>https://testnet.tronsave.io</code></td></tr><tr><td><strong>API 基础地址</strong></td><td><code>https://api.tronsave.io</code></td><td><code>https://api-dev.tronsave.io</code></td></tr><tr><td><strong>TRON 网络</strong></td><td>主网</td><td>Nile 测试网</td></tr><tr><td><strong>TRX</strong></td><td>真实 TRX</td><td>测试 TRX（无实际价值）</td></tr></tbody></table>

{% hint style="info" %}
**所有端点路径在各环境中都是相同的**。要面向测试网，只需替换基础域名即可——例如 `https://api.tronsave.io/v2/user-info` 变为 `https://api-dev.tronsave.io/v2/user-info`。
{% endhint %}

## 切换环境

将基础 URL 保存在单个变量中，这样你只需更改一个值即可在不同环境之间切换。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Mainnet
const TRONSAVE_API = "https://api.tronsave.io";

// Nile testnet
// const TRONSAVE_API = "https://api-dev.tronsave.io";

const res = await fetch(`${TRONSAVE_API}/v2/user-info`, {
  headers: { apikey: "YOUR_API_KEY" },
});
```
{% endtab %}

{% tab title="cURL" %}
```bash
# Mainnet
curl https://api.tronsave.io/v2/user-info -H "apikey: YOUR_API_KEY"

# Nile testnet
curl https://api-dev.tronsave.io/v2/user-info -H "apikey: YOUR_API_KEY"
```
{% endtab %}
{% endtabs %}

## 在 Nile 上测试

Nile 测试网的行为与主网完全一致，但使用的是测试 TRX，因此你可以完整运行预估 → 购买 → 查询状态的流程，而无需花费真实资金。

1. 前往 [testnet.tronsave.io](https://testnet.tronsave.io)，连接一个已配置为 Nile 网络的钱包。
2. 用与主网相同的方式获取 API 密钥和充值地址——参见[快速开始](quickstart.md)。
3. 通过 [Nile 水龙头](https://nileex.io/join/getJoinPage)为你的账户充值测试 TRX。

{% hint style="warning" %}
API 密钥、内部账户余额和订单在主网与 Nile 之间是**相互独立**的。在某一环境中签发的 API 密钥无法在另一环境中使用。
{% endhint %}

## 不支持 Shasta

TronSave **不**支持 **Shasta** 测试网。请使用 **Nile** 测试网进行所有测试。

## 后续步骤

* [快速开始](quickstart.md) · [身份验证](authentication.md)
* [术语表](../concepts/glossary.md) · [订单类型](../concepts/order-types.md)
