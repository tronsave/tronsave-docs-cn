---
description: 通过将活跃买单与已签名的链上代理交易进行撮合，在 TronSave 上手动出售能量或带宽。
---

# 出售资源

出售资源 API 让资源供应商可以在 TronSave 上手动成交未完成的买单。卖家获取活跃订单，构建并签名一笔链上 `DelegateResourceContract` 交易，然后将签名后的交易提交给 TronSave 进行订单撮合与结算。

{% hint style="warning" %}
**需要白名单。** 出售 API 仅对加入白名单的钱包开放。请登录 TronSave 并将您的钱包地址发送给客服，以便将其添加到白名单。加入白名单后，使用从该钱包地址生成的 API 密钥来调用出售相关端点。

通过 Telegram 联系客服：[**@wantingtrx**](https://t.me/wantingtrx)
{% endhint %}

出售是一个两步流程：

1. [**获取活跃订单**](sell-resources.md) — 列出您可以成交的未完成买单。
2. [**出售资源**](./) — 针对选定的订单提交一笔签名代理交易。
3. [**获取委托详情**](sell-resources-2.md) - 通过指定的 `delegateId`，获取对应委托的详细信息及当前状态。

## 后续步骤

* 初次使用 API？请从 [快速开始](../../quickstart.md) 和 [身份验证](../../authentication.md) 开始。
* 了解 [能量与带宽](../../../concepts/energy-and-bandwidth.md) 的区别，并查看 [订单类型](../../../concepts/order-types.md)。
* 想买入而不是卖出？请参见 [购买资源](../buy-resources/)。
