---
description: 使用 TronSave SDK 授权 WTRX、查询额度，以及在 TRX 与 WTRX 之间兑换。
---

# WTRX 操作

[WTRX](../../concepts/glossary.md)（Wrapped TRX，封装 TRX）在某些 SDK 支付流程中会用到。在 TronSave HUB 合约能够代表你花费 WTRX 之前，你必须先对其进行**授权**。本页介绍 SDK 提供的三个 WTRX 辅助方法：授权 WTRX、读取当前额度，以及将 TRX 兑换为 WTRX。

{% hint style="info" %}
所有示例均假设你已经拥有一个初始化完成的 `tronSave` SDK 实例。设置方法请参见 [连接](connect.md)。
{% endhint %}

## 授权 WTRX

在发送任何会花费 WTRX 的请求之前，你必须先向 HUB 合约授权 WTRX。

使用 `approveTronSave()`，它接受三个参数：`value`（要授权的 WTRX 数量）、`options`（[合约发送选项](https://developers.tron.network/reference/methodsend)）以及 `paymentMethod`。

{% tabs %}
{% tab title="Example" %}
```javascript
const onApproveWtrx = async () => {
    await tronSave.approveTronSave({
        value: value,
        options: options,
        paymentMethod: paymentMethod
    })
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>参数</th><th width="114">默认值</th><th width="287">说明</th><th>是否必填</th></tr></thead><tbody><tr><td><code>value</code></td><td></td><td>要授权的 WTRX 数量</td><td>true</td></tr><tr><td><code>options</code></td><td></td><td>合约发送选项</td><td>false</td></tr><tr><td><code>paymentMethod</code></td><td>1</td><td>为指定的支付代币授权 TronSave</td><td>false</td></tr></tbody></table>

## 查询 WTRX 额度

在发送请求之前，先查询已授权的 WTRX 额度。`getAllowanceTronSave(paymentMethod)` 会返回当前的额度值。

{% tabs %}
{% tab title="Example" %}
```javascript
const getAllowanceUser = async () => {
    await tronSave.getAllowanceTronSave(paymentMethod)
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="185">参数</th><th width="163">类型</th><th width="162">默认值</th><th width="136">说明</th><th>是否必填</th></tr></thead><tbody><tr><td><code>paymentMethod</code></td><td><code>1 | 2 | 10 | 11 | 12</code></td><td>1</td><td>参见<a href="payment-methods.md">支付方式参考</a></td><td>false</td></tr></tbody></table>

## 将 TRX 兑换为 WTRX

快速将 TRX 转换为 WTRX。`trxToWtrx()` 接受两个参数：`value`（要存入的 TRX 数量）和 `options`（[合约发送选项](https://developers.tron.network/reference/methodsend)）。

{% tabs %}
{% tab title="Example" %}
```javascript
const depositTrx = async () => {
    const res = await tronSave.trxToWtrx({ value: value, options: options });
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>参数</th><th width="114">默认值</th><th width="287">说明</th><th>是否必填</th></tr></thead><tbody><tr><td><code>value</code></td><td></td><td>要存入的 TRX 数量</td><td>true</td></tr><tr><td><code>options</code></td><td></td><td>合约发送选项</td><td>false</td></tr></tbody></table>

## 典型流程

1. 使用 `trxToWtrx()` 将你所需的 TRX 兑换为 WTRX。
2. 调用 `approveTronSave()`，使 HUB 合约能够花费这些 WTRX。
3. 使用 `getAllowanceTronSave()` 确认授权已生效。

## 后续步骤

- 查看[术语表中的 WTRX](../../concepts/glossary.md)。
- 继续阅读[开发者快速开始](../quickstart.md)以创建你的第一个订单。
