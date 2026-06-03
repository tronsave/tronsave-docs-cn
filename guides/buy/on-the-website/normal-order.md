---
description: 在 tronsave.io 上下普通市价订单，租赁能量或带宽，并立即与当前供应进行撮合。
---

# 普通订单

普通订单（Normal Order）是 TronSave 上的标准购买方式：你指定数量、价格和时长，订单会立即与当前市场供应进行撮合。关于它与挂单（Pending）、智能（Smart）等其他方式的对比，请参阅[订单类型](../../../concepts/order-types.md)。

## 创建订单

1. 打开 [tronsave.io/market](https://tronsave.io/market)。
2. 连接你的钱包（例如 TronLink）。
3. 输入要购买的能量/带宽**数量**、**价格**和**时长**。你还可以选择设置自定义的资源目标地址。填写表单后，点击**创建订单**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FRcFE4tiiS7589OFLKzcZ%2Fanh1.png?alt=media&#x26;token=503fb3a3-cfae-4f06-9503-0e03d9fbdb28" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
点击**价格**字段可打开**订单簿**弹窗。在这里你可以查看不同价位上可用的资源，并为你的租赁选择最合适的价格和数量。
{% endhint %}

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FYl0YUHlWLHmO0MDREK8k%2Fimage.png?alt=media&#x26;token=1f499398-cb02-4ae0-b770-eacc2234c9ef" alt="" width="297"><figcaption></figcaption></figure>

## 高级设置

打开**设置**（⚙️）按钮可进行以下配置：

<table>
<thead>
<tr><th width="240">设置</th><th>说明</th></tr>
</thead>
<tbody>
<tr>
<td><code>Minimum delegate</code>（能量/带宽）</td>
<td>单个供应商可代理（委托）给你的最小能量/带宽数量。设置后，系统只会将你的订单与可用能量/带宽大于或等于该值的供应商进行撮合。</td>
</tr>
<tr>
<td><code>Allow partial fill</code></td>
<td>如果勾选，订单可以被部分成交。如果不勾选，则除非单个地址能在一笔交易中完成订单，否则订单不会被成交。</td>
</tr>
<tr>
<td><code>Immediate buy</code></td>
<td>如果勾选，订单必须立即成交。如果系统尚未准备好进行撮合，则不会创建该订单。</td>
</tr>
<tr>
<td><code>Priority payment</code></td>
<td>首选付款方式。默认值：始终询问确认。</td>
</tr>
</tbody>
</table>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FBNtyqTotN5nsABYxyJEt%2FScreen%20Shot%202025-04-02%20at%2009.55.34.png?alt=media&#x26;token=93ab1d9c-9c88-4bb1-9ffe-fca09af61b14" alt=""><figcaption></figcaption></figure>

下单后，订单会出现在**订单（Orders）**标签页中。如果资源池中有足够的资源，订单会自动成交。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FZ6Y8vxSry068iMK8WqfV%2Fanh3.png?alt=media&#x26;token=930d216a-6ca3-42cb-81f2-946ba88d2a17" alt=""><figcaption></figcaption></figure>

## 更新接收地址

如果订单**未完全撮合**且自创建以来**已过去一小时**，你可以更改订单的目标（接收地址）。

1. 打开**我的订单（My Orders）**标签页，点击进入**订单详情（Order Detail）**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FMLYVevEpHpTOSIKBdC6u%2Fanh4.png?alt=media&#x26;token=0e0f1427-7968-4269-9a4d-c281ad5fda8d" alt=""><figcaption></figcaption></figure>

2. 点击**编辑（Edit）**并输入新地址。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FewVADk23iN7VgdeiACzO%2Fimage.png?alt=media&#x26;token=9a93c4bf-4647-441e-b271-8d57ee9a2c45" alt="" width="540"><figcaption></figcaption></figure>

3. 点击**确认（Confirm）**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FTxo7WVa51MBeZ2oqAmGF%2Fimage.png?alt=media&#x26;token=a17f7e57-0c30-41b2-8d16-423c1539ed74" alt="" width="563"><figcaption></figcaption></figure>

## 更新价格

1. 点击**编辑（Edit）**按钮。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FlGAHYwVVINHj63ofDw6j%2Fimage.png?alt=media&#x26;token=04604010-19a9-4024-be2b-00146b0790a7" alt="" width="550"><figcaption></figcaption></figure>

2. 输入新价格并点击**确认（Confirm）**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2F1Nu3ii3mdP7SSQZ8tumc%2Fimage.png?alt=media&#x26;token=cf304c50-cf3e-4098-980f-7f44bd533eed" alt="" width="547"><figcaption></figcaption></figure>

## 取消订单

如果订单在创建后**5 分钟内未被撮合**，你可以取消该订单。取消费用为 **5 TRX**。

1. 点击订单上的**取消（Cancel）**按钮。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2F55uRKdA3FDxnZd52g4aK%2Fimage.png?alt=media&#x26;token=96af31d5-51e8-447c-a7b5-5f9bfe8f64d8" alt=""><figcaption></figcaption></figure>

2. 点击**是的，我确定（Yes, I'm sure）**以确认取消。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FSSZWWOnFAD1EaKzxQQps%2Fimage.png?alt=media&#x26;token=be955dab-f223-414a-99ce-203c124697f7" alt=""><figcaption></figcaption></figure>

## 后续步骤

* [订单类型](../../../concepts/order-types.md) — 普通订单与挂单、智能、ZapBuy 等的对比
* [定价与 APY](../../../concepts/pricing-and-apy.md)
* [能量与带宽](../../../concepts/energy-and-bandwidth.md)
