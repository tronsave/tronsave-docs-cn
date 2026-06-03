---
description: 完整可运行的示例,演示如何使用 API 密钥延长已有的 TronSave 订单,提供 JavaScript、PHP 和 Python 版本。
---

# 使用 API 密钥延长订单

这是一个完整、可运行的示例,演示如何使用 **API 密钥**延长 TronSave 上已有的订单。它分两步调用 v2 延长端点:首先预估哪些代理可被延长以及需要花费多少 TRX,然后提交延长请求并返回 `orderId`。

## 前提条件

你需要一个绑定到内部账户的 TronSave API 密钥。有两种获取方式:

* **方式 1:** 在 TronSave 网站上生成 API 密钥。
* **方式 2:** 在 Telegram 上生成 API 密钥。

关于这两种方式的详情,以及 API 密钥如何授权针对你内部账户的请求,请参阅 [身份验证](../../authentication.md)。

{% hint style="info" %}
下面的 JavaScript 示例仅使用内置的 `fetch` API,不需要 TronWeb。原始来源提到相关示例需要 TronWeb 5.3.2;仅当你更广泛的集成需要签名时才安装它:

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

更多内容请阅读 [TronWeb 5.3.2 发布说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)。
{% endhint %}

## 工作原理

该流程使用两个端点:

1. [`/v2/get-extendable-delegates`](../../api-reference/extend-orders/get-extendable-delegates.md) — 返回 `extendData` 负载、预估的 TRX 费用,以及是否可以延长。
2. [`/v2/extend-request`](../../api-reference/extend-orders/extend-request.md) — 提交 `extendData` 并返回 `orderId`。

运行前请设置以下变量:

<table><thead><tr><th>变量</th><th>说明</th></tr></thead><tbody>
<tr><td><code>API_KEY</code></td><td>你的 TronSave API 密钥。</td></tr>
<tr><td><code>RECEIVER</code></td><td>接收资源代理的地址。</td></tr>
<tr><td><code>RESOURCE_TYPE</code></td><td><code>ENERGY</code> 或 <code>BANDWIDTH</code>。可选;默认为 <code>ENERGY</code>。</td></tr>
<tr><td><code>extendTo</code></td><td>你希望将代理延长到的时间,以秒为单位(Unix 时间戳)。</td></tr>
<tr><td><code>maxPriceAccepted</code></td><td>可选。你愿意为延长支付的最高价格(以 SUN 为单位)。</td></tr>
</tbody></table>

## 完整示例

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const API_KEY = `your-api-key`;
const TRONSAVE_API_URL = "https://api.tronsave.io"
const RECEIVER = "your-receiver-address"
const RESOURCE_TYPE = "ENERGY"

const GetEstimateExtendData = async (extendTo, maxPriceAccepted) => {
    const url = TRONSAVE_API_URL + `/v2/get-extendable-delegates`
    const body = {
        extendTo, //time in seconds you want to extend to
        receiver: RECEIVER,   //the address that receives the resource delegate
        maxPriceAccepted, //Optional. Number the maximum price you want to pay to extend
        resourceType: RESOURCE_TYPE, //ENERGY or BANDWIDTH. optional. The default is ENERGY
    }
    const data = await fetch(url, {
        method: "POST",
        headers: {
            'apikey': API_KEY,
            "content-type": "application/json",
        },
        body: JSON.stringify(body)
    })
    const response = await data.json()
    /**
     * Example response 
     * @link  
       {
            "error": false,
            "message": "Success",
            "data": {
                "extendOrderBook": [
                    {
                        "price": 784,
                        "value": 1002
                    }
                ],
                "totalDelegateAmount": 5003,
                "totalAvailableExtendAmount": 5003,
                "totalEstimateTrx": 4085783,
                "isAbleToExtend": true,
                "yourBalance": 2377366851,
                "extendData": [
                    {
                        "delegator": "TGGVrYaT8Xoos...6dmSZkohGGcouYL4",
                        "isExtend": true,
                        "extraAmount": 0,
                        "extendTo": 1745833276
                    },
                    {
                        "delegator": "TQBV7xU489Rq8Z...zBhJMdrDr51wA2",
                        "isExtend": true,
                        "extraAmount": 0,
                        "extendTo": 1745833276
                    },
                    {
                        "delegator": "TSHZv6xsYHMRCbdVh...qNozxaPPjDR6",
                        "isExtend": true,
                        "extraAmount": 0,
                        "extendTo": 1745833276
                    }
                ]
            }
        }
     */
    return response
}

/**
 * @param {number} extendTo the time in seconds you want to extend to
 * @param {boolean} maxPriceAccepted number maximum price you want to pay to extend
 * @returns 
 */
const SendExtendRequest = async (extendTo, maxPriceAccepted) => {
    const url = TRONSAVE_API_URL + `/v2/extend-request`
    // Get estimate extendable delegates
    const estimateResponse = await GetEstimateExtendData(extendTo, maxPriceAccepted)
    const extendData = estimateResponse.data?.extendData
    if (extendData && extendData.length) { 
        const body = {
            extendData: extendData,
            receiver: RECEIVER,
        }
        const data = await fetch(url, {
            method: "POST",
            headers: {
                'apikey': API_KEY,
                "content-type": "application/json",
            },
            body: JSON.stringify(body)
        })
        const response = await data.json()
        /**
         * Example response 
         {
            error: false,
            message: 'Success',
            data: { orderId: '680b5ac7b09a385fb3d582ff' }
            }
         */
        return response
    }
    return []
}

//Example run code
const ClientCode = async () => { 
    const extendTo = Math.floor(new Date().getTime() / 1000) + 3 * 86400 //Extend to 3 next days
    const maxPriceAccepted = 900
    const response = await SendExtendRequest(extendTo, maxPriceAccepted)
    console.log(response)
}


ClientCode()
```
{% endtab %}

{% tab title="PHP" %}
```php
//<?php

// Configuration
const API_KEY = 'your-api-key';
const TRONSAVE_API_URL = "https://api.tronsave.io";
const RECEIVER = "your-receiver-address";
const RESOURCE_TYPE = "ENERGY";

/**
 * Get estimate extend data
 * @param int $extendTo
 * @param int $maxPriceAccepted
 * @return array
 */
function getEstimateExtendData(int $extendTo, int $maxPriceAccepted): array {
    $url = TRONSAVE_API_URL . "/v2/get-extendable-delegates";
    $body = [
        'extendTo' => $extendTo,
        'receiver' => RECEIVER,
        'maxPriceAccepted' => $maxPriceAccepted,
        'resourceType' => RESOURCE_TYPE,
    ];

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($body));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'apikey: ' . API_KEY,
        'Content-Type: application/json'
    ]);
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

/**
 * Send extend request
 * @param int $extendTo
 * @param int $maxPriceAccepted
 * @return array
 */
function sendExtendRequest(int $extendTo, int $maxPriceAccepted): array {
    $url = TRONSAVE_API_URL . "/v2/extend-request";
    $estimateResponse = getEstimateExtendData($extendTo, $maxPriceAccepted);
    $extendData = $estimateResponse['data']['extendData'] ?? [];

    if (!empty($extendData)) {
        $body = [
            'extendData' => $extendData,
            'receiver' => RECEIVER,
        ];

        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($body));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
            'apikey: ' . API_KEY,
            'Content-Type: application/json'
        ]);
        $response = curl_exec($ch);
        curl_close($ch);
        return json_decode($response, true);
    }

    return [];
}

// Example run code
try {
    $extendTo = time() + (3 * 86400); // Extend to 3 next days
    $maxPriceAccepted = 900;
    $response = sendExtendRequest($extendTo, $maxPriceAccepted);
    print_r($response);
} catch (Exception $e) {
    echo "Error: " . $e->getMessage() . "\n";
} 
```
{% endtab %}

{% tab title="Python" %}
```python
import time
import requests
from typing import Dict, Any, Optional

# Configuration
API_KEY = 'your-api-key'
TRONSAVE_API_URL = "https://api.tronsave.io"
RECEIVER = "your-receiver-address"
RESOURCE_TYPE = "ENERGY"

def get_estimate_extend_data(extend_to: int, max_price_accepted: int) -> Dict[str, Any]:
    """Get estimate extend data"""
    url = f"{TRONSAVE_API_URL}/v2/get-extendable-delegates"
    headers = {
        'apikey': API_KEY,
        'Content-Type': 'application/json'
    }
    
    body = {
        'extendTo': extend_to,
        'receiver': RECEIVER,
        'maxPriceAccepted': max_price_accepted,
        'resourceType': RESOURCE_TYPE,
    }
    
    response = requests.post(url, headers=headers, json=body)
    return response.json()

def send_extend_request(extend_to: int, max_price_accepted: int) -> Dict[str, Any]:
    """Send extend request"""
    url = f"{TRONSAVE_API_URL}/v2/extend-request"
    headers = {
        'apikey': API_KEY,
        'Content-Type': 'application/json'
    }
    
    estimate_response = get_estimate_extend_data(extend_to, max_price_accepted)
    extend_data = estimate_response.get('data', {}).get('extendData', [])

    if extend_data:
        body = {
            'extendData': extend_data,
            'receiver': RECEIVER,
        }
        
        response = requests.post(url, headers=headers, json=body)
        return response.json()

    return {}

def main() -> None:
    """Main function"""
    try:
        extend_to = int(time.time()) + (3 * 86400)  # Extend to 3 next days
        max_price_accepted = 900
        response = send_extend_request(extend_to, max_price_accepted)
        print("Response:", response)
    except Exception as e:
        print(f"Error: {str(e)}")

if __name__ == "__main__":
    main() 
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [获取可延长的代理](../../api-reference/extend-orders/get-extendable-delegates.md) — 第 1 步的完整参考。
* [提交延长请求](../../api-reference/extend-orders/extend-request.md) — 第 2 步的完整参考。
* [身份验证](../../authentication.md) — 如何获取和使用 API 密钥。
