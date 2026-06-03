---
description: 在一个流程中为多个地址购买能量——预估成本、创建订单、跟踪匹配，然后确认以回收未使用的能量并退还差额。
hidden: true
---

# 快速充值

Fast Charge 让你在单个流程中快速、高效地为多个地址租赁能量，最大化节省成本。你预估所需的 TRX、创建订单、跟踪订单的匹配状态，然后确认已完成的订单以回收未使用的能量并获得 TRX 退款。

{% hint style="info" %}
Fast Charge 是一项 API 密钥功能——每次调用都会针对一个已预先充值的 TronSave 内部账户进行身份验证。请参阅 [Authentication](../../authentication.md) 了解如何获取密钥并为账户充值。
{% endhint %}

## 生命周期

Fast Charge API 遵循一个可预测的生命周期。每个步骤对应一个端点：

<table><thead><tr><th width="170">步骤</th><th>端点</th><th>作用</th></tr></thead><tbody><tr><td><strong>1. 预估</strong></td><td><a href="get-an-estimate-trx.md">Get an Estimate TRX</a></td><td>根据你可接受的最高价格和订单数量，预估所需的 TRX。将其与你的内部账户余额进行核对。</td></tr><tr><td><strong>2. 创建</strong></td><td><a href="create-fast-charge-orders.md">Create Fast Charge Orders</a></td><td>提交订单，包含最高价格、租赁时长以及接收能量的地址列表。</td></tr><tr><td><strong>3. 跟踪</strong></td><td><a href="tracking-fast-charge-order.md">Tracking Fast Charge Order</a></td><td>监控每个订单的状态——<code>Pending</code>（等待匹配）或 <code>Matched</code>。</td></tr><tr><td><strong>4. 确认</strong></td><td><a href="confirm-request.md">Confirm Request</a></td><td>使用完能量后，确认订单以结束租赁、回收未使用的能量并触发 TRX 退款。</td></tr><tr><td><strong>取消</strong></td><td><a href="cancel-order.md">Cancel Order</a></td><td>取消处于 <code>Pending</code> 状态的订单以获得全额 TRX 退款。</td></tr><tr><td><strong>历史</strong></td><td><a href="get-history.md">Get History</a></td><td>查询已完成的租赁和订单的详细信息。</td></tr></tbody></table>

## 步骤 1 — 预估 TRX 支出

提供租赁详情，让系统预估你所需的 TRX：

* 可接受的最高价格。
* 要下的订单数量。

系统返回预估的 TRX 金额。在创建订单之前，请核对你的内部账户余额，确保其中有足够的 TRX 用于租赁。

→ [Get an Estimate TRX](estimate-trx.md)

## 步骤 2 — 创建 Fast Charge 订单

提交订单，包含：

* 可接受的最高价格。
* 租赁时长。
* 接收相应数量能量的地址列表。

→ [Create Fast Charge Orders](create-order.md)

## 步骤 3 — 跟踪订单状态

使用跟踪端点监控每个订单：

* **Matched** — 订单已匹配。
* **Pending** — 订单正在等待匹配。

→ [Tracking Fast Charge Order](track-order.md)

## 步骤 4 — 确认订单

使用完能量后，提交需要确认的订单列表以完成你想要完成的订单：

* 确认标志着租赁的结束。
* 系统会回收未使用的能量，并计算要退还到你账户的 TRX 退款。

{% hint style="success" %}
尽早确认有助于你获得**尽可能多的退款**。如果你一直不确认，系统会在租赁期结束后自动回收订单。
{% endhint %}

→ [Confirm Request](confirm-request.md)

## 取消已匹配的订单

要取消订单，请使用取消端点：

* 只有处于 **Pending** 状态的订单才能被取消。
* 一旦取消，100% 的 TRX 将退还到你的内部账户。

→ [Cancel Order](cancel-order.md)

## 查看交易历史

使用历史端点查看你已完成的租赁和订单的详细信息。

→ [Get History](get-history.md)

## 后续步骤

* [Get an Estimate TRX](estimate-trx.md) · [Create Fast Charge Orders](create-order.md) · [Tracking Fast Charge Order](track-order.md)
* [Authentication](../../authentication.md) · [Errors & Rate Limits](../../errors-and-rate-limits.md)
