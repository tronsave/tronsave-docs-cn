---
description: 检索您的 Fast Charge 订单历史记录，按创建时间排序，并可选地按时间范围和分页进行过滤。
---

# 获取 Fast Charge 历史记录

获取您过去的 Fast Charge 订单，按创建时间排序（最新的在前）。默认情况下，该端点返回最近的 10 个订单；可使用查询参数按时间范围过滤并分页。

{% hint style="info" %}
此端点需要 API 密钥。有关如何获取密钥并为您的内部账户充值，请参阅 [身份验证](../../authentication.md)。
{% endhint %}

## 端点

<mark style="color:blue;">`GET`</mark> `https://api.tronsave.io/v0/fast-charge-order-history`

**速率限制：** 每 2 秒 1 个请求。

## 请求头

<table>
  <thead>
    <tr><th width="131">名称</th><th width="135">类型</th><th>描述</th></tr>
  </thead>
  <tbody>
    <tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与您的内部账户关联的 TronSave API 密钥。</td></tr>
  </tbody>
</table>

## 查询参数

<table>
  <thead>
    <tr><th width="131">名称</th><th width="135">类型</th><th>描述</th></tr>
  </thead>
  <tbody>
    <tr><td><code>from</code></td><td>String</td><td>起始时间（毫秒）。</td></tr>
    <tr><td><code>to</code></td><td>String</td><td>结束时间（毫秒）。</td></tr>
    <tr><td><code>page</code></td><td>Integer</td><td>页索引，从 0 开始。默认值：0。</td></tr>
    <tr><td><code>pageSize</code></td><td>Integer</td><td>每页订单数。默认值：10。</td></tr>
  </tbody>
</table>

### 请求 URL 示例

<mark style="color:blue;">`GET`</mark> `https://api.tronsave.io/v0/fast-charge-order-history?from=1732066773000&to=1732073973000&page=0&pageSize=10`

## 响应

### 成功

响应包含匹配订单的总数以及订单对象数组。

```json
{
    "total": "number",
    "results": [
        {
            "_id": "string",                       // id of order
            "receiver": "string",                  // the address that received the resource
            "resource_type": "string",             // the resource type, e.g. "ENERGY"
            "amount": "number",                    // the amount of resource
            "options": {
                "deadline": "number",              // Maximum matching time in seconds. Exceeding it cancels the order and refunds.
                "max_price_accept": "number"       // Only create an order when the estimated price is less than this value.
            },
            "charge_amount": "number",             // Total payout estimated for this order
            "status": "string",                    // status of this order
            "requester": "string",                 // the address that owns the order
            "unit_price": "number",                // price unit, expressed in SUN
            "is_matched": "boolean",               // matched status of this order
            "charge_duration_sec": "number",       // rent duration, in seconds
            "delegate_amount_in_sun": "number",    // The amount of resource delegated
            "delegate_txid": "string",             // on-chain delegate transaction
            "delegator": "string",                 // The address that delegated resource to the target address
            "paid_amount_actual": "number",        // Actual amount of TRX paid for this order
            "repay_amount": "number"               // The refunded amount, added directly to the internal account
            // ...
        }
    ]
}
```

### 成功（示例）

```json
{
    "total": 1,
    "results": [
        {
            "_id": "673d586ad2451e67c4d0943b",
            "receiver": "TQk2eKHE9ZfCdVmyPnh8DfRMUF5b123456",
            "resource_type": "ENERGY",
            "amount": 65000,
            "options": {
                "deadline": 300,
                "max_price_accept": 50
            },
            "charge_amount": 4485000,
            "status": "Completed",
            "pay_txid": "internal_fast_charge_673d586ad2451e67c4d0943a",
            "request_id": "673d586ad2451e67c4d0943a",
            "requester": "TLoE6dE6EaEfeH6pbWHrtcTfMLtk111111",
            "unit_price": 69,
            "is_matched": true,
            "charge_duration_sec": 900,
            "scan_times": 1,
            "created_at": "2024-11-20T03:32:58.722Z",
            "update_at": "2024-11-20T03:34:43.815Z",
            "delegate_amount_in_sun": 881000000,
            "delegate_at": "2024-11-20T03:33:37.538Z",
            "delegate_txid": "ed287d774bbe2e673fc938feb9a02bc926bc82198ef02292212132aec4951821",
            "delegator": "TQBV7xU489Rq8ZCsYi72zBhJMdr2222222",
            "expire_delegate_at": "2024-11-20T03:38:37.530Z",
            "matched_resource_order_id": "673d58d3d2451e67c4d09440",
            "paid_amount_actual": 4127500,
            "repay_amount": 357500,
            "repay_at": "2024-11-20T03:34:43.815Z",
            "repay_by": "Confirmed",
            "repay_txid": "673d58d3d2451e67c4d09441",
            "resource_order_request_id": "673d58d3d2451e67c4d0943f"
        }
    ]
}
```

### 错误

此端点使用 `apikey` 请求头进行身份验证。身份验证和校验失败时返回以下响应体。

**401 Unauthorized** — 缺少 `apikey` 请求头：

```json
{ "error": true, "message": "TSAS:106 API_KEY_REQUIRED" }
```

**401 Unauthorized** — 提供的 API 密钥无效：

```json
{ "error": true, "message": "TSAS:107 INVALID_API_KEY" }
```

**400 Bad Request** — 查询参数未通过 schema 校验（消息中会指出出错的字段）：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "querystring/pageSize must be integer"
}
```

**429 Too Many Requests** — 超出速率限制（此端点为每 2 秒 1 个请求）：

```json
{ "error": true, "message": "Rate limit reached" }
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v0/fast-charge-order-history?from=1732066773000&to=1732073973000&page=0&pageSize=10" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const params = new URLSearchParams({
  from: "1732066773000",
  to: "1732073973000",
  page: "0",
  pageSize: "10",
});

const res = await fetch(
  `https://api.tronsave.io/v0/fast-charge-order-history?${params}`,
  {
    method: "GET",
    headers: {
      apikey: "YOUR_API_KEY",
    },
  }
);

const data = await res.json();
console.log(data);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/fast-charge-order-history"
headers = {
    "apikey": "YOUR_API_KEY",
}
params = {
    "from": "1732066773000",
    "to": "1732073973000",
    "page": 0,
    "pageSize": 10,
}

res = requests.get(url, headers=headers, params=params)
print(res.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetHistory {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();

        String url = "https://api.tronsave.io/v0/fast-charge-order-history"
            + "?from=1732066773000&to=1732073973000&page=0&pageSize=10";

        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .header("apikey", "YOUR_API_KEY")
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

func main() {
	url := "https://api.tronsave.io/v0/fast-charge-order-history" +
		"?from=1732066773000&to=1732073973000&page=0&pageSize=10"

	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", "YOUR_API_KEY")

	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	data, _ := io.ReadAll(resp.Body)
	fmt.Println(string(data))
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use reqwest::blocking::Client;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    let res = client
        .get("https://api.tronsave.io/v0/fast-charge-order-history")
        .query(&[
            ("from", "1732066773000"),
            ("to", "1732073973000"),
            ("page", "0"),
            ("pageSize", "10"),
        ])
        .header("apikey", "YOUR_API_KEY")
        .send()?;

    println!("{}", res.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [跟踪 Fast Charge 订单](track-order.md) — 通过 ID 查询特定订单的匹配状态。
* [Fast Charge 概览](README.md) · [身份验证](../../authentication.md) · [错误与速率限制](../../errors-and-rate-limits.md)
