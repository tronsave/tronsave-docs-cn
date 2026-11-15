---
description: 获取订单簿接口参考：使用 TronSave API 密钥查询当前能量或带宽订单簿的价格档位与可用资源量，支持按接收地址、租期、资源类型筛选，下单前先了解市场深度。
---

# 获取订单簿

获取当前能量和带宽订单簿及其可用报价和价格。在下单前用它来查看资源价格和深度。

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/order-book`**

{% hint style="info" %}
速率限制：每 **1** 秒 **15** 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="140">名称</th><th width="100">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>与你的内部账户绑定的 TronSave API 密钥。参见<a href="../../../authentication.md">身份验证</a>。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

## 查询参数

<table>
<thead>
<tr><th width="220">名称</th><th width="108">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>address</code></td><td>String</td><td>资源接收地址。</td></tr>
<tr><td><code>minDelegateAmount</code></td><td>Number</td><td>单个供应商代理（委托）的最小能量数量。</td></tr>
<tr><td><code>durationSec</code></td><td>Number</td><td>订单时长（秒）。</td></tr>
<tr><td><code>resourceType</code></td><td>String</td><td><code>"ENERGY"</code> 或 <code>"BANDWIDTH"</code>。默认值：<code>ENERGY</code>。</td></tr>
</tbody>
</table>

## 响应

`data` 中的每一项描述一个价格档位：`price` 是以 SUN 计的价格，`availableResourceAmount` 是该价格下可用的资源数量。

{% tabs %}
{% tab title="200: OK" %}
```json
{
    "error": false,
    "message": "Success",
    "data": [
        {
            "price": 602,
            "availableResourceAmount": 1179
        },
        {
            "price": 650,
            "availableResourceAmount": 2409
        },
        {
            "price": 700,
            "availableResourceAmount": 5395
        },
        {
            "price": 701,
            "availableResourceAmount": 6613
        }
    ]
}
```
{% endtab %}

{% tab title="401：缺少 API 密钥" %}
```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```
{% endtab %}

{% tab title="401：API 密钥无效" %}
```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```
{% endtab %}

{% tab title="400：参数校验错误" %}
```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "querystring must have required property 'address'"
}
```
{% endtab %}
{% endtabs %}

## 示例

查询参数：

<table>
<thead>
<tr><th width="263">键</th><th>值</th></tr>
</thead>
<tbody>
<tr><td><code>address</code></td><td>TFwUFWr3QV376677Z8VWXxGUAMFSrq11111</td></tr>
<tr><td><code>resourceType</code></td><td>BANDWIDTH</td></tr>
<tr><td><code>minDelegateAmount</code></td><td>1000</td></tr>
<tr><td><code>durationSec</code></td><td>86400</td></tr>
</tbody>
</table>

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/order-book?address=YOUR_TRON_ADDRESS" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getOrderBook = async (apiKey, receiverAddress) => {
  const url = `${TRONSAVE_API_URL}/v2/order-book?address=${receiverAddress}`;
  const res = await fetch(url, {
    method: "GET",
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

getOrderBook("YOUR_API_KEY", "YOUR_TRON_ADDRESS").then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"

def get_order_book(api_key: str, receiver_address: str) -> dict:
    url = f"{TRONSAVE_API_URL}/v2/order-book"
    headers = {"apikey": api_key}
    params = {"address": receiver_address}

    response = requests.get(url, headers=headers, params=params)
    return response.json()

print(get_order_book("YOUR_API_KEY", "YOUR_TRON_ADDRESS"))
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
        String apiKey = "YOUR_API_KEY";
        String receiverAddress = "YOUR_TRON_ADDRESS";
        String url = "https://api.tronsave.io/v2/order-book?address=" + receiverAddress;

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
	receiverAddress := "YOUR_TRON_ADDRESS"
	url := "https://api.tronsave.io/v2/order-book?address=" + receiverAddress

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
    let receiver_address = "YOUR_TRON_ADDRESS";
    let url = format!(
        "https://api.tronsave.io/v2/order-book?address={}",
        receiver_address
    );

    let client = reqwest::blocking::Client::new();
    let resp: Value = client
        .get(&url)
        .header("apikey", api_key)
        .send()?
        .json()?;

    println!("{:#?}", resp);
    Ok(())
}
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [预估 TRX](estimate-trx.md)：针对某个资源数量和租赁时长进行预估。
* [购买能量（创建订单）](create-order.md)：在选定价格档位后进行。
* 了解[能量与带宽](../../../../concepts/energy-and-bandwidth.md)之间的区别。
