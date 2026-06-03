---
description: 使用你自己的私钥签名 TRX 交易，通过 TronSave v0 API 购买能量的完整可运行示例。
---

# 使用私钥购买能量（v0）

{% hint style="warning" %}
**旧版示例。** 本页使用 **v0** TronSave API 和 **TronWeb 5.3.2**，仅供参考及向后兼容之用。对于新的集成，请使用 [购买资源](../../api-reference/buy-resources/README.md) 中当前的签名交易流程以及 [开发者快速开始](../../quickstart.md)。
{% endhint %}

本示例展示了完整的签名交易流程：预估成本、使用你的私钥签名一笔转向 TronSave 接收地址的 TRX 转账、并创建购买订单——整个过程无需向内部账户充值 TRX。

## 环境要求

**TronWeb 版本 5.3.2**

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

了解更多：[TronWeb 5.3.2 发布说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)

## 配置

本示例读取四个端点/地址常量。其取值在主网与 Nile（测试网）之间有所不同：

<table>
<thead>
<tr><th>常量</th><th>主网</th><th>测试网（Nile）</th></tr>
</thead>
<tbody>
<tr><td><code>TRONSAVE_RECEIVER_ADDRESS</code></td><td><code>TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S</code></td><td><code>TATT1UzHRikft98bRFqApFTsaSw73ycfoS</code></td></tr>
<tr><td><code>TRON_FULLNODE</code></td><td><code>https://api.trongrid.io</code></td><td><code>https://api.nileex.io</code></td></tr>
<tr><td><code>TRONSAVE_API_URL</code></td><td><code>https://api.tronsave.io</code></td><td><code>https://api-dev.tronsave.io</code></td></tr>
</tbody>
</table>

运行前请设置 `PRIVATE_KEY`、`REQUEST_ADDRESS` 和 `TARGET_ADDRESS`。`BUY_AMOUNT` 和 `DURATION` 可根据你的订单进行修改。

## 完整示例

{% tabs %}
{% tab title="Javascript" %}
```javascript
const TronWeb = require('tronweb')

const TRONSAVE_RECEIVER_ADDRESS = "TWZEhq5JuUVvGtutNgnRBATbF8BnHGyn4S" //in testnet mode change it to "TATT1UzHRikft98bRFqApFTsaSw73ycfoS"
const PRIVATE_KEY = "your_private_key" //Change it
const TRON_FULLNODE = "https://api.trongrid.io"  //in testnet mode change it to "https://api.nileex.io"
const TRONSAVE_API_URL = "https://api.tronsave.io" //in testnet mode change it to "https://api-dev.tronsave.io"

const REQUEST_ADDRESS = "your_request_address" //Change it
const TARGET_ADDRESS = "your_target_address" //Change it
const BUY_AMOUNT = 100000 //Chanageable
const DURATION = 3 * 86400 * 1000 //3 days.Changeable

const tronWeb = new TronWeb({
    fullNode: TRON_FULLNODE,
    solidityNode: TRON_FULLNODE,
    eventServer: TRON_FULLNODE,
    privateKey: PRIVATE_KEY,
})

const GetEstimate = async (request_address, target_address, amount, duration) => {
    const url = TRONSAVE_API_URL + "/v0/estimate-trx"
    //see more at https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/buy-energy
    const body = {
        "amount": amount,
        "buy_energy_type": "MEDIUM",
        "duration_millisec": duration,
        "request_address": request_address,
        "target_address": target_address || request_address,
        "is_partial": true
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
     * @link  https://docs.tronsave.io/market/how-to-buy-energy/buy-on-rest-api
   {
        "unit_price": 45,
        "duration_millisec": 259200000,
        "available_energy": 4298470,
        "estimate_trx": 13500000,
    }
     */
    return response
}

const GetSignedTransaction = async (estimate_trx, request_address) => {
    const dataSendTrx = await tronWeb.transactionBuilder.sendTrx(TRONSAVE_RECEIVER_ADDRESS, estimate_trx, request_address)
    const signed_tx = await tronWeb.trx.sign(dataSendTrx, PRIVATE_KEY);
    return signed_tx
}

const CreateOrder = async (signed_tx, target_address, unit_price, duration) => {
    const url = TRONSAVE_API_URL + "/v0/buy-energy"
    //see more at https://docs.tronsave.io/buy-energy-on-telegram/using-api-key-to/buy-energy
    const body = {
        "resource_type": "ENERGY",
        "unit_price": unit_price,
        "allow_partial_fill": true,
        "target_address": target_address,
        "duration_millisec": duration,
        "tx_id": signed_tx.txID,
        "signed_tx": signed_tx
    }
    const data = await fetch(url, {
        method: "POST",
        headers: {
            "content-type": "application/json",
        },
        body: JSON.stringify(body)
    })
    const response = await data.text()
    //Example response 
    // @link  https://docs.tronsave.io/market/how-to-buy-energy/buy-on-rest-api
    // "651d2306e55c073f6ca0992e" //order id in tronsave
    return response
}

const BuyEnergyUsingPrivateKey = async () => {
    //Step 1: Estimate the cost of buy order
    const estimate_data = await GetEstimate(REQUEST_ADDRESS, TARGET_ADDRESS, BUY_AMOUNT, DURATION)
    const { unit_price, estimate_trx, available_energy } = estimate_data
    console.log(estimate_data)
    /*
    {
        "unit_price": 45,
        "duration_millisec": 259200000,
        "available_energy": 4298470,
        "estimate_trx": 13500000,
    }
     */
    const is_ready_fulfilled = available_energy >= BUY_AMOUNT //if available_energy equal buy_amount it means that your order can be fulfilled 100%
    if (is_ready_fulfilled) {
        //Step 2: Build signed transaction by using private key
        const signed_tx = await GetSignedTransaction(estimate_trx, REQUEST_ADDRESS)
        console.log(signed_tx);
        /*
        {
            "resource_type": "ENERGY",
            "unit_price": 45,
            "allow_partial_fill": true,
            "target_address": "TM6ZeEgpefyGWeMLuzSbfqTGkPv8Z6Jm4X",
            "duration_millisec": 259200000,
            "tx_id": "446eed36e31249b98b201db2e81a3825b185f1a3d8b2fea348b24fc021e58e0d",
            "signed_tx": {
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
        }
         */
        //Step 3: Create order
        const dataCreateOrder = await CreateOrder(signed_tx, TARGET_ADDRESS, unit_price, DURATION)
        console.log(dataCreateOrder);
    }
}

BuyEnergyUsingPrivateKey()
```
{% endtab %}
{% endtabs %}

## 工作原理

1. **预估** — `POST /v0/estimate-trx` 会针对你请求的 `BUY_AMOUNT` 和 `DURATION` 返回 `unit_price`、`available_energy` 以及 `estimate_trx`（以 SUN 计的 TRX 成本）。
2. **签名** — 构建一笔将 `estimate_trx` 转向 `TRONSAVE_RECEIVER_ADDRESS` 的 TRX 转账，并使用你的私钥在本地对其签名。在订单创建之前不会有任何 TRX 被转移。
3. **创建订单** — `POST /v0/buy-energy` 提交该签名交易。成功时响应即为订单 ID，例如 `"651d2306e55c073f6ca0992e"`。

本示例仅在 `available_energy >= BUY_AMOUNT` 时才会继续签名并创建订单，从而确保订单能够 100% 成交。

{% hint style="warning" %}
切勿将你的 `PRIVATE_KEY` 提交到源代码管理或与他人分享。在生产环境中，请从安全的环境变量或密钥管理器中加载它。
{% endhint %}

## 后续步骤

- 迁移到当前 API：[购买资源](../../api-reference/buy-resources/README.md)
- 比较认证方式：[认证](../../authentication.md)
- 了解租赁模型：[核心概念 → 租赁模型](../../../concepts/rental-model.md)
