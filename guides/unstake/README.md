---
description: 在 TronSave 上提前提取已质押的 TRX，无需等待官方的解质押期。
---

# 提前解除质押

在 TRON 上，解质押通常会在你能够提取 TRX 之前，将其锁定整个官方解质押期。TronSave 的 **提前解质押（Early Unstake）** 让你能够更快地获得这部分流动性，并根据解质押金额收取浮动的服务费。

在 TronSave 上处理提前解质押有两种方式：

<table><thead><tr><th width="219">方式</th><th>作用</th></tr></thead><tbody><tr><td><a href="early-unstake.md">提前解质押</a></td><td>即时提取你已质押的 TRX。如果系统有足够的流动性，你的请求会被自动批准；否则将进入人工审核。</td></tr><tr><td><a href="unstake-market.md">解质押市场</a></td><td>当流动性无法立即满足时，将你的解质押订单挂到市场上，由供应商进行人工撮合。</td></tr></tbody></table>

{% hint style="info" %}
所有提前解质押交易都可以在 TRON 区块链上完全验证。在授予钱包权限之前，请务必核实官方的 TronSave 地址 —— 为了更高的安全性，建议先联系 TronSave 支持团队确认真实性。
{% endhint %}

## 流动性如何影响你的请求

当你提交解质押请求时，TronSave 会对照当前的系统流动性进行检查：

* **流动性充足** —— 你的请求会被自动批准并处理。
* **流动性不足** —— 自动批准基于流动性，没有固定阈值，因此你的请求会被发送给管理团队和供应商，进行人工审核和付款批准。
* **24 小时后流动性仍不可用** —— 你的解质押订单会被挂到[解质押市场](unstake-market.md)上，由供应商进行人工撮合。

## 需要帮助？

TronSave 承诺在可用流动性的限度内，尽快处理所有的提前解质押请求。如果你的请求需要人工批准或遇到问题，请通过官方支持渠道联系我们：

* Telegram 支持：[@wantingtrx](https://t.me/wantingtrx)

## 下一步

* [提前解质押](register-unstake.md)
* [解质押市场](unstake-market.md)
