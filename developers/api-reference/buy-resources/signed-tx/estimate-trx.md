---
description: 在创建签名交易订单之前，预估购买指定数量和租赁时长的能量或带宽所需的 TRX。
---

# 预估 TRX

获取购买资源所需 TRX 的预估值。在签名交易流程中首先调用此端点，以了解 `unitPrice`、需要支付的 `estimateTrx`，以及实际可用于成交订单的资源数量。

<mark style="color:orange;">**`POST`**</mark> **`https://api.tronsave.io/v2/estimate-buy-resource`**

{% hint style="info" %}
**速率限制：** 每 1 秒 15 个请求。
{% endhint %}

{% hint style="info" %}
测试网基础 URL：`https://api-dev.tronsave.io`。参见[环境](../../../environments.md)。
{% endhint %}

## 请求头

| Header         | 值                  | 是否必填 |
| -------------- | ------------------ | ---- |
| `Content-Type` | `application/json` | 是    |

## 请求参数

<table><thead><tr><th width="206.33333333333331">字段</th><th width="95">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>resourceAmount</code><mark style="color:red;">*</mark></td><td>Number</td><td>资源数量。</td></tr><tr><td><code>unitPrice</code></td><td>String, Number</td><td><p><strong>"FAST"、"MEDIUM"、"SLOW" 或数字</strong>：</p><p><br>-"FAST"：如果市场可成交比例 = 100%，则 FAST = MEDIUM。如果市场可成交比例 &#x3C; 100%，则 FAST = MEDIUM + 10。如果市场可成交比例 = 0%，则 FAST = SLOW + 20。</p><p>-"MEDIUM"：在该订单达到市场最大成交量时的最低价格。如果市场可成交比例 = 0%，则 MEDIUM = SLOW + 10。</p><p>-"SLOW"：该订单可设置的最低价格。</p><p>-如果价格为数字，价格单位等于 SUN</p></td></tr><tr><td><code>durationSec</code></td><td>Number</td><td>所购资源的时长，时间单位为秒。默认 3 天</td></tr><tr><td><code>requester</code></td><td>String</td><td>请求方地址。</td></tr><tr><td><code>receiver</code></td><td>String</td><td>资源接收地址。</td></tr><tr><td><code>resourceType</code></td><td>String</td><td>"ENERGY" 或 "BANDWIDTH"，默认值："ENERGY"</td></tr><tr><td><code>options</code></td><td>Object</td><td>可选</td></tr><tr><td><code>options.allowPartialFill</code></td><td>Boolean</td><td>是否允许订单部分成交</td></tr><tr><td><code>options.minResourceDelegateRequiredAmount</code></td><td>Number</td><td>单个供应商代理（委托）的最小资源数量。</td></tr></tbody></table>

## 请求体示例

```json
{
    "resourceType": "ENERGY",
    "receiver": "TFwUFWr3QV376677Z8VWXxGUAMF123456",
    "durationSec": 259200,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
        "allowPartialFill": true,
        "minResourceDelegateRequiredAmount": 32000
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
        "unitPrice": 64,
        "durationSec": 259200,
        "estimateTrx": 6144000,
        "availableResource": 32000
    }
}
```

`estimateTrx` 的值以 SUN 返回（1 TRX = 1,000,000 SUN）。`unitPrice` 是以 SUN 计的每单位价格，`availableResource` 是市场可成交的请求资源数量。

### 错误

由于此端点通过签名交易（而非 API 密钥）进行身份验证，因此不会因缺少凭据而返回 `401`。最常见的错误是当必填字段缺失或无效时的模式校验错误。`message` 会指明出错的字段。

**400 Bad Request — 校验错误（缺少必填字段 `resourceAmount`）：**

```json
{
    "statusCode": 400,
    "code": "FST_ERR_VALIDATION",
    "error": "Bad Request",
    "message": "body must have required property 'resourceAmount'"
}
```

**404 Not Found — 路由/路径错误：**

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
curl -X POST https://api.tronsave.io/v2/estimate-buy-resource \
  -H "Content-Type: application/json" \
  -d '{
    "resourceType": "ENERGY",
    "receiver": "YOUR_TRON_ADDRESS",
    "durationSec": 259200,
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "options": {
      "allowPartialFill": true
    }
  }'
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const TRONSAVE_API_URL = "https://api.tronsave.io";

const getEstimate = async (requesterAddress, receiverAddress, resourceAmount, durationSec) => {
  const url = `${TRONSAVE_API_URL}/v2/estimate-buy-resource`;
  const body = {
    resourceAmount,
    unitPrice: "MEDIUM",
    resourceType: "ENERGY",
    durationSec,
    requester: requesterAddress,
    receiver: receiverAddress,
    options: {
      allowPartialFill: true,
    },
  };

  const res = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(body),
  });

  return res.json();
};

// getEstimate("YOUR_TRON_ADDRESS", "YOUR_TRON_ADDRESS", 32000, 259200);
```
{% endtab %}

{% tab title="Python" %}
```python
import requests

TRONSAVE_API_URL = "https://api.tronsave.io"


def get_estimate(requester, receiver, resource_amount, duration_sec):
    url = f"{TRONSAVE_API_URL}/v2/estimate-buy-resource"
    body = {
        "resourceAmount": resource_amount,
        "unitPrice": "MEDIUM",
        "resourceType": "ENERGY",
        "durationSec": duration_sec,
        "requester": requester,
        "receiver": receiver,
        "options": {
            "allowPartialFill": True,
        },
    }

    response = requests.post(url, json=body)
    return response.json()


# get_estimate("YOUR_TRON_ADDRESS", "YOUR_TRON_ADDRESS", 32000, 259200)
```
{% endtab %}

{% tab title="Java" %}
```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class EstimateTrx {
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";

    public static void main(String[] args) throws Exception {
        String body = """
            {
              "resourceType": "ENERGY",
              "receiver": "YOUR_TRON_ADDRESS",
              "durationSec": 259200,
              "resourceAmount": 32000,
              "unitPrice": "MEDIUM",
              "options": {
                "allowPartialFill": true
              }
            }
            """;

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(TRONSAVE_API_URL + "/v2/estimate-buy-resource"))
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
	body := []byte(`{
		"resourceType": "ENERGY",
		"receiver": "YOUR_TRON_ADDRESS",
		"durationSec": 259200,
		"resourceAmount": 32000,
		"unitPrice": "MEDIUM",
		"options": {
			"allowPartialFill": true
		}
	}`)

	req, err := http.NewRequest("POST", tronsaveAPIURL+"/v2/estimate-buy-resource", bytes.NewBuffer(body))
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
use reqwest::blocking::Client;
use serde_json::json;

const TRONSAVE_API_URL: &str = "https://api.tronsave.io";

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let body = json!({
        "resourceType": "ENERGY",
        "receiver": "YOUR_TRON_ADDRESS",
        "durationSec": 259200,
        "resourceAmount": 32000,
        "unitPrice": "MEDIUM",
        "options": {
            "allowPartialFill": true
        }
    });

    let client = Client::new();
    let response = client
        .post(format!("{}/v2/estimate-buy-resource", TRONSAVE_API_URL))
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

* [获取签名交易](get-signed-transaction.md)
* [创建订单](create-order.md)
* 返回[使用签名交易购买](./)
