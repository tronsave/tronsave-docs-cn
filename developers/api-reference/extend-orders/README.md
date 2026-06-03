---
description: 通过两个步骤延长现有的 TronSave 订单（v2）——先获取可延长的代理，然后提交由 API 密钥或签名交易支付的延长请求。
---

# 延长订单

v2 延长接口让你可以延长已持有的订单，而无需重新下单。相比早期的延长功能，第 2 版新增了两项改进：

* 支持通过**签名交易**延长，而不仅限于 API 密钥。
* 新增资源类型：**带宽**（在**能量**之外）。

延长是一个两步流程：先查询订单上哪些代理可以延长，然后为你想延长的代理提交延长请求。

## 两步流程

| 步骤 | 作用 | 页面 |
| --- | --- | --- |
| 1 | 列出订单上符合延长条件的代理 | [获取可延长的代理](get-extendable-delegates.md) |
| 2 | 提交延长并支付 | [提交延长请求](extend-request.md) |

### 步骤 1：获取可延长的代理

查询某个订单，了解哪些代理（委托）仍可延长以及可延长多久。使用查询结果来构建步骤 2 的请求负载。

→ [获取可延长的代理](get-extendable-delegates.md)

### 步骤 2：提交延长请求

为选定的代理发送延长请求。支持两种支付方式：

* **方式一 —— 使用 API 密钥延长：** 在已预充值的内部账户上使用 TronSave API 密钥授权延长；无需对每笔订单进行链上签名。
* **方式二 —— 使用签名交易延长：** 用你自己的私钥对延长进行签名并在链上结算。

→ [提交延长请求](extend-request.md)

## 接口端点

延长流程使用两个 `POST` 端点：

| 步骤 | 方法 | 路径 |
| --- | --- | --- |
| 获取可延长的代理 | `POST` | `/v2/get-extendable-delegates` |
| 提交延长请求 | `POST` | `/v2/extend-request` |

{% hint style="info" %}
延长流程的 Postman 集合可在 [postman.com/tronsave/tronsave](https://www.postman.com/tronsave/tronsave/folder/01yhu2j/extend-order) 获取。
{% endhint %}

## 在 TRON Nile 测试网上测试

使用开发主机针对 Nile（测试网）进行测试：

{% tabs %}
{% tab title="Testnet" %}
* 获取可延长的代理：`POST` `https://api-dev.tronsave.io/v2/get-extendable-delegates`
* 提交延长请求：`POST` `https://api-dev.tronsave.io/v2/extend-request`
{% endtab %}
{% endtabs %}

要从网站接入 Nile（测试网），请将主机替换为 [https://testnet.tronsave.io/](https://testnet.tronsave.io/)，并按照[获取 API 密钥](../../authentication.md)指南中的相同步骤操作。

## 后续步骤

* [获取可延长的代理](get-extendable-delegates.md) —— 开始流程。
* 在调用 API 之前，先设置[身份验证](../../authentication.md)。
* 查看[能量与带宽](../../../concepts/energy-and-bandwidth.md)和[订单类型](../../../concepts/order-types.md)。
