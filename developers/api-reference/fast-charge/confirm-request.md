---
description: 确认已完成的快速充能订单，以结束能量租赁、回收未使用的能量，并触发将 TRX 退款到你的内部账户。
---

# 确认请求

在你使用完能量后，确认你想要完成的订单，系统便会结束该租赁。确认操作会回收所有未使用的能量，并计算返还到你内部账户的 TRX 退款。你越早确认，节省的成本就越多。

要调用此接口，你需要一个绑定到已预先充值内部账户的 TronSave API 密钥。关于如何获取和使用 API 密钥，请参阅 [身份验证](../../authentication.md)。

## 接口

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/fast-charge-order-confirmation`**

{% hint style="info" %}
**速率限制：** 每 2 秒 1 次请求。
{% endhint %}

{% hint style="success" %}
尽早确认有助于你获得 **尽可能多的退款**。
{% endhint %}

## 请求头

<table><thead><tr><th width="131">名称</th><th width="135">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与内部账户关联的 TronSave API 密钥。必填。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="190">名称</th><th width="164">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>order_ids</code></td><td>Array</td><td>已创建且需要<strong>被确认</strong>的订单 ID 列表。</td></tr></tbody></table>

### 请求体示例

```json
{
    "order_ids": [
        "673d5fe2d2451e67c4d09483"
    ]
}
```

## 响应

### 200 OK — 成功

```json
{
    "confirmation_results": [
        {
            "order_id": string,    // id of order
            "is_success": boolean, // the status of the confirm action
            "fail_reason": string  // By default, a successful confirm returns null. If the confirm fails, it returns the reason for failure.
        }
    ]
}
```

成功响应示例：

```json
{
    "confirmation_results": [
        {
            "order_id": "673d5fe2d2451e67c4d09483",
            "is_success": true,
            "fail_reason": null
        }
    ]
}
```

### 错误

这是一个使用 `apikey` 请求头进行身份验证的旧版 `v0` 接口。

#### 401 未授权 — 缺少 API 密钥

当缺少 `apikey` 请求头时返回。

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED"
}
```

#### 401 未授权 — 无效的 API 密钥

当提供的 `apikey` 无效时返回。

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY"
}
```

#### 400 错误请求 — 校验错误

当缺少必填字段（例如 `order_ids`）或字段无效时返回。消息中会指出出错的属性。

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'order_ids'"
}
```

#### 429 请求过多 — 速率限制

当你超过此接口的速率限制（每 2 秒 1 次请求）时返回。

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
curl -X POST 'https://api.tronsave.io/v0/fast-charge-order-confirmation' \
  -H 'apikey: YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
  -d '{
    "order_ids": [
        "673d5fe2d2451e67c4d09483"
    ]
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v0/fast-charge-order-confirmation",
  {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      order_ids: ["673d5fe2d2451e67c4d09483"],
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

url = "https://api.tronsave.io/v0/fast-charge-order-confirmation"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "order_ids": ["673d5fe2d2451e67c4d09483"],
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

public class ConfirmRequest {
    public static void main(String[] args) throws Exception {
        String body = """
            {
                "order_ids": [
                    "673d5fe2d2451e67c4d09483"
                ]
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v0/fast-charge-order-confirmation"))
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
			"673d5fe2d2451e67c4d09483"
		]
	}`)

	url := "https://api.tronsave.io/v0/fast-charge-order-confirmation"
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
        "order_ids": ["673d5fe2d2451e67c4d09483"]
    });

    let response = client
        .post("https://api.tronsave.io/v0/fast-charge-order-confirmation")
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

* 使用 [取消订单](cancel-order.md) 取消仍处于待处理状态的订单。
* 使用 [获取历史记录](get-history.md) 查看你已完成的租赁。
* 参阅 [身份验证](../../authentication.md) 以设置你的 API 密钥和内部账户。
* 参阅 [错误与速率限制](../../errors-and-rate-limits.md) 了解如何处理错误响应。
