---
description: 一个完整的可运行示例，演示如何通过用你的私钥在本地构建的签名交易来支付费用，从而延长现有的资源代理（委托）。
---

# 使用私钥延长订单

这是一个完整的、可运行的示例，演示如何使用 v2 Extend Orders API 延长现有的资源代理（委托），同时通过本地用你的私钥签名的交易来支付费用。

整个流程包含两次 API 调用：

1. **`POST /v2/get-extendable-delegates`** — 预估哪些代理（委托）可以延长，以及需要花费多少 TRX（`totalEstimateTrx`）。
2. **`POST /v2/extend-request`** — 提交延长请求，并附上一笔向 TronSave 接收地址支付预估金额的 `signedTx`。

在这两次调用之间，你需要用自己的私钥构建并签名一笔发往 TronSave 接收地址的 TRX 转账，因此 TronSave 永远不会持有你的资金。

{% hint style="info" %}
本示例使用 TronWeb 来构建并签名转账。需要 **TronWeb 5.3.2 版本**。

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

了解更多：[https://tronweb.network/docu/docs/5.3.2/Release%20Note/](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)
{% endhint %}

## 配置

在运行之前，请设置以下值：

<table><thead><tr><th width="260">常量</th><th>说明</th></tr></thead><tbody><tr><td><code>TRONSAVE_API_URL</code></td><td>TronSave API 基础 URL。主网：<code>https://api.tronsave.io</code>。</td></tr><tr><td><code>RECEIVER</code></td><td>接收延长后资源代理（委托）的地址。</td></tr><tr><td><code>RESOURCE_TYPE</code></td><td><code>ENERGY</code> 或 <code>BANDWIDTH</code>。可选 — 默认为 <code>ENERGY</code>。</td></tr><tr><td><code>REQUESTER_ADDRESS</code></td><td>请求（并支付）延长费用的地址。</td></tr><tr><td><code>PRIVATE_KEY</code></td><td><code>REQUESTER_ADDRESS</code> 的私钥，用于在本地签名 TRX 转账。</td></tr><tr><td><code>TRON_FULL_NODE</code></td><td>TronWeb 用于构建交易的 TRON 全节点（例如 <code>https://api.trongrid.io</code>）。</td></tr><tr><td><code>TRONSAVE_RECEIVER_ADDRESS</code></td><td>接收你付款的 TronSave 地址。主网：<code>TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S</code>。测试网：<code>TATT1UzHRikft98bRFqApFTsaSw73ycfoS</code>。</td></tr></tbody></table>

{% hint style="warning" %}
请妥善保管你的私钥。仅在受信任的服务器环境中运行此代码 — 切勿在客户端或浏览器代码中暴露 `PRIVATE_KEY`。
{% endhint %}

完整的主网和测试网端点列表请参阅 [环境](../../environments.md)，端点详情请参阅 [Extend Orders API 参考](../../api-reference/extend-orders/README.md)。

## 完整示例

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const TronWeb = require('tronweb')

const TRONSAVE_API_URL = "https://api.tronsave.io"
const RECEIVER = "your-receiver-address"
const RESOURCE_TYPE = "ENERGY"
const REQUESTER_ADDRESS = "TFwUFWr3QV376677Z8VWXxGUAMFSrq1MbM"
const PRIVATE_KEY = "your-private-key"
const TRON_FULL_NODE = "https://api.trongrid.io"
const TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S"; //in testnet mode change it to "TATT1UzHRikft98bRFqApFTsaSw73ycfoS"


// Initialize TronWeb instance
const tronWeb = new TronWeb({
    fullNode: TRON_FULL_NODE,
    solidityNode: TRON_FULL_NODE,
    eventServer: TRON_FULL_NODE,
});

const GetEstimateExtendData = async (requester, extendTo, maxPriceAccepted) => {
    const url = TRONSAVE_API_URL + `/v2/get-extendable-delegates`
    const body = {
        extendTo, //time in seconds you want to extend to
        receiver: RECEIVER,   //the address that receives the resource delegate
        requester, //the address that requests the resource delegate
        maxPriceAccepted, //Optional. Number the maximum price you want to pay to extend
        resourceType: RESOURCE_TYPE, //ENERGY or BANDWIDTH. optional. The default is ENERGY
    }
    const data = await fetch(url, {
        method: "POST",
        headers: {
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


const GetSignedTx = async (totalEstimateTrx) => {
    const dataSendTrx = await tronWeb.transactionBuilder.sendTrx(TRONSAVE_RECEIVER_ADDRESS, totalEstimateTrx, REQUESTER_ADDRESS);
    const signedTx = await tronWeb.trx.sign(dataSendTrx, PRIVATE_KEY);
    return signedTx;
};
/**
 * @param {number} extendTo time in seconds you want to extend to
 * @param {boolean} maxPriceAccepted number maximum price you want to pay to extend
 * @returns 
 */
const SendExtendRequest = async (extendTo, maxPriceAccepted) => {
    const url = TRONSAVE_API_URL + `/v2/extend-request`
    // Get estimate extendable delegates
    const estimateResponse = await GetEstimateExtendData(REQUESTER_ADDRESS, extendTo, maxPriceAccepted)
    const extendData = estimateResponse.data?.extendData
    // check if there are extendable delegates
    if (extendData && extendData.length) { 
        const totalEstimateTrx = estimateResponse.data?.totalEstimateTrx
        // Build a signed transaction by using the private key
        const signedTx = await GetSignedTx(totalEstimateTrx)
        const body = {
            extendData: extendData,
            receiver: RECEIVER,
            signedTx
        }
        const data = await fetch(url, {
            method: "POST",
            headers: {
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
<?php

// Configuration
const TRONSAVE_API_URL = "https://api.tronsave.io";
const RECEIVER = "your-receiver-address";
const RESOURCE_TYPE = "ENERGY";
const REQUESTER_ADDRESS = "TFwUFWr3QV376677Z8VWXxGUAMFSrq1MbM";
const PRIVATE_KEY = "your-private-key";
const TRON_FULL_NODE = "https://api.trongrid.io";
const TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S";

/**
 * Get estimate extend data
 * @param string $requester
 * @param int $extendTo
 * @param int $maxPriceAccepted
 * @return array
 */
function getEstimateExtendData(string $requester, int $extendTo, int $maxPriceAccepted): array {
    $url = TRONSAVE_API_URL . "/v2/get-extendable-delegates";
    $body = [
        'extendTo' => $extendTo,
        'receiver' => RECEIVER,
        'requester' => $requester,
        'maxPriceAccepted' => $maxPriceAccepted,
        'resourceType' => RESOURCE_TYPE,
    ];

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($body));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json'
    ]);
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

/**
 * Get signed transaction
 * @param int $totalEstimateTrx
 * @return array
 */
function getSignedTx(int $totalEstimateTrx): array {
    // This is a placeholder. In real implementation, you would use TronWeb PHP SDK
    return [
        'visible' => false,
        'txID' => '...',
        'raw_data' => [
            'contract' => [
                [
                    'parameter' => [
                        'value' => [
                            'amount' => $totalEstimateTrx,
                            'owner_address' => REQUESTER_ADDRESS,
                            'to_address' => TRONSAVE_RECEIVER_ADDRESS
                        ],
                        'type_url' => 'type.googleapis.com/protocol.TransferContract'
                    ],
                    'type' => 'TransferContract'
                ]
            ]
        ]
    ];
}

/**
 * Send extend request
 * @param int $extendTo
 * @param int $maxPriceAccepted
 * @return array
 */
function sendExtendRequest(int $extendTo, int $maxPriceAccepted): array {
    $url = TRONSAVE_API_URL . "/v2/extend-request";
    $estimateResponse = getEstimateExtendData(REQUESTER_ADDRESS, $extendTo, $maxPriceAccepted);
    $extendData = $estimateResponse['data']['extendData'] ?? [];

    if (!empty($extendData)) {
        $totalEstimateTrx = $estimateResponse['data']['totalEstimateTrx'];
        $signedTx = getSignedTx($totalEstimateTrx);

        $body = [
            'extendData' => $extendData,
            'receiver' => RECEIVER,
            'signedTx' => $signedTx
        ];

        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($body));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
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

{% hint style="warning" %}
上面的 PHP `getSignedTx()` 函数只是一个占位符。PHP 没有官方的 TronWeb SDK，因此在发送延长请求之前，你必须使用一个兼容 TRON 的签名库来构建并签名 TRX 转账。
{% endhint %}
{% endtab %}

{% tab title="Python" %}
```python
//import time
import requests
from typing import Dict, Any, Optional

# Configuration
TRONSAVE_API_URL = "https://api.tronsave.io"
RECEIVER = "your-receiver-address"
RESOURCE_TYPE = "ENERGY"
REQUESTER_ADDRESS = "TFwUFWr3QV376677Z8VWXxGUAMFSrq1MbM"
PRIVATE_KEY = "your-private-key"
TRON_FULL_NODE = "https://api.trongrid.io"
TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S"

def get_estimate_extend_data(requester: str, extend_to: int, max_price_accepted: int) -> Dict[str, Any]:
    """Get estimate extend data"""
    url = f"{TRONSAVE_API_URL}/v2/get-extendable-delegates"
    body = {
        'extendTo': extend_to,
        'receiver': RECEIVER,
        'requester': requester,
        'maxPriceAccepted': max_price_accepted,
        'resourceType': RESOURCE_TYPE,
    }
    
    response = requests.post(url, json=body)
    return response.json()

def get_signed_tx(total_estimate_trx: int) -> Dict[str, Any]:
    """Get signed transaction"""
    # This is a placeholder. In real implementation, you would use TronWeb Python SDK
    return {
        'visible': False,
        'txID': '...',
        'raw_data': {
            'contract': [{
                'parameter': {
                    'value': {
                        'amount': total_estimate_trx,
                        'owner_address': REQUESTER_ADDRESS,
                        'to_address': TRONSAVE_RECEIVER_ADDRESS
                    },
                    'type_url': 'type.googleapis.com/protocol.TransferContract'
                },
                'type': 'TransferContract'
            }]
        }
    }

def send_extend_request(extend_to: int, max_price_accepted: int) -> Dict[str, Any]:
    """Send extend request"""
    url = f"{TRONSAVE_API_URL}/v2/extend-request"
    
    estimate_response = get_estimate_extend_data(REQUESTER_ADDRESS, extend_to, max_price_accepted)
    extend_data = estimate_response.get('data', {}).get('extendData', [])

    if extend_data:
        total_estimate_trx = estimate_response['data']['totalEstimateTrx']
        signed_tx = get_signed_tx(total_estimate_trx)

        body = {
            'extendData': extend_data,
            'receiver': RECEIVER,
            'signedTx': signed_tx
        }
        
        response = requests.post(url, json=body)
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

{% hint style="warning" %}
上面的 Python `get_signed_tx()` 函数只是一个占位符。在发送延长请求之前，请使用诸如 `tronpy` 之类的 TRON 签名库来构建并签名 TRX 转账。请注意，示例还引用了 `time.time()`，但 `import time` 这一行被注释掉了 — 运行前请取消注释或添加 `import time`。
{% endhint %}
{% endtab %}
{% endtabs %}

## 工作原理

1. **预估。** `GetEstimateExtendData` 使用 `extendTo`、`receiver`、`requester`、可选的 `maxPriceAccepted` 和 `resourceType` 调用 `/v2/get-extendable-delegates`。响应中包含 `extendData`（可延长的代理（委托））和 `totalEstimateTrx`（以 SUN 计的费用）。
2. **签名。** 如果 `extendData` 非空，`GetSignedTx` 会构建一笔从 `REQUESTER_ADDRESS` 发往 `TRONSAVE_RECEIVER_ADDRESS`、金额为 `totalEstimateTrx` 的 TRX 转账，并使用 `PRIVATE_KEY` 在本地对其签名。
3. **提交。** `SendExtendRequest` 将 `extendData`、`receiver` 和 `signedTx` 提交（POST）到 `/v2/extend-request`。成功时，响应中会包含一个 `orderId`。

{% hint style="info" %}
`maxPriceAccepted` 是你愿意为延长支付的价格上限（以 SUN 计）。如果订单簿价格超过该上限，`extendData` 中返回的代理（委托）可能会更少。
{% endhint %}

## 后续步骤

- [Extend Orders API 参考](../../api-reference/extend-orders/README.md) — `get-extendable-delegates` 和 `extend-request` 的完整请求和响应结构。
- [认证](../../authentication.md) — 将私钥/签名交易流程与 API 密钥流程进行对比。
- [订单类型](../../../concepts/order-types.md) — 了解延长订单与其他订单类型的关系。
