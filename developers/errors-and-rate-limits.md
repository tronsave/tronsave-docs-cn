---
description: 汇总 TronSave API 的速率限制（默认每秒 15 次请求）、统一响应结构、HTTP 状态码与错误码对照表，并给出 429 退避重试等常见错误的处理建议，助你构建健壮的集成。
---

# 错误与速率限制

每个 TronSave API 端点都共享相同的速率限制策略和错误响应结构。本页面对两者都进行了说明，方便你一次性构建健壮的重试和错误处理逻辑，并在所有调用中复用。

## 速率限制

大多数端点默认允许 **每秒 15 次请求**。

{% hint style="warning" %}
[出售资源](api-reference/sell-resources.md) 端点被限制为 **每秒 2–3 次请求**。所有其他端点使用默认的每秒 15 次请求。
{% endhint %}

每个端点当前的限制都在其 API 参考页面中说明（例如，[创建订单](api-reference/buy-resources/api-key/create-order.md) 和 [预估 TRX](api-reference/buy-resources/api-key/estimate-trx.md) 端点都注明 `Rate limit: 15 requests per 1 second`）。

当你超过限制时，API 会返回 **HTTP 429**：

```json
{
    "error": true,
    "message": "Rate limit reached"
}
```

{% hint style="info" %}
通过退避并重试来处理 `429`。简单的指数退避（例如先等待 1 秒，再 2 秒，然后 4 秒）通常足以保持在每秒预算之内。
{% endhint %}

## 响应结构

成功的响应使用统一的封装结构：`error` 为 `false`，`message` 为 `"Success"`，负载位于 `data` 之下。

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "orderId": "6818426a65fa8ea36d119d2c"
    }
}
```

错误响应返回非 2xx 的 HTTP 状态码。大多数应用层错误使用与成功响应相同的封装结构：`error` 为 `true`，可供程序识别的错误码嵌入在 `message` 中（例如 `TSAS:106 API_KEY_REQUIRED`），且 `data` 为 `null`：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

模式校验（schema validation）失败由 HTTP 层产生，使用不同的结构，并在 `message` 中指明出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'receiver'"
}
```

## 错误码

下表列出了 API 中观察到的错误码，按 HTTP 状态分组。请使用 **code**（JSON 键）进行程序化处理——**message** 文本可能会发生变化。

<table>
  <thead>
    <tr><th width="90">HTTP</th><th width="320">Code</th><th>Message</th></tr>
  </thead>
  <tbody>
    <tr><td>400</td><td><code>MISSING_PARAMS</code></td><td>请求主体中缺少某些参数</td></tr>
    <tr><td>400</td><td><code>INVALID_PARAMS</code></td><td>某些参数无效</td></tr>
    <tr><td>400</td><td><code>MIN_PRICE_INVALID</code></td><td>minPrice 低于系统的最低价格，或格式不正确。</td></tr>
    <tr><td>400</td><td><code>INTERNAL_ACCOUNT_NOT_FOUND</code></td><td>内部账户不存在</td></tr>
    <tr><td>400</td><td><code>INTERNAL_BALANCE_ACCOUNT_TOO_LOW</code></td><td>余额不足</td></tr>
    <tr><td>400</td><td><code>CANNOT_FULFILLED</code></td><td>该订单要求立即完全匹配，但系统无法 100% 完成它。</td></tr>
    <tr><td>400</td><td><code>MUST_BE_WAIT_PREVIOUS_ORDER_FILLED</code></td><td>系统中已存在一个具有相同参数的待处理订单。</td></tr>
    <tr><td>400</td><td><code>PRICE_EXCEED_MAX_PRICE_REQUIRED</code></td><td>订单价格超过了可接受的最高价格。</td></tr>
    <tr><td>401</td><td><code>API_KEY_REQUIRED</code></td><td>请求头中缺少 API 密钥</td></tr>
    <tr><td>401</td><td><code>INVALID_API_KEY</code></td><td>API 密钥不正确</td></tr>
    <tr><td>429</td><td><code>RATE_LIMIT</code></td><td>已达到速率限制</td></tr>
  </tbody>
</table>

{% hint style="info" %}
并非每个错误码都适用于每个端点。例如，`CANNOT_FULFILLED` 和 `PRICE_EXCEED_MAX_PRICE_REQUIRED` 仅在创建订单时返回。各个 API 参考页面会列出特定于每个端点的错误码。
{% endhint %}

## 错误负载示例

{% tabs %}
{% tab title="400: 请求错误" %}
模式校验（必填字段缺失或无效——消息会指明该字段）：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'receiver'"
}
```

业务逻辑校验（由订单相关端点返回）使用标准封装结构：

```json
{
    "error": true,
    "message": "TSAS:xxx INTERNAL_BALANCE_ACCOUNT_TOO_LOW",
    "data": null
}
```
{% endtab %}

{% tab title="401: 未授权" %}
缺少 `apikey` 请求头：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

`apikey` 无效：

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```

{% hint style="info" %}
旧版 v0 API 不包含 `data` 字段：`{"error": true, "message": "TSAS:107 INVALID_API_KEY"}`。
{% endhint %}
{% endtab %}

{% tab title="404: 未找到" %}
路由或路径错误：

```json
{
    "message": "Route POST:/v2/... not found",
    "error": "Not Found",
    "statusCode": 404
}
```
{% endtab %}

{% tab title="429: 请求过多" %}
```json
{
    "error": true,
    "message": "Rate limit reached"
}
```
{% endtab %}
{% endtabs %}

## 处理常见情况

- **`401`（`API_KEY_REQUIRED` / `INVALID_API_KEY`）** —— 检查 `apikey` 请求头是否存在且正确。参见 [身份验证](authentication.md)。
- **`INTERNAL_BALANCE_ACCOUNT_TOO_LOW`** —— 在创建订单之前为你的 [内部账户](../concepts/glossary.md) 充值。
- **`CANNOT_FULFILLED`** —— 市场无法以请求的价格完全成交该订单。降低预期（例如启用 `allowPartialFill`）或调整 `unitPrice`。
- **`PRICE_EXCEED_MAX_PRICE_REQUIRED`** —— 预估价格高于你的 `options.maxPriceAccepted`。重试前请通过 [预估 TRX](api-reference/buy-resources/api-key/estimate-trx.md) 重新核对价格。
- **`429`（`RATE_LIMIT`）** —— 退避并在你的每秒预算内重试。

## 后续步骤

- [身份验证](authentication.md) —— 如何获取并发送你的 API 密钥。
- [API 参考](api-reference/README.md) —— 各端点的参数和响应。
