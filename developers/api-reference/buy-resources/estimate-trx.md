---
description: 预估为所选租赁时长购买指定数量的能量或带宽所需的 TRX 数量。
---

# 预估 TRX

在下单之前，预估购买能量或带宽资源所需的 TRX 数量。该端点会返回解析出的单价、时长、预估的 TRX 成本，以及当前可用于成交的请求资源数量。

## 端点

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/order-book/estimate-buy-resource`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="106">名称</th><th width="94">类型</th><th>说明</th></tr></thead><tbody><tr><td>apikey<mark style="color:red;">*</mark></td><td>String</td><td>属于你内部账户的 TronSave API 密钥。参见 <a href="../../authentication.md">身份验证</a>。</td></tr></tbody></table>

## 请求体

<table><thead><tr><th width="208.33333333333331">字段</th><th width="95">类型</th><th>说明</th></tr></thead><tbody><tr><td>resourceAmount<mark style="color:red;">*</mark></td><td>Number</td><td>资源数量。</td></tr><tr><td>unitPrice</td><td>String, Number</td><td><p><strong>"FAST"、"MEDIUM"、"SLOW" 或数字</strong>：</p><p><br>-"FAST"：如果市场可成交 = 100%，则 FAST = MEDIUM。如果市场可成交 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交 = 0%，则 FAST = SLOW + 20。</p><p></p><p>-"MEDIUM"：此订单达到最大市场成交量的最低价格。如果市场可成交 = 0%，则 MEDIUM = SLOW + 10。</p><p></p><p>-"SLOW"：此订单可设置的最低价格。</p><p></p><p>-如果价格为数字，则价格单位等于 SUN</p></td></tr><tr><td>durationSec</td><td>Number</td><td>所购资源的时长，时间单位为秒。默认 3d</td></tr><tr><td>requester</td><td>String</td><td>请求方的地址。</td></tr><tr><td>receiver</td><td>String</td><td>资源接收地址。</td></tr><tr><td>resourceType</td><td>String</td><td>"ENERGY" 或 "BANDWIDTH"，默认："ENERGY"</td></tr><tr><td>options</td><td>Object</td><td>可选</td></tr><tr><td>options.allowPartialFill</td><td>Boolean</td><td>是否允许订单部分成交</td></tr><tr><td>options.minResourceDelegateRequiredAmount</td><td>Number</td><td>单个供应商代理(委托)的最小资源数量。</td></tr></tbody></table>

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

`estimateTrx` 值以 SUN 为单位返回（1 TRX = 1,000,000 SUN）。

### 错误

**`401 Unauthorized`** — 缺少 `apikey` 请求头。

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

**`401 Unauthorized`** — 提供的 API 密钥无效。

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```

**`400 Bad Request`** — 缺少必填字段或字段无效。`message` 会指出出错的字段。

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'resourceAmount'"
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST 'https://api.tronsave.io/v2/order-book/estimate-buy-resource' \
  -H 'apikey: YOUR_API_KEY' \
  -H 'Content-Type: application/json' \
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
      apikey: "YOUR_API_KEY",
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
body = {
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
	body := []byte(`{
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

	req, _ := http.NewRequest(
		"POST",
		"https://api.tronsave.io/v2/order-book/estimate-buy-resource",
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

    let client = reqwest::blocking::Client::new();
    let res = client
        .post("https://api.tronsave.io/v2/order-book/estimate-buy-resource")
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

* 当你对预估结果满意后即可下单：[购买能量（创建订单）](api-key/create-order.md)。
* 在 [获取订单簿](api-key/get-order-book.md) 端点中查看可用的定价档位。
* 了解 [能量与带宽](../../../concepts/energy-and-bandwidth.md) 之间的区别。
