---
description: 获取买家账户最近 6 个月的月度交易统计数据。需要已加入白名单的 API 密钥。
---

# 月度统计

获取买家账户的详细月度统计和交易活动。此端点返回最近 6 个月每月的订单数量和 TRX 充值总额。

{% hint style="warning" %}
**需要白名单**\
此端点仅对已加入白名单的地址可用。登录 TronSave 并将您的钱包地址发送给支持团队，以便将其添加到白名单。加入白名单后，使用从该钱包地址生成的 API 密钥调用此端点。

通过 Telegram 联系支持团队：[**@wantingtrx**](https://t.me/wantingtrx)
{% endhint %}

## 端点

<mark style="color:blue;">**`GET`**</mark> **`https://api.tronsave.io/v2/internal/buyer/monthly-stats`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 个请求。
{% endhint %}

## 请求头

<table><thead><tr><th width="120">名称</th><th width="100">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>apikey</code><mark style="color:red;">*</mark></td><td>String</td><td>TronSave API 密钥。必须属于已加入白名单的地址。请参阅<a href="../../../authentication.md">身份验证</a>以获取您的 API 密钥。</td></tr></tbody></table>

<mark style="color:red;">\*</mark> 必填。

## 请求体

此端点不接受请求体——仅需传递 `apikey` 请求头。

## 响应

成功的响应返回 `error: false`，并在 `data` 中包含统计时间窗口。

<table><thead><tr><th width="280">字段</th><th width="100">类型</th><th>描述</th></tr></thead><tbody><tr><td><code>data.fromMonth</code></td><td>String</td><td>结果时间窗口中最早的月份，格式为 <code>YYYY-MM</code>。</td></tr><tr><td><code>data.toMonth</code></td><td>String</td><td>当前月份，格式为 <code>YYYY-MM</code>。</td></tr><tr><td><code>data.monthCount</code></td><td>Number</td><td><code>data</code> 数组中的月份总数。</td></tr><tr><td><code>data.timezone</code></td><td>String</td><td>应用于所有月份边界的时区——始终为 UTC。</td></tr><tr><td><code>data.data[].month</code></td><td>String</td><td>月份标识符，格式为 <code>YYYY-MM</code>。</td></tr><tr><td><code>data.data[].totalOrder</code></td><td>Number</td><td>该月内提交的订单总数。</td></tr><tr><td><code>data.data[].totalOrderEnergy</code></td><td>Number</td><td>该月内提交的能量订单数量。</td></tr><tr><td><code>data.data[].totalOrderBW</code></td><td>Number</td><td>该月内提交的带宽订单数量。</td></tr><tr><td><code>data.data[].totalTrxDepositToCreateBuyOrder</code></td><td>Number</td><td>该月内为创建买单所花费的 TRX 总额。</td></tr><tr><td><code>data.data[].totalTrxDepositInternal</code></td><td>Number</td><td>该月内充值到内部账户的 TRX 总额。</td></tr></tbody></table>

### 200: OK

```json
{
    "error": false,
    "message": "Success",
    "data": {
        "fromMonth": string, // oldest month in the result window, formatted as YYYY-MM
        "toMonth": string, // current month, formatted as YYYY-MM
        "monthCount": number, // Total number of months in the data array
        "timezone": string, // Timezone applied to all month boundaries — always UTC
        "data": [
            {
                "month": string, // Month identifier, formatted as YYYY-MM
                "totalOrder": number, // Total number of orders placed in the month
                "totalOrderEnergy": number, // Number of energy orders placed in the month
                "totalOrderBW": number, // Number of bandwidth orders placed in the month
                "totalTrxDepositToCreateBuyOrder": number, // Total TRX spent to create buy orders in the month
                "totalTrxDepositInternal": number // Total TRX deposited into the internal account in the month
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
        "fromMonth": "2025-12",
        "toMonth": "2026-05",
        "monthCount": 6,
        "timezone": "UTC",
        "data": [
            {
                "month": "2026-05",
                "totalOrder": 16,
                "totalOrderEnergy": 13,
                "totalOrderBW": 3,
                "totalTrxDepositToCreateBuyOrder": 1080.88,
                "totalTrxDepositInternal": 2000
            },
            {
                "month": "2026-04",
                "totalOrder": 23,
                "totalOrderEnergy": 23,
                "totalOrderBW": 0,
                "totalTrxDepositToCreateBuyOrder": 99.6,
                "totalTrxDepositInternal": 164
            }
        ]
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

## 请求示例

{% tabs %}
{% tab title="cURL" %}
```bash
curl -X GET "https://api.tronsave.io/v2/internal/buyer/monthly-stats" \
  -H "apikey: YOUR_API_KEY"
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getMonthlyStats = async (apiKey) => {
  const url = `${TRONSAVE_API_URL}/v2/internal/buyer/monthly-stats`;
  const res = await fetch(url, {
    headers: {
      apikey: apiKey,
    },
  });
  return res.json();
};

getMonthlyStats("YOUR_API_KEY").then(console.log);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"
API_KEY = "YOUR_API_KEY"


def get_monthly_stats() -> dict:
    """Get buyer monthly statistics (last 6 months)."""
    url = f"{TRONSAVE_API_URL}/v2/internal/buyer/monthly-stats"
    headers = {"apikey": API_KEY}
    response = requests.get(url, headers=headers)
    return response.json()


print(get_monthly_stats())
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class GetMonthlyStats {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";
    static final String API_KEY = "YOUR_API_KEY";

    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/internal/buyer/monthly-stats"))
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
	req, err := http.NewRequest(http.MethodGet, tronsaveAPIURL+"/v2/internal/buyer/monthly-stats", nil)
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
        .get(format!("{TRONSAVE_API_URL}/v2/internal/buyer/monthly-stats"))
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

* [获取内部账户信息](get-account-info.md) — 查看您的内部账户余额和充值地址。
* [获取订单簿](get-order-book.md) — 获取当前的资源价格和可用性。
* [身份验证](../../../authentication.md) — 如何在每个请求中传递您的 API 密钥。
