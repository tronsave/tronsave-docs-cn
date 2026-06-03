---
description: 获取与你的 TronSave API 密钥关联的内部账户的订单历史，支持分页和状态筛选。
---

# 获取内部账户订单历史

获取与所提供的 API 密钥关联的内部账户的订单历史。订单按创建时间排序返回，最新的订单排在最前面。默认情况下，该接口返回最新的 10 条订单。

## 接口

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/orders`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="120">名称</th><th width="100">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表内部账户的 TronSave API 密钥。参阅<a href="../../../authentication.md">身份验证</a>以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

## 查询参数

<table>
<thead>
<tr><th width="200">名称</th><th width="156">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>page</code></td><td>Integer</td><td>页索引，从 0 开始。默认值：<code>0</code>。</td></tr>
<tr><td><code>pageSize</code></td><td>Integer</td><td>每页订单数量。默认值：<code>10</code>。</td></tr>
<tr><td><code>status</code></td><td>String</td><td><code>"Active"</code> 或 <code>"Completed"</code>。默认：获取全部。</td></tr>
</tbody>
</table>

## 请求体

该接口不需要请求体——仅需传入 `apikey` 请求头和可选的查询参数。

## 响应

成功的响应会返回 `error: false`，匹配的订单位于 `data.data` 中，同时在 `data.total` 中返回总数。

### 200: OK

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "id":  string, // id of order
        "requester": string, // the address represents the order owner
        "receiver": string, // the address of the resource that is received 
        "resourceAmount": number, // the amount of resource
        "resourceType": string, // the resource type is "ENERGY" or "BANDWIDTH"
        "remainAmount": number, // the remaining amount can be matched by the system,
        "price": number, // price unit is equal to SUN
        "durationSec": number, // rent duration, duration unit is equal to seconds
        "status": string, // the order status, either Active or Completed
        "orderType": string, // type of order is "NORMAL" or "EXTEND"
        "allowPartialFill": boolean, //Allow the order to be filled partially or not
        "payoutAmount": number, // Total payout of this order
        "fulfilledPercent": number, //The percent that shows filling processing. 0-100
        "delegates": [ //All matched delegates for this order
            {
                "delegator": string, // The address that delegates the resource for the target address
                "amount": number, //The amount of resource was delegated
                "txid": number // The transaction ID in on-chain
             }[]
       } 
    }[],
        "total": number
}
```

### 成功响应示例

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "data": [
            {
                "id": "6819c7578729a45600f740d3",
                "requester": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSS",
                "receiver": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSS",
                "resourceAmount": 32000,
                "resourceType": "ENERGY",
                "remainAmount": 0,
                "orderType": "NORMAL",
                "price": 90,
                "durationSec": 300,
                "allowPartialFill": false,
                "payoutAmount": 2880000,
                "fulfilledPercent": 100,
                "delegates": [
                    {
                        "delegator": "THnnMCe67VMDXoivepiA7ZQSB8888888",
                        "amount": 32000,
                        "txid": "transaction_id_1"
                    }
                ]
            },
            {
                "id": "68198bcd8729a45600f740cf",
                "requester": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSS",
                "receiver": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSS",
                "resourceAmount": 1234,
                "resourceType": "BANDWIDTH",
                "remainAmount": 0,
                "orderType": "NORMAL",
                "price": 600,
                "durationSec": 900,
                "allowPartialFill": false,
                "payoutAmount": 740400,
                "fulfilledPercent": 100,
                "delegates": [
                    {
                        "delegator": "TMhiksDwSVjuxdXLwdNQEJpuFCLG77777",
                        "amount": 1234,
                        "txid": "transaction_id_2"
                    }
                ]
            }
        ],
        "total": 2
    }
}
```

### 错误响应

该接口通过 `apikey` 请求头进行身份验证。缺失或无效的密钥会返回 `401`。

**401 Unauthorized——缺少 API 密钥：**

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

**401 Unauthorized——无效的 API 密钥：**

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```

**400 Bad Request——结构校验。** 无效的查询参数会被拒绝。`message` 会指出有问题的字段，例如：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "querystring/status must be equal to one of the allowed values"
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/orders?page=0&pageSize=10" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getOrderHistory = async (apiKey) => {
  const url = `${TRONSAVE_API_URL}/v2/orders`;
  const res = await fetch(url, {
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

getOrderHistory("YOUR_API_KEY").then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"
API_KEY = "YOUR_API_KEY"


def get_order_history() -> dict:
    """Get order history for the internal account."""
    url = f"{TRONSAVE_API_URL}/v2/orders"
    headers = {"apikey": API_KEY}
    response = requests.get(url, headers=headers)
    return response.json()


print(get_order_history())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetOrderHistory {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";
    static final String API_KEY = "YOUR_API_KEY";

    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/orders"))
                .header("apikey", API_KEY)
                .GET()
                .build();

        HttpResponse<String> response =
                client.send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"fmt"
	"io"
	"net/http"
)

const (
	tronsaveAPIURL = "https://api.tronsave.io"
	apiKey         = "YOUR_API_KEY"
)

func main() {
	req, err := http.NewRequest(http.MethodGet, tronsaveAPIURL+"/v2/orders", nil)
	if err != nil {
		panic(err)
	}
	req.Header.Set("apikey", apiKey)

	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	body, _ := io.ReadAll(resp.Body)
	fmt.Println(string(body))
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use serde_json::Value;

const TRONSAVE_API_URL: &str = "https://api.tronsave.io";
const API_KEY: &str = "YOUR_API_KEY";

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::blocking::Client::new();
    let resp: Value = client
        .get(format!("{TRONSAVE_API_URL}/v2/orders"))
        .header("apikey", API_KEY)
        .send()?
        .json()?;

    println!("{resp:#?}");
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [获取内部账户信息](get-account-info.md) — 查看你的余额和充值地址。
* [购买能量（创建订单）](create-order.md) — 使用你的内部账户余额下一笔新订单。
* [身份验证](../../../authentication.md) — 如何在每个请求中传入你的 API 密钥。
