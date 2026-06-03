---
description: 使用 API 密钥延长 TronSave 资源代理的 v0 旧版流程 —— 先检查可延长的代理，然后创建延长请求。
---

# 使用 REST API 延长（v0）

{% hint style="warning" %}
**旧版 API。** 此处描述的 v0 端点为向后兼容而保留。新的集成应改用当前的 [API 参考](../api-reference/README.md)。
{% endhint %}

要使用此功能，您必须拥有 API 密钥。有关如何获取 API 密钥以及它如何与您的内部账户绑定，请参阅[身份验证](../authentication.md)。

延长流程包含两个步骤：

1. **检查可延长的代理** —— 预估哪些代理可以延长、价格以及总 TRX 成本。
2. **创建延长请求** —— 提交第 1 步中得到的 `extend_data` 以实际执行延长。

{% hint style="info" %}
此流程提供了可运行的 Postman 集合：[使用 API 密钥延长订单](https://www.postman.com/tronsave/tronsave/folder/z5xq9ux/extend-order-with-api-key)。
{% endhint %}

## 第 1 步：检查所有可延长的代理

<mark style="color:orange;">`POST`</mark> `https://api.tronsave.io/v0/get-extendable-delegates`

使用您的 API 密钥检查可延长的代理。

速率限制：每 **1** 秒 **1** 次请求。

#### Headers

<table><thead><tr><th width="134">Name</th><th width="143">Type</th><th>Description</th></tr></thead><tbody><tr><td>apikey<mark style="color:red;">*</mark></td><td>String</td><td>与您的内部账户绑定的 TronSave API 密钥</td></tr></tbody></table>

#### Request Body

<table><thead><tr><th width="166">Name</th><th width="167">Type</th><th>Description</th></tr></thead><tbody><tr><td>extend_to<mark style="color:red;">*</mark></td><td>String</td><td>您希望延长到的时间（以毫秒为单位）</td></tr><tr><td>max_price</td><td>Number</td><td>您愿意为延长支付的最高价格</td></tr><tr><td>receiver</td><td>String</td><td>接收资源代理的地址</td></tr></tbody></table>

{% tabs %}
{% tab title="200: OK Success" %}
```javascript
  {
            "extend_order_book": [
                {
                    "price": 133,
                    "value": 64319
                },
                ...
            ], //Overview extend energy amount at every single price
            "total_delegate_amount": 64319, 
            //Total current delegate of receiver address in tronsave
            "total_available_extend_amount": 64319,
            //Total available delegate of receiver address in tronsave
            "total_estimate_trx": 8554427,
            //Estimate TRX payout if using extend_data below to create extend request
            "your_balance": 20000000,
            //api key's internal balance 
            "is_able_to_extend": true,
            //Compare balance and total_estimate_trx 
            "extend_data": [
                {
                    "delegator": "TMN2uTdy6rQYaTm4A5g732kHRf72tKsA4w",
                    "is_extend": true,
                    "extra_amount": 0,
                    "extend_to": 1728459019000
                }
            ]
            //Extend data that are used to create extend requests below
}
```
{% endtab %}

{% tab title="401: 未授权 Invalid api key" %}
```
{
    "API_KEY_REQUIRED": "Missing api key in headers",
    "INVALID_API_KEY":"api key not correct"
}
```
{% endtab %}

{% tab title="429: 请求过多 Rate limit reached" %}
```
{
    "RATE_LIMIT": "Rate limit reached",
}
```
{% endtab %}
{% endtabs %}

响应中的 `extend_data` 数组就是您在第 2 步中用来创建实际延长请求的内容。

### 示例

{% tabs %}
{% tab title="Body" %}
```java
{
    "extend_to":1728704969000,
    "max_price":165,
    "receiver":"TFwUFWr3QV376677Z8VWXxGUAMF11111111"
}
```
{% endtab %}

{% tab title="Headers" %}
```java
{
  "apikey": <YOUR_API_KEY>
}
```
{% endtab %}

{% tab title="成功响应" %}
```java
{
    "extend_order_book": [
        {
            "price": 108,
            "value": 100000
        },
        {
            "price": 122,
            "value": 200000
        }
    ],
    "total_delegate_amount": 500000,
    "total_available_extend_amount": 300000,
    "total_estimate_trx": 24426224,
    "is_able_to_extend": true,
    "your_balance": 37780396,
    "extend_data": [
        {
            "delegator": "TQBV7xU489Rq8ZCsYi72zBhJM2222222",
            "is_extend": true,
            "extra_amount": 0,
            "extend_to": 1728704969000
        },
        {
            "delegator": "TMN2uTdy6rQYaTm4A5g732kHR333333333",
            "is_extend": true,
            "extra_amount": 0,
            "extend_to": 1728704969000
        }
    ]
}
```
{% endtab %}
{% endtabs %}

### 示例代码

{% tabs %}
{% tab title="Javascript" %}
<pre class="language-javascript"><code class="lang-javascript">const GetEstimateExtendData = async () => {
    const url = `https://api.tronsave.io/v0/get-extendable-delegates`
<strong>    const body = {
</strong>        "extend_to": extend_to, //time in milliseconds you want to extend to
        "receiver": RECEIVER,   //the address that receives resource delegate
        "max_price": max_price, //Optional. Number maximum price you want to pay to extend
    }
    const data = await fetch(url, {
            method: "POST",
            headers: {
                'apikey': API_KEY,
                "content-type": "application/json",
            },
            body: JSON.stringify(body)
        })
    const response = await data.json()
        /**
         * Example response:
         {
            "extend_order_book": [
                {
                    "price": 133,
                    "value": 64319
                }
            ],
            "total_delegate_amount": 64319,
            "total_available_extend_amount": 64319,
            "total_estimate_trx": 8554427,
            "is_able_to_extend": true,
            "your_balance": 20000000,
            "extend_data": [
                {
                    "delegator": "TMN2uTdy6rQYaTm4A5g732kHRf72tKsA4w",
                    "is_extend": true,
                    "extra_amount": 0,
                    "extend_to": 1728459019000
                }
            ]
        }
         */
        return response
}
</code></pre>
{% endtab %}

{% tab title="cURL" %}
```powershell
curl --location 'https://api.tronsave.io/v0/get-extendable-delegates' \
--header 'apikey: {{apikey}}' \
--data '
{
 "extend_to": {{extend_timestamp_in_millisecs}}, 
 "receiver": {{receiver_address}},   
 "max_price": {{max_price_per_unit_want_to_pay}}, 
}
'
```
{% endtab %}
{% endtabs %}

## 第 2 步：使用 API 密钥创建延长请求

<mark style="color:orange;">`POST`</mark> `https://api.tronsave.io/v0/internal-extend-request`

使用您的 API 密钥创建一个新的延长请求订单。

速率限制：每 **1** 秒 **15** 次请求。

#### Headers

<table><thead><tr><th width="135">Name</th><th width="154">Type</th><th>Description</th></tr></thead><tbody><tr><td>apikey<mark style="color:red;">*</mark></td><td>String</td><td>与您的内部账户绑定的 TronSave API 密钥</td></tr></tbody></table>

#### Request Body

<table><thead><tr><th width="170">Name</th><th width="152">Type</th><th>Description</th></tr></thead><tbody><tr><td>receiver<mark style="color:red;">*</mark></td><td>String</td><td>接收资源的地址</td></tr><tr><td>extend_data<mark style="color:red;">*</mark></td><td>Array</td><td>延长数据数组。取自预估可延长代理 API（第 1 步）的响应</td></tr></tbody></table>

{% tabs %}
{% tab title="200: OK Success" %}
成功时返回订单 ID 数组。

```json
[<order_id_1>,<order_id_2>,...]
```
{% endtab %}

{% tab title="401: 未授权 Invalid api key" %}
```json
{
    "API_KEY_REQUIRED": "Missing api key in headers",
    "INVALID_API_KEY":"api key not correct"
}
```
{% endtab %}

{% tab title="429: 请求过多 Rate limit reached" %}
```json
{
    "RATE_LIMIT": "Rate limit reached",
}
```
{% endtab %}
{% endtabs %}

### 示例

{% tabs %}
{% tab title="Body" %}
```json
{
    "extend_data":[
        {
            "delegator": {{some_delegator_address}},
            "is_extend": true,
            "extra_amount": 0,
            "extend_to": {{extend_timestamp_in_millisecs}}
        },
        ...
    ],
    "receiver":{{receiver_address}}
}
```
{% endtab %}

{% tab title="Headers" %}
```json
{
  "apikey": <YOUR_API_KEY>
}
```
{% endtab %}

{% tab title="成功响应" %}
```json
["651d2306e55c073f6ca0992e","651d2306e55c073f6ca09923",...]
```
{% endtab %}
{% endtabs %}

### 示例代码

{% tabs %}
{% tab title="Javascript" %}
```javascript
const SendInternalExtendRequest = async () => {
    const url = `https://api.tronsave.io/v0/internal-extend-request`
    const body = {
            "extend_data": [    
                {
                    "delegator": {{some_delegator_address}},
                    "is_extend": true,
                    "extra_amount": 0,
                    "extend_to": {{extend_timestamp_in_millisecs}}
                }
            ],
            "receiver": RECEIVER,
        }
    const data = await fetch(url, {
            method: "POST",
            headers: {
                'apikey': API_KEY,
                "content-type": "application/json",
            },
            body: JSON.stringify(body)
        })
    const response = await data.json()
        /**
         * Example response 
         * @link  //TODO
           [<order_id>]
         */
        return response
    }
    return []
}
```
{% endtab %}

{% tab title="cURL" %}
```powershell
curl --location 'https://api.tronsave.io/v0/internal-extend-request' \
--header 'apikey: {{apikey}}' \
--data '
{
    "extend_data":[
        {
            "delegator": {{some_delegator_address}},
            "is_extend": true,
            "extra_amount": 0,
            "extend_to": {{extend_timestamp_in_millisecs}}
        },
        ...
    ],
    "receiver":{{receiver_address}}
}
'
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [使用 REST API 购买（v0）](buy/api-key.md) —— 旧版购买流程。
* [API 参考](../api-reference/README.md) —— 当前推荐使用的 API。
* [身份验证](../authentication.md) —— 获取并管理您的 API 密钥。
