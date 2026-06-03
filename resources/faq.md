---
description: 关于 TronSave 上定价、能量与带宽以及购买资源的常见问题。
---

# 常见问题

以下是我们最常被问到的问题的解答。如需更深入的背景信息，请参阅 [能量与带宽](../concepts/energy-and-bandwidth.md) 和 [术语表](../concepts/glossary.md)。

## 为什么用机器人购买的价格比网站上更高？

通常有两种原因。

**情况 1 — 目标地址不匹配。** 请仔细核对订单中的 **目标地址**。目标地址的差异可能导致价格不同。

**情况 2 — 不同的撮合策略。** 机器人和网站使用不同的购买流程：

* 在 **网站** 上，你可以设置尽可能低的价格——这意味着订单可能无法 100% 撮合成交。
* 在 **机器人** 上，订单始终以 **`MEDIUM`** 价格下单，以确保每一笔机器人购买都能 100% 撮合成交。

在大多数情况下，机器人和网站的价格是相同的。只有当系统没有足够的资源进行撮合时，机器人的价格才会高于网站的价格。

{% hint style="info" %}
如果你想完全掌控价格（并接受部分成交的可能性），请在网站上购买。如果你想要保证 100% 撮合成交，请使用机器人。
{% endhint %}

## 什么是 TRON 能量和带宽？

能量和带宽是每一笔 TRON 交易都会消耗的两种资源。它们通过 **锁定（质押）TRX** 获得，在锁定期内产生这些资源。在你的 TRX 被冻结期间，它无法被交易、买入或卖出，直到经过指定天数后解冻为止。

* **带宽** 是冻结 TRX 所获得的奖励，用于满足交易的字节大小需求。
* **能量** 是 TRON 独有的资源，代表网络内 CPU 消耗的单位。它只能通过冻结 TRX 获得。

这两种资源对于创建和执行智能合约都至关重要。完整详情请参阅 [能量与带宽](../concepts/energy-and-bandwidth.md)。

## 我如何获得能量？

有三种方式可以获得能量。

### 方法 1 — 燃烧 TRX

如果你的账户没有足够的能量，在转账过程中会 **自动燃烧 TRX** 来支付该转账所需的带宽和能量。与消耗质押所得的能量相比，燃烧 TRX **并不划算**。

### 方法 2 — 冻结（质押）TRX

打开 TRON 资源管理界面（[https://tronscan.io/#/wallet/resources](https://tronscan.io/#/wallet/resources)），选择你想要的资源类型，并输入要冻结的 TRX 数量。

你获得的能量按以下公式计算：

```
Energy obtained = (frozen TRX / total network frozen TRX) × total network Energy limit
```

这会将固定的网络能量平均分配给所有被冻结的 TRX。被冻结的 TRX 可在 **14 天** 后解冻并取回。如果你有大量闲置的 TRX，可以考虑这种方法。

{% hint style="warning" %}
质押会将你的 TRX 锁定 14 天，之后才能解冻并取回。如果你需要流动性，请提前做好规划。
{% endhint %}

### 方法 3 — 在 TronSave 上购买能量

通过 [TronSave](https://tronsave.io) 租赁能量可以节省手续费。你无需冻结大量 TRX 或额外燃烧 TRX，并且最多可节省 **92%** 的手续费。

请参阅 [订单类型](../concepts/order-types.md) 和 [快速开始](../getting-started/quickstart.md) 来开始使用。

## 后续步骤

* [能量与带宽](../concepts/energy-and-bandwidth.md) · [订单类型](../concepts/order-types.md) · [术语表](../concepts/glossary.md)
