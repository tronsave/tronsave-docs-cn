---
description: 用于通过 API 密钥购买能量的旧版 v0 REST 端点——账户信息、订单簿、TRX 预估、创建订单、订单详情、订单历史以及钱包激活。
---

# 购买 — API 密钥

{% hint style="warning" %}
本页面记录的是 **旧版 v0 API**。新的集成应使用当前的 API。受支持的端点请参阅 [通过 API 密钥购买](../api-reference/buy-resources/api-key/)。下方的 v0 端点对现有集成仍然可用。
{% endhint %}

本页面上的所有端点都通过在 `apikey` 请求头中发送的 **API 密钥** 进行身份验证。该密钥与你的 TronSave [内部账户](../authentication.md) 绑定，订单从该账户余额中支付。获取密钥请参阅 [身份验证](../authentication.md)。

**基础 URL（主网）：** `https://api.tronsave.io`

{% hint style="info" %}
**TRON Nile（测试网）：** 将基础 URL 替换为 `https://api-dev.tronsave.io`。

* 预估 TRX：<mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v0/estimate-trx`
* 创建订单：<mark style="color:orange;">`POST`</mark> `https://api-dev.tronsave.io/v0/internal-buy-energy`
* 获取内部账户信息：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v0/user-info`
* 获取内部账户订单历史：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v0/orders`
* 获取单个订单详情：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v0/orders/:id`
* 获取订单簿：<mark style="color:blue;">`GET`</mark> `https://api-dev.tronsave.io/v0/order-book`
{% endhint %}

下方每个端点的速率限制为每 **1** 秒 **15** 次请求。

***

## 获取内部账户信息

通过 API 密钥获取账户信息。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v0/user-info`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 响应

<table><thead><tr><th width="220">字段</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>id</code></td><td>string</td><td>内部账户 id。</td></tr><tr><td><code>balance</code></td><td>string</td><td>内部账户余额，以 SUN 计。</td></tr><tr><td><code>represent_address</code></td><td>string</td><td>代表内部账户作为订单的请求方。</td></tr><tr><td><code>deposit_address</code></td><td>string</td><td>内部账户的充值地址。</td></tr></tbody></table>

```json
{
    "id": "user_id",
    "balance": "1000000",
    "represent_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
    "deposit_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999"
}
```

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl --location 'https://api.tronsave.io/v0/user-info' \
  --header 'apikey: YOUR_API_KEY'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const getAccountInfo = async () => {
  const url = "https://api.tronsave.io/v0/user-info";
  const res = await fetch(url, {
    headers: { apikey: "YOUR_API_KEY" },
  });
  const response = await res.json();
  // {
  //   "id": "user_id",
  //   "balance": "1000000",
  //   "represent_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
  //   "deposit_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999"
  // }
  return response;
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/user-info"
headers = {"apikey": "YOUR_API_KEY"}

response = requests.get(url, headers=headers)
print(response.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetAccountInfo {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://api.tronsave.io/v0/user-info"))
                .header("apikey", "YOUR_API_KEY")
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
	req, _ := http.NewRequest("GET", "https://api.tronsave.io/v0/user-info", nil)
	req.Header.Set("apikey", "YOUR_API_KEY")

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
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::blocking::Client::new();
    let response = client
        .get("https://api.tronsave.io/v0/user-info")
        .header("apikey", "YOUR_API_KEY")
        .send()?;

    println!("{}", response.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

***

## 获取订单簿

通过 API 密钥获取订单簿。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v0/order-book`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 查询参数

<table><thead><tr><th width="220">名称</th><th width="108">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>address</code></td><td>string</td><td>能量接收地址。</td></tr><tr><td><code>min_delegate_amount</code></td><td>number</td><td>单个供应商代理（委托）的最小能量数量。</td></tr><tr><td><code>duration_sec</code></td><td>number</td><td>订单时长，以秒计。</td></tr></tbody></table>

### 响应

```typescript
{
    price: number, // price in SUN
    available_energy_amount: number, // available resource amount at this price
}[]
```

```json
[
    {
        "price": -1,
        "available_energy_amount": 177451
    },
    {
        "price": 30,
        "available_energy_amount": 331088
    },
    {
        "price": 35,
        "available_energy_amount": 2841948
    }
]
```

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl 'https://api.tronsave.io/v0/order-book?address=YOUR_TRON_ADDRESS&min_delegate_amount=100000&duration_sec=86400' \
  --header 'apikey: YOUR_API_KEY'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const getOrderBook = async () => {
  const receiverAddress = "YOUR_TRON_ADDRESS";
  const url = `https://api.tronsave.io/v0/order-book?address=${receiverAddress}&min_delegate_amount=100000&duration_sec=86400`;
  const res = await fetch(url, {
    headers: { apikey: "YOUR_API_KEY" },
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/order-book"
headers = {"apikey": "YOUR_API_KEY"}
params = {
    "address": "YOUR_TRON_ADDRESS",
    "min_delegate_amount": 100000,
    "duration_sec": 86400,
}

response = requests.get(url, headers=headers, params=params)
print(response.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetOrderBook {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v0/order-book"
                + "?address=YOUR_TRON_ADDRESS"
                + "&min_delegate_amount=100000"
                + "&duration_sec=86400";

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("apikey", "YOUR_API_KEY")
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
	url := "https://api.tronsave.io/v0/order-book" +
		"?address=YOUR_TRON_ADDRESS" +
		"&min_delegate_amount=100000" +
		"&duration_sec=86400"

	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", "YOUR_API_KEY")

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
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::blocking::Client::new();
    let response = client
        .get("https://api.tronsave.io/v0/order-book")
        .header("apikey", "YOUR_API_KEY")
        .query(&[
            ("address", "YOUR_TRON_ADDRESS"),
            ("min_delegate_amount", "100000"),
            ("duration_sec", "86400"),
        ])
        .send()?;

    println!("{}", response.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

***

## 预估 TRX

在创建订单之前，预估一笔购买的 TRX 成本。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/estimate-trx`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 请求体

<table><thead><tr><th width="180">字段</th><th width="95">位置</th><th width="120">类型</th><th width="101">必填</th><th>说明</th></tr></thead><tbody><tr><td><code>amount</code></td><td>body</td><td>number</td><td>true</td><td>资源数量。</td></tr><tr><td><code>buy_energy_type</code></td><td>body</td><td>string, number</td><td>true</td><td><p><code>"FAST"</code>、<code>"MEDIUM"</code>、<code>"SLOW"</code>，或一个数字：</p><p><br>- <strong>FAST</strong>：如果市场可成交比例 = 100%，则 FAST = MEDIUM。如果市场可成交比例 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交比例 = 0%，则 FAST = SLOW + 20。</p><p>- <strong>MEDIUM</strong>：为该订单实现最大市场成交量的最低价格。如果市场可成交比例 = 0%，则 MEDIUM = SLOW + 10。</p><p>- <strong>SLOW</strong>：该订单可设置的最低价格。</p><p>- 如果价格为数字，则价格单位为 SUN。</p></td></tr><tr><td><code>duration_millisec</code></td><td>body</td><td>number</td><td>true</td><td>所购资源的时长，以毫秒计。</td></tr><tr><td><code>request_address</code></td><td>body</td><td>string</td><td>false</td><td>请求方的地址。</td></tr><tr><td><code>target_address</code></td><td>body</td><td>string</td><td>false</td><td>资源接收地址。</td></tr><tr><td><code>is_partial</code></td><td>body</td><td>boolean</td><td>false</td><td>是否允许订单被部分成交。</td></tr></tbody></table>

#### 请求体示例

```json
{
      "amount": 100000,
      "buy_energy_type": "MEDIUM",
      "duration_millisec": 259200000
}
```

### 响应

<table><thead><tr><th width="185">字段</th><th width="130">类型</th><th width="110">必填</th><th>说明</th></tr></thead><tbody><tr><td><code>unit_price</code></td><td>number</td><td>true</td><td>符合你 <code>buy_energy_type</code> 的能量价格，以 SUN 计。</td></tr><tr><td><code>duration_millisec</code></td><td>number</td><td>true</td><td>时长，以毫秒计。</td></tr><tr><td><code>available_energy</code></td><td>number</td><td>true</td><td>TronSave 市场上匹配 <code>unit_price</code> 的可用能量总量。</td></tr><tr><td><code>estimate_trx</code></td><td>number</td><td>true</td><td>在 <code>duration_millisec</code> 时长内，以 <code>unit_price</code> 支付全部 <code>available_energy</code> 的预估 TRX 总额。</td></tr></tbody></table>

```json
{
    "unit_price": 45,
    "duration_millisec": 259200000,
    "available_energy": 4298470,
    "estimate_trx": 13500000
}
```

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl --location 'https://api.tronsave.io/v0/estimate-trx' \
  --header 'apikey: YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "amount": 100000,
    "buy_energy_type": "MEDIUM",
    "duration_millisec": 259200000
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const estimateTrx = async () => {
  const url = "https://api.tronsave.io/v0/estimate-trx";
  const body = {
    amount: 100000,
    buy_energy_type: "MEDIUM", // price in SUN, or "SLOW" | "MEDIUM" | "FAST"
    duration_millisec: 259200000,
  };
  const res = await fetch(url, {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "content-type": "application/json",
    },
    body: JSON.stringify(body),
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/estimate-trx"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "amount": 100000,
    "buy_energy_type": "MEDIUM",
    "duration_millisec": 259200000,
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

public class EstimateTrx {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v0/estimate-trx";
        String body = """
            {
              "amount": 100000,
              "buy_energy_type": "MEDIUM",
              "duration_millisec": 259200000
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
	url := "https://api.tronsave.io/v0/estimate-trx"
	body := []byte(`{
		"amount": 100000,
		"buy_energy_type": "MEDIUM",
		"duration_millisec": 259200000
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
    let url = "https://api.tronsave.io/v0/estimate-trx";
    let body = json!({
        "amount": 100000,
        "buy_energy_type": "MEDIUM",
        "duration_millisec": 259200000_u64
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

***

## 购买能量（创建订单）

通过 API 密钥创建一个新的购买能量订单。订单从你的内部账户余额中支付。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/internal-buy-energy`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="140">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 请求体

<table><thead><tr><th width="270">名称</th><th width="93">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>resource_type</code><mark style="color:red;">*</mark></td><td>String</td><td><code>"ENERGY"</code>。</td></tr><tr><td><code>buy_energy_type</code><mark style="color:red;">*</mark></td><td>String</td><td><p>- <strong>FAST</strong>：如果市场可成交比例 = 100%，则 FAST = MEDIUM。如果市场可成交比例 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交比例 = 0%，则 FAST = SLOW + 20。</p><p>- <strong>MEDIUM</strong>：为该订单实现最大市场成交量的最低价格。如果市场可成交比例 = 0%，则 MEDIUM = SLOW + 10。</p><p>- <strong>SLOW</strong>：该订单可设置的最低价格。</p><p>- 如果价格为数字，则价格单位为 SUN。</p></td></tr><tr><td><code>amount</code><mark style="color:red;">*</mark></td><td>Number</td><td>要购买的资源数量。</td></tr><tr><td><code>allow_partial_fill</code><mark style="color:red;">*</mark></td><td>Boolean</td><td>如果为 <code>true</code>，订单可由多个代理方（委托方）成交，相比 <code>false</code> 更易于成交。大于 200k 能量的数量可设置此参数。</td></tr><tr><td><code>target_address</code><mark style="color:red;">*</mark></td><td>String</td><td>接收资源的地址。</td></tr><tr><td><code>duration_millisec</code></td><td>Number</td><td>订单时长，以毫秒计。默认值：259200000（3 天）。</td></tr><tr><td><code>sponsor</code></td><td>String</td><td>赞助码。</td></tr><tr><td><code>only_create_when_fulfilled</code></td><td>Boolean</td><td><p><code>true</code> => 仅在订单可被完全成交时才创建。</p><p><code>false</code> => 即使订单无法被成交也会创建。</p><p>默认值：<code>false</code>。</p></td></tr><tr><td><code>max_price_accepted</code></td><td>Number</td><td>仅当预估价格低于此值时才创建订单。</td></tr><tr><td><code>add_order_incomplete</code></td><td>Boolean</td><td><p><code>true</code> => 仅当订单列表中没有相同参数的未完成订单时才创建订单。</p><p><code>false</code> => 即使订单列表中没有相同参数的未完成订单也会创建订单。</p><p>默认值：<code>false</code>。</p></td></tr></tbody></table>

<sub>\* 必填。</sub>

#### 请求体示例

```json
{
    "resource_type": "ENERGY",
    "buy_energy_type": "FAST",
    "amount": 100000,
    "allow_partial_fill": true,
    "target_address": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
    "duration_millisec": 86400000,
    "only_create_when_fulfilled": false,
    "max_price_accepted": 100,
    "add_order_incomplete": false
}
```

### 响应

{% tabs %}
{% tab title="200: OK Success" %}
成功时返回订单 id。

```json
{
      "order_id": "651d2306e55c073f6ca0992e",
      "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "resource_amount": 100000,
      "resource_type": "ENERGY",
      "remain_amount": 0,
      "price": 67.5,
      "duration": 3600,
      "allow_partial_fill": true,
      "payout_amount": 6750000,
      "fulfilled_percent": 100
}
```
{% endtab %}

{% tab title="400: 请求错误" %}
```json
{
    "MISSING_PARAMS": "Missing some params in body",
    "INVALID_PARAMS": "some params is invalid",
    "ORDER_BUY_ENERGY_AMOUNT_TOO_SMALL": "order amount too small. Cannot less than 40000",
    "CANNOT_SET_PARTIAL_FULFILLED": "order amount less than 100000 cannot set partial fulfilled",
    "INTERNAL_ACCOUNT_NOT_FOUND": "internal account not exists",
    "ORDER_BUY_ENERGY_CAN_NOT_CREATE": "Something error occurs when create order, cannot create, please try later",
    "INTERNAL_BALANCE_ACCOUNT_TOO_LOW": "Balance is not enough"
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
curl --location 'https://api.tronsave.io/v0/internal-buy-energy' \
  --header 'apikey: YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "resource_type": "ENERGY",
    "amount": 40000,
    "buy_energy_type": "MEDIUM",
    "duration_millisec": 3600000,
    "target_address": "YOUR_TRON_ADDRESS",
    "allow_partial_fill": false,
    "only_create_when_fulfilled": false,
    "max_price_accepted": 100,
    "add_order_incomplete": false
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const buyEnergy = async () => {
  const url = "https://api.tronsave.io/v0/internal-buy-energy";
  const body = {
    resource_type: "ENERGY",
    buy_energy_type: "MEDIUM", // price in SUN, or "SLOW" | "MEDIUM" | "FAST"
    amount: 100000, // amount of resource to buy
    allow_partial_fill: true,
    target_address: "YOUR_TRON_ADDRESS",
    duration_millisec: 86400000, // order duration in ms. Default: 259200000 (3 days)
    only_create_when_fulfilled: false,
    max_price_accepted: 100,
    add_order_incomplete: false,
  };
  const res = await fetch(url, {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "content-type": "application/json",
    },
    body: JSON.stringify(body),
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/internal-buy-energy"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "resource_type": "ENERGY",
    "buy_energy_type": "MEDIUM",  # price in SUN, or "SLOW" | "MEDIUM" | "FAST"
    "amount": 100000,
    "allow_partial_fill": True,
    "target_address": "YOUR_TRON_ADDRESS",
    "duration_millisec": 86400000,
    "only_create_when_fulfilled": False,
    "max_price_accepted": 100,
    "add_order_incomplete": False,
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

public class BuyEnergy {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v0/internal-buy-energy";
        String body = """
            {
              "resource_type": "ENERGY",
              "buy_energy_type": "MEDIUM",
              "amount": 100000,
              "allow_partial_fill": true,
              "target_address": "YOUR_TRON_ADDRESS",
              "duration_millisec": 86400000,
              "only_create_when_fulfilled": false,
              "max_price_accepted": 100,
              "add_order_incomplete": false
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
	url := "https://api.tronsave.io/v0/internal-buy-energy"
	body := []byte(`{
		"resource_type": "ENERGY",
		"buy_energy_type": "MEDIUM",
		"amount": 100000,
		"allow_partial_fill": true,
		"target_address": "YOUR_TRON_ADDRESS",
		"duration_millisec": 86400000,
		"only_create_when_fulfilled": false,
		"max_price_accepted": 100,
		"add_order_incomplete": false
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
    let url = "https://api.tronsave.io/v0/internal-buy-energy";
    let body = json!({
        "resource_type": "ENERGY",
        "buy_energy_type": "MEDIUM",
        "amount": 100000,
        "allow_partial_fill": true,
        "target_address": "YOUR_TRON_ADDRESS",
        "duration_millisec": 86400000_u64,
        "only_create_when_fulfilled": false,
        "max_price_accepted": 100,
        "add_order_incomplete": false
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

***

## 获取单个订单详情

通过 API 密钥获取单个订单的详情。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v0/orders/:id`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 响应

```json
{
    "id": "string",                 // id of order
    "requester": "string",          // the address representing the order owner
    "target": "string",             // the address that receives the resource
    "resource_amount": "number",    // the amount of resource
    "resource_type": "string",      // the resource type is "ENERGY"
    "remain_amount": "number",      // the remaining amount the system can match
    "price": "number",              // price unit is SUN
    "duration": "number",           // rent duration, in seconds
    "allow_partial_fill": "boolean",// allow the order to be filled partially or not
    "payout_amount": "number",      // total payout of this order
    "fulfilled_percent": "number",  // fill progress, 0-100
    "matched_delegates": [          // all matched delegates for this order
       {
         "delegator": "string",     // the address that delegates resource to target address
         "amount": "number",        // the amount of resource delegated
         "txid": "string"           // the on-chain transaction id
       }
    ]
}
```

```json
{
      "id": "651d2306e55c073f6ca0992e",
      "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
      "resource_amount": 100000,
      "resource_type": "ENERGY",
      "remain_amount": 0,
      "price": 67.5,
      "duration": 3600,
      "allow_partial_fill": true,
      "payout_amount": 6750000,
      "fulfilled_percent": 100,
      "matched_delegates": [
          {
              "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
              "amount": 100000,
              "txid": "transaction_id_1"
          }
      ]
}
```

{% hint style="info" %}
此端点的规范路径为 `GET https://api.tronsave.io/v0/orders/:id`，其中 `:id` 为订单 id。将 id 作为路径片段传递（而非查询参数）。下方所有示例均使用此路径。
{% endhint %}

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl --location 'https://api.tronsave.io/v0/orders/ORDER_ID' \
  --header 'apikey: YOUR_API_KEY'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const getOneOrderDetails = async (orderId) => {
  const url = `https://api.tronsave.io/v0/orders/${orderId}`;
  const res = await fetch(url, {
    headers: { apikey: "YOUR_API_KEY" },
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

order_id = "ORDER_ID"
url = f"https://api.tronsave.io/v0/orders/{order_id}"
headers = {"apikey": "YOUR_API_KEY"}

response = requests.get(url, headers=headers)
print(response.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetOneOrderDetails {
    public static void main(String[] args) throws Exception {
        String orderId = "ORDER_ID";
        String url = "https://api.tronsave.io/v0/orders/" + orderId;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("apikey", "YOUR_API_KEY")
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
	orderID := "ORDER_ID"
	url := "https://api.tronsave.io/v0/orders/" + orderID

	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", "YOUR_API_KEY")

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
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let order_id = "ORDER_ID";
    let url = format!("https://api.tronsave.io/v0/orders/{order_id}");

    let client = reqwest::blocking::Client::new();
    let response = client
        .get(&url)
        .header("apikey", "YOUR_API_KEY")
        .send()?;

    println!("{}", response.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

***

## 获取内部账户订单历史

按创建时间排序获取多个订单。默认值：返回最新的 10 个订单。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v0/orders`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

### 请求头

<table><thead><tr><th width="150">名称</th><th width="110">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

### 查询参数

<table><thead><tr><th width="192">名称</th><th width="182">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>page</code></td><td>Integer</td><td>从 0 开始。默认值：0。</td></tr><tr><td><code>pageSize</code></td><td>Integer</td><td>默认值：10。</td></tr></tbody></table>

### 响应

```json
{
   "data": [
     {
       "id": "string",                 // id of order
       "requester": "string",          // the address representing the order owner
       "target": "string",             // the address that receives the resource
       "resource_amount": "number",    // the amount of resource
       "resource_type": "string",      // the resource type is "ENERGY"
       "remain_amount": "number",      // the remaining amount the system can match
       "price": "number",              // price unit is SUN
       "duration": "number",           // rent duration, in seconds
       "allow_partial_fill": "boolean",// allow the order to be filled partially or not
       "payout_amount": "number",      // total payout of this order
       "fulfilled_percent": "number",  // fill progress, 0-100
       "matched_delegates": [          // all matched delegates for this order
         {
           "delegator": "string",      // the address that delegates resource to target address
           "amount": "number",         // the amount of resource delegated
           "txid": "string"            // the on-chain transaction id
         }
       ]
     }
   ],
   "total": "number"
}
```

```json
{
    "data": [
        {
            "id": "651d0e5d8248d002ea08a231",
            "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "resource_amount": 100000,
            "resource_type": "ENERGY",
            "remain_amount": 100000,
            "price": 45,
            "duration": 259200,
            "allow_partial_fill": false,
            "payout_amount": 4500000,
            "fulfilled_percent": 100,
            "matched_delegates": [
                {
                    "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
                    "amount": 100000,
                    "txid": "transaction_id_1"
                }
            ]
        },
        {
            "id": "651d2306e55c073f6ca0992e",
            "requester": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "target": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
            "resource_amount": 100000,
            "resource_type": "ENERGY",
            "remain_amount": 0,
            "price": 67.5,
            "duration": 3600,
            "allow_partial_fill": true,
            "payout_amount": 6750000,
            "fulfilled_percent": 100,
            "matched_delegates": [
                {
                    "delegator": "TKVSaJQDWeKFSEXmA44pjxduGTxy888888",
                    "amount": 100000,
                    "txid": "transaction_id_2"
                }
            ]
        }
    ],
    "total": 2
}
```

### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl --location 'https://api.tronsave.io/v0/orders' \
  --header 'apikey: YOUR_API_KEY'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const getOrderHistory = async () => {
  const url = "https://api.tronsave.io/v0/orders";
  const res = await fetch(url, {
    headers: { apikey: "YOUR_API_KEY" },
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/orders"
headers = {"apikey": "YOUR_API_KEY"}
params = {"page": 0, "pageSize": 10}

response = requests.get(url, headers=headers, params=params)
print(response.json())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetOrderHistory {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v0/orders?page=0&pageSize=10";

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("apikey", "YOUR_API_KEY")
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
	url := "https://api.tronsave.io/v0/orders?page=0&pageSize=10"

	req, _ := http.NewRequest("GET", url, nil)
	req.Header.Set("apikey", "YOUR_API_KEY")

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
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = reqwest::blocking::Client::new();
    let response = client
        .get("https://api.tronsave.io/v0/orders")
        .header("apikey", "YOUR_API_KEY")
        .query(&[("page", "0"), ("pageSize", "10")])
        .send()?;

    println!("{}", response.text()?);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

***

## 激活钱包地址

在激活任何钱包地址之前，你必须先检查其当前的激活状态。只有状态为 **0**（未激活）的地址才应提交进行激活。

### 步骤 1 — 检查激活状态

检查一个或多个 TRON 钱包地址的激活状态。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/helper/is-active-address-check`**

#### 请求体

<table><thead><tr><th width="140">字段</th><th width="150">类型</th><th width="110">必填</th><th>说明</th></tr></thead><tbody><tr><td><code>addresses</code></td><td>array&#x3C;string></td><td>true</td><td>要检查的 TRON 钱包地址列表。</td></tr></tbody></table>

```json
{
  "addresses": [
    "address1",
    "address2",
    "address3"
  ]
}
```

#### 响应

返回一个整数数组，其中每个值对应请求中相同索引位置的地址。

<table><thead><tr><th width="100">值</th><th width="150">状态</th><th>说明</th><th>操作</th></tr></thead><tbody><tr><td>0</td><td>未激活</td><td>该地址存在但从未被激活。</td><td>继续执行步骤 2 进行激活。</td></tr><tr><td>1</td><td>合约</td><td>该地址是一个智能合约。</td><td>跳过——不适用激活。</td></tr><tr><td>2</td><td>已激活</td><td>该地址已被激活。</td><td>跳过——无需操作。</td></tr><tr><td>3</td><td>获取失败</td><td>无法从网络获取状态。</td><td>稍后重试或检查连接。</td></tr><tr><td>4</td><td>无效地址</td><td>地址格式无效。</td><td>核实并更正该地址。</td></tr></tbody></table>

{% hint style="info" %}
筛选结果数组，收集所有对应状态值等于 `0` 的地址。这些地址将用于 **步骤 2**。
{% endhint %}

#### 示例

{% tabs %}
{% tab title="Body" %}
```json
{
  "addresses": [
    "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
    "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
    "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii"
  ]
}
```
{% endtab %}

{% tab title="Response" %}
```json
[
    0,
    2,
    1
]
```
{% endtab %}
{% endtabs %}

### 步骤 2 — 激活钱包

提交一批 **未激活** 的地址进行激活。此端点会为提供的每个地址创建激活请求。

{% hint style="info" %}
**费用：** 每次激活花费 **每个地址 1.5 TRX**。在调用此端点之前，请确保你的账户有足够的余额。
{% endhint %}

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v0/helper/multi-active-address`**

#### 请求头

<table><thead><tr><th width="150">名称</th><th width="140">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表你内部账户的 TronSave API 密钥。</td></tr></tbody></table>

<sub>\* 必填。</sub>

#### 请求体

仅包含步骤 1 中状态为 `0` 的地址。

<table><thead><tr><th width="130">字段</th><th width="130">类型</th><th width="106">必填</th><th>说明</th></tr></thead><tbody><tr><td><code>addresses</code></td><td>array&#x3C;string></td><td>true</td><td>要激活的未激活 TRON 地址列表。</td></tr></tbody></table>

```json
{
  "addresses": [
    "address1",
    "address2",
    "address3"
  ]
}
```

#### 响应

<table><thead><tr><th width="121">字段</th><th width="129">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>message</code></td><td>string</td><td>确认消息，指示创建了多少个激活请求。</td></tr></tbody></table>

```json
{
  "message": "Success create 3 active address request(s)"
}
```

#### 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl --location 'https://api.tronsave.io/v0/helper/multi-active-address' \
  --header 'apikey: YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data '{
    "addresses": [
      "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
      "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
      "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii"
    ]
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const activateAddresses = async () => {
  const url = "https://api.tronsave.io/v0/helper/multi-active-address";
  const body = {
    addresses: [
      "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
      "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
      "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii",
    ],
  };
  const res = await fetch(url, {
    method: "POST",
    headers: {
      apikey: "YOUR_API_KEY",
      "content-type": "application/json",
    },
    body: JSON.stringify(body),
  });
  return res.json();
};
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

url = "https://api.tronsave.io/v0/helper/multi-active-address"
headers = {
    "apikey": "YOUR_API_KEY",
    "Content-Type": "application/json",
}
body = {
    "addresses": [
        "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
        "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
        "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii",
    ]
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

public class ActivateAddresses {
    public static void main(String[] args) throws Exception {
        String url = "https://api.tronsave.io/v0/helper/multi-active-address";
        String body = """
            {
              "addresses": [
                "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
                "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
                "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii"
              ]
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
	url := "https://api.tronsave.io/v0/helper/multi-active-address"
	body := []byte(`{
		"addresses": [
			"TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
			"TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
			"TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii"
		]
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
    let url = "https://api.tronsave.io/v0/helper/multi-active-address";
    let body = json!({
        "addresses": [
            "TLRkWWsDDikzXxGKpVXpEzRH8bpCoL2222",
            "TZ2RPVoKVoqxAkhhTUycni8t42N2dBssss",
            "TYUdv83jM61ZQctjEXeiQNgTCXSebqRiii"
        ]
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

## 后续步骤

* 迁移到当前 API：[通过 API 密钥购买](../api-reference/buy-resources/api-key/)。
* 了解 [身份验证](../authentication.md) 以及如何获取 API 密钥。
* 在下单前查看 [订单类型](../../concepts/order-types.md)。
