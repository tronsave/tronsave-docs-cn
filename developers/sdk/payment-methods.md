---
description: 配置在你的 dApp 中由谁支付交易手续费 —— Gas Enterprise(由开发者出资)与 Gas-paid WTRX(由用户出资)—— 以及 SDK 接受的支付方式代码。
---

# 支付方式

当你将 TronSave 集成到 dApp 中时,你需要决定由谁来支付链上交易手续费,以及使用哪种代币支付。TronSave 将这一选择以一组**支付方式代码**的形式提供给你,你将其传递给 SDK,此外还提供两种交付模式:**Gas Enterprise**(由开发者赞助手续费)和 **Gas-paid WTRX**(由终端用户使用其已持有的代币支付手续费)。

## 支付方式代码

通过 `paymentMethodType` 字段将支付方式传递给 SDK。SDK 接受以下取值:

<table>
  <thead>
    <tr><th>代码</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr><td><code>1</code></td><td>链上 WTRX —— 用户使用 WTRX 支付交易手续费(默认)。</td></tr>
    <tr><td><code>11</code></td><td>由 dApp 代表用户支付交易手续费。</td></tr>
    <tr><td><code>12</code></td><td>由 dApp 支付交易手续费,但作为回报会从用户处收取一种代币。</td></tr>
    <tr><td><code>2</code>、<code>10</code>、<code>4</code></td><td>即将推出。</td></tr>
  </tbody>
</table>

{% hint style="info" %}
代码 `2`、`10` 和 `4` 目前尚不可用。
{% endhint %}

## Gas Enterprise(由开发者出资)

使用 Gas Enterprise 时,你的项目维护一个 gas 资金池,并代表你的用户支付交易手续费。这对应于支付方式 `11`(开发者使用 TRX 支付手续费)。你的用户无需在自己的钱包中持有 TRX 即可进行交易。

要进行设置,请在 TronSave 控制台中注册你的 dApp 并为你的项目充值:

* **主网控制台:** [dashboard.tronsave.io](https://dashboard.tronsave.io/)
* **测试网控制台:** [testnet.dashboard.tronsave.io](https://testnet.dashboard.tronsave.io)

步骤:

1. **充值并创建你的第一个项目。**

{% embed url="https://youtu.be/u1tygbEFTYU" %}

2. **添加一个合约并设置合约方法。**

{% embed url="https://youtu.be/tAU1zwrl89A" %}

Gas Enterprise 没有公布的最低充值额度或 gas 资金池补充门槛 —— 你可以根据预期的交易量,为项目充入任意合适的余额。

## Gas-paid WTRX(由用户出资)

如今,钱包中没有原生代币的用户必须退出 dApp,购买或兑换原生代币,然后再返回发送交易。启用使用 WTRX 支付 gas 的功能,可让你的用户使用其已持有的代币支付 gas 手续费。TronSave 目前支持使用 TRX 和 WTRX 支付 gas 手续费,并计划很快支持更多代币。这对应于支付方式 `1`(用户使用 WTRX 支付手续费)。

该功能主要通过以下两种方式改善终端用户的使用体验:

1. 让你的终端用户在其交互的区块链上节省 TRX 或原生代币。
2. 使用 dApp 时不会出现交易卡顿。

### 流程说明

该功能由 TronSave 升级后的中继器(relayer)基础设施提供支持。

在此流程中:

1. 中继器分享一份受支持代币的列表,你在 UI 中将其展示给用户。
2. 你的用户选择使用哪种受支持的代币来支付 gas 手续费。
3. 你展示使用该 WTRX 代币需支付的预估 gas。
4. 用户确认后,交易被发送至中继器。
5. 中继器使用原生代币支付 gas,并将交易发送至链上。
6. 中继器从用户的智能合约钱包中以 WTRX 形式获得退款。

<figure><img src="https://content.gitbook.com/content/tWFcrYCIaFhC14CKjggz/blobs/FXKKVxbhxOF0SoUtQqJ4/Screen%20Shot%202023-02-01%20at%2016.53.44.png" alt=""><figcaption></figcaption></figure>

## 后续步骤

* [开发者快速开始](../quickstart.md) —— 发起你的第一个 TronSave API 调用。
* [身份验证](../authentication.md) —— 比较 API 密钥与签名交易两种方式。
* [术语表](../../concepts/glossary.md) —— 能量、带宽、TRX、SUN 和 WTRX 的定义。
