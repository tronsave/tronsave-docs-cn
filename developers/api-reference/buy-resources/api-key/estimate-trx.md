---
description: 使用你的 TronSave API 密钥，预估在选定的租赁时长内购买给定数量能量或带宽所需的 TRX。
---

# 预估 TRX

使用此端点可以在下单前计算一笔订单的成本。传入资源数量、租赁时长以及单价（或速度档位），TronSave 会返回解析后的单价、预估的 TRX，以及当前可用的请求资源数量。

## 端点

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/order-book/estimate-buy-resource`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="140">名称</th><th width="100">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户绑定的 TronSave API 密钥。<a href="README.md">获取你的 API 密钥</a>。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

## 请求体

<table>
<thead>
<tr><th width="320">字段</th><th width="130">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>resourceAmount</code><mark style="color:red;">*</mark></td><td>Number</td><td>资源数量。</td></tr>
<tr><td><code>unitPrice</code></td><td>String, Number</td><td>
<p><strong>"FAST"、"MEDIUM"、"SLOW" 或数字：</strong></p>
<p>- <strong>"FAST"</strong>：如果市场可成交量 = 100%，则 FAST = MEDIUM。如果市场可成交量 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交量 = 0%，则 FAST = SLOW + 20。</p>
<p>- <strong>"MEDIUM"</strong>：使该订单达到最大市场成交量的最低价格。如果市场可成交量 = 0%，则 MEDIUM = SLOW + 10。</p>
<p>- <strong>"SLOW"</strong>：该订单可设置的最低价格。</p>
<p>- 如果价格为数字，则价格单位等于 SUN。</p>
</td></tr>
<tr><td><code>durationSec</code></td><td>Number</td><td>所购资源的时长，单位为秒。默认 3 天。</td></tr>
<tr><td><code>requester</code></td><td>String</td><td>请求方的地址。</td></tr>
<tr><td><code>receiver</code></td><td>String</td><td>资源接收地址。</td></tr>
<tr><td><code>resourceType</code></td><td>String</td><td>"ENERGY" 或 "BANDWIDTH"。默认："ENERGY"。</td></tr>
<tr><td><code>options</code></td><td>Object</td><td>可选。</td></tr>
<tr><td><code>options.allowPartialFill</code></td><td>Boolean</td><td>是否允许订单被部分成交。</td></tr>
<tr><td><code>options.minResourceDelegateRequiredAmount</code></td><td>Number</td><td>单个供应商代理（委托）的最小资源数量。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

### 请求体示例

```json
{
    "resourceType": "ENERGY",
    "receiver": "TFwUFWr3QV376677Z8VWXxGUAMF123456",
    "durationSec": 259200,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
        "allowPartialFill": true,
        "minResourceDelegateRequiredAmount": 32000
    }
}
```

## 响应

### 成功

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "unitPrice": 64,
        "durationSec": 259200,
        "estimateTrx": 6144000,
        "availableResource": 32000
    }
}
```

`estimateTrx` 的值以 SUN 返回（1 TRX = 1,000,000 SUN）。

### 错误

{% tabs %}
{% tab title="401 缺少 API 密钥" %}
当缺少 `apikey` 请求头时返回。

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```
{% endtab %}

{% tab title="401 无效的 API 密钥" %}
当提供了 `apikey` 请求头但无效时返回。

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```
{% endtab %}

{% tab title="400 校验错误" %}
当必填字段缺失或无效时返回。`message` 会指明出错的字段。

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'resourceAmount'"
}
```
{% endtab %}
{% endtabs %}

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/order-book/estimate-buy-resource" \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "resourceType": "ENERGY",
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 259200,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
      "allowPartialFill": true,
      "minResourceDelegateRequiredAmount": 32000
    }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v2/order-book/estimate-buy-resource",
  {
    method: "POST",
    headers: {
      "apikey": "YOUR_API_KEY",
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      resourceType: "ENERGY",
      receiver: "YOUR_TRON_ADDRESS",
      durationSec: 259200,
      resourceAmount: 32000,
      unitPrice: "MEDIUM",
      options: {
        allowPartialFill: true,
        minResourceDelegateRequiredAmount: 32000,
      },
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

url = "https://api.tronsave.io/v2/order-book/estimate-buy-resource"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
payload = {
    "resourceType": "ENERGY",
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 259200,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
        "allowPartialFill": True,
        "minResourceDelegateRequiredAmount": 32000,
    },
}

res = requests.post(url, headers=headers, json=payload)
print(res.json())
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
              "resourceType": "ENERGY",
              "receiver": "YOUR_TRON_ADDRESS",
              "durationSec": 259200,
              "resourceAmount": 32000,
              "unitPrice": "MEDIUM",
              "options": {
                "allowPartialFill": true,
                "minResourceDelegateRequiredAmount": 32000
              }
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://api.tronsave.io/v2/order-book/estimate-buy-resource"))
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
	url := "https://api.tronsave.io/v2/order-book/estimate-buy-resource"
	payload := []byte(`{
		"resourceType": "ENERGY",
		"receiver": "YOUR_TRON_ADDRESS",
		"durationSec": 259200,
		"resourceAmount": 32000,
		"unitPrice": "MEDIUM",
		"options": {
			"allowPartialFill": true,
			"minResourceDelegateRequiredAmount": 32000
		}
	}`)

	req, _ := http.NewRequest("POST", url, bytes.NewBuffer(payload))
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("Content-Type", "application/json")

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
use reqwest::blocking::Client;
use serde_json::json;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();
    let payload = json!({
        "resourceType": "ENERGY",
        "receiver": "YOUR_TRON_ADDRESS",
        "durationSec": 259200,
        "resourceAmount": 32000,
        "unitPrice": "MEDIUM",
        "options": {
            "allowPartialFill": true,
            "minResourceDelegateRequiredAmount": 32000
        }
    });

    let res = client
        .post("https://api.tronsave.io/v2/order-book/estimate-buy-resource")
        .header("apikey", "YOUR_API_KEY")
        .header("Content-Type", "application/json")
        .json(&payload)
        .send()?;

    println!("{}", res.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [购买能量（创建订单）](create-order.md) — 在获得预估后下单。
* [获取订单簿](get-order-book.md) — 查看当前价格与可用量。
* [身份验证](../../../authentication.md) — 如何在每次请求中传递你的 API 密钥。
