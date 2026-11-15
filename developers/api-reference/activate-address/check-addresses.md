---
description: 一次检查最多 100 个 TRON 地址——已激活、未激活、合约地址或无效地址。无需 API 密钥。
---

# 检查地址

在向地址转账、委托资源或付费激活之前，先检查这些 TRON 地址是否已激活。

## 接口

<mark style="color:green;">**`POST`**</mark> **`https://api.tronsave.io/v0/helper/is-active-address-check`**

{% hint style="info" %}
**无需 API 密钥。** **速率限制：** 每秒 5 次请求。**上限：** 每次请求最多 100 个地址。
{% endhint %}

## 请求体

<table><thead><tr><th width="160">名称</th><th width="140">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>addresses</code><mark style="color:red;">*</mark></td><td>String[]</td><td>最多 100 个 TRON 地址（base58 格式，以 <code>T</code> 开头）。</td></tr></tbody></table>

### 请求体示例

```json
{
    "addresses": [
        "TXYZ...address1",
        "TABC...address2"
    ]
}
```

## 响应

### 200 OK — 成功

一个状态码数组，**顺序与你提交的地址顺序一致**：

| 状态码 | 含义 |
| --- | --- |
| `0` | 未激活 |
| `1` | 合约地址 |
| `2` | 已激活 |
| `3` | 不是有效的 TRON 地址 |
| `4` | 无法检查（请重试） |

```json
[2, 0]
```

### 400 Bad Request

当 `addresses` 缺失、不是数组或超过 100 个时返回。

```json
{
    "error": true,
    "message": "..."
}
```
