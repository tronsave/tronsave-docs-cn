---
description: >-
  为资源订单生成已签名的 TRX 支付交易——既可以使用你自己的代码，也可以通过 TronSave 的 Get Signed Transaction API
  实现。
---

# 获取签名交易

这是[使用签名交易购买](./)流程的**第 2 步**。在[第 1 步 — 预估 TRX](estimate-trx.md) 返回 `estimateTrx`（购买订单的成本，以 SUN 为单位）之后，你需要生成一笔签名转账交易，从买家钱包向 TronSave 资金地址支付该金额。然后在[第 3 步 — 创建订单](create-order.md)中提交它。

你可以通过两种方式完成此操作：自己对交易签名（[方案 1](get-signed-transaction.md#option-1-write-your-own-function)），或者让 TronSave API 替你签名（[方案 2](get-signed-transaction.md#option-2-use-the-tronsave-api)）。

## 编写你自己的函数

使用[第 1 步](estimate-trx.md)中得到的 `estimateTrx` 值，用 `transactionBuilder` 构建一笔 TRX 转账交易，从买家地址向 TronSave 资金地址发送等于 `estimateTrx` 的金额，然后用买家的私钥对其签名：

```tsx
const dataSendTrx = await tronWeb.transactionBuilder.sendTrx('TRONSAVE_FUND_ADDRESS', estimate_trx, 'BUYER_ADDRESS')
const signed_tx = await tronWeb.trx.sign(dataSendTrx, 'PRIVATE_KEY');
```

{% hint style="info" %}
* `BUYER_ADDRESS` — 买家的公开地址。
* `PRIVATE_KEY` — 买家的私钥。
* `TRONSAVE_FUND_ADDRESS` — TronSave 资金地址（见下文）。
{% endhint %}

**TronSave 资金地址（`TRONSAVE_FUND_ADDRESS`）：**

{% tabs %}
{% tab title="MAINNET" %}
```
TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S
```
{% endtab %}

{% tab title="Nile (for testing)" %}
{% hint style="warning" %}
此资金地址仅供在 TRON Nile（测试网）网络上进行测试使用。
{% endhint %}

```
TATT1UzHRikft98bRFqApFTsaSw73ycfoS
```
{% endtab %}
{% endtabs %}

生成的 `signed_tx` 随后将传递给[第 3 步 — 创建订单](create-order.md)。

## 后续步骤

* [第 1 步 — 预估 TRX](estimate-trx.md)
* [第 3 步 — 创建订单](create-order.md)
* [使用签名交易购买概览](./)
