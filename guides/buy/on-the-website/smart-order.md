---
description: 下达大额能量租赁订单，随着供应商重新获得能量而持续撮合填充，并自动以 TRX 退还未使用的时长。
---

# 智能订单

**智能订单**是面向**大额**能量租赁的可选功能。当市场在订单创建时无法完全撮合你的订单时，智能订单会持续工作：它会监控那些部分填充了你订单的供应商，并在他们重新获得能量时自动撮合更多能量，同时以 TRX 退还任何未使用的时长。

有关所有订单类型的概念性概述，请参阅[订单类型](../../../concepts/order-types.md)。

## 要求

<table>
<thead>
<tr><th>字段</th><th>最低值</th></tr>
</thead>
<tbody>
<tr><td><code>Amount</code></td><td>10,000,000 能量</td></tr>
<tr><td><code>Duration</code></td><td>3 天</td></tr>
</tbody>
</table>

## 如何创建智能订单

1. 在购买表单中**输入你的订单详情**：
   * `Amount`：最低 **10,000,000 能量**
   * `Duration`：最低 **3 天**
2. 点击**高级设置（Advanced Settings）**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FpCQuU6uMZpa6fqXlPsXE%2Fimage.png?alt=media&#x26;token=49876c43-692d-46ad-be90-e93f7a3c4973" alt=""><figcaption></figcaption></figure>

3. 勾选复选框以启用**智能撮合（Smart Matching）**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FGep4LSwOOubWjI9rsFw3%2Fimage.png?alt=media&#x26;token=d59e6ab3-0e50-4111-bdef-999caea1c707" alt=""><figcaption></figcaption></figure>

## 工作原理

启用智能订单后：

1. 你的订单会正常撮合——系统会从供应商当前代理（委托）的能量中尽可能多地填充。
2. 如果订单**未被完全撮合**，系统会**持续监控那些已经部分填充该订单的供应商**。
3. 如果其中任何供应商**重新获得至少 100,000 能量**（通过 TRX 充值或代理委托）：
   * 系统会**自动从他们那里撮合更多能量**到你的订单中。
   * 这部分额外撮合的**时长**从当前时间起算，直到你订单的**原始到期时间**。
   * 你将获得这部分新撮合数量未使用时长的**退款**。

{% hint style="info" %}
实际时长从**当前时间到解锁时间**计算，因此它可能**短于**你选择的时长。缺少的时长会被计算并自动**以 TRX 退还**。
{% endhint %}

### 示例

你下达了一个 **10,000,000 能量、为期 3 天**的订单。最初只撮合了 **9,000,000**。一天后，一个曾撮合了你订单部分内容的供应商释放出 **1,000,000 能量**。

* 系统会为剩余的 **2 天**自动撮合这 1,000,000 能量。
* 你会获得该数量**1 天的退款**（未使用的部分）。

## 优势

* 无需手动跟踪即可获得全额能量。
* 无需下达新订单即可利用供应商**重新获得的能量**。
* 当撮合时长短于请求时长时提供公平退款。
* 大额能量请求具有更高的撮合成功率。

## 注意事项

* 智能订单**不保证**完全履行，但会随时间提高成功几率。
* 它仅适用于**已经撮合了你订单部分内容**且**重新获得至少 100,000 能量**的供应商。
* 智能订单**仅在撮合时长的前三分之一期间生效**。例如，如果某供应商撮合了 3 天，智能订单只会**在第一天内**自动撮合更多能量。此后仅适用常规撮合。

## 查看退款详情

1. 打开 **My Order** 标签页并选择该订单。
2. 打开 **Refund** 标签页。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FFcbsOqafoNdnXbESYqQn%2Fimage.png?alt=media&#x26;token=0cbf3138-dde5-41da-b032-f7c0199f16a4" alt="" width="563"><figcaption></figcaption></figure>

## 后续步骤

* [普通订单](normal-order.md) · [挂单订单](pending-order.md)
* [订单类型](../../../concepts/order-types.md) · [能量与带宽](../../../concepts/energy-and-bandwidth.md)
