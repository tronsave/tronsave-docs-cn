---
description: 在 TronSave 网站购买能量和带宽 — 连接钱包，然后下达普通订单、挂单或智能订单。
---

# 在网站购买

TronSave 网站 [tronsave.io](https://tronsave.io) 提供完整的购买界面，包含所有订单类型和工具。这是无需代码的方式：连接 TRON 钱包，选择要租赁的资源，然后签名。

## 开始之前

* 一个通过 **Connect** 连接到网站的 TRON 钱包（例如 TronLink）。
* 应接收能量或带宽的[接收地址](../../../concepts/glossary.md) — 默认情况下为你已连接的钱包。

{% hint style="info" %}
初次接触市场？请先阅读[运作原理](../../../getting-started/how-it-works.md)和[快速开始](../../../getting-started/quickstart.md)。
{% endhint %}

## 基本流程

1. 前往 [tronsave.io/market](https://tronsave.io/market) 并点击 **Connect** 连接你的钱包。
2. 选择 **Buy**，然后设置订单参数（资源、数量、时长、价格）。
3. 选择一种订单类型 — 普通订单、挂单或智能订单（见下文）。
4. 确认并签名交易。订单一旦匹配，资源会在链上代理（委托）到你的接收地址。

## 选择订单类型

网站支持三种订单类型。每种都有各自的分步指南：

<table>
  <thead>
    <tr><th>订单类型</th><th>适用场景</th><th>指南</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>普通订单</strong></td>
      <td>日常、即时购买 — 立即与当前供应匹配。</td>
      <td><a href="normal-order.md">普通订单</a></td>
    </tr>
    <tr>
      <td><strong>挂单</strong></td>
      <td>对价格敏感的买家 — 在订单簿中等待，直到市场匹配你的价格。</td>
      <td><a href="pending-order.md">挂单</a></td>
    </tr>
    <tr>
      <td><strong>智能订单</strong></td>
      <td>市场无法一次性完全匹配的大额租赁（≥ 1000 万能量，≥ 3 天）。</td>
      <td><a href="smart-order.md">智能订单</a></td>
    </tr>
  </tbody>
</table>

有关所有订单类型在概念上的区别，请参阅[订单类型](../../../concepts/order-types.md)。

## 其他购买方式

* **Telegram** — [TronSave 机器人](../on-telegram/README.md)
* **API / SDK** — [REST API](../../../developers/api-reference/README.md) 和 TypeScript SDK

## 后续步骤

* [普通订单](normal-order.md) · [挂单](pending-order.md) · [智能订单](smart-order.md)
* [订单类型](../../../concepts/order-types.md) · [运作原理](../../../getting-started/how-it-works.md)
