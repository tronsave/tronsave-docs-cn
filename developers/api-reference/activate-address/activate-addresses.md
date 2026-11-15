---
description: 一次 API 调用激活最多 100 个 TRON 地址。每个未激活地址 1.5 TRX，从你的 TronSave 内部账户支付；已激活地址免费。
---

# 激活地址

在一次请求中激活最多 100 个 TRON 地址。TronSave 会逐一检查每个地址，**对每个尚未激活的地址收取 1.5 TRX**，并为你完成激活。已激活的地址会被跳过，不收取费用；请求中的重复地址会被去重。

调用此接口需要一个绑定了已预充值内部账户的 TronSave API 密钥。请参阅 [身份验证](../../authentication.md)。

## 接口

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/helper/multi-active-address`**

{% hint style="info" %}
**速率限制：** 每秒 5 次请求。**上限：** 每次请求最多 100 个地址。
{% endhint %}

## 请求头

<table><thead><tr><th width="131">名称</th><th width="135">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与支付激活费用的内部账户关联的 TronSave API 密钥。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="160">名称</th><th width="140">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>addresses</code><mark style="color:red;">*</mark></td><td>String[]</td><td>最多 100 个需要激活的 TRON 地址。</td></tr></tbody></table>

### 请求体示例

```json
{
    "addresses": [
        "TXYZ...address1",
        "TABC...address2",
        "TDEF...address3"
    ]
}
```

## 响应

### 200 OK — 请求已接受

已扣除未激活地址的费用，激活已加入队列：

```json
{
    "message": "Success create 2 active address request(s)"
}
```

数字表示需要激活（并已收费）的地址数量。激活会异步完成——请使用 [检查地址](check-addresses.md) 确认每个地址返回 `2`（已激活）。

### 错误响应

```json
{
    "error": true,
    "message": "..."
}
```

| 原因 | 处理方式 |
| --- | --- |
| 缺少 `apikey` 请求头，或密钥无效 | 发送有效的密钥——请参阅 [身份验证](../../authentication.md) |
| `addresses` 缺失、不是数组或超过 100 个 | 每次请求发送 1–100 个地址 |
| 内部账户余额不足 | 充值 TRX：需要 1.5 TRX × 未激活地址数量 |
| 请求过多 | 保持在每秒 5 次请求以内 |

## 示例

```bash
curl -X POST https://api.tronsave.io/v0/helper/multi-active-address \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"addresses": ["TXYZ...address1", "TABC...address2"]}'
```
