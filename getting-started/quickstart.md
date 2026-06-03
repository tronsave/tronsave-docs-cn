---
description: 在 10 分钟内完成你的首次能量购买——网站或 API。
---

# 快速开始

购买首份能量有两条快捷路径。任选其一。

## 路径 A——在网站上购买（无需代码）

1. 前往 [tronsave.io/market](https://tronsave.io/market) 并点击 **Connect** 连接你的 TRON 钱包（例如 TronLink）。
2. 选择 **Buy**，然后设置：
   * **Resource**：能量（默认）或带宽
   * **Amount**：例如 `32000` 能量（约等于一次 USDT TRC‑20 转账）
   * **Duration**：例如 1 小时或 3 天
   * **Price tier**：`MEDIUM` 是不错的默认值——参见[订单类型](../concepts/order-types.md)
3. 确认并签名。订单匹配后，能量会在几秒内代理(委托)到你的地址。

{% hint style="success" %}
完成了。要了解不同的购买方式（Normal、Pending、Smart、ZapBuy、Auto Buy），请参见[购买能量与带宽](../guides/buy/)。
{% endhint %}

## 路径 B——通过 API 购买（面向开发者）

大约 10 分钟内，你将获取 API 密钥、充值 TRX、预估成本并下单。完整教程：

➡️ [**开发者快速开始**](../developers/quickstart.md)

最简版本：

```bash
# 1. Estimate cost
curl -X POST https://api.tronsave.io/v2/estimate-buy-resource \
  -H "Content-Type: application/json" \
  -d '{"receiver":"YOUR_TRON_ADDRESS","resourceType":"ENERGY","resourceAmount":65000,"durationSec":900,"unitPrice":"MEDIUM"}'

# 2. Create the order
curl -X POST https://api.tronsave.io/v2/buy-resource \
  -H "apikey: YOUR_API_KEY" -H "Content-Type: application/json" \
  -d '{"receiver":"YOUR_TRON_ADDRESS","resourceType":"ENERGY","resourceAmount":65000,"durationSec":900,"unitPrice":"MEDIUM","options":{"allowPartialFill":true,"preventDuplicateIncompleteOrders":true}}'
```

## 想先测试一下？

使用 **Nile(测试网)**——流程相同，不消耗真实 TRX：

<table><thead><tr><th width="150"></th><th>生产环境</th><th>测试网 (Nile)</th></tr></thead><tbody><tr><td>网站</td><td><code>https://tronsave.io</code></td><td><code>https://testnet.tronsave.io</code></td></tr><tr><td>API</td><td><code>https://api.tronsave.io</code></td><td><code>https://api-dev.tronsave.io</code></td></tr></tbody></table>

## 后续步骤

* [开发者快速开始](../developers/quickstart.md) · [身份认证](../developers/authentication.md) · [API 参考](../developers/api-reference/)
* [工作原理](how-it-works.md) · [订单类型](../concepts/order-types.md)
