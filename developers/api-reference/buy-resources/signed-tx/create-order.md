---
description: 提交一笔已签名的 TRON 交易以创建资源订单 — 签名交易购买流程的第 3 步。
---

# 创建订单

通过提交一笔签名交易来完成资源购买。这是[签名交易流程](README.md)的最后一步：在你[预估所需 TRX](estimate-trx.md) 并[获取签名交易](get-signed-transaction.md)之后，调用此接口来下达订单。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/buy-resource`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

| 请求头 | 值 | 是否必需 |
| --- | --- | --- |
| `Content-Type` | `application/json` | 是 |

{% hint style="info" %}
此接口通过你提交的 `signedTx` 进行身份验证 — TRX 付款由你自己的钱包签名 — 因此不需要 `apikey` 请求头。参见[身份验证](../../../authentication.md)。
{% endhint %}

## 请求体

<table><thead><tr><th width="206">字段</th><th width="130">类型</th><th width="398">说明</th></tr></thead><tbody><tr><td><code>resourceType</code></td><td>String</td><td>"ENERGY" 或 "BANDWIDTH"，默认值：ENERGY</td></tr><tr><td><code>unitPrice</code></td><td>Number</td><td>价格单位等于 SUN。</td></tr><tr><td><code>resourceAmount</code> <mark style="color:red;">*</mark></td><td>Number</td><td>资源数量。</td></tr><tr><td><code>receiver</code> <mark style="color:red;">*</mark></td><td>String</td><td>资源接收地址</td></tr><tr><td><code>durationSec</code></td><td>Number</td><td>所购买资源的时长，时间单位为秒。默认值 259200（3 天）</td></tr><tr><td><code>sponsor</code></td><td>String</td><td>赞助码</td></tr><tr><td><code>signedTx</code></td><td>SignedTransaction</td><td>签名交易，请注意它是一个 JSON 对象（在<a href="get-signed-transaction.md">第 2 步</a>中生成的 <code>signed_tx</code>）</td></tr><tr><td><code>options</code></td><td>Object</td><td>可选</td></tr><tr><td><code>options.allowPartialFill</code></td><td>Boolean</td><td>是否允许订单被部分成交</td></tr><tr><td><code>options.onlyCreateWhenFulfilled</code></td><td>Boolean</td><td><p>[true] =&#x3E; 仅当订单能够被完全成交时才创建</p><p>[false] =&#x3E; 即使订单无法被成交也会创建</p><p>默认值：false</p></td></tr><tr><td><code>options.maxPriceAccepted</code></td><td>Number</td><td>仅当预估价格低于此值时才创建订单。</td></tr><tr><td><code>options.preventDuplicateIncompleteOrders</code></td><td>Boolean</td><td><p>[true] =&#x3E; 仅当不存在参数相同的<strong>未完成订单</strong>时才创建。</p><p>[false] =&#x3E; 始终创建新订单，无论是否存在未完成的订单。</p><p>默认值：<strong>false</strong></p></td></tr><tr><td><code>options.minResourceDelegateRequiredAmount</code></td><td>Number</td><td>单个供应商代理（委托）的最小资源数量。</td></tr></tbody></table>

<mark style="color:red;">*</mark> 必填字段。

### 请求体示例

```json
{
    "resourceType": "ENERGY",
    "receiver": "TFFbwz3UpmgaPT4UudwsxbiJf63t777777",
    "durationSec": 3600,
    "resourceAmount": 32000,
    "unitPrice": 80,
    "options": {
        "allowPartialFill": true,
        "onlyCreateWhenFulfilled": true,
        "preventDuplicateIncompleteOrders": false,
        "maxPriceAccepted": 100,
        "minResourceDelegateRequiredAmount": 100000
    },
    "signedTx": {
        "visible": false,
        "txID": "795f8195893e8da2ef2f70fc3a1f2720f9077244c4b7b1c50c99c72fda675a32",
        "raw_data_hex": "0a02b32e2208ce1cb373c238875e4098fbd9a0eb325a68080112640a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412330a1541417ca6f74356a5d4454498e3f4856c945a04ab01121541055756f33f419278d9ea059bd2b21120e6add74818e0e5a40170b8a6d6a0eb32",
        "raw_data": {
            "contract": [
                {
                    "parameter": {
                        "value": {
                            "to_address": "41055756f33f419278d9ea059bd2b21120e6add748",
                            "owner_address": "41417ca6f74356a5d4454498e3f4856c945a04ab01",
                            "amount": 2700000
                        },
                        "type_url": "type.googleapis.com/protocol.TransferContract"
                    },
                    "type": "TransferContract"
                }
            ],
            "ref_block_bytes": "b32e",
            "ref_block_hash": "ce1cb373c238875e",
            "expiration": 1746778095000,
            "timestamp": 1746778035000
        },
        "signature": [
            "xxxxxxxxxxxxxxxxxxx"
        ]
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
        "orderId": "6818426a65fa8ea36d119d2c"
    }
}
```

### 错误

此接口通过 `signedTx` 进行身份验证，因此**不会**返回 `401`/API 密钥错误。无效或缺失的输入会在创建订单之前以 `400` 模式校验错误被拒绝。

**`400 Bad Request`** — 缺少必填字段或字段无效。`message` 会指明出错的属性（此处为 `receiver`）：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'receiver'"
}
```

订单也可能因业务逻辑错误而失败，此类错误在成功响应外层结构中返回（`error: true`）。例如，当订单无法被成交或账户余额不足时：

```json
{
    "error": true,
    "message": "<error message>"
}
```

<!-- [NEEDS CONFIRMATION: exact business-logic error messages (e.g. balance too low / cannot be fulfilled) for this endpoint are not provided by the source.] -->

## 请求示例

请在适用的地方替换 `YOUR_API_KEY` 和 `YOUR_TRON_ADDRESS`。下方的 `signedTx` 对象是简化的 — 请传入在[第 2 步](get-signed-transaction.md)中获取的完整签名交易。

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST "https://api.tronsave.io/v2/buy-resource" \
  -H "Content-Type: application/json" \
  -d '{
    "resourceType": "ENERGY",
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 3600,
    "resourceAmount": 32000,
    "unitPrice": 80,
    "options": {
        "allowPartialFill": true,
        "onlyCreateWhenFulfilled": true,
        "preventDuplicateIncompleteOrders": false,
        "maxPriceAccepted": 100,
        "minResourceDelegateRequiredAmount": 100000
    },
    "signedTx": {
        "visible": false,
        "txID": "795f8195893e8da2ef2f70fc3a1f2720f9077244c4b7b1c50c99c72fda675a32",
        "raw_data_hex": "0a02b32e...",
        "raw_data": {},
        "signature": ["xxxxxxxxxxxxxxxxxxx"]
    }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const createOrder = async (resourceAmount, signedTx, receiverAddress, unitPrice, durationSec, options) => {
  const url = `${TRONSAVE_API_URL}/v2/buy-resource`;
  const body = {
    resourceType: "ENERGY",
    resourceAmount,
    unitPrice,
    receiver: receiverAddress,
    durationSec,
    signedTx,
    options,
  };

  const res = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(body),
  });

  // Example response:
  // { "error": false, "message": "Success", "data": { "orderId": "6809fdb7b9ba217a41d726fd" } }
  return res.json();
};

createOrder(
  32000,
  signedTx, // the signed transaction from Step 2
  "YOUR_TRON_ADDRESS",
  80,
  3600,
  { allowPartialFill: true, onlyCreateWhenFulfilled: true, maxPriceAccepted: 100 }
).then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"


def create_order(resource_amount, signed_tx, receiver_address, unit_price, duration_sec, options):
    url = f"{TRONSAVE_API_URL}/v2/buy-resource"
    body = {
        "resourceType": "ENERGY",
        "resourceAmount": resource_amount,
        "unitPrice": unit_price,
        "receiver": receiver_address,
        "durationSec": duration_sec,
        "signedTx": signed_tx,
        "options": options,
    }

    response = requests.post(url, json=body)
    return response.json()


if __name__ == "__main__":
    result = create_order(
        resource_amount=32000,
        signed_tx=signed_tx,  # the signed transaction from Step 2
        receiver_address="YOUR_TRON_ADDRESS",
        unit_price=80,
        duration_sec=3600,
        options={"allowPartialFill": True, "onlyCreateWhenFulfilled": True, "maxPriceAccepted": 100},
    )
    print(result)
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class CreateOrder {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";

    public static void main(String[] args) throws Exception {
        // signedTx is the JSON object obtained in Step 2.
        String signedTx = "{\"visible\":false,\"txID\":\"795f8195...\",\"raw_data_hex\":\"0a02b32e...\",\"raw_data\":{},\"signature\":[\"xxxxxxxxxxxxxxxxxxx\"]}";

        String body = """
            {
              "resourceType": "ENERGY",
              "receiver": "YOUR_TRON_ADDRESS",
              "durationSec": 3600,
              "resourceAmount": 32000,
              "unitPrice": 80,
              "options": {
                "allowPartialFill": true,
                "onlyCreateWhenFulfilled": true,
                "maxPriceAccepted": 100
              },
              "signedTx": %s
            }
            """.formatted(signedTx);

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/buy-resource"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .build();

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
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

const tronsaveAPIURL = "https://api.tronsave.io"

func main() {
	// signedTx is the JSON object obtained in Step 2.
	body := []byte(`{
        "resourceType": "ENERGY",
        "receiver": "YOUR_TRON_ADDRESS",
        "durationSec": 3600,
        "resourceAmount": 32000,
        "unitPrice": 80,
        "options": {
            "allowPartialFill": true,
            "onlyCreateWhenFulfilled": true,
            "maxPriceAccepted": 100
        },
        "signedTx": {
            "visible": false,
            "txID": "795f8195...",
            "raw_data_hex": "0a02b32e...",
            "raw_data": {},
            "signature": ["xxxxxxxxxxxxxxxxxxx"]
        }
    }`)

	req, err := http.NewRequest(http.MethodPost, tronsaveAPIURL+"/v2/buy-resource", bytes.NewBuffer(body))
	if err != nil {
		panic(err)
	}
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
// Cargo.toml:
//   reqwest = { version = "0.12", features = ["blocking", "json"] }
//   serde_json = "1"

use serde_json::json;

const TRONSAVE_API_URL: &str = "https://api.tronsave.io";

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // signed_tx is the JSON object obtained in Step 2.
    let signed_tx = json!({
        "visible": false,
        "txID": "795f8195...",
        "raw_data_hex": "0a02b32e...",
        "raw_data": {},
        "signature": ["xxxxxxxxxxxxxxxxxxx"]
    });

    let body = json!({
        "resourceType": "ENERGY",
        "receiver": "YOUR_TRON_ADDRESS",
        "durationSec": 3600,
        "resourceAmount": 32000,
        "unitPrice": 80,
        "options": {
            "allowPartialFill": true,
            "onlyCreateWhenFulfilled": true,
            "maxPriceAccepted": 100
        },
        "signedTx": signed_tx
    });

    let client = reqwest::blocking::Client::new();
    let resp = client
        .post(format!("{TRONSAVE_API_URL}/v2/buy-resource"))
        .header("Content-Type", "application/json")
        .json(&body)
        .send()?;

    println!("{}", resp.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [预估 TRX](estimate-trx.md) — 在下达另一笔订单前重新计算成本。
* [获取签名交易](get-signed-transaction.md) — 生成此接口所需的 `signedTx`。
* [使用签名交易购买概览](README.md) — 完整的三步流程。
