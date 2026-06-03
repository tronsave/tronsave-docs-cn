---
description: 在单个请求中创建一个或多个快速充电（Fast Charge）订单，为一组接收地址租赁能量，使用你的 TronSave API 密钥进行身份验证。
---

# 创建快速充电订单

提交一个快速充电订单，指定可接受的最高价格、租赁时长以及应接收能量的地址列表。该接口会返回所创建订单的 ID，以便你可以追踪并在稍后确认它们。

{% hint style="info" %}
快速充电是一项基于 API 密钥的功能。每次调用都会针对一个已预先充值的 TronSave 内部账户进行身份验证。关于如何获取密钥并为账户充值，请参阅 [身份验证](../../authentication.md)。
{% endhint %}

## 接口

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/fast-charge-order-request`**

{% hint style="info" %}
**速率限制：** 每 2 秒 1 次请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="131">名称</th><th width="135">类型</th><th>说明</th></tr></thead><tbody><tr><td>apikey<mark style="color:red;">*</mark></td><td>String</td><td>属于你内部账户的 TronSave API 密钥。请参阅 <a href="../../authentication.md">身份验证</a>。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="190">名称</th><th width="137">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>amount</code></td><td>Number</td><td>想要购买的资源数量。</td></tr><tr><td><code>duration_sec</code></td><td>Number</td><td>租赁资源的时长，时间单位为秒。</td></tr><tr><td><code>max_price_accept</code></td><td>Number</td><td>仅当预估价格低于此值时才创建订单。</td></tr><tr><td><code>receivers</code></td><td>Array</td><td>接收方数据数组。接收能量的钱包地址列表。</td></tr><tr><td><code>deadline</code></td><td>Number</td><td>最长匹配时间，单位为 <strong>秒</strong>。超过该时间将取消订单并退款。</td></tr></tbody></table>

### 请求体示例

```json
{
    "max_price_accept": 70,
    "amount": 130000,
    "duration_sec": 900,
    "deadline": 300,
    "receivers": [
        "TQk2eKHE9ZfCdVmyPnh8DfRMUF0123456",
        "TQk2eKHE9ZfCdVmyPnh8DfRMUF1111111"
    ]
}
```

## 响应

### 成功

返回订单 ID 数组。

```json
{
    "order_ids": [
        "673c17a3129f1881382e98e2",
        "673c17a3129f1881382e98e3"
    ]
}
```

### 错误

**`401 Unauthorized`** — 缺少 `apikey` 请求头：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED"
}
```

**`401 Unauthorized`** — 提供的 API 密钥无效：

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY"
}
```

**`400 Bad Request`** — 缺少必填字段或字段无效。消息中会指明出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'receivers'"
}
```

该接口还可能返回业务逻辑相关的 `400` 错误（例如当预先充值的内部账户余额不足以支付该订单时）。<!-- [NEEDS CONFIRMATION: exact business-logic 400 message bodies for insufficient balance / unfulfillable orders on this endpoint] -->

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v0/fast-charge-order-request" \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "max_price_accept": 70,
    "amount": 130000,
    "duration_sec": 900,
    "deadline": 300,
    "receivers": [
        "YOUR_TRON_ADDRESS",
        "YOUR_TRON_ADDRESS"
    ]
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v0/fast-charge-order-request",
  {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      max_price_accept: 70,
      amount: 130000,
      duration_sec: 900,
      deadline: 300,
      receivers: ["YOUR_TRON_ADDRESS", "YOUR_TRON_ADDRESS"],
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

url = "https://api.tronsave.io/v0/fast-charge-order-request"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "max_price_accept": 70,
    "amount": 130000,
    "duration_sec": 900,
    "deadline": 300,
    "receivers": ["YOUR_TRON_ADDRESS", "YOUR_TRON_ADDRESS"],
}

res = requests.post(url, headers=headers, json=body)
print(res.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class CreateFastChargeOrder {
    public static void main(String[] args) throws Exception {
        String body = """
            {
                "max_price_accept": 70,
                "amount": 130000,
                "duration_sec": 900,
                "deadline": 300,
                "receivers": [
                    "YOUR_TRON_ADDRESS",
                    "YOUR_TRON_ADDRESS"
                ]
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v0/fast-charge-order-request"))
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
		"max_price_accept": 70,
		"amount": 130000,
		"duration_sec": 900,
		"deadline": 300,
		"receivers": [
			"YOUR_TRON_ADDRESS",
			"YOUR_TRON_ADDRESS"
		]
	}`)

	req, _ := http.NewRequest(
		"POST",
		"https://api.tronsave.io/v0/fast-charge-order-request",
		bytes.NewBuffer(body),
	)
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("Content-Type", "application/json")

	res, err := http.DefaultClient.Do(req)
	if err != nil {
		panic(err)
	}
	defer res.Body.Close()

	out, _ := io.ReadAll(res.Body)
	fmt.Println(string(out))
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use serde_json::json;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let body = json!({
        "max_price_accept": 70,
        "amount": 130000,
        "duration_sec": 900,
        "deadline": 300,
        "receivers": ["YOUR_TRON_ADDRESS", "YOUR_TRON_ADDRESS"]
    });

    let client = reqwest::blocking::Client::new();
    let res = client
        .post("https://api.tronsave.io/v0/fast-charge-order-request")
        .header("apikey", "YOUR_API_KEY")
        .header("Content-Type", "application/json")
        .json(&body)
        .send()?;

    println!("{}", res.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* 追踪每个订单的匹配状态：[追踪快速充电订单](track-order.md)。
* 确认已完成的订单以收回未使用的能量并退还差额：[确认请求](confirm-request.md)。
* 还没有密钥？请参阅 [身份验证](../../authentication.md)。
