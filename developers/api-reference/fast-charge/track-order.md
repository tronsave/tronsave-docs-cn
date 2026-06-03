---
description: 通过订单 ID 查询一个或多个 Fast Charge 订单的撮合状态。
---

# 追踪 Fast Charge 订单

创建 Fast Charge 订单后，使用此端点查询它们的状态——每个订单是仍在等待撮合，还是已经撮合完成。

{% hint style="info" %}
此端点需要 API 密钥。请参阅 [身份验证](../../authentication.md)，了解如何获取密钥并为你的内部账户充值。
{% endhint %}

## 端点

<mark style="color:orange;">`POST`</mark> `https://api.tronsave.io/v0/fast-charge-order-tracking`

**速率限制：** 每 2 秒 1 次请求。

## 请求头

<table>
  <thead>
    <tr><th width="131">名称</th><th width="135">类型</th><th>描述</th></tr>
  </thead>
  <tbody>
    <tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户关联的 TronSave API 密钥。</td></tr>
  </tbody>
</table>

## 请求体

<table>
  <thead>
    <tr><th width="190">名称</th><th width="164">类型</th><th>描述</th></tr>
  </thead>
  <tbody>
    <tr><td><code>order_ids</code></td><td>Array</td><td>订单 ID 数组。一组已创建且需要查询状态的订单 ID 列表。</td></tr>
  </tbody>
</table>

### 请求体示例

```json
{
    "order_ids": [
        "673c17a3129f1881382e98e2",
        "673c17a3129f1881382e98e3"
    ]
}
```

## 响应

### 成功

```json
{
    "results": [
        {
            "order_id": "673c17a3129f1881382e98e2",
            "status": "Completed"
        },
        {
            "order_id": "673c17a3129f1881382e98e3",
            "status": "Pending"
        }
    ]
}
```

### 错误

此端点通过 `apikey` 请求头进行身份验证。

**401 Unauthorized** —— 缺少 `apikey` 请求头：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED"
}
```

**401 Unauthorized** —— API 密钥无效：

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY"
}
```

**400 Bad Request** —— 缺少必填字段或字段无效。错误消息会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'order_ids'"
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v0/fast-charge-order-tracking" \
  -H "Content-Type: application/json" \
  -H "apikey: YOUR_API_KEY" \
  -d '{
    "order_ids": [
        "673c17a3129f1881382e98e2",
        "673c17a3129f1881382e98e3"
    ]
}'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch("https://api.tronsave.io/v0/fast-charge-order-tracking", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    apikey: "YOUR_API_KEY",
  },
  body: JSON.stringify({
    order_ids: [
      "673c17a3129f1881382e98e2",
      "673c17a3129f1881382e98e3",
    ],
  }),
});

const data = await res.json();
console.log(data);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/fast-charge-order-tracking"
headers = {
    "Content-Type": "application/json",
    "apikey": "YOUR_API_KEY",
}
body = {
    "order_ids": [
        "673c17a3129f1881382e98e2",
        "673c17a3129f1881382e98e3",
    ],
}

res = requests.post(url, json=body, headers=headers)
print(res.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class TrackOrder {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();

        String body = """
            {
                "order_ids": [
                    "673c17a3129f1881382e98e2",
                    "673c17a3129f1881382e98e3"
                ]
            }
            """;

        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v0/fast-charge-order-tracking"))
            .header("Content-Type", "application/json")
            .header("apikey", "YOUR_API_KEY")
            .POST(HttpRequest.BodyPublishers.ofString(body))
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
	"bytes"
	"fmt"
	"io"
	"net/http"
)

func main() {
	body := []byte(`{
		"order_ids": [
			"673c17a3129f1881382e98e2",
			"673c17a3129f1881382e98e3"
		]
	}`)

	req, _ := http.NewRequest(
		"POST",
		"https://api.tronsave.io/v0/fast-charge-order-tracking",
		bytes.NewBuffer(body),
	)
	req.Header.Set("Content-Type", "application/json")
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
use serde_json::json;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    let body = json!({
        "order_ids": [
            "673c17a3129f1881382e98e2",
            "673c17a3129f1881382e98e3"
        ]
    });

    let res = client
        .post("https://api.tronsave.io/v0/fast-charge-order-tracking")
        .header("Content-Type", "application/json")
        .header("apikey", "YOUR_API_KEY")
        .json(&body)
        .send()?;

    println!("{}", res.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [确认请求](confirm-request.md) —— 完成已撮合的订单，回收未使用的能量并获得 TRX 退款。
* [取消订单](cancel-order.md) —— 取消 `Pending` 状态的订单以获得全额退款。
* [Fast Charge 概览](README.md) · [身份验证](../../authentication.md) · [错误与速率限制](../../errors-and-rate-limits.md)
