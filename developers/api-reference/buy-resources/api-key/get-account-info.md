---
description: 获取内部账户信息接口参考：通过 apikey 请求头查询与 TronSave API 密钥关联的账户 ID、以 SUN 计的余额、代表地址与 TRX 充值地址，并附响应字段说明与示例。
---

# 获取内部账户信息

获取与所提供 API 密钥关联的内部账户信息，包括账户 `id`、当前以 SUN 计的 `balance`、在订单中代表该账户的地址，以及用于充值 TRX 的地址。

## 端点

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/user-info`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 次请求。
{% endhint %}

## 请求头

<table>
<thead>
<tr><th width="120">名称</th><th width="100">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>代表内部账户的 TronSave API 密钥。请参阅 <a href="../../../authentication.md">身份验证</a> 以获取你的 API 密钥。</td></tr>
</tbody>
</table>

<mark style="color:red;">*</mark> 必填。

## 请求体

此端点不接受请求体——仅传入 `apikey` 请求头。

## 响应

成功的响应将返回 `error: false`，并在 `data` 中返回账户详情。

<table>
<thead>
<tr><th width="180">字段</th><th width="100">类型</th><th>说明</th></tr>
</thead>
<tbody>
<tr><td><code>data.id</code></td><td>String</td><td>内部账户 ID。</td></tr>
<tr><td><code>data.balance</code></td><td>String</td><td>内部账户余额，以 SUN 计。</td></tr>
<tr><td><code>data.representAddress</code></td><td>String</td><td>作为订单请求者代表内部账户的地址。</td></tr>
<tr><td><code>data.depositAddress</code></td><td>String</td><td>将 TRX 发送到此地址即可充值到你的内部账户。</td></tr>
</tbody>
</table>

### 200: OK

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "id": string, //internal account id
        "balance": string, //internal account balance in SUN
        "representAddress": string, //represents the internal account as the requester of the order
        "depositAddress": string, //Send TRX to this address to deposit into your internal account.
    }
}
```

### 成功响应示例

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "id": "user_id",
        "balance": "306773887",
        "representAddress": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999",
        "depositAddress": "TKVSaJQDWeKFSEXmA44pjxduGTxy999999"
    }
}
```

### 错误

此端点使用 `apikey` 请求头进行身份验证，因此常见错误为身份验证失败。

**401 Unauthorized —— 缺少 API 密钥**（未发送 `apikey` 请求头）：

```json
{
    "error": true,
    "message": "TSAS:106 API_KEY_REQUIRED",
    "data": null
}
```

**401 Unauthorized —— 无效的 API 密钥**：

```json
{
    "error": true,
    "message": "TSAS:107 INVALID_API_KEY",
    "data": null
}
```

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/user-info" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getAccountInfo = async (apiKey) => {
  const url = `${TRONSAVE_API_URL}/v2/user-info`;
  const res = await fetch(url, {
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

getAccountInfo("YOUR_API_KEY").then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"
API_KEY = "YOUR_API_KEY"


def get_account_info() -> dict:
    """Get internal account information."""
    url = f"{TRONSAVE_API_URL}/v2/user-info"
    headers = {"apikey": API_KEY}
    response = requests.get(url, headers=headers)
    return response.json()


print(get_account_info())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetAccountInfo {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";
    static final String API_KEY = "YOUR_API_KEY";

    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/user-info"))
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
	req, err := http.NewRequest(http.MethodGet, tronsaveAPIURL+"/v2/user-info", nil)
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
    let client = reqwest::blocking::Client::new();
    let resp: Value = client
        .get(format!("{TRONSAVE_API_URL}/v2/user-info"))
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

* [获取订单簿](get-order-book.md) —— 获取当前的资源价格和可用情况。
* [购买能量（创建订单）](create-order.md) —— 下单并从你的内部账户付款。
* [身份验证](../../../authentication.md) —— 如何在每次请求时传入你的 API 密钥。
