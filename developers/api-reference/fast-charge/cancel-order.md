---
description: 使用你的 API 密钥取消一个或多个尚未匹配的待处理快充订单。
---

# 取消订单

通过订单 ID 取消一个或多个快充订单。你只能取消处于 **Pending（待处理）** 状态且尚未被匹配的订单。

调用此端点需要一个与预充值内部账户绑定的 TronSave API 密钥。有关如何获取和使用 API 密钥，请参阅 [身份验证](../../authentication.md)。

## 端点

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/fast-charge-order-cancel`**

{% hint style="info" %}
**速率限制：** 每 2 秒 1 次请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="131">名称</th><th width="135">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与内部账户关联的 TronSave API 密钥。必填。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="190">名称</th><th width="164">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>order_ids</code></td><td>Array</td><td>订单 ID 数组。已创建且需要取消的订单 ID 列表。</td></tr></tbody></table>

### 请求体示例

```json
{
    "order_ids": [
        "673c17a3129f1881382e98e3"
    ]
}
```

## 响应

### 200 OK — 成功

```json
{
    "cancel_results": [
        {
            "order_id": "673c17a3129f1881382e98e3",
            "is_success": true,
            "fail_reason": null
        }
    ]
}
```

### 400 Bad Request — 校验错误

当请求体缺少必填字段或字段值无效时返回。`message` 会指出出错的属性名称。

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'order_ids'"
}
```

### 401 Unauthorized — API 密钥无效

当 `apikey` 请求头缺失或无效时返回。

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY"
}
```

### 429 Too Many Requests — 速率限制

当超出速率限制（此端点为每 2 秒 1 次请求）时返回。

```json
{
    "error": true,
    "message": "Rate limit reached"
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST 'https://api.tronsave.io/v0/fast-charge-order-cancel' \
  -H 'apikey: YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
  -d '{
    "order_ids": [
        "673c17a3129f1881382e98e3"
    ]
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v0/fast-charge-order-cancel",
  {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      order_ids: ["673c17a3129f1881382e98e3"],
    }),
  }
);

const data = await res.json();
console.log(data);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/fast-charge-order-cancel"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "order_ids": ["673c17a3129f1881382e98e3"],
}

response = requests.post(url, headers=headers, json=body)
print(response.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class CancelOrder {
    public static void main(String[] args) throws Exception {
        String body = """
            {
                "order_ids": [
                    "673c17a3129f1881382e98e3"
                ]
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v0/fast-charge-order-cancel"))
            .header("apikey", "YOUR_API_KEY")
            .header("Content-Type", "application/json")
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
			"673c17a3129f1881382e98e3"
		]
	}`)

	url := "https://api.tronsave.io/v0/fast-charge-order-cancel"
	req, _ := http.NewRequest("POST", url, bytes.NewBuffer(body))
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("Content-Type", "application/json")

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
use serde_json::json;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::blocking::Client::new();

    let body = json!({
        "order_ids": ["673c17a3129f1881382e98e3"]
    });

    let response = client
        .post("https://api.tronsave.io/v0/fast-charge-order-cancel")
        .header("apikey", "YOUR_API_KEY")
        .header("Content-Type", "application/json")
        .json(&body)
        .send()?;

    println!("{}", response.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* 查看 [身份验证](../../authentication.md) 以设置你的 API 密钥和内部账户。
* 参阅 [错误与速率限制](../../errors-and-rate-limits.md) 了解如何处理错误响应。
* 了解 [能量与带宽](../../../concepts/energy-and-bandwidth.md)。
