---
description: 通过将活跃买单与已签名的链上代理交易进行撮合，在 TronSave 上手动出售能量或带宽。
---

# 出售资源

出售资源 API 让资源供应商可以在 TronSave 上手动成交未完成的买单。卖家获取活跃订单，构建并签名一笔链上 `DelegateResourceContract` 交易，然后将签名交易提交给 TronSave 进行订单撮合与结算。

{% hint style="warning" %}
**需要白名单。** 出售 API 仅对加入白名单的钱包开放。请登录 TronSave 并将您的钱包地址发送给客服，以便将其加入白名单。加入白名单后，使用从该钱包地址生成的 API 密钥来调用出售相关端点。

通过 Telegram 联系客服：[**@wantingtrx**](https://t.me/wantingtrx)
{% endhint %}

出售是一个两步流程：

1. [**获取活跃订单**](#step-1-get-active-orders) — 列出您可以成交的未完成买单。
2. [**出售资源**](#step-2-sell-the-resource) — 针对选定的订单提交一笔签名代理交易。

## Step 1: 获取活跃订单

返回可供撮合的全局活跃买单列表。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/orders/active-global`**

{% hint style="info" %}
**速率限制：** 每 1 秒 3 次请求。
{% endhint %}

### Headers

<table><thead><tr><th width="160">Name</th><th width="120">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>apikey</code> <mark style="color:red;">*</mark></td><td>String</td><td>TronSave API 密钥（必须属于白名单地址）。参见 <a href="../authentication.md">身份验证</a>。</td></tr></tbody></table>

### 查询参数

<table><thead><tr><th width="221">Name</th><th width="172">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>page</code></td><td>Integer</td><td>页索引，从 0 开始。默认值：<code>0</code></td></tr><tr><td><code>pageSize</code></td><td>Integer</td><td>每页订单数量。默认值：<code>10</code></td></tr></tbody></table>

### 响应

```javascript
{
  "error": false,
  "message": "Success",
  "data": {
    "data": [
      {
        "id": string,            // Order identifier — use as orderId in Step 2
        "receiver": string,      // TRON address that receives the delegated resource
        "resourceAmount": number,// the amount of resource
        "resourceType": string,  // the resource type is "ENERGY" or "BANDWIDTH"
        "remainAmount": number,  // the remaining amount that can be matched by the system
        "orderType": string,     // type of order is "NORMAL" or "EXTEND"
        "price": number,         // price unit is equal to SUN
        "durationSec": number,   // rent duration, duration unit is equal to seconds
        "status": string,        // the order status, either Active or Completed
        "allowPartialFill": boolean, // Allow the order to be filled partially or not
        "payoutAmount": number,  // Total payout of this order (SUN)
        "fulfilledPercent": number, // The percent that shows filling processing. 0-100
        "createdAt": 1778467289
      }
    ],
    "total": 3
  }
}
```

**响应示例**

```javascript
{
  "error": false,
  "message": "Success",
  "data": {
    "data": [
      {
        "id": "6a0141d98e6f3f91d2284444",
        "receiver": "TA4JgKPtrPLtGQ1WMz3qmorqbL22221111",
        "resourceAmount": 500000,
        "resourceType": "ENERGY",
        "remainAmount": 339999,
        "orderType": "NORMAL",
        "price": 50,
        "durationSec": 259200,
        "status": "Active",
        "allowPartialFill": true,
        "payoutAmount": 24000150,
        "fulfilledPercent": 32,
        "createdAt": 1778467289
      }
    ],
    "total": 3
  }
}
```

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET \
  "https://api.tronsave.io/v2/orders/active-global?page=0&pageSize=10" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const res = await fetch(
  "https://api.tronsave.io/v2/orders/active-global?page=0&pageSize=10",
  {
    method: "GET",
    headers: {
      apikey: "YOUR_API_KEY",
      "content-type": "application/json",
    },
  }
);

const data = await res.json();
console.log(data.data.data);
```
{% endtab %}

{% tab title="Python" %}
```python
import urllib.parse
import urllib.request
import json

query = urllib.parse.urlencode({"page": 0, "pageSize": 10})
url = f"https://api.tronsave.io/v2/orders/active-global?{query}"

req = urllib.request.Request(
    url,
    method="GET",
    headers={"apikey": "YOUR_API_KEY", "content-type": "application/json"},
)
with urllib.request.urlopen(req) as response:
    data = json.loads(response.read().decode("utf-8"))
print(data["data"]["data"])
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetActiveOrders {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v2/orders/active-global?page=0&pageSize=10"))
            .header("apikey", "YOUR_API_KEY")
            .header("content-type", "application/json")
            .GET()
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
	"fmt"
	"io"
	"net/http"
)

func main() {
	url := "https://api.tronsave.io/v2/orders/active-global?page=0&pageSize=10"
	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("content-type", "application/json")

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
// Cargo.toml:
// reqwest = { version = "0.12", features = ["blocking", "json"] }
use std::error::Error;

fn main() -> Result<(), Box<dyn Error>> {
    let client = reqwest::blocking::Client::new();
    let res = client
        .get("https://api.tronsave.io/v2/orders/active-global")
        .query(&[("page", "0"), ("pageSize", "10")])
        .header("apikey", "YOUR_API_KEY")
        .header("content-type", "application/json")
        .send()?;

    let body: serde_json::Value = res.json()?;
    println!("{}", body);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## Step 2: 出售资源

提交一笔签名代理交易以成交选定的订单。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/orders/sell-manual`**

{% hint style="info" %}
**速率限制：** 每 1 秒 2 次请求。
{% endhint %}

### Headers

<table><thead><tr><th width="160">Name</th><th width="120">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>apikey</code> <mark style="color:red;">*</mark></td><td>String</td><td>TronSave API 密钥（必须属于白名单地址）。参见 <a href="../authentication.md">身份验证</a>。</td></tr></tbody></table>

### 请求体

<table><thead><tr><th width="268">Name</th><th width="122">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>orderId</code> <mark style="color:red;">*</mark></td><td>string</td><td>来自 <strong>Step 1</strong> 中 <code>id</code> 字段的订单 ID。</td></tr><tr><td><code>paymentAddress</code></td><td>string</td><td>接收出售订单付款的 TRON 地址。如果未提供，付款将发送至委托方（delegator）地址。</td></tr><tr><td><code>isAllowSellForLockedDelegator</code></td><td>boolean</td><td><code>false</code>（默认）— 不出售给当前在链上被锁定的目标地址。<br><code>true</code> — 出售给所有目标地址。</td></tr><tr><td><code>signedTx</code> <mark style="color:red;">*</mark></td><td>object</td><td>签名交易。请注意它是一个 JSON 对象。</td></tr></tbody></table>

### 请求体示例

```javascript
{
  "orderId": "6a0141d98e6f3f91d2284444",
  "paymentAddress": "TCtk4viKFyywGkxmPSLLTfsBqrGPFFFFFF",
  "isAllowSellForLockedDelegator": false,
  "signedTx": {
    "visible": false,
    "txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
    "raw_data_hex": "0a026cc42208738b6bf2033b649740b0f594b0e1335a79083912750a35747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e44656c65676174655265736f75726365436f6e7472616374123c0a1541be35e6bcd33e46894072909d72bd31ccd93b1b73100118a6a1bc810522154100f6d191a641af2c015b52b1ffef788a3352cef628013080a30570d0a091b0e133",
    "raw_data": {
      "contract": [
        {
          "parameter": {
            "value": {
              "owner_address": "41be35e6bcd33e46894072909d72bd31ccd93b1b73",
              "receiver_address": "4100f6d191a641af2c015b52b1ffef788a3352cef6",
              "balance": 1345261734,
              "resource": "ENERGY",
              "lock": true,
              "lock_period": 86400
            },
            "type_url": "type.googleapis.com/protocol.DelegateResourceContract"
          },
          "type": "DelegateResourceContract"
        }
      ],
      "ref_block_bytes": "6cc4",
      "ref_block_hash": "738b6bf2033b6497",
      "expiration": 1778485902000,
      "timestamp": 1778485842000
    },
    "signature": [
      "xxxxxxxxx"
    ]
  }
}
```

### 响应（成功）

```javascript
{
  "error": false,
  "message": "Success",
  "data": {
    "success": true,
    "message": "Sell manual success",
    "code": 200,
    "delegatedId": "6a015f126da945e2f7773333"
  }
}
```

### 响应（错误）

两个出售端点都要求提供属于白名单地址的有效 `apikey` 请求头。身份验证和校验失败会返回下列响应体之一。

**401 Unauthorized — 缺少 API 密钥**（没有 `apikey` 请求头）：

```json
{
  "error": true,
  "message": "TSAS:106 API_KEY_REQUIRED",
  "data": null
}
```

**401 Unauthorized — 无效的 API 密钥**：

```json
{
  "error": true,
  "message": "TSAS:107 INVALID_API_KEY",
  "data": null
}
```

**400 Bad Request — 请求体校验失败**（缺少必填字段或字段无效；`message` 会指出出错的字段，例如 `orderId` 或 `signedTx`）：

```json
{
  "statusCode": 400,
  "code": "FST_ERR_VALIDATION",
  "error": "Bad Request",
  "message": "body must have required property 'orderId'"
}
```

**404 Not Found — 路由/路径错误**：

```json
{
  "message": "Route POST:/v2/... not found",
  "error": "Not Found",
  "statusCode": 404
}
```

业务逻辑失败（例如所选订单已无法成交，或签名交易与订单不匹配）会返回标准的 TronSave 响应封装，其中 `error` 为 `true` 且 `message` 包含描述性说明：

```javascript
{
  "error": true,
  "message": "<error description>"
}
```

<!-- [NEEDS CONFIRMATION: exact business-logic error messages for /v2/orders/sell-manual (e.g. order already filled, signedTx mismatch, locked delegator) — source does not include these samples] -->

{% hint style="info" %}
**速率限制：** 全局默认限制为每秒 15 次请求；出售资源端点限制为每秒 2–3 次请求（`active-global` 为每秒 3 次，`sell-manual` 为每秒 2 次）。超过限制会返回 `{"error": true, "message": "Rate limit reached"}`。
{% endhint %}

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X POST \
  "https://api.tronsave.io/v2/orders/sell-manual" \
  -H "apikey: YOUR_API_KEY" \
  -H "content-type: application/json" \
  -d '{
    "orderId": "6a0141d98e6f3f91d2284444",
    "paymentAddress": "YOUR_TRON_ADDRESS",
    "isAllowSellForLockedDelegator": false,
    "signedTx": {
      "visible": false,
      "txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
      "raw_data_hex": "0a026cc4...",
      "raw_data": { },
      "signature": ["xxxxxxxxx"]
    }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const body = {
  orderId: "6a0141d98e6f3f91d2284444",
  paymentAddress: "YOUR_TRON_ADDRESS",
  isAllowSellForLockedDelegator: false,
  signedTx, // signed DelegateResourceContract object (e.g. from TronWeb)
};

const res = await fetch("https://api.tronsave.io/v2/orders/sell-manual", {
  method: "POST",
  headers: {
    apikey: "YOUR_API_KEY",
    "content-type": "application/json",
  },
  body: JSON.stringify(body),
});

const data = await res.json();
console.log(data);
```
{% endtab %}

{% tab title="Python" %}
```python
import urllib.request
import json

signed_tx = {
    "visible": False,
    "txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
    "raw_data_hex": "0a026cc4...",
    "raw_data": {},
    "signature": ["xxxxxxxxx"],
}

payload = {
    "orderId": "6a0141d98e6f3f91d2284444",
    "paymentAddress": "YOUR_TRON_ADDRESS",
    "isAllowSellForLockedDelegator": False,
    "signedTx": signed_tx,
}

req = urllib.request.Request(
    "https://api.tronsave.io/v2/orders/sell-manual",
    method="POST",
    data=json.dumps(payload).encode("utf-8"),
    headers={"apikey": "YOUR_API_KEY", "content-type": "application/json"},
)
with urllib.request.urlopen(req) as response:
    data = json.loads(response.read().decode("utf-8"))
print(data)
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class SellManual {
    public static void main(String[] args) throws Exception {
        // signedTx must be a JSON object (e.g. produced by a TRON SDK)
        String body = """
            {
              "orderId": "6a0141d98e6f3f91d2284444",
              "paymentAddress": "YOUR_TRON_ADDRESS",
              "isAllowSellForLockedDelegator": false,
              "signedTx": {
                "visible": false,
                "txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
                "raw_data_hex": "0a026cc4...",
                "raw_data": { },
                "signature": ["xxxxxxxxx"]
              }
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.tronsave.io/v2/orders/sell-manual"))
            .header("apikey", "YOUR_API_KEY")
            .header("content-type", "application/json")
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
	// signedTx must be a JSON object (e.g. produced by a TRON SDK)
	body := []byte(`{
		"orderId": "6a0141d98e6f3f91d2284444",
		"paymentAddress": "YOUR_TRON_ADDRESS",
		"isAllowSellForLockedDelegator": false,
		"signedTx": {
			"visible": false,
			"txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
			"raw_data_hex": "0a026cc4...",
			"raw_data": {},
			"signature": ["xxxxxxxxx"]
		}
	}`)

	req, _ := http.NewRequest("POST", "https://api.tronsave.io/v2/orders/sell-manual", bytes.NewBuffer(body))
	req.Header.Set("apikey", "YOUR_API_KEY")
	req.Header.Set("content-type", "application/json")

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
// reqwest = { version = "0.12", features = ["blocking", "json"] }
// serde_json = "1"
use serde_json::json;
use std::error::Error;

fn main() -> Result<(), Box<dyn Error>> {
    // signed_tx must be a JSON object (e.g. produced by a TRON SDK)
    let payload = json!({
        "orderId": "6a0141d98e6f3f91d2284444",
        "paymentAddress": "YOUR_TRON_ADDRESS",
        "isAllowSellForLockedDelegator": false,
        "signedTx": {
            "visible": false,
            "txID": "997ce381e04206eac74ab911bc21ded3bc1e6a1dc032d900e9e690b549933333",
            "raw_data_hex": "0a026cc4...",
            "raw_data": {},
            "signature": ["xxxxxxxxx"]
        }
    });

    let client = reqwest::blocking::Client::new();
    let res = client
        .post("https://api.tronsave.io/v2/orders/sell-manual")
        .header("apikey", "YOUR_API_KEY")
        .header("content-type", "application/json")
        .json(&payload)
        .send()?;

    let body: serde_json::Value = res.json()?;
    println!("{}", body);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 端到端示例：构建签名交易

Step 2 中的签名交易是一笔 TRON `DelegateResourceContract`，它将资源从您的钱包（委托方 delegator）代理给订单的 `receiver`（接收地址）。下面的示例使用 TronWeb 获取活跃订单、选取价格最高的订单、推导冻结费率、构建并签名代理交易，然后提交。

{% hint style="info" %}
本示例面向 Nile（测试网）。TronSave 基础 URL `https://api-dev.tronsave.io` 和 TronWeb 全节点 `https://api.nileex.io` 用于测试。对于主网，请使用 `https://api.tronsave.io` 和 `https://api.trongrid.io`。
{% endhint %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
/**
 * Copy-and-run manual sell flow:
 * 1) Fill the config below
 * 2) node src/test/sellManualFlow.guide.js
 */
const { TronWeb } = require("tronweb");

const API_KEY = "your_api_key"; // change it later, (CONTACT :https://t.me/wantingtrx)
const TRONSAVE_API_URL = "https://api-dev.tronsave.io"; // change it later (api-dev = Nile testnet) or "https://api.tronsave.io" (mainnet)
const PRIVATE_KEY = "your_private_key"; // change it later
const INPUT_RESOURCE_TYPE = "ENERGY"; // change it later ("ENERGY" | "BANDWIDTH")
const PAGE = 0; // change it later
const PAGE_SIZE = 10; // change it later
const PAYMENT_ADDRESS = ""; // optional, change it later
const IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR = false; // change it later
const TRONWEB_FULL_HOST = "https://api.nileex.io"; // change it later if needed (api-dev = Nile testnet) or "https://api.trongrid.io" (mainnet)
const RESOURCE_AMOUNT_OVERRIDE = 0; // optional, 0 means use targetOrder.remainAmount
const TRON_BLOCK_TIME_IN_SECONDS = 3;
const FALLBACK_FREEZE_RATE = 150; // resource per 1 TRX
const ALLOW_FALLBACK_FREEZE_RATE = false; // true only if you intentionally want fallback

const getChainParamValue = (params, key) => {
    const found = params.find((param) => param.key === key);
    return Number((found && found.value) || 0);
};

const getFreezeRateFromAccountAndChainParams = async (tronWeb, delegatorAddress, resourceType) => {
    const account = await tronWeb.trx.getAccount(delegatorAddress);
    if (!account || !account.address) {
        throw new Error("Delegator address is not activated on chain");
    }

    const accountResources = await tronWeb.trx.getAccountResources(delegatorAddress);
    if (resourceType === "ENERGY") {
        const totalLimit = Number((accountResources && accountResources.TotalEnergyLimit) || 0);
        const totalWeight = Number((accountResources && accountResources.TotalEnergyWeight) || 0);
        if (totalLimit > 0 && totalWeight > 0) return totalLimit / totalWeight;
    } else {
        const totalLimit = Number((accountResources && accountResources.TotalNetLimit) || 0);
        const totalWeight = Number((accountResources && accountResources.TotalNetWeight) || 0);
        if (totalLimit > 0 && totalWeight > 0) return totalLimit / totalWeight;
    }

    const chainParams = await tronWeb.trx.getChainParameters();
    if (!Array.isArray(chainParams) || chainParams.length === 0) {
        throw new Error("Cannot load chain parameters");
    }

    if (resourceType === "ENERGY") {
        const totalLimit = getChainParamValue(chainParams, "getTotalEnergyCurrentLimit");
        const totalWeight = getChainParamValue(chainParams, "getTotalEnergyWeight");
        if (totalLimit > 0 && totalWeight > 0) return totalLimit / totalWeight;
    } else {
        const totalLimit = getChainParamValue(chainParams, "getTotalNetLimit");
        const totalWeight = getChainParamValue(chainParams, "getTotalNetWeight");
        if (totalLimit > 0 && totalWeight > 0) return totalLimit / totalWeight;
    }

    throw new Error("Cannot derive freeze rate from chain params");
};

const assertConfig = () => {
    if (!API_KEY || API_KEY === "your_api_key") throw new Error("API_KEY is required");
    if (!PRIVATE_KEY || PRIVATE_KEY === "your_private_key") throw new Error("PRIVATE_KEY is required");
    if (!["ENERGY", "BANDWIDTH"].includes(INPUT_RESOURCE_TYPE)) {
        throw new Error("INPUT_RESOURCE_TYPE must be ENERGY or BANDWIDTH");
    }
};

const requestJson = async (url, init) => {
    const response = await fetch(url, {
        ...init,
        headers: {
            apikey: API_KEY,
            "content-type": "application/json",
            ...(init && init.headers ? init.headers : {}),
        },
    });
    const data = await response.json();
    if (!response.ok || data.error) throw new Error(`Request failed (${response.status}): ${data.message}`);
    return data;
};

const getOrdersActiveGlobal = async () => {
    const url = `${TRONSAVE_API_URL}/v2/orders/active-global?page=${PAGE}&pageSize=${PAGE_SIZE}`;
    const result = await requestJson(url, { method: "GET" });
    return result.data.data;
};

const buildAndSignDelegateTransaction = async (targetOrder) => {
    const tronWeb = new TronWeb({ fullHost: TRONWEB_FULL_HOST });
    const delegatorAddress = tronWeb.address.fromPrivateKey(PRIVATE_KEY);
    if (!delegatorAddress) throw new Error("Invalid PRIVATE_KEY");

    const targetResourceAmount = RESOURCE_AMOUNT_OVERRIDE > 0 ? RESOURCE_AMOUNT_OVERRIDE : targetOrder.remainAmount;
    if (!targetResourceAmount || targetResourceAmount <= 0) {
        throw new Error("Invalid target resource amount. Check target order remainAmount or RESOURCE_AMOUNT_OVERRIDE");
    }

    let freezeRate = FALLBACK_FREEZE_RATE;
    try {
        freezeRate = await getFreezeRateFromAccountAndChainParams(tronWeb, delegatorAddress, INPUT_RESOURCE_TYPE);
    } catch (error) {
        if (!ALLOW_FALLBACK_FREEZE_RATE) {
            throw new Error(
                `Cannot derive freeze rate on node ${TRONWEB_FULL_HOST}: ${error.message}. ` +
                "Check TRONWEB_FULL_HOST matches api-dev network and delegator address is activated."
            );
        }
        console.log(`Cannot derive freeze rate from node, fallback to ${FALLBACK_FREEZE_RATE}`);
    }
    if (!freezeRate || freezeRate <= 0) throw new Error("Invalid freezeRate");

    const delegateTrxAmount = Math.ceil(targetResourceAmount / freezeRate);
    const delegateAmountInSun = delegateTrxAmount * 1000000;
    if (delegateAmountInSun <= 0) throw new Error("Invalid delegateAmountInSun");

    const lockPeriodInBlocks = Math.floor(targetOrder.durationSec / TRON_BLOCK_TIME_IN_SECONDS);
    if (lockPeriodInBlocks <= 0) {
        throw new Error("Invalid lockPeriodInBlocks");
    }
    console.log({ freezeRate, delegateTrxAmount, delegateAmountInSun, lockPeriodInBlocks, tronNode: TRONWEB_FULL_HOST });

    const unsignedTx = await tronWeb.transactionBuilder.delegateResource(
        delegateAmountInSun,
        targetOrder.receiver,
        INPUT_RESOURCE_TYPE,
        delegatorAddress,
        true,
        lockPeriodInBlocks
    );

    return tronWeb.trx.sign(unsignedTx, PRIVATE_KEY);
};

const sellManual = async (orderId, signedTx) => {
    const url = `${TRONSAVE_API_URL}/v2/orders/sell-manual`;
    return requestJson(url, {
        method: "POST",
        body: JSON.stringify({
            orderId,
            signedTx,
            paymentAddress: PAYMENT_ADDRESS || undefined,
            isAllowSellForLockedDelegator: IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR,
        }),
    });
};

const run = async () => {
    assertConfig();

    console.log("Step 1: Get active orders");
    const orders = await getOrdersActiveGlobal();
    const activeOrders = orders.filter(
        (order) => order.status === "Active" && order.remainAmount > 0 && order.resourceType === INPUT_RESOURCE_TYPE
    );
    if (activeOrders.length === 0) throw new Error("No active order found");

    const targetOrder = activeOrders.sort((a, b) => {
        if (b.price !== a.price) return b.price - a.price;
        return b.remainAmount - a.remainAmount;
    })[0];

    console.log("Step 2: Picked highest-price active order", {
        orderId: targetOrder.id,
        receiver: targetOrder.receiver,
        remainAmount: targetOrder.remainAmount,
        price: targetOrder.price,
        resourceType: targetOrder.resourceType,
        durationSec: targetOrder.durationSec,
        inputResourceType: INPUT_RESOURCE_TYPE,
    });

    console.log("Step 3: Build and sign delegate tx with TronWeb");
    const signedTx = await buildAndSignDelegateTransaction(targetOrder);
    console.log({ signedTxId: signedTx.txID || "N/A" });

    console.log("Step 4: Sell manual");
    const result = await sellManual(targetOrder.id, signedTx);
    console.log("Done:", result);
};

run().catch((error) => {
    console.error("Failed:", error.message);
    process.exitCode = 1;
});
```
{% endtab %}

{% tab title="PHP" %}
```php
<?php
/**
 * Copy-and-run manual sell flow (PHP):
 * 1) Fill the config below
 * 2) php src/test/sellManualFlow.guide.php
 */

$API_KEY = "your_api_key"; // change it later, (CONTACT :https://t.me/wantingtrx)
$TRONSAVE_API_URL = "https://api-dev.tronsave.io"; // change it later
$INPUT_RESOURCE_TYPE = "ENERGY"; // change it later: ENERGY | BANDWIDTH
$PAGE = 0; // change it later
$PAGE_SIZE = 10; // change it later
$PAYMENT_ADDRESS = ""; // optional, change it later
$IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR = false; // change it later
$SIGNED_TX_JSON = '{"txID":"your_signed_tx_id","raw_data_hex":"your_raw_data_hex"}'; // change it later

function assertConfig(
    string $apiKey,
    string $resourceType,
    string $signedTxJson
): void {
    if ($apiKey === "" || $apiKey === "your_api_key") {
        throw new RuntimeException("API_KEY is required");
    }
    if (!in_array($resourceType, ["ENERGY", "BANDWIDTH"], true)) {
        throw new RuntimeException("INPUT_RESOURCE_TYPE must be ENERGY or BANDWIDTH");
    }
    if (str_contains($signedTxJson, "your_signed_tx_id")) {
        throw new RuntimeException("Please update SIGNED_TX_JSON");
    }
}

function requestJson(string $url, string $apiKey, string $method = "GET", ?array $body = null): array
{
    $headers = [
        "apikey: {$apiKey}",
        "content-type: application/json",
    ];
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, $method);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    if ($body !== null) {
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($body, JSON_UNESCAPED_SLASHES));
    }
    $raw = curl_exec($ch);
    $httpCode = (int) curl_getinfo($ch, CURLINFO_HTTP_CODE);
    if ($raw === false) {
        $error = curl_error($ch);
        curl_close($ch);
        throw new RuntimeException("cURL error: {$error}");
    }
    curl_close($ch);

    $data = json_decode($raw, true);
    if (!is_array($data)) {
        throw new RuntimeException("Invalid JSON response: {$raw}");
    }
    if ($httpCode >= 400 || !empty($data["error"])) {
        $message = $data["message"] ?? "Unknown error";
        throw new RuntimeException("Request failed ({$httpCode}): {$message}");
    }
    return $data;
}

function getOrdersActiveGlobal(string $baseUrl, string $apiKey, int $page, int $pageSize): array
{
    $query = http_build_query(["page" => $page, "pageSize" => $pageSize]);
    $url = "{$baseUrl}/v2/orders/active-global?{$query}";
    $result = requestJson($url, $apiKey, "GET");
    return $result["data"]["data"] ?? [];
}

function pickHighestPriceOrder(array $orders, string $resourceType): array
{
    $candidates = array_values(array_filter($orders, function ($order) use ($resourceType) {
        return ($order["status"] ?? "") === "Active"
            && (int)($order["remainAmount"] ?? 0) > 0
            && ($order["resourceType"] ?? "") === $resourceType;
    }));
    if (count($candidates) === 0) {
        throw new RuntimeException("No active order found");
    }

    usort($candidates, function ($a, $b) {
        $priceDiff = (float)($b["price"] ?? 0) <=> (float)($a["price"] ?? 0);
        if ($priceDiff !== 0) return $priceDiff;
        return (int)($b["remainAmount"] ?? 0) <=> (int)($a["remainAmount"] ?? 0);
    });
    return $candidates[0];
}

function sellManual(
    string $baseUrl,
    string $apiKey,
    string $orderId,
    array $signedTx,
    string $paymentAddress,
    bool $allowLockedDelegator
): array {
    $url = "{$baseUrl}/v2/orders/sell-manual";
    $payload = [
        "orderId" => $orderId,
        "signedTx" => $signedTx,
        "paymentAddress" => $paymentAddress !== "" ? $paymentAddress : null,
        "isAllowSellForLockedDelegator" => $allowLockedDelegator,
    ];
    return requestJson($url, $apiKey, "POST", $payload);
}

try {
    assertConfig($API_KEY, $INPUT_RESOURCE_TYPE, $SIGNED_TX_JSON);
    $signedTx = json_decode($SIGNED_TX_JSON, true);
    if (!is_array($signedTx)) {
        throw new RuntimeException("SIGNED_TX_JSON must be valid JSON object");
    }

    echo "Step 1: Get active orders\n";
    $orders = getOrdersActiveGlobal($TRONSAVE_API_URL, $API_KEY, $PAGE, $PAGE_SIZE);

    $targetOrder = pickHighestPriceOrder($orders, $INPUT_RESOURCE_TYPE);
    echo "Step 2: Picked highest-price active order\n";
    print_r([
        "orderId" => $targetOrder["id"] ?? null,
        "receiver" => $targetOrder["receiver"] ?? null,
        "remainAmount" => $targetOrder["remainAmount"] ?? null,
        "price" => $targetOrder["price"] ?? null,
        "resourceType" => $targetOrder["resourceType"] ?? null,
        "durationSec" => $targetOrder["durationSec"] ?? null,
        "inputResourceType" => $INPUT_RESOURCE_TYPE,
    ]);

    echo "Step 3: Use pre-signed delegate tx from SIGNED_TX_JSON\n";
    print_r(["signedTxId" => $signedTx["txID"] ?? "N/A"]);

    echo "Step 4: Sell manual\n";
    $result = sellManual(
        $TRONSAVE_API_URL,
        $API_KEY,
        (string)$targetOrder["id"],
        $signedTx,
        $PAYMENT_ADDRESS,
        $IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR
    );
    echo "Done:\n";
    print_r($result);
} catch (Throwable $e) {
    fwrite(STDERR, "Failed: " . $e->getMessage() . PHP_EOL);
    exit(1);
}
```
{% endtab %}

{% tab title="Python" %}
```python
#!/usr/bin/env python3
"""
Copy-and-run manual sell flow (Python):
1) Fill the config below
2) python3 src/test/sellManualFlow.guide.py
"""

import json
import urllib.error
import urllib.parse
import urllib.request
from typing import Any, Dict, List

API_KEY = "your_api_key"  # change it later, (CONTACT :https://t.me/wantingtrx)
TRONSAVE_API_URL = "https://api-dev.tronsave.io"  # change it later
INPUT_RESOURCE_TYPE = "ENERGY"  # change it later: ENERGY | BANDWIDTH
PAGE = 0  # change it later
PAGE_SIZE = 10  # change it later
PAYMENT_ADDRESS = ""  # optional, change it later
IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR = False  # change it later
SIGNED_TX_JSON = '{"txID":"your_signed_tx_id","raw_data_hex":"your_raw_data_hex"}'  # change it later


def assert_config() -> None:
    if not API_KEY or API_KEY == "your_api_key":
        raise ValueError("API_KEY is required")
    if INPUT_RESOURCE_TYPE not in ("ENERGY", "BANDWIDTH"):
        raise ValueError("INPUT_RESOURCE_TYPE must be ENERGY or BANDWIDTH")
    if "your_signed_tx_id" in SIGNED_TX_JSON:
        raise ValueError("Please update SIGNED_TX_JSON")


def request_json(url: str, method: str = "GET", body: Dict[str, Any] | None = None) -> Dict[str, Any]:
    payload = None
    if body is not None:
        payload = json.dumps(body).encode("utf-8")
    req = urllib.request.Request(
        url=url,
        method=method,
        data=payload,
        headers={
            "apikey": API_KEY,
            "content-type": "application/json",
        },
    )
    try:
        with urllib.request.urlopen(req) as response:
            data = json.loads(response.read().decode("utf-8"))
            if response.status >= 400 or data.get("error"):
                raise RuntimeError(f"Request failed ({response.status}): {data.get('message')}")
            return data
    except urllib.error.HTTPError as e:
        raw = e.read().decode("utf-8", errors="ignore")
        raise RuntimeError(f"HTTPError ({e.code}): {raw}") from e


def get_orders_active_global() -> List[Dict[str, Any]]:
    query = urllib.parse.urlencode({"page": PAGE, "pageSize": PAGE_SIZE})
    url = f"{TRONSAVE_API_URL}/v2/orders/active-global?{query}"
    result = request_json(url, "GET")
    return result["data"]["data"]


def pick_highest_price_order(orders: List[Dict[str, Any]]) -> Dict[str, Any]:
    candidates = [
        o for o in orders
        if o.get("status") == "Active"
        and int(o.get("remainAmount", 0)) > 0
        and o.get("resourceType") == INPUT_RESOURCE_TYPE
    ]
    if not candidates:
        raise RuntimeError("No active order found")
    candidates.sort(key=lambda o: (float(o.get("price", 0)), int(o.get("remainAmount", 0))), reverse=True)
    return candidates[0]


def sell_manual(order_id: str, signed_tx: Dict[str, Any]) -> Dict[str, Any]:
    url = f"{TRONSAVE_API_URL}/v2/orders/sell-manual"
    payload = {
        "orderId": order_id,
        "signedTx": signed_tx,
        "paymentAddress": PAYMENT_ADDRESS or None,
        "isAllowSellForLockedDelegator": IS_ALLOW_SELL_FOR_LOCKED_DELEGATOR,
    }
    return request_json(url, "POST", payload)


def main() -> None:
    assert_config()
    signed_tx = json.loads(SIGNED_TX_JSON)

    print("Step 1: Get active orders")
    orders = get_orders_active_global()

    target = pick_highest_price_order(orders)
    print("Step 2: Picked highest-price active order", {
        "orderId": target.get("id"),
        "receiver": target.get("receiver"),
        "remainAmount": target.get("remainAmount"),
        "price": target.get("price"),
        "resourceType": target.get("resourceType"),
        "durationSec": target.get("durationSec"),
        "inputResourceType": INPUT_RESOURCE_TYPE,
    })

    print("Step 3: Use pre-signed delegate tx from SIGNED_TX_JSON")
    print({"signedTxId": signed_tx.get("txID", "N/A")})

    print("Step 4: Sell manual")
    result = sell_manual(target["id"], signed_tx)
    print("Done:", result)


if __name__ == "__main__":
    main()
```
{% endtab %}
{% endtabs %}

## 后续步骤

* 初次使用 API？请从 [快速开始](../quickstart.md) 和 [身份验证](../authentication.md) 开始。
* 了解 [能量与带宽](../../concepts/energy-and-bandwidth.md) 的区别，并查看 [订单类型](../../concepts/order-types.md)。
* 想买入而不是卖出？请参见 [购买资源](buy-resources/README.md)。
