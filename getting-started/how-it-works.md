---
description: 端到端解析 TronSave 租赁流程：买家在订单簿市场下单，供应商以质押 TRX 供应能量，系统按市场价撮合并在链上完成代理（委托），到期回收，支持续期与两种付款方式。
---

# 工作原理

TronSave 是一个面向 TRON 资源的**订单簿市场**。买家为能量/带宽下单；供应商用其质押的 TRX 提供资源；TronSave 撮合双方并执行链上**代理(委托)**。

## 流程概览

```
Buyer ──places order──▶  TronSave order book  ◀──supplies resource── Provider
                              │
                              ├─ matches order at market price
                              ▼
                       On-chain delegation (Stake 2.0)
                              │
                              ▼
                   Receiver address gets Energy/Bandwidth
                        for the rental duration
```

1. **预估** —— 买家询问某笔订单的成本(`estimate-buy-resource`)。TronSave 返回单价(以 [SUN](../concepts/glossary.md) 计)和可用供应量。
2. **下单** —— 买家创建订单，指定 `resourceType`、`resourceAmount`、`durationSec`、`receiver` 以及一个[价格档位](../concepts/order-types.md)(`SLOW`/`MEDIUM`/`FAST` 或固定的 SUN 值)。
3. **撮合** —— 订单簿将订单与供应商的供应进行撮合。根据供应量和订单选项,订单可以完全成交、部分成交或保持待处理状态。
4. **代理** —— 已撮合的资源会从供应商账户**在链上代理(委托)**到买家的 `receiver` 接收地址,租赁时长为 `durationSec`。
5. **过期 / 续期** —— 当时长结束时,代理会被收回。买家可以在过期前进行[续期](../developers/api-reference/extend-orders/README.md),无需重新下单。

## 两种付款方式

| | API 密钥(内部账户) | 签名交易 |
| --- | --- | --- |
| **方式** | 将 TRX 存入 TronSave 内部账户;费用自动扣除 | 每次购买时从你自己的钱包签名一笔 TRX 付款 |
| **适用于** | 机器人、后端、高频买家 | 不愿在 TronSave 持有 TRX 的用户 |
| **托管** | TronSave 持有你存入的余额 | 你保留完全的自我托管 |

两种方式详见[身份验证](../developers/authentication.md)。

## 在哪里使用

* **网站** —— [tronsave.io](https://tronsave.io)(完整 UI、所有订单类型和工具)
* **Telegram** —— [TronSave 机器人](../guides/buy/on-telegram/README.md)
* **API / SDK** —— [REST API](../developers/api-reference/README.md) 和 [SDK](../developers/sdk.md)(TypeScript、Rust、Python、Java 和 PHP)

## 后续步骤

* [快速开始](quickstart.md) · [订单类型](../concepts/order-types.md) · [租赁模型](../concepts/rental-model.md)
