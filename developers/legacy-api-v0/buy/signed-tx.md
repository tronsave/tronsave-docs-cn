---
description: 使用签名交易购买能量的旧版 v0 流程 —— 预估 TRX、生成签名转账并创建订单。
---

# 使用签名交易购买（v0）

{% hint style="warning" %}
**旧版 API。** 本页介绍位于 `/v0/` 基础路径下的 v0 签名交易流程。新的集成应使用当前 API。参见[使用签名交易购买（v2）](../../api-reference/buy-resources/signed-tx/README.md)。
{% endhint %}

签名交易流程让你可以直接从自己的钱包支付 TRX 来购买能量 —— 无需 API 密钥，因为 TRX 转账是用你的私钥签名的。共分三步：

1. [预估 TRX](#step-1-estimate-trx) —— 计算所需数量和租赁时长对应的 TRX。
2. [获取签名交易](#step-2-get-a-signed-transaction) —— 对向 TronSave 资金地址的 TRX 转账进行签名，可自行签名或通过 API 签名。
3. [创建订单](#step-3-create-an-order) —— 提交签名交易以下达购买订单。

{% hint style="info" %}
**开始之前：** 请确保你拥有钱包的私钥，并且钱包中持有足够的 TRX 以支付预估费用。
{% endhint %}

主网基础 URL 为 `https://api.tronsave.io`。如需在 TRON Nile 测试网上进行测试，请使用 `https://api-dev.tronsave.io`。参见[环境](../../environments.md)。

| 步骤 | 方法 | 主网 | 测试网（Nile） |
| --- | --- | --- | --- |
| 预估 TRX | `POST` | `https://api.tronsave.io/v0/estimate-trx` | `https://api-dev.tronsave.io/v0/estimate-trx` |
| 获取签名交易 | `POST` | `https://api.tronsave.io/v0/signed-tx` | `https://api-dev.tronsave.io/v0/signed-tx` |
| 创建订单 | `POST` | `https://api.tronsave.io/v0/buy-energy` | `https://api-dev.tronsave.io/v0/buy-energy` |

---

## Step 1: 预估 TRX

计算给定资源数量和租赁时长所需的 TRX。响应会返回 `unit_price` 以及你在第 2 步中必须转账的 `estimate_trx`。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/estimate-trx`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 个请求。
{% endhint %}

### 请求头

| 请求头 | 值 | 是否必需 |
| --- | --- | --- |
| `Content-Type` | `application/json` | 是 |

### 请求参数

<table><thead><tr><th width="174">字段</th><th width="94">位置</th><th width="92">类型</th><th width="100">是否必需</th><th>说明</th></tr></thead><tbody><tr><td><code>amount</code></td><td>body</td><td>number</td><td>true</td><td>资源数量</td></tr><tr><td><code>buy_energy_type</code></td><td>body</td><td>string, number</td><td>true</td><td><p>"FAST"、"MEDIUM"、"SLOW" 或数字：</p><p><br>-"FAST"：如果市场可成交率 = 100%，则 FAST = MEDIUM。如果市场可成交率 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交率 = 0%，则 FAST = SLOW + 20。</p><p></p><p>-"MEDIUM"：使该订单获得最大市场成交量的最低价格。如果市场可成交率 = 0%，则 MEDIUM = SLOW + 10。</p><p></p><p>-"SLOW"：该订单可设置的最低价格。</p><p></p><p>-如果价格为数字，则价格单位等于 SUN</p></td></tr><tr><td><code>duration_millisec</code></td><td>body</td><td>number</td><td>true</td><td>所购资源的时长，时间单位为毫秒。</td></tr><tr><td><code>request_address</code></td><td>body</td><td>string</td><td>false</td><td>请求者的地址。</td></tr><tr><td><code>target_address</code></td><td>body</td><td>string</td><td>false</td><td>资源接收地址。</td></tr><tr><td><code>is_partial</code></td><td>body</td><td>boolean</td><td>false</td><td>是否允许订单被部分成交。</td></tr></tbody></table>

### 请求体示例

```json
{
    "amount": 100000,
    "buy_energy_type": "MEDIUM",
    "duration_millisec": 259200000
}
```

### 响应

<table><thead><tr><th width="185">字段</th><th width="171">类型</th><th width="158">是否必需</th><th>说明</th></tr></thead><tbody><tr><td><code>unit_price</code></td><td>number</td><td>true</td><td>与你的 <code>buy_energy_type</code> 匹配的能量价格，以 SUN 计</td></tr><tr><td><code>duration_millisec</code></td><td>number</td><td>true</td><td></td></tr><tr><td><code>available_energy</code></td><td>number</td><td>true</td><td>TronSave 市场上与 <code>unit_price</code> 匹配的可用能量总量</td></tr><tr><td><code>estimate_trx</code></td><td>number</td><td>true</td><td>在 <code>duration_millisec</code> 内以 <code>unit_price</code> 价格购买全部 <code>available_energy</code> 所需支付的预估 trx 总额</td></tr></tbody></table>

#### 成功

```json
{
    "unit_price": 45,
    "duration_millisec": 259200000,
    "available_energy": 4298470,
    "estimate_trx": 13500000
}
```

`estimate_trx` 值以 SUN 返回（1 TRX = 1,000,000 SUN）。

#### 错误

此端点不需要 `apikey` 请求头 —— 签名交易流程通过钱包签名的 TRX 转账进行身份验证，因此此处不存在身份验证错误。当请求缺少必需字段或字段无效时，会返回 `400 Bad Request`，其 `message` 会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'amount'"
}
```

---

## Step 2: 获取签名交易

你需要一笔从买家地址向 TronSave 资金地址转账 `estimate_trx` SUN 的签名 TRX 转账。你可以自行构建，也可以让 API 来完成。

### TronSave 资金地址

{% tabs %}
{% tab title="MAINNET" %}
```
TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S
```
{% endtab %}

{% tab title="Nile (for testing)" %}
{% hint style="warning" %}
此资金地址仅用于在 TRON Nile 网络上进行测试。
{% endhint %}

```
TATT1UzHRikft98bRFqApFTsaSw73ycfoS
```
{% endtab %}
{% endtabs %}

### 选项 1：编写你自己的函数

使用 `tronWeb`，为 `estimate_trx`（来自第 1 步）构建一笔 `sendTrx` 转账，并用买家的私钥签名：

```javascript
const dataSendTrx = await tronWeb.transactionBuilder.sendTrx('TRONSAVE_FUND_ADDRESS', estimate_trx, 'BUYER_ADDRESS')
const signed_tx = await tronWeb.trx.sign(dataSendTrx, 'PRIVATE_KEY');
```

{% hint style="info" %}
* `BUYER_ADDRESS` 是买家的公开地址。
* `PRIVATE_KEY` 是买家的私钥。
* `TRONSAVE_FUND_ADDRESS` 是上面显示的 TronSave 资金地址。
{% endhint %}

### 选项 2：使用获取签名交易 API

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/signed-tx`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 个请求。
{% endhint %}

#### 请求头

| 请求头 | 值 | 是否必需 |
| --- | --- | --- |
| `Content-Type` | `application/json` | 是 |

#### 请求参数

<table><thead><tr><th width="166">字段</th><th width="112">位置</th><th width="110">类型</th><th width="101">是否必需</th><th>说明</th></tr></thead><tbody><tr><td><code>address</code></td><td>body</td><td>string</td><td>true</td><td>买家的公开地址</td></tr><tr><td><code>private_key</code></td><td>body</td><td>string</td><td>true</td><td>买家的私钥</td></tr><tr><td><code>estimate_trx</code></td><td>body</td><td>number</td><td>true</td><td>需支付的 TRX 数量，以 SUN 计算。（来自<a href="#step-1-estimate-trx">第 1 步</a>的 <code>estimate_trx</code>）</td></tr></tbody></table>

#### 请求体示例

```json
{
    "address": "TM6ZeEgpefyGWeMLuzSbfqTGkPv8Z65432",
    "private_key": "{{yourprivateKey}}",
    "estimate_trx": 13500000
}
```

#### 响应

##### 成功

```json
{
    "visible": false,
    "txID": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
    "raw_data": {
        "contract": [
            {
                "parameter": {
                    "value": {
                        "amount": 13500000,
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
```

##### 错误

此端点不需要 `apikey` 请求头。当请求缺少必需字段或字段无效时，会返回 `400 Bad Request`，其 `message` 会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'estimate_trx'"
}
```

---

## Step 3: 创建订单

将签名交易（来自第 2 步）与订单参数一起提交，以下达能量购买订单。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/buy-energy`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 个请求。
{% endhint %}

### 请求头

| 请求头 | 值 | 是否必需 |
| --- | --- | --- |
| `Content-Type` | `application/json` | 是 |

{% hint style="info" %}
此端点通过你提交的 `signed_tx` 进行身份验证 —— TRX 付款由你自己的钱包签名 —— 因此不需要 `apikey` 请求头。
{% endhint %}

### 请求参数

<table><thead><tr><th width="221">字段</th><th width="105">类型</th><th width="91">是否必需</th><th>说明</th></tr></thead><tbody><tr><td><code>resource_type</code></td><td>string</td><td>true</td><td>"ENERGY"</td></tr><tr><td><code>unit_price</code></td><td>number</td><td>true</td><td>价格单位等于 SUN。</td></tr><tr><td><code>allow_partial_fill</code></td><td>boolean</td><td>true</td><td>是否允许订单被部分成交</td></tr><tr><td><code>target_address</code></td><td>string</td><td>true</td><td>资源接收地址</td></tr><tr><td><code>duration_millisec</code></td><td>number</td><td>true</td><td>所购资源的时长，时间单位等于毫秒。</td></tr><tr><td><code>tx_id</code></td><td>string</td><td>true</td><td>交易 ID</td></tr><tr><td><code>signed_tx</code></td><td>SignedTransaction</td><td>true</td><td>签名交易，请注意它是一个 JSON 对象（来自<a href="#step-2-get-a-signed-transaction">第 2 步</a>的 <code>signed_tx</code>）</td></tr><tr><td><code>only_create_when_fulfilled</code></td><td>Boolean</td><td>false</td><td><p>[true] =&#x3E; 仅当订单可被完全成交时才创建</p><p>[false] =&#x3E; 即使订单无法被完全成交也会创建</p><p>默认值：false</p></td></tr><tr><td><code>max_price_accepted</code></td><td>Number</td><td>false</td><td>仅当预估价格低于此值时才创建订单。</td></tr><tr><td><code>add_order_incomplete</code></td><td>Boolean</td><td>false</td><td><p>[true] =&#x3E; 仅当订单列表中不存在相同参数的未完成订单时才创建订单</p><p>[false] =&#x3E; 即使订单列表中不存在相同参数的未完成订单也会创建订单</p><p>默认值：false</p></td></tr></tbody></table>

### 请求体示例

```json
{
    "resource_type": "ENERGY",
    "unit_price": 45,
    "allow_partial_fill": true,
    "target_address": "TM6ZeEgpefyGWeMLuzSbfqTGkPv8Z6Jm4X",
    "duration_millisec": 259200000,
    "tx_id": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
    "signed_tx": {
        "visible": false,
        "txID": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
        "raw_data": {
            "contract": [
                {
                    "parameter": {
                        "value": {
                            "amount": 13500000,
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
    },
    "only_create_when_fulfilled": false,
    "max_price_accepted": 200,
    "add_order_incomplete": false
}
```

### 响应

#### 成功

```json
{
    "message": "651d2306e55c073f6ca0992e"
}
```

`message` 值是所创建订单的 `order_id`。

#### 错误

此端点通过提交的 `signed_tx` 进行身份验证，因此不需要 `apikey` 请求头，也不存在身份验证错误。当请求缺少必需字段或字段无效时，会返回 `400 Bad Request`，其 `message` 会指出出错的字段：

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'signed_tx'"
}
```

<!-- [NEEDS CONFIRMATION: business-logic 400 messages specific to the buy-energy order (e.g. insufficient TRX in the signed transfer, or order cannot be fulfilled) were not captured live] -->

---

## 请求示例

以下示例展示完整流程：预估、对向资金地址的 TRX 转账进行签名，然后创建订单。请根据需要替换 `YOUR_TRON_ADDRESS`、私钥和资金地址。对 TRX 转账进行签名（第 2 步，选项 1）通常使用 TRON 库；cURL 示例改用 API（第 2 步，选项 2）。

{% tabs %}
{% tab title="cURL" %}
```bash
# Step 1: Estimate TRX
curl -X POST https://api.tronsave.io/v0/estimate-trx \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 100000,
    "buy_energy_type": "MEDIUM",
    "duration_millisec": 259200000
  }'

# Step 2: Get a signed transaction (Option 2)
curl -X POST https://api.tronsave.io/v0/signed-tx \
  -H "Content-Type: application/json" \
  -d '{
    "address": "YOUR_TRON_ADDRESS",
    "private_key": "YOUR_PRIVATE_KEY",
    "estimate_trx": 13500000
  }'

# Step 3: Create the order (use the signed_tx returned above)
curl -X POST https://api.tronsave.io/v0/buy-energy \
  -H "Content-Type: application/json" \
  -d '{
    "resource_type": "ENERGY",
    "unit_price": 45,
    "allow_partial_fill": true,
    "target_address": "YOUR_TRON_ADDRESS",
    "duration_millisec": 259200000,
    "tx_id": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
    "signed_tx": { "visible": false, "txID": "446eed36...", "raw_data": {}, "raw_data_hex": "0a020713...", "signature": ["xxxxxxxxx"] },
    "only_create_when_fulfilled": false,
    "max_price_accepted": 200,
    "add_order_incomplete": false
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Requires tronweb for signing the TRX transfer.
const TRONSAVE_API_URL = "https://api.tronsave.io";
const TRONSAVE_FUND_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S";

// Step 1: Estimate TRX
const estimateRes = await fetch(`${TRONSAVE_API_URL}/v0/estimate-trx`, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    amount: 100000,
    buy_energy_type: "MEDIUM",
    duration_millisec: 259200000,
  }),
});
const { unit_price, duration_millisec, estimate_trx } = await estimateRes.json();

// Step 2 (Option 1): Sign the TRX transfer to the fund address
const dataSendTrx = await tronWeb.transactionBuilder.sendTrx(
  TRONSAVE_FUND_ADDRESS,
  estimate_trx,
  "YOUR_TRON_ADDRESS"
);
const signed_tx = await tronWeb.trx.sign(dataSendTrx, "YOUR_PRIVATE_KEY");

// Step 3: Create the order
const orderRes = await fetch(`${TRONSAVE_API_URL}/v0/buy-energy`, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    resource_type: "ENERGY",
    unit_price,
    allow_partial_fill: true,
    target_address: "YOUR_TRON_ADDRESS",
    duration_millisec,
    tx_id: signed_tx.txID,
    signed_tx,
    only_create_when_fulfilled: false,
    max_price_accepted: 200,
    add_order_incomplete: false,
  }),
});
console.log(await orderRes.text());
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"

# Step 1: Estimate TRX
estimate = requests.post(
    f"{TRONSAVE_API_URL}/v0/estimate-trx",
    json={
        "amount": 100000,
        "buy_energy_type": "MEDIUM",
        "duration_millisec": 259200000,
    },
).json()
unit_price = estimate["unit_price"]
duration_millisec = estimate["duration_millisec"]
estimate_trx = estimate["estimate_trx"]

# Step 2 (Option 2): Get a signed transaction from the API
signed_tx = requests.post(
    f"{TRONSAVE_API_URL}/v0/signed-tx",
    json={
        "address": "YOUR_TRON_ADDRESS",
        "private_key": "YOUR_PRIVATE_KEY",
        "estimate_trx": estimate_trx,
    },
).json()

# Step 3: Create the order
order = requests.post(
    f"{TRONSAVE_API_URL}/v0/buy-energy",
    json={
        "resource_type": "ENERGY",
        "unit_price": unit_price,
        "allow_partial_fill": True,
        "target_address": "YOUR_TRON_ADDRESS",
        "duration_millisec": duration_millisec,
        "tx_id": signed_tx["txID"],
        "signed_tx": signed_tx,
        "only_create_when_fulfilled": False,
        "max_price_accepted": 200,
        "add_order_incomplete": False,
    },
).json()
print(order)
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class BuyWithSignedTx {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";

    static String post(HttpClient client, String path, String body) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + path))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .build();
        return client.send(request, HttpResponse.BodyHandlers.ofString()).body();
    }

    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();

        // Step 1: Estimate TRX
        String estimate = post(client, "/v0/estimate-trx", """
            {
              "amount": 100000,
              "buy_energy_type": "MEDIUM",
              "duration_millisec": 259200000
            }
            """);
        System.out.println(estimate);

        // Step 2 (Option 2): Get a signed transaction from the API
        String signedTx = post(client, "/v0/signed-tx", """
            {
              "address": "YOUR_TRON_ADDRESS",
              "private_key": "YOUR_PRIVATE_KEY",
              "estimate_trx": 13500000
            }
            """);
        System.out.println(signedTx);

        // Step 3: Create the order (embed the signedTx JSON from Step 2)
        String order = post(client, "/v0/buy-energy", """
            {
              "resource_type": "ENERGY",
              "unit_price": 45,
              "allow_partial_fill": true,
              "target_address": "YOUR_TRON_ADDRESS",
              "duration_millisec": 259200000,
              "tx_id": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
              "signed_tx": %s,
              "only_create_when_fulfilled": false,
              "max_price_accepted": 200,
              "add_order_incomplete": false
            }
            """.formatted(signedTx));
        System.out.println(order);
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

func post(path string, body []byte) string {
	req, err := http.NewRequest(http.MethodPost, tronsaveAPIURL+path, bytes.NewBuffer(body))
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
	return string(out)
}

func main() {
	// Step 1: Estimate TRX
	estimate := post("/v0/estimate-trx", []byte(`{
		"amount": 100000,
		"buy_energy_type": "MEDIUM",
		"duration_millisec": 259200000
	}`))
	fmt.Println(estimate)

	// Step 2 (Option 2): Get a signed transaction from the API
	signedTx := post("/v0/signed-tx", []byte(`{
		"address": "YOUR_TRON_ADDRESS",
		"private_key": "YOUR_PRIVATE_KEY",
		"estimate_trx": 13500000
	}`))
	fmt.Println(signedTx)

	// Step 3: Create the order (embed the signed_tx JSON from Step 2)
	order := post("/v0/buy-energy", []byte(`{
		"resource_type": "ENERGY",
		"unit_price": 45,
		"allow_partial_fill": true,
		"target_address": "YOUR_TRON_ADDRESS",
		"duration_millisec": 259200000,
		"tx_id": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
		"signed_tx": {},
		"only_create_when_fulfilled": false,
		"max_price_accepted": 200,
		"add_order_incomplete": false
	}`))
	fmt.Println(order)
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
// Cargo.toml:
//   reqwest = { version = "0.12", features = ["blocking", "json"] }
//   serde_json = "1"

use reqwest::blocking::Client;
use serde_json::{json, Value};

const TRONSAVE_API_URL: &str = "https://api.tronsave.io";

fn post(client: &Client, path: &str, body: &Value) -> Value {
    client
        .post(format!("{TRONSAVE_API_URL}{path}"))
        .header("Content-Type", "application/json")
        .json(body)
        .send()
        .unwrap()
        .json()
        .unwrap()
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Step 1: Estimate TRX
    let estimate = post(&client, "/v0/estimate-trx", &json!({
        "amount": 100000,
        "buy_energy_type": "MEDIUM",
        "duration_millisec": 259200000
    }));
    println!("{estimate}");

    // Step 2 (Option 2): Get a signed transaction from the API
    let signed_tx = post(&client, "/v0/signed-tx", &json!({
        "address": "YOUR_TRON_ADDRESS",
        "private_key": "YOUR_PRIVATE_KEY",
        "estimate_trx": estimate["estimate_trx"]
    }));
    println!("{signed_tx}");

    // Step 3: Create the order
    let order = post(&client, "/v0/buy-energy", &json!({
        "resource_type": "ENERGY",
        "unit_price": estimate["unit_price"],
        "allow_partial_fill": true,
        "target_address": "YOUR_TRON_ADDRESS",
        "duration_millisec": estimate["duration_millisec"],
        "tx_id": signed_tx["txID"],
        "signed_tx": signed_tx,
        "only_create_when_fulfilled": false,
        "max_price_accepted": 200,
        "add_order_incomplete": false
    }));
    println!("{order}");

    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* 迁移到当前 API：[使用签名交易购买（v2）](../../api-reference/buy-resources/signed-tx/README.md)。
* [身份验证](../../authentication.md)和[环境](../../environments.md)。
* [术语表](../../../concepts/glossary.md)，了解能量、带宽、TRX 和 SUN 的定义。
