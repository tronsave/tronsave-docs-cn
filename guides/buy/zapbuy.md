---
description: 通过向 TronSave 的机器人地址发送 TRX 即可即时购买能量——系统会根据你发送的金额自动创建一个 1 小时的租赁订单。
---

# ZapBuy

**ZapBuy** 是一种快速、简洁的能量购买方式：你**直接发送 TRX** 到 TronSave 的默认机器人地址，系统便会根据收到的 TRX 金额自动创建一个**1 小时的能量租赁订单**。无需账户、表单或任何额外步骤。

有关所有订单类型的概念性概述，请参阅[订单类型](../../concepts/order-types.md)。

## 工作原理

1. **使用[能量计算器工具](https://tronsave.io/tools/zapbuy)计算需要发送的 TRX**。你可以根据自己的需求灵活购买能量——不受固定倍数的限制。
2. **发送 TRX** 到 TronSave 的机器人地址：

   ```ini
   TLx8h8fjv5pyuxCu292ZgjbU14XZSiLGg4
   ```

3. 系统计算你的 TRX 可以租赁多少能量。
4. 系统自动创建一个**1 小时的租赁订单**。
5. 能量将直接代理（委托）到发送方钱包（如果符合接收条件）。

{% hint style="info" %}
* 你至少必须租赁**65,000 能量**。低于 65,000 能量的请求将被**忽略**，且**不会创建任何订单**。
* ZapBuy 仅在你的订单能够与可用能量**立即匹配**时才有效。如果未找到匹配，你将不会收到能量。

  请将你的交易详情发送给 TronSave 以申请退款：[https://t.me/wantingtrx](https://t.me/wantingtrx)
{% endhint %}

## 技术细节

<table>
<thead>
<tr><th width="211">参数</th><th>值</th></tr>
</thead>
<tbody>
<tr><td>支持的代币</td><td><strong>仅 TRX</strong></td></tr>
<tr><td>接收 TRX 的地址</td><td>TronSave 机器人地址：<strong>TLx8h8fjv5pyuxCu292ZgjbU14XZSiLGg4</strong></td></tr>
<tr><td>能量接收方</td><td><strong>发送 TRX 的钱包</strong></td></tr>
<tr><td>租赁时长</td><td><strong>1 小时</strong>（固定）</td></tr>
<tr><td>最低购买量</td><td><strong>65,000 能量</strong></td></tr>
<tr><td>订单创建</td><td>收到 TRX 后自动创建</td></tr>
</tbody>
</table>

{% hint style="warning" %}
如果你的请求换算下来不足**65,000 能量**，则不会租赁任何能量，你的交易将被忽略。
{% endhint %}

## 常见问题

**问：我可以选择 1 小时以外的租赁时长吗？**\
答：不可以。ZapBuy 专为快速、固定时长为 1 小时的租赁而设计。

**问：如果我在一笔交易中发送更多 TRX 会怎样？**\
答：系统仍然只创建**一个订单**，并按相应的能量金额确定其大小。

**问：我发送了 TRX 但没有收到能量——为什么？**\
答：请检查以下几点：

* 你是从**普通钱包**发送 TRX，而不是从**智能合约**或**交易所**。
* 你租赁的数量至少为**65,000 能量**。
* 你的交易已在 **TRON 区块链上成功确认**。

如果你已满足以上所有条件但仍未收到能量，请**立即**通过 [https://t.me/wantingtrx](https://t.me/wantingtrx) 联系我们。

## 后续步骤

* [订单类型](../../concepts/order-types.md) · [能量与带宽](../../concepts/energy-and-bandwidth.md)
