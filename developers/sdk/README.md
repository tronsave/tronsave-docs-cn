---
description: TronSave 官方 SDK，用于在 TRON 上购买和管理能量与带宽 — 支持 TypeScript、Rust、Python、Java 和 PHP。
---

# SDK

**TronSave SDK** 可让你与 TronSave API 交互，从而管理 TRON 区块链上的资源（能量和带宽）。每个 SDK 都提供强类型函数，用于预估成本、购买资源、延长订单以及跟踪订单状态，并能干净地集成到后端服务和浏览器 dApp 中。

{% hint style="info" %}
更喜欢直接调用 HTTP API？请参阅 [API 参考](../api-reference/README.md)。各 SDK 封装了那里所记录的相同端点。
{% endhint %}

## 可用的 SDK

所有 SDK 均面向 **v2 API**。

| 语言 | 包 | 仓库 | 最新版本 |
| --- | --- | --- | --- |
| TypeScript / JS | `tronsave-sdk` | [npm](https://www.npmjs.com/package/tronsave-sdk) | — |
| Rust | `tronsave` | [crates.io](https://crates.io/crates/tronsave) | 2.0.0 |
| Python | `tronsave` | [PyPI](https://pypi.org/project/tronsave/) | 2.0.0 |
| Java | `io.tronsave:sdk` | [Maven Central](https://central.sonatype.com/artifact/io.tronsave/sdk) | 2.0.0 |
| PHP | `tronsave/sdk` | [Packagist](https://packagist.org/packages/tronsave/sdk) | 2.0.0 |

## 安装

{% tabs %}
{% tab title="npm" %}
```bash
npm install tronsave-sdk
# 或：yarn add tronsave-sdk
```

需要 **Node.js v18.0.0+**。
{% endtab %}

{% tab title="Rust" %}
```bash
cargo add tronsave
```

或在 `Cargo.toml` 中：

```toml
[dependencies]
tronsave = "2.0.0"
```
{% endtab %}

{% tab title="Python" %}
```bash
pip install tronsave
```

需要 **Python 3.9+**。
{% endtab %}

{% tab title="Java" %}
Maven（`pom.xml`）：

```xml
<dependency>
    <groupId>io.tronsave</groupId>
    <artifactId>sdk</artifactId>
    <version>2.0.0</version>
</dependency>
```

Gradle（`build.gradle`）：

```groovy
implementation 'io.tronsave:sdk:2.0.0'
```
{% endtab %}

{% tab title="PHP" %}
```bash
composer require tronsave/sdk
```

需要 **PHP 8.1+**。
{% endtab %}
{% endtabs %}

## 连接并购买

最快的入门方式是 API 密钥流程：创建一个 SDK 实例、预估成本，然后下单。从 [TronSave 控制台](https://tronsave.io/market) 获取 API 密钥 — 详情请参阅 [身份验证](../authentication.md)。

{% tabs %}
{% tab title="Node.js" %}
```javascript
import { TronsaveSDK } from "tronsave-sdk";

const main = async () => {
    const apiKey = "your_api_key";
    const sdk = new TronsaveSDK({ network: "testnet", apiKey });

    const userInfo = await sdk.getUserInfo();
    console.log(userInfo);

    // Example: Estimate cost for 32,000 ENERGY units for 1 hour
    const estimate = await sdk.estimateBuyResource({
        receiver: "TAk6jzZqHwNUkUcbvMyAE1YAoUPk7r2T6h",
        resourceType: "ENERGY",
        durationSec: 3600, // 1 hour
        resourceAmount: 32000,
    });
    console.log(estimate);
    if (estimate.estimateTrx > Number(userInfo.balance)) throw new Error("Insufficient balance");

    const { orderId } = await sdk.buyResource({
        receiver: "TAk6jzZqHwNUkUcbvMyAE1YAoUPk7r2T6h",
        resourceType: "ENERGY",
        durationSec: 3600, // 1 hour
        resourceAmount: 32000,
    });
    console.log(`Buy resource success -> orderId: ${orderId}`);

    // Wait for 5 seconds for the order to be filled
    console.log("Waiting for 5 seconds for the order to be filled...");
    await new Promise((resolve) => setTimeout(resolve, 5000));

    const order = await sdk.getOrder(orderId);
    console.log(order);
    if (order.fulfilledPercent < 100) console.log("Order is not filled");
    else console.log("Order is filled");
};

main();
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
生产环境请设置 `network: "mainnet"`。上面的示例使用 `"testnet"`（Nile（测试网）），在那里一切运作方式相同，但不使用真实的 TRX。请参阅 [环境与网络](../environments.md)。
{% endhint %}

## 功能

该 SDK 提供以下操作：

* **购买资源** — 购买能量或带宽。
* **预估购买资源** — 在下单前预估资源购买的成本。
* **延长请求** — 延长现有的资源代理（委托）。
* **获取可延长的代理** — 列出可以延长的代理。
* **获取用户信息** — 获取账户信息和余额。
* **获取订单 / 获取多个订单** — 获取订单详情和订单历史。
* **获取订单簿** — 读取当前的订单簿。

## 浏览器 dApp 流程

在浏览器 dApp 中，你可以通过注入的 `tronWeb` 实例（而非 API 密钥）进行连接、预估交易将消耗的能量，并从已连接的钱包付款。此流程在下方的子页面中有所介绍 — 请从 [连接](connect.md) 开始。

## 后续步骤

* [连接](connect.md) — 使用 API 密钥或注入的 `tronWeb` 初始化 SDK。
* [发送](send.md) — 发送交易并购买能量。
* [预估能量](estimate-energy.md) — 预估一笔交易所需的能量。
* [支付方式](payment-methods.md) — 选择购买的付款方式。
* [WTRX（授权 / 额度 / 兑换）](wtrx.md) — 使用包装后的 TRX。
* [合约地址](contract-addresses.md) — 链上合约引用。
* [配置与历史](config-and-history.md) — 配置选项和订单历史。
