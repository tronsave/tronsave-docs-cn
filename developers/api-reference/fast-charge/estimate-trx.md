---
description: 在创建快速充值订单之前，使用你的 API 密钥和已预充值的内部账户，预估创建订单所需的 TRX。
---

# 预估 TRX

在下单之前预估创建一个或多个快速充值订单所需的 TRX 总额。该接口会解析单价、报告系统当前能够匹配的订单数量，并返回你当前的内部账户余额。

调用此接口需要一个绑定了已预充值内部账户的 TronSave API 密钥。请参阅 [身份验证](../../authentication.md)，了解如何获取和使用 API 密钥。

## 接口

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/fast-charge-estimate-order-request`**

{% hint style="info" %}
**速率限制：** 每 2 秒 1 次请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="131">名称</th><th width="135">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与内部账户关联的 TronSave API 密钥。必填。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="190">名称</th><th width="164">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>receiver_count</code></td><td>String</td><td>要创建的订单数量。</td></tr><tr><td><code>amount</code></td><td>Number</td><td>你想要购买的资源数量。</td></tr><tr><td><code>max_price_accept</code></td><td>Number</td><td>仅当预估价格低于此值时才创建订单。</td></tr><tr><td><code>resource_type</code></td><td>String</td><td><code>"ENERGY"</code></td></tr><tr><td><code>duration_sec</code></td><td>Number</td><td>资源租赁的时长，单位为秒。</td></tr></tbody></table>

### 请求体示例

```json
{
    "receiver_count": 10,
    "amount": 130000,
    "max_price_accept": 70,
    "resource_type": "ENERGY",
    "duration_sec": 900
}
```

## 响应

### 200 OK — 成功

```json
{
    "charge_amount": number,                // Total payout for all orders
    "price": number,                        // Price, unit is SUN
    "max_receiver_can_provide": number,     // The maximum number of orders the system is ready to match
    "internal_balance": string,             // Balance of your internal account
    "duration_sec": number                  // Rent duration, unit is seconds
}
```

成功响应示例：

```json
{
    "charge_amount": 44850000,
    "price": 69,
    "max_receiver_can_provide": 8,
    "able_to_match": true,
    "internal_balance": "117505470"
}
```

### 400: 请求错误 — 校验失败

当某个必填字段缺失或无效时返回。错误消息会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'receiver_count'"
}
```

该接口还可能返回以下业务逻辑错误：

```json
{
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account not exists",
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough"
}
```

### 401: 未授权 — 缺少或无效的 API 密钥

当缺少 `apikey` 请求头时返回：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED"
}
```

当 `apikey` 请求头存在但无效时返回：

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY"
}
```

### 429: 请求过多 — 达到速率限制

```json
{
    "RATE_LIMIT": "Rate limit reached"
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST 'https://api.tronsave.io/v0/fast-charge-estimate-order-request' \
  -H 'apikey: YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
  -d '{
    "receiver_count": 10,
    "amount": 130000,
    "max_price_accept": 70,
    "resource_type": "ENERGY",
    "duration_sec": 900
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v0/fast-charge-estimate-order-request",
  {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      receiver_count: 10,
      amount: 130000,
      max_price_accept: 70,
      resource_type: "ENERGY",
      duration_sec: 900,
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

url = "https://api.tronsave.io/v0/fast-charge-estimate-order-request"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "receiver_count": 10,
    "amount": 130000,
    "max_price_accept": 70,
    "resource_type": "ENERGY",
    "duration_sec": 900,
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

public class EstimateTrx {
    public static void main(String[] args) throws Exception {
        String body = """
            {
                "receiver_count": 10,
                "amount": 130000,
                "max_price_accept": 70,
                "resource_type": "ENERGY",
                "duration_sec": 900
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v0/fast-charge-estimate-order-request"))
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
		"receiver_count": 10,
		"amount": 130000,
		"max_price_accept": 70,
		"resource_type": "ENERGY",
		"duration_sec": 900
	}`)

	url := "https://api.tronsave.io/v0/fast-charge-estimate-order-request"
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
        "receiver_count": 10,
        "amount": 130000,
        "max_price_accept": 70,
        "resource_type": "ENERGY",
        "duration_sec": 900
    });

    let response = client
        .post("https://api.tronsave.io/v0/fast-charge-estimate-order-request")
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
* 参阅 [错误与速率限制](../../errors-and-rate-limits.md)，了解如何处理上述错误响应。
* 了解 [能量与带宽](../../../concepts/energy-and-bandwidth.md)。
