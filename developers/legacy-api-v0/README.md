---
description: 旧版 TronSave REST API v0 — 为现有集成保留。新项目应使用 v2。
---

# 旧版 API (v0)

REST API v0 是用于以编程方式购买能量的原始 TronSave API。它支持两种为订单付款和授权的方式：签名交易或 TronSave API 密钥。

{% hint style="warning" %}
**v0 为旧版。** 它仅为与现有集成的向后兼容而维护。新集成应使用当前的 [API 参考 (v2)](../api-reference/README.md)，它新增了带宽支持、重新设计的结构、更多端点以及灵活的付款方式。
{% endhint %}

## 为何升级到 v2

REST API v2 发布时相比 v0 带来了显著改进：

* **新资源支持：带宽** — v0 仅购买能量。
* **重新设计的结构**，便于集成并提供更好的开发者体验。
* **更全面的功能**，以更好地支持您的使用场景。
* **灵活的付款方式** — 支持签名交易和内部账户付款。

如果您当前使用 v0，请升级到 [v2](../api-reference/README.md)，以充分利用这些增强功能。

## 付款方式

使用 TronSave API 密钥和签名交易购买能量有两种方式。

<table>
  <thead>
    <tr><th width="220">方式</th><th>概述</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>签名交易</strong></td>
      <td>您构建一笔交易并使用您的私钥对其签名。签名交易会发送到网络进行验证和执行，因此只有私钥的所有者才能执行它。</td>
    </tr>
    <tr>
      <td><strong>API 密钥</strong></td>
      <td>您创建一个 TronSave 内部账户、存入资金，并使用生成的 API 密钥对每个请求进行身份验证。</td>
    </tr>
  </tbody>
</table>

### 使用签名交易

* 您创建一笔交易并使用您的私钥对其签名。
* 然后将签名交易发送到区块链网络进行验证和执行。这确保只有私钥的所有者才能执行该交易。

**优点**

* 使用私钥签名时需要较高的安全级别。
* 对付款资金有更多控制权，因为客户直接管理这些资金。

**缺点**

* 发送到区块链网络的交易将产生额外的交易费用。
* 集成更复杂 — 需要理解交易签名和验证流程。

参见旧版代码示例：[使用私钥购买能量](../code-examples/v0/buy-with-private-key.md)。

### 使用 API 密钥

* 要使用 API 密钥，您需要创建一个 TronSave 内部账户并向其中存入资金。参见[身份验证](../authentication.md)。
* 创建内部账户后，将生成一个 API 密钥，用于对您的交易进行身份验证。

**优点**

* 交易快速且安全。
* 易于集成。
* 节省交易费用。

**缺点**

* 需要向内部账户存入资金才能执行交易。
* 客户的内部账户由 TronSave 系统管理。

参见旧版代码示例：[使用 API 密钥购买能量](../code-examples/v0/buy-with-api-key.md) 和[使用 API 密钥延长订单](../code-examples/v0/extend-with-api-key.md)。

## 后续步骤

* [API 参考 (v2)](../api-reference/README.md) — 适用于所有新集成的当前 API。
* [代码示例](../code-examples/README.md) — 可运行的 v0 和 v2 脚本。
* [身份验证](../authentication.md) — 获取 API 密钥并为您的内部账户充值。
