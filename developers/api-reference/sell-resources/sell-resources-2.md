---
description: 根据 Delegate ID 获取指定委托的详细信息。
---

# 获取单笔委托详情

{% hint style="warning" %}
**需要白名单。** 出售 API 仅对加入白名单的钱包开放。请登录 TronSave 并将您的钱包地址发送给客服，以便将其添加到白名单。加入白名单后，使用从该钱包地址生成的 API 密钥来调用出售相关端点。

通过 Telegram 联系客服：[**@wantingtrx**](https://t.me/wantingtrx)
{% endhint %}

## 端点

返回指定资源委托的详细信息。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/delegate/:delegateId`**

{% hint style="info" %}
**速率限制：** 每 1 秒 5 次请求。
{% endhint %}

### Headers

<table><thead><tr><th width="160">Name</th><th width="120">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>apikey</code> <mark style="color:red;">*</mark></td><td>String</td><td>TronSave API 密钥（必须属于白名单地址）。参见 <a href="../../authentication.md">身份验证</a>。</td></tr></tbody></table>

#### 路径参数（Path parameters）

<table><thead><tr><th width="221">名称（Name）</th><th width="172">类型（Type）</th><th>描述（Description）</th></tr></thead><tbody><tr><td><code>delegateId</code></td><td>string</td><td>要查询的资源委托的唯一 ID。（<a href="./">出售资源</a>）</td></tr></tbody></table>

### 响应

```javascript
{
  "error": false,
  "message": "Success",
  "data": {
    "id": "string",                 // Delegate ID
    "delegator": {
      "address": "string"           // TRON address of the delegator
    },
    "receiver": {
      "address": "string"           // TRON address of the receiver
    },
    "order": {
      "id": "string"                // ID of the order
    },
    "resourceType": "string",       // Resource type: "ENERGY" or "BANDWIDTH"
    "resourceAmount": "number",     // Amount of delegated resource
    "trxAmount": "number",          // TRX amount to delegate(SUN)
    "durationSec": "number",        // Delegation duration in seconds
    "unitPrice": "number",          // Unit price (SUN)
    "typeDelegate": "string",       // Delegation type, e.g. "AUTO", "MANUAL" or "EXTEND"
    "status": "string",             // Delegation status, e.g. "SUCCESS"
    "paidAmount": "string",         // Amount paid (SUN)
    "paidFee": "string",            // Payment fee (SUN)
    "paidAt": "number",             // Payment timestamp (Unix timestamp)
    "paymentAddress": "string",     // TRON address used for payment
    "paidTxid": {
      "txid": "string"              // Payment transaction ID
    },
    "delegateAt": "number",        // Delegation timestamp (Unix timestamp)
    "delegateTxid": {
      "txid": "string"              // Delegation transaction ID
    },
    "smartMatchingInfo": "object | null", // Smart Matching details, if applicable
    "extendInfo": "object | null",         // Extension details, if applicable
    "fastInfo": "object | null"            // Fast delegation details, if applicable
  }
}
```

**响应示例**

```javascript
{
    "error": false,
    "message": "Success",
    "data": {
        "id": "6ae083908a976b17d6714777",
        "delegator": {
            "address": "TURL8VVSs9Z8ryd2r8vLZsqPtzDD383838"
        },
        "receiver": {
            "address": "TWrUyds2aPLhRgwwj3dzaEL8Z7udcbcbcb"
        },
        "order": {
            "id": "6ae0839036d832402c293add"
        },
        "resourceType": "BANDWIDTH",
        "resourceAmount": 1001,
        "trxAmount": 1585700000,
        "durationSec": 300,
        "unitPrice": 600,
        "typeDelegate": "AUTO",
        "status": "SUCCESS",
        "paidAmount": "450450",
        "paidFee": "150150",
        "paidAt": 1793145599,
        "paymentAddress": "TURL8VVSs9Z8ryd2r8vLZsqPtzDD383838",
        "paidTxid": {
            "txid": "ac9299d9def7b3f2be93ceae9e78cecd4c957311bc2e1f4e65b4dfbacb5ee522"
        },
        "delegateAt": 1793098640,
        "delegateTxid": {
            "txid": "12b9d9bcbf01fd29555276aef58dfba0c457e68dbf4698d6ca093b54c37a8599"
        },
        "smartMatchingInfo": null,
        "extendInfo": null,
        "fastInfo": null
    }
}
```

## 后续步骤

* 初次使用 API？请从 [快速开始](../../quickstart.md) 和 [身份验证](../../authentication.md) 开始。
* 了解 [能量与带宽](../../../concepts/energy-and-bandwidth.md) 的区别，并查看 [订单类型](../../../concepts/order-types.md)。
* 想买入而不是卖出？请参见 [购买资源](../buy-resources/)。
