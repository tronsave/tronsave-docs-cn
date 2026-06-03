---
description: 为资源订单生成已签名的 TRX 支付交易——既可以使用你自己的代码，也可以通过 TronSave 的 Get Signed Transaction API 实现。
---

# 获取签名交易

这是[使用签名交易购买](README.md)流程的**第 2 步**。在[第 1 步 — 预估 TRX](estimate-trx.md) 返回 `estimateTrx`（购买订单的成本，以 SUN 为单位）之后，你需要生成一笔签名转账交易，从买家钱包向 TronSave 资金地址支付该金额。然后在[第 3 步 — 创建订单](create-order.md)中提交它。

你可以通过两种方式完成此操作：自己对交易签名（[方案 1](#option-1-write-your-own-function)），或者让 TronSave API 替你签名（[方案 2](#option-2-use-the-tronsave-api)）。

## 方案 1：编写你自己的函数

使用[第 1 步](estimate-trx.md)中得到的 `estimateTrx` 值，用 `transactionBuilder` 构建一笔 TRX 转账交易，从买家地址向 TronSave 资金地址发送等于 `estimateTrx` 的金额，然后用买家的私钥对其签名：

```tsx
const dataSendTrx = await tronWeb.transactionBuilder.sendTrx('TRONSAVE_FUND_ADDRESS', estimate_trx, 'BUYER_ADDRESS')
const signed_tx = await tronWeb.trx.sign(dataSendTrx, 'PRIVATE_KEY');
```

{% hint style="info" %}
* `BUYER_ADDRESS` — 买家的公开地址。
* `PRIVATE_KEY` — 买家的私钥。
* `TRONSAVE_FUND_ADDRESS` — TronSave 资金地址（见下文）。
{% endhint %}

**TronSave 资金地址（`TRONSAVE_FUND_ADDRESS`）：**

{% tabs %}
{% tab title="MAINNET" %}
```
TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S
```
{% endtab %}

{% tab title="Nile (for testing)" %}
{% hint style="warning" %}
此资金地址仅供在 TRON Nile（测试网）网络上进行测试使用。
{% endhint %}

```
TATT1UzHRikft98bRFqApFTsaSw73ycfoS
```
{% endtab %}
{% endtabs %}

生成的 `signed_tx` 随后将传递给[第 3 步 — 创建订单](create-order.md)。

## 方案 2：使用 TronSave API

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/signed-tx`**

给定买家的地址、私钥以及第 1 步得到的 `estimateTrx`，此端点会替你构建并签名转账交易。

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

### 请求头

| 请求头 | 值 |
| --- | --- |
| `Content-Type` | `application/json` |

### 请求体

<table><thead><tr><th width="166">字段</th><th width="112">位置</th><th width="110">类型</th><th width="101">必填</th><th>说明</th></tr></thead><tbody><tr><td><code>address</code></td><td>body</td><td>string</td><td>true</td><td>买家的公开地址。</td></tr><tr><td><code>privateKey</code></td><td>body</td><td>string</td><td>true</td><td>买家的私钥。</td></tr><tr><td><code>estimateTrx</code></td><td>body</td><td>number</td><td>true</td><td>要支付的 TRX 金额，以 SUN 为单位（来自<a href="estimate-trx.md"><strong>第 1 步</strong></a>的 <code>estimateTrx</code> 值）。</td></tr></tbody></table>

### 请求体示例

```json
{
    "address": "TM6ZeEgpefyGWeMLuzSbfqTGkPv8Z65432",
    "privateKey": "YOUR_PRIVATE_KEY",
    "estimateTrx": 13500000
}
```

### 响应

```json
{
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
```

### 错误

此端点不使用 `apikey` 请求头。整个流程的身份验证由[第 3 步 — 创建订单](create-order.md)中的签名交易本身提供；此端点仅负责构建并签名交易。因此，当请求格式有误时，它返回的是模式校验错误（`400`），而不是 `401`。

**`400 Bad Request`** — 缺少必填字段（`address`、`privateKey` 或 `estimateTrx`）或字段无效。`message` 会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'address'"
}
```

**`404 Not Found`** — 路由/路径不正确：

```json
{
    "message": "Route POST:/v2/... not found",
    "error": "Not Found",
    "statusCode": 404
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.tronsave.io/v2/signed-tx \
  -H "Content-Type: application/json" \
  -d '{
    "address": "YOUR_TRON_ADDRESS",
    "privateKey": "YOUR_PRIVATE_KEY",
    "estimateTrx": 13500000
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch("https://api.tronsave.io/v2/signed-tx", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    address: "YOUR_TRON_ADDRESS",
    privateKey: "YOUR_PRIVATE_KEY",
    estimateTrx: 13500000,
  }),
});

const data = await res.json();
console.log(data);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

res = requests.post(
    "https://api.tronsave.io/v2/signed-tx",
    headers={"Content-Type": "application/json"},
    json={
        "address": "YOUR_TRON_ADDRESS",
        "privateKey": "YOUR_PRIVATE_KEY",
        "estimateTrx": 13500000,
    },
)

print(res.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetSignedTransaction {
    public static void main(String[] args) throws Exception {
        String body = """
            {
              "address": "YOUR_TRON_ADDRESS",
              "privateKey": "YOUR_PRIVATE_KEY",
              "estimateTrx": 13500000
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v2/signed-tx"))
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
		"address": "YOUR_TRON_ADDRESS",
		"privateKey": "YOUR_PRIVATE_KEY",
		"estimateTrx": 13500000
	}`)

	req, _ := http.NewRequest("POST", "https://api.tronsave.io/v2/signed-tx", bytes.NewBuffer(body))
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

    let res = client
        .post("https://api.tronsave.io/v2/signed-tx")
        .header("Content-Type", "application/json")
        .json(&json!({
            "address": "YOUR_TRON_ADDRESS",
            "privateKey": "YOUR_PRIVATE_KEY",
            "estimateTrx": 13500000
        }))
        .send()?;

    println!("{}", res.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
此端点需要在请求体中提供买家的 `privateKey`。请仅从受信任的服务器端环境调用它，切勿从客户端代码调用。如果你不希望发送私钥，请使用[方案 1](#option-1-write-your-own-function) 在本地对交易签名。
{% endhint %}

## 后续步骤

* [第 1 步 — 预估 TRX](estimate-trx.md)
* [第 3 步 — 创建订单](create-order.md)
* [使用签名交易购买概览](README.md)
