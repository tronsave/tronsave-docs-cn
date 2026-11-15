---
description: 购买能量或与 TronSave 集成时最常见问题的解决方法——订单未成交、余额不足、速率限制、ZapBuy 交付以及网络错误。
---

# 故障排查

本页汇总了最常见的集成与购买问题、你会看到的确切错误字符串，以及解决方法。如需完整的机器可读 API 错误码列表，请参阅[错误与速率限制](../developers/errors-and-rate-limits.md)。

## 订单未成交

当你创建一个需要立即完全匹配的订单，但市场无法提供其 100% 的供应时，API 会返回 **HTTP 400** 并附带：

```json
{
    "CANNOT_FULFILLED": "The order requires an immediate full match, but the system cannot fulfill 100% of it."
}
```

这通常意味着以下情况之一：

* **在你的价格下供应不足。** 市场没有足够的能量可在等于或低于你的 `unitPrice` 的价格上提供。
* **你的价格上限过低。** 如果预估价格超过 `options.maxPriceAccepted`，你将会收到 `PRICE_EXCEED_MAX_PRICE_REQUIRED`。

**如何修复：**

* 允许部分成交（`allowPartialFill`），使订单匹配当前可用的部分，而不要求立即 100% 成交。
* 在提交前使用 [Estimate TRX](../developers/api-reference/buy-resources/api-key/estimate-trx.md) 重新核对价格，然后将 `unitPrice` 设置为返回的值。
* 对于市场无法一次性匹配的大额租赁，请使用[智能订单](../concepts/order-types.md)（最低 10,000,000 能量，最短 3 天时长），它会随着时间推移持续进行更多匹配。
* 如果你不需要立即交付，请使用[挂单](../guides/buy/on-the-website/pending-order.md)，它会在订单簿中等待，直到市场能够匹配你的价格。

{% hint style="info" %}
在机器人/Telegram 上，订单始终以 **MEDIUM** 价格提交，以保证 100% 匹配，因此机器人购买的成本可能高于你在网站上设置最低可能价格的订单。这两个价格仅在系统资源短缺时才会出现差异。
{% endhint %}

### “挂单已存在”

```json
{
    "MUST_BE_WAIT_PREVIOUS_ORDER_FILLED": "A pending order with the same parameters already exists in the system."
}
```

你已经有一个参数完全相同的挂单。请等待它成交（或取消它），再重新提交相同的订单，或者更改某个参数（例如 `unitPrice`）。

## 余额不足

订单从你的[内部账户](../concepts/glossary.md)余额中支付。如果余额不足，你会看到 **HTTP 400**：

```json
{
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough"
}
```

**如何修复：** 在创建订单前为你的内部账户充值。如果该账户本身尚不存在，你会看到：

```json
{
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account does not exist"
}
```

请先创建/充值账户，然后重试。

## 触发速率限制（HTTP 429）

大多数端点允许**每秒 15 次请求**；少数端点更低。超出限制会返回 **HTTP 429**：

```json
{
    "RATE_LIMIT": "Rate limit reached"
}
```

**如何修复：** 退避并在你的每秒预算内重试。简单的指数退避（等待 1 秒，然后 2 秒，再 4 秒）通常就足够了。各端点的限制请参阅[错误与速率限制](../developers/errors-and-rate-limits.md)。

## 认证失败（HTTP 401）

```json
{
    "API_KEY_REQUIRED": "Missing api key in headers",
    "INVALID_API_KEY": "api key not correct"
}
```

* `API_KEY_REQUIRED` — 请求中缺少 `apikey` 头。
* `INVALID_API_KEY` — 密钥存在但不正确（拼写错误、已吊销，或来自不同环境）。

**如何修复：** 确认已设置 `apikey` 头并且与有效密钥匹配。请参阅[认证](../developers/authentication.md)。

{% hint style="warning" %}
在测试网上签发的密钥无法用于主网，反之亦然。请确保密钥与你正在调用的[环境](../developers/environments.md)匹配。
{% endhint %}

## 通过 ZapBuy 未收到能量

使用 [ZapBuy](../guides/buy/zapbuy.md) 时，你将 TRX 发送到 TronSave 机器人地址，系统会自动为发送钱包创建一个 1 小时的能量租赁。如果没有收到能量，请逐项检查以下内容：

* **你至少发送了最低金额。** ZapBuy 要求至少租赁 **65,000 能量**。低于该值的请求会被**忽略**，且不会创建订单。
* **你从普通钱包发送。** 能量会被代理（委托）回发送地址，该地址必须是普通钱包——**而非智能合约或交易所钱包**。
* **交易已确认。** 交易必须在 TRON 区块链上成功确认。
* **订单能够立即匹配。** ZapBuy 仅在当前有可用能量时才会成交。如果找不到匹配，你将不会收到能量。
* **你将正确的代币发送到了正确的地址。** ZapBuy 仅接受 **TRX**，发送到 TronSave 机器人地址：

  ```ini
  TLx8h8fjv5pyuxCu292ZgjbU14XZSiLGg4
  ```

如果你满足上述所有条件但仍未收到能量，请**立即**携带你的交易详情联系 TronSave 以请求帮助或退款：[https://t.me/wantingtrx](https://t.me/wantingtrx)。

## 网络错误

发送到错误的网络是“丢失”资金和能量未交付最常见的原因。

* **在非 TRON 网络上发送 TRX。** ZapBuy 和内部账户充值仅接受 **TRON 网络上的原生 TRX**。通过其他链（或包装/跨链桥代币）发送的资金不会入账。<!-- [NEEDS CONFIRMATION: recovery procedure / whether cross-network deposits can be recovered] -->
* **API 调用打到了错误的环境。** 测试网和主网有各自独立的基础 URL 和独立的 API 密钥。用测试网密钥（或 URL）调用主网会产生 `INVALID_API_KEY` 或意外的空结果。请对照[环境](../developers/environments.md)核实你的基础 URL 和密钥。

{% hint style="warning" %}
请务必再次核对你要代理（委托）能量到的**目标地址**。目标地址不一致也可能是机器人购买与网站之间价格差异的原因。<!-- [NEEDS CONFIRMATION: exact recovery path when Energy is delegated to an unintended target address] -->
{% endhint %}

## 快速参考

<table>
  <thead>
    <tr><th width="120">HTTP</th><th width="320">Code</th><th>Fix</th></tr>
  </thead>
  <tbody>
    <tr><td>400</td><td><code>CANNOT_FULFILLED</code></td><td>允许部分成交、调整价格，或使用智能订单/挂单。</td></tr>
    <tr><td>400</td><td><code>PRICE_EXCEED_MAX_PRICE_REQUIRED</code></td><td>重新预估并提高 <code>options.maxPriceAccepted</code>。</td></tr>
    <tr><td>400</td><td><code>MUST_BE_WAIT_PREVIOUS_ORDER_FILLED</code></td><td>等待现有订单，或更改某个参数。</td></tr>
    <tr><td>400</td><td><code>INTERNAL_BALANCE_ACCOUNT_TOO_LOW</code></td><td>为你的内部账户充值。</td></tr>
    <tr><td>400</td><td><code>INTERNAL_ACCOUNT_NOT_FOUND</code></td><td>先创建/充值内部账户。</td></tr>
    <tr><td>401</td><td><code>API_KEY_REQUIRED</code> / <code>INVALID_API_KEY</code></td><td>为正确的环境设置有效的 <code>apikey</code> 头。</td></tr>
    <tr><td>429</td><td><code>RATE_LIMIT</code></td><td>退避并使用指数退避重试。</td></tr>
  </tbody>
</table>

## 后续步骤

* [错误与速率限制](../developers/errors-and-rate-limits.md) — 完整的错误码参考。
* [订单类型](../concepts/order-types.md) — 选择正确的订单以避免成交失败。
* [ZapBuy](../guides/buy/zapbuy.md) · [环境](../developers/environments.md) · [认证](../developers/authentication.md)
