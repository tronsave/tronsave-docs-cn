---
description: 说明 TronSave 的租赁定价与收益机制：买家通过 unitPrice（SUN）或价格档位定价，供应商 APY 由租赁收入复利加 TRON 投票奖励构成，并附完整公式与约 18% 的计算示例。
---

# 定价与 APY

## 买家如何定价

租赁价格按每个订单通过 `unitPrice`（每资源单位的 SUN）设定 —— 既可以是固定数值，也可以是某个档位（`SLOW` / `MEDIUM` / `FAST`）。市场的实时订单簿决定某个档位最终对应的价格。关于具体的档位规则，请参阅 [租赁模型](rental-model.md#the-price-tiers)。

* **1 TRX = 1,000,000 SUN。**
* 下单前请始终先进行[预估](/broken/pages/sgNfEWCRfinn74vpkq9A)，以查看当前价格和可用供应量。

## 供应商 APY 如何计算

供应商的收益由复利计算的租赁收入（APR）加上 TRON 投票奖励（VR）组成：

$$
APR_{tronsave} = \frac{AP \times FR}{1{,}000{,}000} \times PS \times 365
$$

<table><thead><tr><th width="230">符号</th><th>含义</th></tr></thead><tbody><tr><td><strong>AP</strong></td><td>最接近的 200 笔已匹配订单的平均价格（SUN）</td></tr><tr><td><strong>FR</strong></td><td>TRON 网络上的冻结率（每质押 1 TRX 对应的 SUN）</td></tr><tr><td><strong>PS</strong></td><td>供应商在 TronSave 上的利润分成 = <strong>0.75</strong></td></tr><tr><td>1,000,000</td><td>每 TRX 对应的 SUN</td></tr></tbody></table>

$$
APY_{total} = \left(1 + \frac{APR_{tronsave}}{12}\right)^{12} - 1 + VR
$$

* **VR**（投票率）：潜在的投票奖励，约 **4%** —— 可在 [tronscan.org](https://tronscan.org/#/sr/votes) 查询。

### 计算示例

如果 **AP = 30 SUN**、**FR = 16.5**、**VR = 4%**：

$$
APR_{tronsave} = \frac{30 \times 16.5}{1{,}000{,}000} \times 0.75 \times 365 \approx 0.1355 = 13.55\%
$$

$$
APY_{total} = \left(1 + \frac{0.1355}{12}\right)^{12} - 1 + 4\% \approx 18.4\%
$$

{% hint style="info" %}
AP、FR 和 VR 都会随市场状况变动，因此实时 APY 会有所不同。请使用上述公式结合当前数值进行计算，或查看 TronSave 应用中显示的数字。
{% endhint %}

## 后续步骤

* [质押 2.0](staking-2.0.md) · [出售 / 供应商](../guides/sell/) · [计算 APY（常见问题）](../resources/faq.md)
