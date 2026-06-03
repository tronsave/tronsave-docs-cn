---
description: 通过 TronSave v2 API 使用钱包私钥对支付交易进行签名，购买能量或带宽的完整可运行示例。
---

# 通过 API 使用私钥购买资源

这是 **签名交易** 购买流程的完整可运行示例：预估成本、使用钱包私钥构建并签名一笔 TRX 支付交易，然后提交以创建订单。无需预付 TronSave 余额，你可以直接从钱包按订单付款。

整个流程分为三步：

1. **预估** — 调用 `/v2/estimate-buy-resource` 获取单价以及所需的 TRX。
2. **签名** — 构建一笔发送至 TronSave 接收地址的 `sendTrx` 交易，并用你的私钥进行签名。
3. **创建订单** — 将签名交易提交至 `/v2/buy-resource`。

{% hint style="warning" %}
切勿将真实私钥提交到代码仓库。在生产环境运行前，请从环境变量或密钥存储中加载 `PRIVATE_KEY`。
{% endhint %}

## 要求

JavaScript 示例针对 **TronWeb 版本 5.3.2**：

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

更多内容请参阅 [TronWeb 5.3.2 发布说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)。

## 配置

示例默认使用主网值。若要在 Nile（测试网）上运行，请替换下方内联标注的值：

<table><thead><tr><th>常量</th><th>主网</th><th>Nile 测试网</th></tr></thead><tbody><tr><td><code>TRONSAVE_RECEIVER_ADDRESS</code></td><td><code>TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S</code></td><td><code>TATT1UzHRikft98bRFqApFTsaSw73ycfoS</code></td></tr><tr><td><code>TRON_FULL_NODE</code></td><td><code>https://api.trongrid.io</code></td><td><code>https://api.nileex.io</code></td></tr><tr><td><code>TRONSAVE_API_URL</code></td><td><code>https://api.tronsave.io</code></td><td><code>https://api-dev.tronsave.io</code></td></tr></tbody></table>

有关切换网络的更多信息，请参阅 [环境](../../environments.md)。

## 完整示例

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const {TronWeb} = require('tronweb');

// Configuration constants
const TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S"; //in testnet mode change it to "TATT1UzHRikft98bRFqApFTsaSw73ycfoS"
const PRIVATE_KEY = "your_private_key"; //Change it
const TRON_FULL_NODE = "https://api.trongrid.io"; //in testnet mode change it to "https://api.nileex.io"
const TRONSAVE_API_URL = "https://api.tronsave.io"; //in testnet mode change it to "https://api-dev.tronsave.io"
const RESOURCE_TYPE = "ENERGY"; // ENERGY or BANDWIDTH

const REQUEST_ADDRESS = "your_request_address"; //Change it
const RECEIVER_ADDRESS = "your_receiver_address"; //Change it
const BUY_AMOUNT = 32000; //Chanageable
const DURATION_SEC = 3600; // 1 hour

// Initialize TronWeb instance
const tronWeb = new TronWeb({
    fullNode: TRON_FULL_NODE,
    solidityNode: TRON_FULL_NODE,
    eventServer: TRON_FULL_NODE,
});

const GetEstimate = async (resourceAmount, durationSec) => {
    const url = TRONSAVE_API_URL + "/v2/estimate-buy-resource";
    const body = {
        resourceAmount,
        unitPrice: "MEDIUM",
        resourceType: RESOURCE_TYPE,
        durationSec,
    };
    const data = await fetch(url, {
        method: "POST",
        headers: {
            "content-type": "application/json",
        },
        body: JSON.stringify(body),
    });
    const response = await data.json();
    /**
     * Example response 
   {
        "error": false,
        "message": 'Success',
        "data": {
            "unitPrice": 50,
            "durationSec": 259200,
            "estimateTrx": 7680000,
            "availableResource": 32000
        }
    }
     */
    return response;
};

const GetSignedTransaction = async (estimateTrx, requestAddress) => {
    const dataSendTrx = await tronWeb.transactionBuilder.sendTrx(TRONSAVE_RECEIVER_ADDRESS, estimateTrx, requestAddress);
    const signedTx = await tronWeb.trx.sign(dataSendTrx, PRIVATE_KEY);
    return signedTx;
};

const CreateOrder = async (resourceAmount, signedTx, receiverAddress, unitPrice, durationSec, options) => {
    const url = TRONSAVE_API_URL + "/v2/buy-resource";
    const body = {
        resourceType: RESOURCE_TYPE,
        resourceAmount,
        unitPrice,
        allowPartialFill: true,
        receiver: receiverAddress,
        durationSec,
        signedTx,
        options
    };
    const data = await fetch(url, {
        method: "POST",
        headers: {
            "content-type": "application/json",
        },
        body: JSON.stringify(body),
    });
    const response = await data.json();
    /**
     * Example response
     * {
     *     "error": false,
     *     "message": "Success",
     *     "data": {
     *         "orderId": "6809fdb7b9ba217a41d726fd"
     *     }
     * }
     */
    return response;
};

/**
 * Main function to buy resources using private key
 * @returns {Promise<void>}
 */
const BuyResourceUsingPrivateKey = async () => {
    //Step 1: Estimate the cost of buy order
    const estimateData = await GetEstimate(BUY_AMOUNT, DURATION_SEC);
    if (estimateData.error) throw new Error(estimateData.message);
    console.log(estimateData);
    const { unitPrice, estimateTrx, durationSec, availableResource } = estimateData.data;
    /*
    {
        "unitPrice": 60,
        "durationSec": 259200,
        "availableResource": 4298470,
        "estimateTrx": 13500000,
    }
     */
    const isReadyFulfilled = availableResource >= BUY_AMOUNT; //if availableResource equal BUY_AMOUNT it means that your order can be fulfilled 100%
    if (isReadyFulfilled) {
        //Step 2: Build signed transaction by using the private key
        const signedTx = await GetSignedTransaction(estimateTrx, REQUEST_ADDRESS);
        console.log(signedTx);
        /*
        {
            "visible": false,
            "txID": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
            "raw_data": {
                "contract": [
                {
                    "parameter": {
                    "value": {
                        "amount": 13500000,
                        "owner_address": "417a0d868d1418c9038584af1252f85d486502eec0",
                        "to_address": "41055756f33f419278d9ea059bd2b21120e6add748"
                    },
                    "type_url": "type.googleapis.com/protocol.TransferContract"
                    },
                    "type": "TransferContract"
                }
                ],
                "ref_block_bytes": "0713",
                "ref_block_hash": "6c5f7686f4176139",
                "expiration": 1691465106000,
                "timestamp": 1691465046758
            },
            "raw_data_hex": "0a02071322086c5f7686f417613940d084b5999d315a68080112640a2d747970652e676f6f676c65617069732e636f6d2f70726f746f636f6c2e5472616e73666572436f6e747261637412330a15417a0d868d1418c9038584af1252f85d486502eec0121541055756f33f419278d9ea059bd2b21120e6add74818e0fcb70670e6b5b1999d31",
            "signature": ["xxxxxxxxx"]
        }
         */

        //Step 3: Create order
        const dataCreateOrder = await CreateOrder(BUY_AMOUNT, signedTx, RECEIVER_ADDRESS, unitPrice, durationSec);
        console.log(dataCreateOrder);
    }
};
BuyResourceUsingPrivateKey()
```
{% endtab %}

{% tab title="Python" %}
```python
import requests
from typing import Dict, Any

# Configuration constants
TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S"
PRIVATE_KEY = "your_private_key"
TRON_FULL_NODE = "https://api.trongrid.io"
TRONSAVE_API_URL = "https://api.tronsave.io"
RESOURCE_TYPE = "ENERGY"

REQUEST_ADDRESS = "your_request_address"
RECEIVER_ADDRESS = "your_receiver_address"
BUY_AMOUNT = 32000
DURATION_SEC = 3600  # 1 hour

def get_estimate(resource_amount: int, duration_sec: int) -> Dict[str, Any]:
    url = f"{TRONSAVE_API_URL}/v2/estimate-buy-resource"
    body = {
        'resourceAmount': resource_amount,
        'unitPrice': "MEDIUM",
        'resourceType': RESOURCE_TYPE,
        'durationSec': duration_sec,
    }
    
    response = requests.post(url, json=body)
    return response.json()

def get_signed_transaction(estimate_trx: int, request_address: str) -> Dict[str, Any]:
    # This is a placeholder. In real implementation, you would use TronWeb Python SDK
    return {
        'visible': False,
        'txID': '...',
        'raw_data': {
            'contract': [{
                'parameter': {
                    'value': {
                        'amount': estimate_trx,
                        'owner_address': request_address,
                        'to_address': TRONSAVE_RECEIVER_ADDRESS
                    },
                    'type_url': 'type.googleapis.com/protocol.TransferContract'
                },
                'type': 'TransferContract'
            }]
        }
    }

def create_order(resource_amount: int, signed_tx: Dict[str, Any], 
                receiver_address: str, unit_price: int, duration_sec: int) -> Dict[str, Any]:
    url = f"{TRONSAVE_API_URL}/v2/buy-resource"
    body = {
        'resourceType': RESOURCE_TYPE,
        'resourceAmount': resource_amount,
        'unitPrice': unit_price,
        'allowPartialFill': True,
        'receiver': receiver_address,
        'durationSec': duration_sec,
        'signedTx': signed_tx
    }
    
    response = requests.post(url, json=body)
    return response.json()

def buy_resource_using_private_key() -> None:
    try:
        # Step 1: Estimate the cost
        estimate_data = get_estimate(BUY_AMOUNT, DURATION_SEC)
        if estimate_data['error']:
            raise Exception(estimate_data['message'])
        print(estimate_data)

        data = estimate_data['data']
        unit_price = data['unitPrice']
        estimate_trx = data['estimateTrx']
        duration_sec = data['durationSec']
        available_resource = data['availableResource']

        is_ready_fulfilled = available_resource >= BUY_AMOUNT
        if is_ready_fulfilled:
            # Step 2: Build signed transaction
            signed_tx = get_signed_transaction(estimate_trx, REQUEST_ADDRESS)
            print(signed_tx)

            # Step 3: Create order
            data_create_order = create_order(
                BUY_AMOUNT, signed_tx, RECEIVER_ADDRESS, unit_price, duration_sec)
            print(data_create_order)

    except Exception as e:
        print(f"Error: {str(e)}")

if __name__ == "__main__":
    buy_resource_using_private_key()
```

{% hint style="info" %}
上面的 `get_signed_transaction` 辅助函数只是一个占位符。要在 Python 中真正对 TRX 支付进行签名，请使用诸如 [`tronpy`](https://github.com/andelf/tronpy) 这样的 TRON 库构建并签名一个 `TransferContract`，或调用 TronSave 的 [获取签名交易](../../api-reference/buy-resources/signed-tx/get-signed-transaction.md) API。
{% endhint %}
{% endtab %}

{% tab title="PHP" %}
```php
<?php

// Configuration constants
const TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S";
const PRIVATE_KEY = "your_private_key";
const TRON_FULL_NODE = "https://api.trongrid.io";
const TRONSAVE_API_URL = "https://api.tronsave.io";
const RESOURCE_TYPE = "ENERGY";

const REQUEST_ADDRESS = "your_request_address";
const RECEIVER_ADDRESS = "your_receiver_address";
const BUY_AMOUNT = 32000;
const DURATION_SEC = 3600; // 1 hour

function getEstimate($resourceAmount, $durationSec) {
    $url = TRONSAVE_API_URL . "/v2/estimate-buy-resource";
    $body = [
        'resourceAmount' => $resourceAmount,
        'unitPrice' => "MEDIUM",
        'resourceType' => RESOURCE_TYPE,
        'durationSec' => $durationSec,
    ];

    $ch = curl_init();
    curl_setopt_array($ch, [
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => json_encode($body),
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_HTTPHEADER => ['Content-Type: application/json']
    ]);

    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

function getSignedTransaction($estimateTrx, $requestAddress) {
    // This is a placeholder. In real implementation, you would use TronWeb PHP SDK
    return [
        'visible' => false,
        'txID' => '...',
        'raw_data' => [
            'contract' => [
                [
                    'parameter' => [
                        'value' => [
                            'amount' => $estimateTrx,
                            'owner_address' => $requestAddress,
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

function createOrder($resourceAmount, $signedTx, $receiverAddress, $unitPrice, $durationSec) {
    $url = TRONSAVE_API_URL . "/v2/buy-resource";
    $body = [
        'resourceType' => RESOURCE_TYPE,
        'resourceAmount' => $resourceAmount,
        'unitPrice' => $unitPrice,
        'allowPartialFill' => true,
        'receiver' => $receiverAddress,
        'durationSec' => $durationSec,
        'signedTx' => $signedTx
    ];

    $ch = curl_init();
    curl_setopt_array($ch, [
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => json_encode($body),
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_HTTPHEADER => ['Content-Type: application/json']
    ]);

    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

function buyResourceUsingPrivateKey() {
    try {
        // Step 1: Estimate the cost
        $estimateData = getEstimate(BUY_AMOUNT, DURATION_SEC);
        if ($estimateData['error']) {
            throw new Exception($estimateData['message']);
        }
        print_r($estimateData);

        $unitPrice = $estimateData['data']['unitPrice'];
        $estimateTrx = $estimateData['data']['estimateTrx'];
        $durationSec = $estimateData['data']['durationSec'];
        $availableResource = $estimateData['data']['availableResource'];

        $isReadyFulfilled = $availableResource >= BUY_AMOUNT;
        if ($isReadyFulfilled) {
            // Step 2: Build signed transaction
            $signedTx = getSignedTransaction($estimateTrx, REQUEST_ADDRESS);
            print_r($signedTx);

            // Step 3: Create order
            $dataCreateOrder = createOrder(BUY_AMOUNT, $signedTx, RECEIVER_ADDRESS, $unitPrice, $durationSec);
            print_r($dataCreateOrder);
        }
    } catch (Exception $e) {
        echo "Error: " . $e->getMessage() . "\n";
    }
}

// Run the main function
buyResourceUsingPrivateKey();
```

{% hint style="info" %}
上面的 `getSignedTransaction` 辅助函数只是一个占位符。要真正对 TRX 支付进行签名，请使用 TRON PHP SDK 构建并签名一个 `TransferContract`，或调用 TronSave 的 [获取签名交易](../../api-reference/buy-resources/signed-tx/get-signed-transaction.md) API。
{% endhint %}
{% endtab %}

{% tab title="cURL" %}
```bash
# The cURL flow covers the two TronSave API calls (estimate and create order).
# Signing the TRX payment transaction must be done with a TRON wallet/SDK and
# the resulting signed transaction passed into the second request as "signedTx".

# Step 1: Estimate the cost
curl -X POST https://api.tronsave.io/v2/estimate-buy-resource \
  -H "content-type: application/json" \
  -d '{
    "resourceAmount": 32000,
    "unitPrice": "MEDIUM",
    "resourceType": "ENERGY",
    "durationSec": 3600
  }'

# Example response:
# {
#   "error": false,
#   "message": "Success",
#   "data": {
#     "unitPrice": 50,
#     "durationSec": 259200,
#     "estimateTrx": 7680000,
#     "availableResource": 32000
#   }
# }

# Step 2: Build and sign a sendTrx transaction to TRONSAVE_RECEIVER_ADDRESS
# (TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S on Mainnet) for "estimateTrx" SUN, using
# your wallet's private key. This produces the signedTx object.

# Step 3: Create the order with the signed transaction
curl -X POST https://api.tronsave.io/v2/buy-resource \
  -H "content-type: application/json" \
  -d '{
    "resourceType": "ENERGY",
    "resourceAmount": 32000,
    "unitPrice": 50,
    "allowPartialFill": true,
    "receiver": "your_receiver_address",
    "durationSec": 3600,
    "signedTx": { "...": "the signed transaction object from step 2" }
  }'

# Example response:
# {
#   "error": false,
#   "message": "Success",
#   "data": {
#     "orderId": "6809fdb7b9ba217a41d726fd"
#   }
# }
```
{% endtab %}

{% tab title="Go" %}
```go
// The Go example covers the two TronSave API calls. Signing the TRX payment
// transaction requires a TRON Go SDK (e.g. github.com/fbsobreira/gotron-sdk);
// pass the resulting signed transaction as the "signedTx" field below.
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
)

const (
	TronsaveReceiverAddress = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S"
	TronsaveAPIURL          = "https://api.tronsave.io"
	ResourceType            = "ENERGY"

	ReceiverAddress = "your_receiver_address"
	BuyAmount       = 32000
	DurationSec     = 3600
)

func postJSON(url string, body map[string]interface{}) (map[string]interface{}, error) {
	payload, _ := json.Marshal(body)
	resp, err := http.Post(url, "application/json", bytes.NewReader(payload))
	if err != nil {
		return nil, err
	}
	defer resp.Body.Close()
	data, _ := io.ReadAll(resp.Body)
	var out map[string]interface{}
	if err := json.Unmarshal(data, &out); err != nil {
		return nil, err
	}
	return out, nil
}

func getEstimate(resourceAmount, durationSec int) (map[string]interface{}, error) {
	return postJSON(TronsaveAPIURL+"/v2/estimate-buy-resource", map[string]interface{}{
		"resourceAmount": resourceAmount,
		"unitPrice":      "MEDIUM",
		"resourceType":   ResourceType,
		"durationSec":    durationSec,
	})
}

func createOrder(resourceAmount int, signedTx interface{}, receiver string, unitPrice, durationSec float64) (map[string]interface{}, error) {
	return postJSON(TronsaveAPIURL+"/v2/buy-resource", map[string]interface{}{
		"resourceType":    ResourceType,
		"resourceAmount":  resourceAmount,
		"unitPrice":       unitPrice,
		"allowPartialFill": true,
		"receiver":        receiver,
		"durationSec":     durationSec,
		"signedTx":        signedTx,
	})
}

func main() {
	// Step 1: Estimate the cost
	estimate, err := getEstimate(BuyAmount, DurationSec)
	if err != nil {
		panic(err)
	}
	if estimate["error"] == true {
		panic(estimate["message"])
	}
	data := estimate["data"].(map[string]interface{})
	unitPrice := data["unitPrice"].(float64)
	durationSec := data["durationSec"].(float64)
	availableResource := data["availableResource"].(float64)
	// estimateTrx := data["estimateTrx"].(float64) // amount of SUN to sign in step 2

	if availableResource >= BuyAmount {
		// Step 2: Build and sign a sendTrx transaction with your private key,
		// producing signedTx. (Use a TRON Go SDK.)
		var signedTx interface{} // = your signed transaction object

		// Step 3: Create the order
		order, err := createOrder(BuyAmount, signedTx, ReceiverAddress, unitPrice, durationSec)
		if err != nil {
			panic(err)
		}
		fmt.Println(order)
	}
}
```
{% endtab %}

{% tab title="Java" %}
```java
// The Java example covers the two TronSave API calls. Signing the TRX payment
// transaction requires a TRON Java SDK (e.g. Trident); pass the resulting
// signed transaction as the "signedTx" field below.
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class BuyResourceUsingPrivateKey {
    static final String TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S";
    static final String TRONSAVE_API_URL = "https://api.tronsave.io";
    static final String RESOURCE_TYPE = "ENERGY";
    static final String RECEIVER_ADDRESS = "your_receiver_address";
    static final int BUY_AMOUNT = 32000;
    static final int DURATION_SEC = 3600;

    static final HttpClient client = HttpClient.newHttpClient();

    static String postJson(String url, String body) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("content-type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .build();
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        return response.body();
    }

    static String getEstimate() throws Exception {
        String body = String.format(
            "{\"resourceAmount\":%d,\"unitPrice\":\"MEDIUM\",\"resourceType\":\"%s\",\"durationSec\":%d}",
            BUY_AMOUNT, RESOURCE_TYPE, DURATION_SEC);
        return postJson(TRONSAVE_API_URL + "/v2/estimate-buy-resource", body);
    }

    static String createOrder(String signedTxJson, long unitPrice, long durationSec) throws Exception {
        String body = String.format(
            "{\"resourceType\":\"%s\",\"resourceAmount\":%d,\"unitPrice\":%d,"
            + "\"allowPartialFill\":true,\"receiver\":\"%s\",\"durationSec\":%d,\"signedTx\":%s}",
            RESOURCE_TYPE, BUY_AMOUNT, unitPrice, RECEIVER_ADDRESS, durationSec, signedTxJson);
        return postJson(TRONSAVE_API_URL + "/v2/buy-resource", body);
    }

    public static void main(String[] args) throws Exception {
        // Step 1: Estimate the cost
        String estimate = getEstimate();
        System.out.println(estimate);
        // Parse estimate to read unitPrice, estimateTrx, durationSec, availableResource.

        // Step 2: Build and sign a sendTrx transaction for estimateTrx SUN to
        // TRONSAVE_RECEIVER_ADDRESS using your private key (use a TRON Java SDK),
        // producing signedTxJson.
        String signedTxJson = "{}"; // = your signed transaction object

        // Step 3: Create the order (substitute parsed unitPrice and durationSec)
        String order = createOrder(signedTxJson, 50L, 259200L);
        System.out.println(order);
    }
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
// The Rust example covers the two TronSave API calls. Signing the TRX payment
// transaction requires a TRON Rust library; pass the resulting signed
// transaction as the "signedTx" field below.
use serde_json::{json, Value};

const TRONSAVE_RECEIVER_ADDRESS: &str = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S";
const TRONSAVE_API_URL: &str = "https://api.tronsave.io";
const RESOURCE_TYPE: &str = "ENERGY";
const RECEIVER_ADDRESS: &str = "your_receiver_address";
const BUY_AMOUNT: i64 = 32000;
const DURATION_SEC: i64 = 3600;

async fn post_json(url: &str, body: Value) -> Result<Value, reqwest::Error> {
    let client = reqwest::Client::new();
    client.post(url).json(&body).send().await?.json::<Value>().await
}

async fn get_estimate() -> Result<Value, reqwest::Error> {
    post_json(
        &format!("{}/v2/estimate-buy-resource", TRONSAVE_API_URL),
        json!({
            "resourceAmount": BUY_AMOUNT,
            "unitPrice": "MEDIUM",
            "resourceType": RESOURCE_TYPE,
            "durationSec": DURATION_SEC,
        }),
    )
    .await
}

async fn create_order(signed_tx: Value, unit_price: i64, duration_sec: i64) -> Result<Value, reqwest::Error> {
    post_json(
        &format!("{}/v2/buy-resource", TRONSAVE_API_URL),
        json!({
            "resourceType": RESOURCE_TYPE,
            "resourceAmount": BUY_AMOUNT,
            "unitPrice": unit_price,
            "allowPartialFill": true,
            "receiver": RECEIVER_ADDRESS,
            "durationSec": duration_sec,
            "signedTx": signed_tx,
        }),
    )
    .await
}

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    // Step 1: Estimate the cost
    let estimate = get_estimate().await?;
    println!("{estimate}");
    let data = &estimate["data"];
    let unit_price = data["unitPrice"].as_i64().unwrap();
    let duration_sec = data["durationSec"].as_i64().unwrap();
    let available_resource = data["availableResource"].as_i64().unwrap();
    // let estimate_trx = data["estimateTrx"].as_i64().unwrap(); // SUN to sign in step 2

    if available_resource >= BUY_AMOUNT {
        // Step 2: Build and sign a sendTrx transaction to TRONSAVE_RECEIVER_ADDRESS
        // for estimate_trx SUN using your private key, producing signed_tx.
        let signed_tx = json!({}); // = your signed transaction object

        // Step 3: Create the order
        let order = create_order(signed_tx, unit_price, duration_sec).await?;
        println!("{order}");
    }
    Ok(())
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
只有 JavaScript 示例完整地实现了交易签名（通过 TronWeb）。对于其他语言，签名步骤被留作占位实现 — 请使用对应语言的 TRON SDK 构建并签名一笔发送至 TronSave 接收地址的 `TransferContract`（`sendTrx`），或调用 TronSave 的 [获取签名交易](../../api-reference/buy-resources/signed-tx/get-signed-transaction.md) API，并将其结果作为 `signedTx` 传入。
{% endhint %}

## 工作原理

| 步骤 | 端点 | 用途 |
| --- | --- | --- |
| 1 | `POST /v2/estimate-buy-resource` | 返回 `unitPrice`、`estimateTrx`、`durationSec` 和 `availableResource`。 |
| 2 | （钱包 / SDK） | 签名一笔发送至 `TRONSAVE_RECEIVER_ADDRESS`、金额为 `estimateTrx` SUN 的 `sendTrx` 交易。 |
| 3 | `POST /v2/buy-resource` | 提交 `signedTx` 并创建订单；返回一个 `orderId`。 |

示例中的几点说明：

* 预估请求中的 `unitPrice: "MEDIUM"` 用于选择一个档位；预估响应会返回你传递给 `/v2/buy-resource` 的具体数值 `unitPrice`。
* `availableResource >= BUY_AMOUNT` 表示订单可被完全成交。当设置 `allowPartialFill: true` 时，即使可用资源较少，订单仍可被创建。 <!-- [NEEDS CONFIRMATION: exact partial-fill behavior when availableResource < BUY_AMOUNT] -->
* `estimateTrx` 以 SUN 计价（1 TRX = 1,000,000 SUN），即你在支付交易中签名的金额。

## 后续步骤

* [使用签名交易购买（API 参考）](../../api-reference/buy-resources/signed-tx/README.md)
* [预估 TRX](../../api-reference/buy-resources/signed-tx/estimate-trx.md) · [获取签名交易](../../api-reference/buy-resources/signed-tx/get-signed-transaction.md) · [创建订单](../../api-reference/buy-resources/signed-tx/create-order.md)
* [环境](../../environments.md) · [订单类型](../../../concepts/order-types.md)
