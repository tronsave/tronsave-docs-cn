---
description: 读取当前生效的 TronSave SDK 配置，并获取终端用户的交易历史记录。
---

# 配置与历史

TronSave SDK 实例上的两个只读辅助方法：查看 SDK 当前运行所使用的配置，以及拉取通过 TronSave 为指定地址发起的交易历史记录。

{% hint style="info" %}
这两个方法都在已初始化的 `tronSave` 实例上调用。关于如何创建该实例，请参阅 [连接 TronSave](./connect.md)。
{% endhint %}

## 查看当前配置

`showConfig()` 返回 SDK 当前正在使用的配置。

{% tabs %}
{% tab title="Example" %}
```tsx
const showConfig = () => {
    const configs = tronSave.showConfig()
}
```
{% endtab %}
{% endtabs %}

返回对象的具体结构尚未正式记录。请在运行时查看该值（例如 `console.log(tronSave.showConfig())`），以了解你所用 SDK 版本中可用的字段。

## 获取终端用户历史记录

`getHistory()` 返回指定地址通过 TronSave 发起的所有交易历史记录。结果是一个 JSON 字符串，因此使用前需要先进行解析。

{% tabs %}
{% tab title="Example" %}
```javascript
const handleGetHistory = async (address, from, to, page, pageSize) => {
    const dataHistory = await tronSave.getHistory(address, from, to, page, pageSize)
    console.log(JSON.parse(dataHistory))
}
```
{% endtab %}
{% endtabs %}

### 参数

<table>
  <thead>
    <tr><th>参数</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr><td><code>address</code></td><td>请求查询交易历史的 TRON 地址。</td></tr>
    <tr><td><code>from</code></td><td>查询时间范围的起始时间。 <!-- [NEEDS CONFIRMATION: exact type/format of `from` — timestamp, ms, or ISO string] --></td></tr>
    <tr><td><code>to</code></td><td>查询时间范围的结束时间。 <!-- [NEEDS CONFIRMATION: exact type/format of `to` — timestamp, ms, or ISO string] --></td></tr>
    <tr><td><code>page</code></td><td>用于分页的页码。 <!-- [NEEDS CONFIRMATION: is `page` 0-indexed or 1-indexed] --></td></tr>
    <tr><td><code>pageSize</code></td><td>每页返回的记录数。 <!-- [NEEDS CONFIRMATION: max allowed pageSize] --></td></tr>
  </tbody>
</table>

{% hint style="warning" %}
`getHistory()` 解析结果为一个 JSON **字符串**。在读取数据之前，请用 `JSON.parse()` 包裹该调用（如上所示）。
{% endhint %}

每条历史记录的结构尚未正式记录。`getHistory()` 返回的是一个 JSON **字符串**，因此请调用 `JSON.parse()` 并在运行时查看解析后的对象，以了解你所用 SDK 版本中可用的字段。

## 后续步骤

* [连接 TronSave](./connect.md) — 初始化这些方法所使用的 SDK 实例。
* [术语表](../../concepts/glossary.md) — 文档中使用的术语。
