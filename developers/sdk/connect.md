---
description: 安装 TronSave SDK，将其连接到 TronWeb，并查询你的 WTRX 支付余额。
---

# 连接 SDK

`@tronsave/sdk` 包让你可以使用 TronWeb 直接从应用程序中预估、购买和确认能量订单。本页介绍安装方法、在浏览器和服务器环境中连接 SDK、可用的配置选项，以及如何查询支付余额。

{% hint style="info" %}
最新的 SDK 版本是 **1.1.34**。
{% endhint %}

## 安装

SDK 依赖 `tronweb`，因此请同时安装两者：

{% tabs %}
{% tab title="npm" %}
```bash
npm i @tronsave/sdk tronweb
```
{% endtab %}

{% tab title="yarn" %}
```bash
yarn add @tronsave/sdk tronweb
```
{% endtab %}
{% endtabs %}

在连接之前，请先注册你的项目并获取 API 密钥。关于如何获取密钥并设置内部账户，请参阅[身份验证](../authentication.md)。

{% hint style="warning" %}
如果没有有效的 API 密钥，你将无法使用 `useRelay` 或 `forceUseRelay` 选项。
{% endhint %}

## 连接

`TronSave` 使用一个 TronWeb 实例和一个 `options` 对象进行初始化。`options` 用于配置如何验证请求以及如何构建支付交易。

{% tabs %}
{% tab title="Browser" %}
只有当 `tronWeb` 被注入到页面中（例如由钱包扩展程序注入）之后，TronSave 才能被初始化，因此请在构造实例之前等待它：

```typescript
import { TronSave } from '@tronsave/sdk'

// TronSave can only be initialized when tronWeb already exists
let tronSave = null;

const waitTronSave = () => {
  return new Promise((res) => {
    let attempts = 0,
      maxAttempts = 20;
    const checkTronWeb = () => {
      if ((window as any).tronWeb?.defaultAddress.base58) {
        const options = {
          apiKey: API_KEY,
          networks: "mainnet",
          // ...other options — see the table below
        };
        res(new TronSave(window.tronWeb, options));
        return;
      }
      attempts++;
      if (attempts >= maxAttempts) {
        res(null);
        return;
      }
      setTimeout(checkTronWeb, 100);
    };
    checkTronWeb();
  });
};

waitTronSave().then((res) => {
  if (res) {
    tronSave = res;
  }
});
```
{% endtab %}

{% tab title="Server" %}
在服务器端，请使用私钥构造你自己的 TronWeb 实例：

```javascript
import { TronSave } from '@tronsave/sdk'
import TronWeb from 'tronweb'

const tronWeb = new TronWeb({
  fullHost: 'https://api.trongrid.io',
  // ...
  privateKey: 'your private key'
})

const options = {
  apiKey: API_KEY,
  networks: "mainnet",
  // ...
}

const tronSave = new TronSave(tronWeb, options)
```
{% endtab %}
{% endtabs %}

## 配置选项

<table><thead><tr><th width="223">参数</th><th width="149">类型</th><th width="269">说明</th><th>是否必需</th></tr></thead><tbody><tr><td><code>apiKey</code></td><td>string</td><td>dApp API 密钥。如果你没有有效的 API 密钥，则无法使用 <strong>useRelay</strong> 或 <strong>forceUseRelay</strong> 选项。</td><td>false</td></tr><tr><td><code>networks</code></td><td>"<strong>mainnet</strong>" | "<strong>nile</strong>"</td><td>节点网络：<strong>mainnet</strong>（主网）或 <strong>nile</strong>（Nile 测试网）</td><td>true</td></tr><tr><td><code>domainName</code></td><td>string</td><td>域名</td><td>false</td></tr><tr><td><code>name</code></td><td>string</td><td>项目名称</td><td>false</td></tr><tr><td><code>version</code></td><td>string</td><td>项目版本</td><td>false</td></tr><tr><td><code>chainId</code></td><td>string</td><td>区块链链 ID</td><td>false</td></tr><tr><td><code>verifyingContract</code></td><td>string</td><td>地址</td><td>false</td></tr><tr><td><code>validAfter</code></td><td>number</td><td>请求可被转发的最高区块号；如果请求有效性不受时间限制，则为 0</td><td>false</td></tr><tr><td><code>paymentMethodType</code></td><td>enum（默认 <strong>1</strong>）</td><td>1 - 链上 WTRX（默认）<br>11 - dApp 为用户支付<br>12 - dApp 为用户支付但收取代币<br>2 / 10 / 4 - 即将推出</td><td>false</td></tr></tbody></table>

{% hint style="info" %}
配置键名为 `networks`（不是 `network`），取值为 `"mainnet"` 或 `"nile"`。
{% endhint %}

## 查询支付余额

使用 `getBalancePayment(paymentMethod)` 来读取你在指定支付方式下的 WTRX 余额。

{% tabs %}
{% tab title="Example" %}
```javascript
const getBalance = async () => {
  await tronSave.getBalancePayment(paymentMethod)
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="190">参数</th><th width="164">类型</th><th width="117">默认值</th><th width="220">说明</th><th>是否必需</th></tr></thead><tbody><tr><td><code>paymentMethod</code></td><td>1 | 2 | 10 | 11 | 12</td><td>1</td><td>请参阅上方配置选项表中的 <code>paymentMethodType</code> 值。</td><td>false</td></tr></tbody></table>

## 后续步骤

连接完成后，可继续完成完整的购买流程：预估一笔交易所需的能量、预估 TRX 支出、购买能量并确认订单状态。完整的端到端流程请参阅 [API 参考](../api-reference/README.md)和[开发者快速开始](../quickstart.md)。
