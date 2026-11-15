---
description: 详解 TronSave 的双边订单簿租赁模式：买家与供应商如何撮合、SLOW/MEDIUM/FAST 价格档位的定价规则、订单成交状态，以及链上代理（委托）与租期回收机制。
---

# 租赁模式

TronSave 是一个**双边市场**，通过订单簿撮合**买家**（需要能量/带宽的一方）与**供应商**（拥有闲置质押资源的一方）。

## 关键角色

* **买家** — 为某个 `receiver` 地址在 `durationSec` 时长内下单租赁资源。
* **供应商（卖家）** — 从其质押的 TRX 中代理（委托）能量，并赚取 [APY](pricing-and-apy.md)。
* **订单簿** — 决定市场价格的实时供需集合。

## 价格档位

下单购买时，你需要设置 `unitPrice`（以 SUN 为单位，按每个资源单位计）——可以是一个固定数值，也可以是以下三个档位之一：

| 档位 | 含义 |
| --- | --- |
| `SLOW` | 订单可设定的最低价格。最便宜，但可能耗时更久 / 成交量更少。 |
| `MEDIUM` | ⭐ 默认值。在市场最大成交量下的最低价格。如果市场完全无法成交，则 `MEDIUM = SLOW + 10`。 |
| `FAST` | 优先即时成交。如果市场 100% 就绪，则 `FAST = MEDIUM`；如果就绪度 <100%，则 `FAST = MEDIUM + 10`；如果就绪度为 0%，则 `FAST = SLOW + 20`。 |
| `number` | 以 SUN 为单位的固定价格，可完全自主控制（例如 `80`）。 |

## 订单如何成交

根据供给情况和你的[订单选项](order-types.md)，订单可能处于：

* **完全成交** — 100% 撮合并完成代理（委托）。
* **部分成交** — 仅在 `allowPartialFill: true` 时可能发生。
* **待处理** — 等待供应商进行撮合（例如[待处理订单](order-types.md)）。

状态通过 `fulfilledPercent` 报告（0 = 待处理，1–99 = 部分成交，100 = 完全成交）。

## 代理（委托）与时长

撮合成功的资源会从供应商**在链上代理（委托）**给买家的 `receiver`，时长为 `durationSec`。当时长结束时，除非订单被[续期](../developers/api-reference/extend-orders/README.md)，否则代理（委托）将被回收。

{% hint style="info" %}
由于实际撮合的时长可能短于请求的时长（供应商的资源可能更早解锁），TronSave 会自动以 TRX **退还未使用的时长**——参见[智能订单](order-types.md#smart-order)。
{% endhint %}

## 后续步骤

* [订单类型](order-types.md) · [定价与 APY](pricing-and-apy.md) · [质押 2.0](staking-2.0.md)
