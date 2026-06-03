---
description: 使用 API 密钥创建能量或带宽购买订单，从你的 TronSave 内部账户付款，并获得用于追踪订单状态的 orderId。
---

# 购买能量（创建订单）

使用 API 密钥创建能量或带宽购买订单。订单从你预先充值的[内部账户](../../../authentication.md)中付款，因此无需对每笔订单进行链上签名。成功时，该接口会返回一个 `orderId`，你可以用它来追踪订单状态。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/buy-resource`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="140">名称</th><th width="110">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户绑定的 TronSave API 密钥。请参阅<a href="../../../authentication.md">身份验证</a>以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

## 请求体

<table>
<thead>
<tr><th width="290">字段</th><th width="140">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>resourceType</code></td><td>String</td><td><code>"ENERGY"</code> 或 <code>"BANDWIDTH"</code>。默认值：<code>ENERGY</code>。</td></tr>
<tr><td><code>unitPrice</code></td><td>Number, String</td><td><p><code>"FAST"</code>、<code>"MEDIUM"</code>、<code>"SLOW"</code> 或一个数字。<strong>默认值：<code>"MEDIUM"</code></strong>。</p><p><br>- <strong>FAST</strong>：如果市场可成交量 = 100%，则 FAST = MEDIUM。如果市场可成交量 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交量 = 0%，则 FAST = SLOW + 20。</p><p>- <strong>MEDIUM</strong>：使该订单获得最大市场成交量的最低价格。如果市场可成交量 = 0%，则 MEDIUM = SLOW + 10。</p><p>- <strong>SLOW</strong>：该订单可设置的最低价格。</p><p>- 如果价格为数字，则价格单位为 SUN。</p></td></tr>
<tr><td><code>resourceAmount</code><mark style="color:red;">*</mark></td><td>Number</td><td>资源数量。</td></tr>
<tr><td><code>receiver</code><mark style="color:red;">*</mark></td><td>String</td><td>资源接收地址。</td></tr>
<tr><td><code>durationSec</code></td><td>Number</td><td>所购资源的时长，单位为秒。<strong>默认值：</strong>259200（3 天）。</td></tr>
<tr><td><code>sponsor</code></td><td>String</td><td>赞助码。</td></tr>
<tr><td><code>options</code></td><td>Object</td><td>可选。</td></tr>
<tr><td><code>options.allowPartialFill</code></td><td>Boolean</td><td>是否允许订单被部分成交。</td></tr>
<tr><td><code>options.onlyCreateWhenFulfilled</code></td><td>Boolean</td><td><p><code>true</code> => 仅当订单可被完全成交时才创建。</p><p><code>false</code> => 即使订单无法被成交也会创建。</p><p>默认值：<code>false</code>。</p></td></tr>
<tr><td><code>options.maxPriceAccepted</code></td><td>Number</td><td>仅当预估价格低于该值时才创建订单。</td></tr>
<tr><td><code>options.preventDuplicateIncompleteOrders</code></td><td>Boolean</td><td><p><code>true</code> => 仅当不存在具有相同参数的<strong>未完成订单</strong>时才创建。</p><p><code>false</code> => 始终创建新订单，无论是否存在未完成的订单。</p><p>默认值：<strong><code>false</code></strong>。</p></td></tr>
<tr><td><code>options.minResourceDelegateRequiredAmount</code></td><td>Number</td><td>单个供应商代理（委托）的最小资源数量。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

### 请求体示例

```json
{
    "resourceType": "ENERGY",
    "receiver": "TFFbwz3UpmgaPT4UudwsxbiJf63t777777",
    "durationSec": 3600,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
        "allowPartialFill": true,
        "onlyCreateWhenFulfilled": true,
        "preventDuplicateIncompleteOrders": false,
        "maxPriceAccepted": 100,
        "minResourceDelegateRequiredAmount": 100000
    }
}
```

## 响应

{% tabs %}
{% tab title="201: 成功" %}
成功时返回订单 ID。

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "orderId": "6818426a65fa8ea36d119d2c"
    }
}
```
{% endtab %}

{% tab title="400: 请求错误" %}
```json
{
    "MISSING_PARAMS": "Missing some params in body",
    "INVALID_PARAMS": "Some params are invalid",
    "MIN_PRICE_INVALID": "minPrice is less than the system's minimum price or not in the correct format.",
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account does not exist",
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough",
    "CANNOT_FULFILLED": "The order requires an immediate full match, but the system cannot fulfill 100% of it.",
    "MUST_BE_WAIT_PREVIOUS_ORDER_FILLED": "A pending order with the same parameters already exists in the system.",
    "PRICE_EXCEED_MAX_PRICE_REQUIRED": "The price of the order exceeds the maximum price accepted."
}
```
{% endtab %}

{% tab title="401: 未授权" %}
```json
{
    "API_KEY_REQUIRED": "Missing api key in headers",
    "INVALID_API_KEY": "api key not correct"
}
```
{% endtab %}

{% tab title="429: 请求过多" %}
```json
{
    "RATE_LIMIT": "Rate limit reached"
}
```
{% endtab %}
{% endtabs %}

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/buy-resource" \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "resourceType": "ENERGY",
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 3600,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
      "allowPartialFill": true,
      "onlyCreateWhenFulfilled": false,
      "maxPriceAccepted": 100
    }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const buyResource = async () => {
  const url = "https://api.tronsave.io/v2/buy-resource";
  const body = {
    resourceType: "ENERGY",
    unitPrice: "MEDIUM", // price in SUN, or "SLOW" | "MEDIUM" | "FAST"
    resourceAmount: 32000, // amount of resource to buy
    receiver: "YOUR_TRON_ADDRESS",
    durationSec: 3600, // order duration in seconds. Default: 259200 (3 days)
    options: {
      allowPartialFill: true,
      onlyCreateWhenFulfilled: false,
      maxPriceAccepted: 100,
    },
  };

  const res = await fetch(url, {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "content-type": "application/json",
    },
    body: JSON.stringify(body),
  });

  const response = await res.json();
  // {
  //   "error": false,
  //   "message": "Success",
  //   "data": { "orderId": "6818426a65fa8ea36d119d2c" }
  // }
  return response;
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v2/buy-resource"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "resourceType": "ENERGY",
    "unitPrice": "MEDIUM",  # price in SUN, or "SLOW" | "MEDIUM" | "FAST"
    "resourceAmount": 32000,
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 3600,
    "options": {
        "allowPartialFill": True,
        "onlyCreateWhenFulfilled": False,
        "maxPriceAccepted": 100,
    },
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

public class CreateOrder {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v2/buy-resource";
        String body = """
            {
              "resourceType": "ENERGY",
              "unitPrice": "MEDIUM",
              "resourceAmount": 32000,
              "receiver": "YOUR_TRON_ADDRESS",
              "durationSec": 3600,
              "options": {
                "allowPartialFill": true,
                "onlyCreateWhenFulfilled": false,
                "maxPriceAccepted": 100
              }
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
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
	url := "https://api.tronsave.io/v2/buy-resource"
	body := []byte(`{
		"resourceType": "ENERGY",
		"unitPrice": "MEDIUM",
		"resourceAmount": 32000,
		"receiver": "YOUR_TRON_ADDRESS",
		"durationSec": 3600,
		"options": {
			"allowPartialFill": true,
			"onlyCreateWhenFulfilled": false,
			"maxPriceAccepted": 100
		}
	}`)

	req, _ := http.NewRequest("POST", url, bytes.NewBuffer(body))
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("Content-Type", "application/json")

	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	out, _ := io.ReadAll(resp.Body)
	fmt.Println(string(out))
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use serde_json::json;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let url = "https://api.tronsave.io/v2/buy-resource";
    let body = json!({
        "resourceType": "ENERGY",
        "unitPrice": "MEDIUM",
        "resourceAmount": 32000,
        "receiver": "YOUR_TRON_ADDRESS",
        "durationSec": 3600,
        "options": {
            "allowPartialFill": true,
            "onlyCreateWhenFulfilled": false,
            "maxPriceAccepted": 100
        }
    });

    let client = reqwest::blocking::Client::new();
    let response = client
        .post(url)
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

{% hint style="info" %}
若要与 **TRON Nile（测试网）** 集成，请将基础 URL 替换为 `https://api-dev.tronsave.io`。
{% endhint %}

## 后续步骤

* 使用[获取订单详情](get-order-details.md)追踪订单。
* 先使用[预估 TRX](estimate-trx.md) 预览费用。
* 在下单前查看[订单类型](../../../../concepts/order-types.md)。
