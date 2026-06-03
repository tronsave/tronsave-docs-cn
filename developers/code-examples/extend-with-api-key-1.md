---
description: 完整的 v0 示例，展示如何使用 API 密钥延长资源订单。
---

# v0 — 使用 API 密钥延长

这是一个完整的 v0 示例，使用 TronSave **API 密钥**延长一个有效的资源代理（委托）。它通过 `get-extendable-delegates` 预估成本，然后通过 `internal-extend-request` 提交延长请求。

{% hint style="warning" %}
这是一个旧版 v0 示例，仅作参考保留。对于新的集成，请使用当前的 API。参见 [延长订单](../api-reference/extend-orders/) 和 [快速开始](../quickstart.md)。
{% endhint %}

## 前提条件

你需要一个 API 密钥。有两种获取方式：

* [在网站上获取 API 密钥](../authentication.md)
* [在 Telegram 上获取 API 密钥](../authentication.md)

**要求：TronWeb 版本 5.3.2**

```bash
npm i tronweb@5.3.2 @noble/secp256k1@1.7.1
```

（延伸阅读：[TronWeb 5.3.2 发布说明](https://tronweb.network/docu/docs/5.3.2/Release%20Note/)）

## 代码

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const API_KEY = `your_api_key`;
const TRONSAVE_API_URL = "https://api.tronsave.io/v0"
const RECEIVER = "the_address_that_receive_resource"

/**
 * @param {*} extend_to time in milliseconds you want to extend to
 * @param {*} max_price number maximum price you want to pay to extend
 * @returns 
 */
const GetEstimateExtendData = async (extend_to, max_price) => {
    const url = TRONSAVE_API_URL + `/get-extendable-delegates`
    const body = {
        "extend_to": extend_to, //time in milliseconds you want to extend to
        "receiver": RECEIVER,   //the address that receives the resource delegate
        "max_price": max_price, //Optional. Number maximum price you want to pay to extend
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
     * @link  //TODO
       {
            "extend_order_book": [
                {
                    "price": 133,
                    "value": 64319
                }
            ],
            "total_delegate_amount": 64319,
            "total_available_extend_amount": 64319,
            "total_estimate_trx": 8554427,
            "is_able_to_extend": true,
            "your_balance": 20000000,
            "extend_data": [
                {
                    "delegator": "TMN2uTdy6rQYaTm4A5g732kHRf72tKsA4w",
                    "is_extend": true,
                    "extra_amount": 0,
                    "extend_to": 1728459019000
                }
            ]
        }
     */
    return response
}

/**
 * @param {*} extend_to time in milliseconds you want to extend to
 * @param {*} max_price number maximum price you want to pay to extend
 * @returns 
 */
const SendInternalExtendRequest = async (extend_to, max_price) => {
    const url = TRONSAVE_API_URL + `/internal-extend-request`
    const estimate_response = await GetEstimateExtendData(extend_to, max_price)
    if (estimate_response.extend_data && estimate_response.extend_data.length) { 
        const body = {
            "extend_data": estimate_response.extend_data,
            "receiver": RECEIVER,
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
         * @link  //TODO
           [<order_id>]
         */
        return response
    }
    return []
}

//Example run code
const ClientCode = () => { 
    const extend_to = +new Date() + 86400 * 1000 //Extend to 1 next day
    const max_price = 200

    SendInternalExtendRequest(extend_to, max_price).then(console.log)
}


ClientCode()
```
{% endtab %}
{% endtabs %}

## 后续步骤

* [延长订单 API 参考](../api-reference/extend-orders/)
* [身份验证](../authentication.md)
