---
description: 为现有的 TronSave 订单提交续期请求——使用你的 API 密钥（内部账户）或签名交易进行支付——并获取 orderId。
---

# 提交续期请求

为你在 [步骤 1：获取可续期的代理](get-extendable-delegates.md) 中选择的代理（委托）进行续期。支持两种支付方式：

* **方式 1 — API 密钥：** 使用 TronSave API 密钥在预先充值的 [内部账户](../../authentication.md) 上授权续期；无需对每笔订单进行链上签名。
* **方式 2 — 签名交易：** 使用你自己的私钥对续期进行签名，并在链上完成结算。

两种方式使用同一个端点，成功时都会返回一个 `orderId`。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/extend-request`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

## 方式 1：使用 API 密钥续期

### 请求头

<table>
<thead>
<tr><th width="140">名称</th><th width="110">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户绑定的 TronSave API 密钥。请参阅 <a href="../../authentication.md">身份验证</a> 以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

### 请求体

<table>
<thead>
<tr><th width="150">字段</th><th width="120">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>receiver</code><mark style="color:red;">*</mark></td><td>String</td><td>接收资源的接收地址。</td></tr>
<tr><td><code>extendData</code><mark style="color:red;">*</mark></td><td>Array</td><td>续期数据数组。使用预估 API 的响应——请参阅 <a href="step-1-get-extendable-delegates.md">获取可续期的代理</a>。</td></tr>
<tr><td><code>resourceType</code></td><td>String</td><td><code>"ENERGY"</code> 或 <code>"BANDWIDTH"</code>。默认值：<code>"ENERGY"</code>。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

### 请求体示例

```json
{
    "extendData": [
        {
            "delegator": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSSS",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1746702000
        },
        {
            "delegator": "TFwUFWr3QV376677Z8VWXxGUAMFFFFFFFF",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1746702000
        }
    ],
    "receiver": "TFwUFWr3QV376677Z8VWXxGUAMF1111111",
    "resourceType": "BANDWIDTH"
}
```

### 响应

{% tabs %}
{% tab title="201: 成功" %}
成功时返回订单 ID。

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "orderId": "6819da2d4d1b2aadb0d44eee"
    }
}
```
{% endtab %}

{% tab title="400: 请求错误" %}
```json
{
    "MISSING_PARAMS": "Missing some params in body",
    "INVALID_PARAMS": "Some params are invalid",
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account does not exist",
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough",
    "SOME_DELEGATE_CANNOT_EXTEND": "This delegate order can't be extended due to some errors. Please try again later."
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

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/extend-request" \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "extendData": [
      {
        "delegator": "YOUR_TRON_ADDRESS",
        "isExtend": true,
        "extraAmount": 0,
        "extendTo": 1746702000
      }
    ],
    "receiver": "YOUR_TRON_ADDRESS",
    "resourceType": "BANDWIDTH"
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const sendExtendRequest = async () => {
  const url = "https://api.tronsave.io/v2/extend-request";

  // extendData comes from the Get extendable delegates response.
  const extendData = [
    {
      delegator: "YOUR_TRON_ADDRESS",
      isExtend: true,
      extraAmount: 0,
      extendTo: 1746702000,
    },
  ];

  const body = {
    extendData,
    receiver: "YOUR_TRON_ADDRESS",
    resourceType: "BANDWIDTH",
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
  //   "data": { "orderId": "6819da2d4d1b2aadb0d44eee" }
  // }
  return response;
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v2/extend-request"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
# extend_data comes from the Get extendable delegates response.
extend_data = [
    {
        "delegator": "YOUR_TRON_ADDRESS",
        "isExtend": True,
        "extraAmount": 0,
        "extendTo": 1746702000,
    },
]
body = {
    "extendData": extend_data,
    "receiver": "YOUR_TRON_ADDRESS",
    "resourceType": "BANDWIDTH",
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

public class ExtendRequest {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v2/extend-request";
        String body = """
            {
              "extendData": [
                {
                  "delegator": "YOUR_TRON_ADDRESS",
                  "isExtend": true,
                  "extraAmount": 0,
                  "extendTo": 1746702000
                }
              ],
              "receiver": "YOUR_TRON_ADDRESS",
              "resourceType": "BANDWIDTH"
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
	url := "https://api.tronsave.io/v2/extend-request"
	body := []byte(`{
		"extendData": [
			{
				"delegator": "YOUR_TRON_ADDRESS",
				"isExtend": true,
				"extraAmount": 0,
				"extendTo": 1746702000
			}
		],
		"receiver": "YOUR_TRON_ADDRESS",
		"resourceType": "BANDWIDTH"
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
    let url = "https://api.tronsave.io/v2/extend-request";
    let body = json!({
        "extendData": [
            {
                "delegator": "YOUR_TRON_ADDRESS",
                "isExtend": true,
                "extraAmount": 0,
                "extendTo": 1746702000_i64
            }
        ],
        "receiver": "YOUR_TRON_ADDRESS",
        "resourceType": "BANDWIDTH"
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

## 方式 2：使用签名交易续期

此方式不需要 API 密钥。你需要使用自己的私钥构建并签名支付交易，并将其作为 `signedTx` 包含在请求中。

### 请求体

<table>
<thead>
<tr><th width="150">字段</th><th width="170">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>receiver</code><mark style="color:red;">*</mark></td><td>String</td><td>接收资源的接收地址。</td></tr>
<tr><td><code>extendData</code><mark style="color:red;">*</mark></td><td>Array</td><td>续期数据数组。使用预估 API 的响应——请参阅 <a href="step-1-get-extendable-delegates.md">获取可续期的代理</a>。</td></tr>
<tr><td><code>resourceType</code></td><td>String</td><td><code>"ENERGY"</code> 或 <code>"BANDWIDTH"</code>。默认值：<code>"ENERGY"</code>。</td></tr>
<tr><td><code>signedTx</code></td><td>SignedTransaction</td><td>签名交易，以 JSON 对象形式提供（来自 <a href="../buy-resources/signed-tx/get-signed-transaction.md">获取签名交易</a> 的 <code>signedTx</code>）。</td></tr>
</tbody>
</table>

<sub>* 必填。</sub>

{% hint style="info" %}
* 要创建签名交易，请参阅 [获取签名交易](../buy-resources/signed-tx/get-signed-transaction.md)。
* 签名所需的 TRX 数量在 [获取可续期的代理](get-extendable-delegates.md) API 响应的 `total_estimate_trx` 字段中提供。
{% endhint %}

### 请求体示例

```json
{
    "extendData": [
        {
            "delegator": "TGGVrYaT8XoosBEXPp6dmSZkoh11223344",
            "isExtend": true,
            "extraAmount": 0,
            "extendTo": 1746403201
        }
    ],
    "receiver": "TGGVrYaT8XoosBEXPp6dmSZkoh123456",
    "resourceType": "BANDWIDTH",
    "signedTx": {
        "visible": false,
        "txID": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
        "raw_data": {
            "contract": [
                {
                    "parameter": {
                        "value": {
                            "amount": 5500000,
                            "owner_address": "417a0d868d1418c9038584af1252f85d486502eec0",
                            "to_address": "41055756f33f419278d9ea059bd2b21120e6add748"
                        },
                        "type_url": "type.googleapis.com/protocol.TransferContract"
                    },
                    "type": "TransferContract"
                }
            ],
            "ref_block_bytes": "0713",
            "ref_block_hash": "6c5f7686f4176139",
            "expiration": 1691465106000,
            "timestamp": 1691465046758
        },
        "raw_data_hex": "0a02071322086c5f7686f417613940d084b5999d315a68080112640a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412330a15417a0d868d1418c9038584af1252f85d486502eec0121541055756f33f419278d9ea059bd2b21120e6add74818e0fcb70670e6b5b1999d31",
        "signature": ["xxxxxxxxx"]
    }
}
```

### 响应

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
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account does not exist",
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough",
    "SOME_DELEGATE_CANNOT_EXTEND": "This delegate order can't be extended due to some errors. Please try again later."
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

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/extend-request" \
  -H "Content-Type: application/json" \
  -d '{
    "extendData": [
      {
        "delegator": "YOUR_TRON_ADDRESS",
        "isExtend": true,
        "extraAmount": 0,
        "extendTo": 1746403201
      }
    ],
    "receiver": "YOUR_TRON_ADDRESS",
    "resourceType": "BANDWIDTH",
    "signedTx": { "...": "signed transaction object" }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const sendExtendRequest = async (extendData, signedTx) => {
  const url = "https://api.tronsave.io/v2/extend-request";

  // extendData comes from Get extendable delegates.
  // signedTx is built from your private key — see Get signed transaction.
  const body = {
    extendData,
    receiver: "YOUR_TRON_ADDRESS",
    resourceType: "BANDWIDTH",
    signedTx,
  };

  const res = await fetch(url, {
    method: "POST",
    headers: {
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

url = "https://api.tronsave.io/v2/extend-request"
headers = {
    "Content-Type": "application/json",
}
# extend_data comes from Get extendable delegates.
# signed_tx is built from your private key — see Get signed transaction.
body = {
    "extendData": extend_data,
    "receiver": "YOUR_TRON_ADDRESS",
    "resourceType": "BANDWIDTH",
    "signedTx": signed_tx,
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

public class ExtendRequestSigned {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v2/extend-request";
        // extendData comes from Get extendable delegates.
        // signedTx is built from your private key — see Get signed transaction.
        String body = """
            {
              "extendData": [
                {
                  "delegator": "YOUR_TRON_ADDRESS",
                  "isExtend": true,
                  "extraAmount": 0,
                  "extendTo": 1746403201
                }
              ],
              "receiver": "YOUR_TRON_ADDRESS",
              "resourceType": "BANDWIDTH",
              "signedTx": { }
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
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
	url := "https://api.tronsave.io/v2/extend-request"
	// extendData comes from Get extendable delegates.
	// signedTx is built from your private key — see Get signed transaction.
	body := []byte(`{
		"extendData": [
			{
				"delegator": "YOUR_TRON_ADDRESS",
				"isExtend": true,
				"extraAmount": 0,
				"extendTo": 1746403201
			}
		],
		"receiver": "YOUR_TRON_ADDRESS",
		"resourceType": "BANDWIDTH",
		"signedTx": {}
	}`)

	req, _ := http.NewRequest("POST", url, bytes.NewBuffer(body))
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
    let url = "https://api.tronsave.io/v2/extend-request";
    // extendData comes from Get extendable delegates.
    // signed_tx is built from your private key — see Get signed transaction.
    let body = json!({
        "extendData": [
            {
                "delegator": "YOUR_TRON_ADDRESS",
                "isExtend": true,
                "extraAmount": 0,
                "extendTo": 1746403201_i64
            }
        ],
        "receiver": "YOUR_TRON_ADDRESS",
        "resourceType": "BANDWIDTH",
        "signedTx": {}
    });

    let client = reqwest::blocking::Client::new();
    let response = client
        .post(url)
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
要与 **TRON Nile 测试网** 集成，请将基础 URL 替换为 `https://api-dev.tronsave.io`。
{% endhint %}

## 后续步骤

* 通过 [获取可续期的代理](get-extendable-delegates.md) 开始流程。
* 使用 [获取签名交易](../buy-resources/signed-tx/get-signed-transaction.md) 构建签名交易。
* 在调用 API 之前，先设置 [身份验证](../../authentication.md)。
