---
description: 使用订单的 orderId 获取特定订单的详细信息和当前状态。
---

# 获取订单详情

使用订单的 `orderId` 获取特定订单的详细信息和当前状态。响应包含订单参数、履约进度，以及与该订单匹配的每一笔链上代理(委托)。

## 端点

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/order/:id`**

`:id` 路径段是创建订单时返回的 `orderId`。

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="120">名称</th><th width="100">类型</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表内部账户的 TronSave API 密钥。请参阅 <a href="../../../authentication.md">身份验证</a> 以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

## 路径参数

<table>
<thead>
<tr><th width="120">名称</th><th width="100">类型</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td><code>id</code><mark style="color:red;">*</mark></td><td>String</td><td>要获取的订单的 <code>orderId</code>。</td></tr>
</tbody>
</table>

## 请求体

此端点不接受请求体——在路径中传入 `apikey` 请求头和订单 `id` 即可。

## 响应

成功的响应返回 `error: false`，并在 `data` 中返回订单详情。

<table>
<thead>
<tr><th width="220">字段</th><th width="110">类型</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td><code>data.id</code></td><td>String</td><td>订单的 ID。</td></tr>
<tr><td><code>data.requester</code></td><td>String</td><td>代表订单所有者的地址。</td></tr>
<tr><td><code>data.receiver</code></td><td>String</td><td>接收资源的地址。</td></tr>
<tr><td><code>data.resourceAmount</code></td><td>Number</td><td>资源数量。</td></tr>
<tr><td><code>data.resourceType</code></td><td>String</td><td>资源类型，为 <code>ENERGY</code> 或 <code>BANDWIDTH</code>。</td></tr>
<tr><td><code>data.remainAmount</code></td><td>Number</td><td>系统仍可匹配的剩余数量。</td></tr>
<tr><td><code>data.price</code></td><td>Number</td><td>价格，单位为 SUN。</td></tr>
<tr><td><code>data.durationSec</code></td><td>Number</td><td>租赁时长，单位为秒。</td></tr>
<tr><td><code>data.orderType</code></td><td>String</td><td>订单类型，为 <code>NORMAL</code> 或 <code>EXTEND</code>。</td></tr>
<tr><td><code>data.allowPartialFill</code></td><td>Boolean</td><td>订单是否可被部分成交。</td></tr>
<tr><td><code>data.payoutAmount</code></td><td>Number</td><td>此订单的总支付金额。</td></tr>
<tr><td><code>data.fulfilledPercent</code></td><td>Number</td><td>成交进度百分比，0–100。</td></tr>
<tr><td><code>data.delegates</code></td><td>Array</td><td>此订单所有已匹配的代理(委托)。</td></tr>
<tr><td><code>data.delegates[].delegator</code></td><td>String</td><td>将资源代理(委托)给目标地址的地址。</td></tr>
<tr><td><code>data.delegates[].amount</code></td><td>Number</td><td>已代理(委托)的资源数量。</td></tr>
<tr><td><code>data.delegates[].txid</code></td><td>String</td><td>链上交易 ID。</td></tr>
</tbody>
</table>

### 200: OK

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "id":  string, // id of order
        "requester": string, // the address represents the order owner
        "receiver": string, // the address of the resource that is received 
        "resourceAmount": number, // the amount of resource
        "resourceType": string, // the resource type is "ENERGY" or "BANDWIDTH"
        "remainAmount": number, // the remaining amount can be matched by the system,
        "price": number, // price unit is equal to SUN
        "durationSec": number, // rent duration, duration unit is equal to seconds
        "orderType": string, // type of order is "NORMAL" or "EXTEND"
        "allowPartialFill": boolean, //Allow the order to be filled partially or not
        "payoutAmount": number, // Total payout of this order
        "fulfilledPercent": number, //The percent that shows filling processing. 0-100
        "delegates": [ //All matched delegates for this order
            {
                "delegator": string, // The address that delegates the resource for the target address
                "amount": number, //The amount of resource was delegated
                "txid": number // The transaction ID in on-chain
            }
        ]
    }
}
```

### 成功响应示例

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "id": "6819c7578729a45600f740d1",
        "requester": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSS",
        "receiver": "TFwUFWr3QV376677Z8VWXxGUAMFSSSSSSS",
        "resourceAmount": 32000,
        "resourceType": "ENERGY",
        "remainAmount": 0,
        "price": 90,
        "durationSec": 300,
        "orderType": "NORMAL",
        "allowPartialFill": false,
        "payoutAmount": 2880000,
        "fulfilledPercent": 100,
        "delegates": [
            {
                "delegator": "THnnMCe67VMDXoivepiA7ZQSB888888",
                "amount": 32000,
                "txid": "19d3fa76a722d6d6e671e6141eb8057760d38d42b353153a3825f19a7d34326f"
            }
        ]
    }
}
```

### 错误

此端点使用 `apikey` 请求头进行身份验证。缺失或无效的密钥会返回 `401`。

**401 Unauthorized — 缺失 API 密钥**

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

**401 Unauthorized — 无效的 API 密钥**

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```

**404 Not Found — 路由/路径错误**

```json
{
    "message": "Route GET:/v2/order/ not found",
    "error": "Not Found",
    "statusCode": 404
}
```

<!-- [NEEDS CONFIRMATION: the exact response body returned when the orderId does not match an existing order is not documented; the order-not-found business message could not be confirmed against the live testnet] -->

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/order/YOUR_ORDER_ID" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getOrderDetails = async (apiKey, orderId) => {
  const url = `${TRONSAVE_API_URL}/v2/order/${orderId}`;
  const res = await fetch(url, {
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

getOrderDetails("YOUR_API_KEY", "YOUR_ORDER_ID").then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"
API_KEY = "YOUR_API_KEY"


def get_order_details(order_id: str) -> dict:
    """Get order details by orderId."""
    url = f"{TRONSAVE_API_URL}/v2/order/{order_id}"
    headers = {"apikey": API_KEY}
    response = requests.get(url, headers=headers)
    return response.json()


print(get_order_details("YOUR_ORDER_ID"))
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetOrderDetails {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";
    static final String API_KEY = "YOUR_API_KEY";

    public static void main(String[] args) throws Exception {
        String orderId = "YOUR_ORDER_ID";

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/order/" + orderId))
                .header("apikey", API_KEY)
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

const (
	tronsaveAPIURL = "https://api.tronsave.io"
	apiKey         = "YOUR_API_KEY"
)

func main() {
	orderID := "YOUR_ORDER_ID"

	req, err := http.NewRequest(http.MethodGet, tronsaveAPIURL+"/v2/order/"+orderID, nil)
	if err != nil {
		panic(err)
	}
	req.Header.Set("apikey", apiKey)

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
use serde_json::Value;

const TRONSAVE_API_URL: &str = "https://api.tronsave.io";
const API_KEY: &str = "YOUR_API_KEY";

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let order_id = "YOUR_ORDER_ID";

    let client = reqwest::blocking::Client::new();
    let resp: Value = client
        .get(format!("{TRONSAVE_API_URL}/v2/order/{order_id}"))
        .header("apikey", API_KEY)
        .send()?
        .json()?;

    println!("{resp:#?}");
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [购买能量(创建订单)](create-order.md) — 使用内部账户付款下单。
* [获取订单簿](get-order-book.md) — 获取当前资源价格和可用量。
* [身份验证](../../../authentication.md) — 如何在每次请求中传入你的 API 密钥。
