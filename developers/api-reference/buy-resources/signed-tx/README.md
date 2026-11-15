---
description: 使用签名交易通过 TronSave API 购买 TRON 能量或带宽：先预估所需 TRX，再生成并签名交易，最后提交创建订单——全程从自己的钱包付款，无需托管余额。
---

# 使用签名交易购买

签名交易流程让你可以使用自己的钱包密钥对 TRON 交易进行签名来购买资源。无需持有 TronSave 余额，你可以直接从钱包按订单付款。该流程包含三个 API 步骤：

1. **预估 TRX** — 计算所需资源数量和租赁时长对应的 TRX。
2. **获取签名交易** — 通过你自己的代码或 TronSave API 生成签名交易。
3. **创建订单** — 提交签名交易以完成资源购买。

{% hint style="info" %}
不想自己签名交易？你也可以使用 API 密钥通过预付余额购买。请参阅 [使用 API 密钥购买](../api-key/README.md)。
{% endhint %}

## 开始之前

1. 拥有你打算用于交易的钱包的私钥访问权限。
2. 确保钱包持有足够的 TRX 来支付订单。

## 流程

### 步骤 1 — 预估所需 TRX

调用 [预估 TRX](estimate-trx.md) 端点，计算所需资源数量和租赁时长对应的 TRX。

### 步骤 2 — 生成签名交易

你有两个选项：

* **选项 1：** [编写你自己的函数](get-signed-transaction.md)，使用钱包私钥生成签名交易。
* **选项 2：** 使用 TronSave 的 [获取签名交易](get-signed-transaction.md) API 自动生成签名交易。

### 步骤 3 — 创建订单

调用 [创建订单](create-order.md) 端点以完成购买。需传入：

* 签名交易（来自 API 响应，或你自定义签名的交易）。
* 预估的 TRX 数量及其他必需参数。

## 端点（TRON Nile 测试网）

{% tabs %}
{% tab title="Testnet" %}
* 预估 TRX： <mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v2/estimate-buy-resource`
* 获取签名交易： <mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v2/signed-tx`
* 创建订单： <mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v2/buy-resource`
{% endtab %}
{% endtabs %}

要与 **Nile 测试网** 集成，请将基础 URL 替换为 [https://testnet.tronsave.io/](https://testnet.tronsave.io/) 并遵循相同的步骤。

{% hint style="info" %}
此流程提供了一个开箱即用的 Postman 集合：[使用签名交易购买（Postman）](https://www.postman.com/tronsave/tronsave/folder/kuv179r/buy-with-signtx)。
{% endhint %}

## 后续步骤

* [预估 TRX](estimate-trx.md)
* [获取签名交易](get-signed-transaction.md)
* [创建订单](create-order.md)
