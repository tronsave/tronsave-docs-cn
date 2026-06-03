---
description: 使用 send() 广播合约调用，并在您的能量不足时可选择通过 TronSave 中继执行。
---

# send()

`send()` 用于广播合约方法调用，与标准 TronWeb 的 `method.send()` 完全相同。TronSave SDK 增加了中继选项，因此当您自己的能量不足时，可以通过 TronSave 的能量来执行交易。

您可以传入任何通常会传给 `method.send()` 的参数，外加下面这些与中继相关的选项。

* `useRelay: true` — 当您的能量不足以执行时，通过 **TronSave** 调用该方法。
* `forceUseRelay: true` — 始终通过 **TronSave** 调用该方法，即使您拥有足够的能量来执行。其优先级高于 `useRelay`。

{% tabs %}
{% tab title="Example" %}
```javascript
const onSend = async () => {
   const instance = await tronSave.contract().at("...")
   const testSend = await instance.method().send({
        useRelay: true,
        useSignType: "personal_trx",
        feeLimit: 1e9,
        ...
    })
}
```
{% endtab %}
{% endtabs %}

参考：[https://developers.tron.network/reference/methodsend](https://developers.tron.network/reference/methodsend)

## 参数

<table><thead><tr><th width="167">参数</th><th width="118">类型</th><th width="171">默认值</th><th>说明</th></tr></thead><tbody><tr><td><code>useRelay</code></td><td>boolean</td><td>false</td><td>当您的能量不足以执行时，通过 <strong>TronSave</strong> 调用该方法。</td></tr><tr><td><code>forceUseRelay</code></td><td>boolean</td><td>false</td><td>始终通过 <strong>TronSave</strong> 调用该方法，即使您拥有足够的能量来执行。优先级高于 <code>useRelay</code>。</td></tr><tr><td><code>useSignType</code></td><td>string</td><td>"personal_trx"</td><td><ul><li><code>personal_trx</code>：使用 <em>tronweb.trx.sign</em> 进行个人签名</li><li><code>typed_data</code>：使用 <em>tronweb.trx._signTypedData</em>（目前 <strong>TronLink</strong> 不支持此功能）</li></ul></td></tr></tbody></table>

{% hint style="info" %}
`useSignType: "typed_data"` 不被 TronLink 支持。使用 TronLink 签名时请使用 `personal_trx`（默认值）。
{% endhint %}

## 后续步骤

* 了解[能量与带宽](../../concepts/energy-and-bandwidth.md)，以理解何时需要中继。
* 查看[术语表](../../concepts/glossary.md)了解相关术语。
