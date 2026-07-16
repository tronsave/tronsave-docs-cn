---
description: 使用 API 密钥从预充值的 TronSave 内部账户购买 TRON 能量或带宽：无需每单链上签名，集成简单且节省手续费；本节汇总账户信息、订单簿、预估与下单等端点。
---

# API 密钥

API 密钥流程让你可以从预充值的 TronSave 内部账户购买资源（能量和带宽）。你无需为每笔订单使用钱包签名交易，而是通过 API 密钥对每个请求进行身份验证，并从你的内部账户余额中付款——因此没有每笔订单的链上费用，集成也保持简单。

{% hint style="info" %}
更愿意直接从钱包按订单付款？请改用[使用签名交易购买](../signed-tx/)流程。
{% endhint %}

## 开始之前

你需要一个 TronSave API 密钥，以及一个余额足以覆盖订单的内部账户。有两种方式创建 API 密钥：

* **方式 1：** 在 TronSave 网站上生成 API 密钥。
* **方式 2：** 在 Telegram 上生成 API 密钥。

有关如何在每个请求中传递 API 密钥的信息，请参阅[身份验证](../../../authentication.md)。

## 端点

使用 API 密钥可调用以下任意端点：

<table><thead><tr><th width="264">端点</th><th>说明</th></tr></thead><tbody><tr><td><a href="get-account-info.md">获取内部账户信息</a></td><td>读取你的内部账户余额和详情。</td></tr><tr><td><a href="get-order-book.md">获取订单簿</a></td><td>获取当前订单簿，了解资源定价和可用情况。</td></tr><tr><td><a href="estimate-trx.md">预估 TRX</a></td><td>计算指定资源数量和租赁时长所需的 TRX。</td></tr><tr><td><a href="create-order.md">购买能量（创建订单）</a></td><td>下单购买能量或带宽，从内部账户付款。</td></tr><tr><td><a href="get-order-details.md">获取订单详情</a></td><td>通过 ID 查询单个订单的详情和状态。</td></tr><tr><td><a href="order-history.md">获取内部账户订单历史</a></td><td>列出你的内部账户的订单历史。</td></tr></tbody></table>

## 端点（TRON Nile 测试网）

{% tabs %}
{% tab title="Testnet" %}
* 预估 TRX：<mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v2/estimate-buy-resource`
* 创建订单：<mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v2/buy-resource`
* 获取内部账户信息：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v2/user-info`
* 获取内部账户订单历史：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v2/orders`
* 获取订单详情：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v2/order/:id`
* 获取订单簿：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v2/order-book`
{% endtab %}
{% endtabs %}

要与 **Nile 测试网**集成，请将基础 URL 替换为 [https://testnet.tronsave.io/](https://testnet.tronsave.io/)，并按照相同步骤操作。

## 后续步骤

* [预估 TRX](estimate-trx.md)
* [购买能量（创建订单）](create-order.md)
* 了解[能量和带宽](../../../../concepts/energy-and-bandwidth.md)之间的区别。
* 下单前请查看[订单类型](../../../../concepts/order-types.md)。
