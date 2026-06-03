---
description: 在发送合约调用之前，预估其将消耗的能量。
---

# 预估能量

在广播合约调用之前，你可以请求 TronSave 预估该调用将消耗多少能量，以及在市场上租赁这些能量需要花费多少。使用 `estimateV2()` 的方式与 `send()` 完全相同——传入相同的参数，但返回的是预估结果，而不是广播交易。

## 用法

调用签名与 `send()` 完全一致。要对某个请求进行预估，只需将 `send()` 替换为 `estimateV2()`：

```tsx
// Example: method().send()

const onSend = async () => {
  try {
    const instance = await tronSave.contract().at("...");
    const testSend = await instance.method().send({
      feeLimit: 1e9,
      callValue: 1000000,
      ...
    });
  } catch (err) {
    console.log(err);
  }
};

// With estimateV2()

const onEstimateEnergy = async () => {
  try {
    const instance = await tronSave.contract().at("...");
    const testEstimateEnergy = await instance.method().estimateV2({
      feeLimit: 1e9,
      callValue: 1000000,
      ...
    });
    console.log(testEstimateEnergy);
    // Response type: {
    //   available_energy: 0,
    //   discount_percent: 0,
    //   total_estimate_energy: 0,
    //   buy_energy_price: 0
    // }
  } catch (err) {
    console.log(err);
  }
};
```

## 响应

{% tabs %}
{% tab title="Result estimateV2()" %}
```typescript
{
  available_energy: 976304,
  discount_percent: 80,
  total_estimate_energy: 976304,
  buy_energy_price: 65,
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="227">键</th><th width="99">类型</th><th>说明</th></tr></thead><tbody><tr><td><code>available_energy</code></td><td>number</td><td>系统能够为上述请求提供的能量数量。如果满足 100%，则返回与预估值相同的能量数量。</td></tr><tr><td><code>discount_percent</code></td><td>number</td><td>与销毁 TRX 相比所节省的百分比。例如，<code>80</code> 表示租赁这些能量比链上销毁成本便宜约 80%。</td></tr><tr><td><code>total_estimate_energy</code></td><td>number</td><td>系统为该指令预估的能量数量。</td></tr><tr><td><code>buy_energy_price</code></td><td>number</td><td>你可以在 TronSave 市场上购买全部预估能量数量时所对应的能量价格（SUN）。</td></tr></tbody></table>

{% hint style="info" %}
`buy_energy_price` 以每单位能量的 SUN 计价。1 TRX = 1,000,000 SUN。有关资源和定价的术语，请参阅[术语表](../../concepts/glossary.md)。
{% endhint %}

## 后续步骤

- 在[能量与带宽](../../concepts/energy-and-bandwidth.md)中了解能量是如何被消耗的。
- 查看[定价与 APY](../../concepts/pricing-and-apy.md)以了解市场价格。
