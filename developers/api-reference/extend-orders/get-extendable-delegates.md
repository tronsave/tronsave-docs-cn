---
description: 查询某个订单，找出哪些代理（委托）符合延期条件、需要花费多少 TRX，以及在第 2 步中传递给延期请求的 extendData 负载。
---

# 获取可延期的代理（委托）

这是 v2 延期流程的第 1 步。给定一个 `receiver` 接收地址和目标 `extendTo` 时间，该端点会返回仍然可以延期的代理（委托）、预估的 TRX 费用，以及一个 `extendData` 数组。使用该 `extendData` 来构建 [提交延期请求](extend-request.md) 的负载。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/get-extendable-delegates`**

{% hint style="info" %}
速率限制：每 **1** 秒 **1** 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="140">名称</th><th width="110">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户绑定的 TronSave API 密钥。参见 <a href="../../authentication.md">身份验证</a> 以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

## 请求体

<table>
<thead>
<tr><th width="200">字段</th><th width="120">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>extendTo</code><mark style="color:red;">*</mark></td><td>String</td><td>你希望延期到的时间（以秒为单位）。</td></tr>
<tr><td><code>receiver</code><mark style="color:red;">*</mark></td><td>String</td><td>接收资源代理（委托）的地址。</td></tr>
<tr><td><code>requester</code></td><td>String</td><td>请求方的地址。如果未提供，则从 <strong>API 密钥</strong> 中获取请求方。</td></tr>
<tr><td><code>resourceType</code></td><td>String</td><td><code>"ENERGY"</code> 或 <code>"BANDWIDTH"</code>。默认值：<code>"ENERGY"</code>。</td></tr>
<tr><td><code>maxPriceAccepted</code></td><td>Number</td><td>你愿意为延期支付的最高价格。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

### 请求体示例

```json
{
    "extendTo": 1728704969000,
    "maxPriceAccepted": 165,
    "receiver": "TFwUFWr3QV376677Z8VWXxGUAMF11111111",
    "resourceType": "ENERGY"
}
```

## 响应

{% tabs %}
{% tab title="200: Success" %}
```javascript
{
    "extendOrderBook": [
        {
            "price": 133,
            "value": 64319
        },
        ...
    ], // Overview of the extendable resource amount at every single price
    "totalDelegateAmount": 64319,
    // Total current delegate of the receiver address in TronSave
    "totalAvailableExtendAmount": 64319,
    // Total available delegates of the receiver address in TronSave
    "totalEstimateTrx": 8554427,
    // Estimated TRX payout if using the extendData below to create the extend request
    "yourBalance": 20000000,
    // API key's internal balance
    "isAbleToExtend": true,
    // Compares internal balance and totalEstimateTrx
    "extendData": [
        {
            "delegator": "TMN2uTdy6rQYaTm4A5g732kHRf72222222",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1728459019
        }
    ]
    // extendData that is used to create the extend request in Step 2
}
```
{% endtab %}

{% tab title="400: 请求错误" %}
```json
{
    "MISSING_PARAMS": "Missing some params in body",
    "INVALID_PARAMS": "Some params are invalid"
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

### 成功响应示例

```json
{
    "extendOrderBook": [
        {
            "price": 108,
            "value": 100000
        },
        {
            "price": 122,
            "value": 200000
        }
    ],
    "totalDelegateAmount": 500000,
    "totalAvailableExtendAmount": 300000,
    "totalEstimateTrx": 24426224,
    "isAbleToExtend": true,
    "yourBalance": 37780396,
    "extendData": [
        {
            "delegator": "TQBV7xU489Rq8ZCsYi72zBhJM44444444",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1728704969000
        },
        {
            "delegator": "TMN2uTdy6rQYaTm4A5g732kHR333333333",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1728704969000
        }
    ]
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/get-extendable-delegates" \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "extendTo": 1728704969000,
    "maxPriceAccepted": 165,
    "receiver": "YOUR_TRON_ADDRESS",
    "resourceType": "ENERGY"
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const getExtendableDelegates = async () => {
  const url = "https://api.tronsave.io/v2/get-extendable-delegates";
  const body = {
    extendTo: 1728704969000, // time in seconds you want to extend to
    receiver: "YOUR_TRON_ADDRESS", // the address that received the resource delegate
    maxPriceAccepted: 165, // optional. Max price you want to pay to extend
    resourceType: "ENERGY", // "ENERGY" or "BANDWIDTH". Optional. Default: "ENERGY"
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
  // response.extendData is used to create the extend request in Step 2
  return response;
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v2/get-extendable-delegates"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "extendTo": 1728704969000,
    "receiver": "YOUR_TRON_ADDRESS",
    "maxPriceAccepted": 165,
    "resourceType": "ENERGY",
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

public class GetExtendableDelegates {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v2/get-extendable-delegates";
        String body = """
            {
              "extendTo": 1728704969000,
              "receiver": "YOUR_TRON_ADDRESS",
              "maxPriceAccepted": 165,
              "resourceType": "ENERGY"
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
	url := "https://api.tronsave.io/v2/get-extendable-delegates"
	body := []byte(`{
		"extendTo": 1728704969000,
		"receiver": "YOUR_TRON_ADDRESS",
		"maxPriceAccepted": 165,
		"resourceType": "ENERGY"
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
    let url = "https://api.tronsave.io/v2/get-extendable-delegates";
    let body = json!({
        "extendTo": 1728704969000_i64,
        "receiver": "YOUR_TRON_ADDRESS",
        "maxPriceAccepted": 165,
        "resourceType": "ENERGY"
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
要与 **TRON Nile（测试网）** 集成，请将基础 URL 替换为 `https://api-dev.tronsave.io`。
{% endhint %}

## 后续步骤

* 将本响应中的 `extendData` 传入 [提交延期请求](extend-request.md) 以完成延期。
* 在调用 API 之前先设置好 [身份验证](../../authentication.md)。
* 查阅 [能量与带宽](../../../concepts/energy-and-bandwidth.md) 和 [订单类型](../../../concepts/order-types.md)。
