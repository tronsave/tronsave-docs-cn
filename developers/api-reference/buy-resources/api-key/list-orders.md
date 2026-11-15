---
description: 使用 TronSave API 密钥进行身份验证，检索由已加入白名单的买家账户创建的订单的详细分页列表。
---

# 列出订单

检索由买家账户创建的订单的详细列表。此端点为高级集成和分析返回扩展的订单数据。它需要 API 密钥白名单访问权限。

{% hint style="warning" %}
**需要白名单**\
登录 TronSave 并将你的钱包地址发送给我们的支持团队，以便将其加入白名单。一旦加入白名单，你就可以使用从该钱包地址生成的 API 密钥来调用此端点。

通过 Telegram 联系支持：[**@wantingtrx**](https://t.me/wantingtrx)。
{% endhint %}

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/internal/buyer/orders`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="140">名称</th><th width="100">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>TronSave API 密钥（必须属于已加入白名单的地址）。参见<a href="../../../authentication.md">身份验证</a>。</td></tr></tbody></table>

<mark style="color:red;">\*</mark> 必填。

## 查询参数

<table><thead><tr><th width="170">名称</th><th width="100">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>startTimestamp</code></td><td>Number</td><td>以秒为单位的时间戳：用于筛选订单的时间范围起点。</td></tr><tr><td><code>endTimestamp</code></td><td>Number</td><td>以秒为单位的时间戳：用于筛选订单的时间范围终点。</td></tr><tr><td><code>pageSize</code></td><td>Number</td><td>每页记录数。（默认值：20，最大值：20）</td></tr><tr><td><code>cursor</code></td><td>String</td><td>分页游标。使用 <code>nextCursor</code> 向前翻页，使用 <code>prevCursor</code> 向后翻页。（省略以获取第一页。）</td></tr><tr><td><code>direction</code></td><td>String</td><td>仅在<strong>向后</strong>翻页时需要：将 <code>direction=prev</code> 与 <code>cursor=prevCursor</code> 一起传入。要向前翻页，只需传入 <code>cursor=nextCursor</code> —— 无需 <code>direction</code>。</td></tr></tbody></table>

## 响应

`data` 对象包装了分页结果。内层 `data` 数组中的每一项都是一个订单。在后续调用中将 `id` 用作 `orderId`。`price` 和 `payoutAmount` 以 SUN 为单位；`durationSec` 以秒为单位；`fulfilledPercent` 的取值范围为 0 到 100。

```javascript
{
    "error": false,
    "message": "Success",
    "data": {
        "startTimestamp": number,// Start of the time range to filter orders.
        "endTimestamp": number,// End of the time range to filter orders.
        "data": [
        {
          "id": string,//Order identifier — use as orderId in Step 2
          "receiver": string,//TRON address that receives the delegated resource
          "resourceAmount": number, // the amount of resource
          "resourceType": string, // the resource type is "ENERGY" or "BANDWIDTH"
          "remainAmount": number,// the remaining amount can be matched by the system
          "orderType":  string, // type of order is "NORMAL" or "EXTEND"
          "price": number, // price unit is equal to SUN
          "durationSec": number, // rent duration, duration unit is equal to seconds
          "status": string, // the order status, either Active or Completed
          "allowPartialFill": boolean, //Allow the order to be filled partially or not
          "payoutAmount": number, // Total payout of this order (SUN)
          "fulfilledPercent":  number, //The percent that shows filling processing. 0-100
          "smartMatching": {object}, // Contains smart matching details (order count, matched amount, and refund amount)
          "totalMergeInfo": {object}, // Contains merge summary (merged amount and merged TRX)
          "extendInfo": [], // Lists extend delegation records (delegator, amount, payout, etc.)
          "createdAt": 1778467289
        }
      ],
        "pageSize": number, // Number of records per page
        "prevCursor": string, // Pass as cursor with direction=prev to fetch the previous page.
        "nextCursor": string // Pass as cursor to fetch the next page. null when there are no more pages ahead.
    }
}
```



### 成功响应示例

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "startTimestamp": 1777507200,
        "endTimestamp": 1779349961,
        "data": [
            {
                "id": "6a0eb6708f648f498632acee",
                "receiver": "TFwUFWr3QV376677Z8VWXxGUAMFSrq1111",
                "resourceAmount": 66666,
                "resourceType": "ENERGY",
                "remainAmount": 0,
                "orderType": "NORMAL",
                "price": 65,
                "durationSec": 900,
                "status": "Completed",
                "allowPartialFill": false,
                "payoutAmount": 4333290,
                "fulfilledPercent": 100,
                "createdAt": 1779349104
            },
            {
                "id": "6a02e2a5302419258a85d524",
                "receiver": "TUP4FoZGxFZTXzCPZmfuKdGniB8J8eAAAA",
                "resourceAmount": 1000,
                "resourceType": "BANDWIDTH",
                "remainAmount": 0,
                "orderType": "NORMAL",
                "price": 600,
                "durationSec": 900,
                "status": "Completed",
                "allowPartialFill": false,
                "payoutAmount": 600000,
                "fulfilledPercent": 100,
                "createdAt": 1778573989
            }
        ],
        "pageSize": 2,
        "prevCursor": "af28gmn9vIINZjkQk46sf-bkhEM",
        "nextCursor": "af28dGn9vHQNZjkQk46sfUTFQXU"
    }
}
```

### 错误

{% tabs %}
{% tab title="401: 缺少 API 密钥" %}
未提供 `apikey` 请求头。

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```
{% endtab %}

{% tab title="401: 无效的 API 密钥" %}
提供的 API 密钥无效

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```
{% endtab %}

{% tab title="400:  Forbidden" %}
其钱包地址未加入白名单。

```json
{
    "error": true,
    "message": "TSAS:108 FORBIDDEN Forbidden",
    "data": null
}
```
{% endtab %}
{% endtabs %}

## 示例

查询参数：

<table><thead><tr><th width="263">键</th><th>值</th></tr></thead><tbody><tr><td><code>startTimestamp</code></td><td>1778236532</td></tr><tr><td><code>endTimestamp</code></td><td>1779349961</td></tr><tr><td><code>pageSize</code></td><td>2</td></tr><tr><td><code>cursor</code></td><td>af28gmn9vIINZjkQk46sf-bkhEM</td></tr></tbody></table>

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/internal/buyer/orders?startTimestamp=1778236532&endTimestamp=1779349961&pageSize=2&cursor=af28gmn9vIINZjkQk46sf-bkhEM" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const listBuyerOrders = async (apiKey, params = {}) => {
  const query = new URLSearchParams(params).toString();
  const url = `${TRONSAVE_API_URL}/v2/internal/buyer/orders?${query}`;
  const res = await fetch(url, {
    method: "GET",
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

listBuyerOrders("YOUR_API_KEY", {
  startTimestamp: 1778236532,
  endTimestamp: 1779349961,
  pageSize: 2,
  cursor: "af28gmn9vIINZjkQk46sf-bkhEM",
}).then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"

def list_buyer_orders(api_key: str, params: dict) -> dict:
    url = f"{TRONSAVE_API_URL}/v2/internal/buyer/orders"
    headers = {"apikey": api_key}

    response = requests.get(url, headers=headers, params=params)
    return response.json()

print(list_buyer_orders("YOUR_API_KEY", {
    "startTimestamp": 1778236532,
    "endTimestamp": 1779349961,
    "pageSize": 2,
    "cursor": "af28gmn9vIINZjkQk46sf-bkhEM",
}))
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class ListBuyerOrders {
    public static void main(String[] args) throws Exception {
        String apiKey = "YOUR_API_KEY";
        String url = "https://api.tronsave.io/v2/internal/buyer/orders"
                + "?startTimestamp=1778236532"
                + "&endTimestamp=1779349961"
                + "&pageSize=2"
                + "&cursor=af28gmn9vIINZjkQk46sf-bkhEM";

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("apikey", apiKey)
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
	apiKey := "YOUR_API_KEY"
	url := "https://api.tronsave.io/v2/internal/buyer/orders" +
		"?startTimestamp=1778236532" +
		"&endTimestamp=1779349961" +
		"&pageSize=2" +
		"&cursor=af28gmn9vIINZjkQk46sf-bkhEM"

	req, err := http.NewRequest(http.MethodGet, url, nil)
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

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let api_key = "YOUR_API_KEY";
    let url = "https://api.tronsave.io/v2/internal/buyer/orders";

    let client = reqwest::blocking::Client::new();
    let resp: Value = client
        .get(url)
        .header("apikey", api_key)
        .query(&[
            ("startTimestamp", "1778236532"),
            ("endTimestamp", "1779349961"),
            ("pageSize", "2"),
            ("cursor", "af28gmn9vIINZjkQk46sf-bkhEM"),
        ])
        .send()?
        .json()?;

    println!("{:#?}", resp);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [创建订单](create-order.md)以购买能量或带宽。
* [获取订单簿](get-order-book.md)以在下单前查看定价。
* 了解[能量与带宽](../../../../concepts/energy-and-bandwidth.md)之间的区别。
