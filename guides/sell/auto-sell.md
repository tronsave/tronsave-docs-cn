---
description: 配置 TronSave，按照你自己的价格和时长规则，使用你质押的能量或带宽自动匹配并履行买家订单。
---

# 自动卖出

**自动卖出**让 TronSave 使用你质押的能量或带宽自动匹配并履行收到的买家订单。你只需一次性设置好价格下限、时长上限和利润分成，授予系统代理（委托）权限，TronSave 便会为你处理匹配、代理（委托）和结算。

{% hint style="info" %}
要卖出，你首先需要有可代理（委托）的已质押资源。如果你只持有 TRX，请先进行质押 — 参见[通过质押 2.0 获取能量](staking-2.0.md)。关于底层机制，请参见[质押 2.0](../../concepts/staking-2.0.md) 和[订单类型](../../concepts/order-types.md)核心概念。
{% endhint %}

## 配置自动卖出

### 第 1 步：在 TronSave 市场中打开卖家标签页

前往[卖家设置页面](https://tronsave.io/dashboard/seller/settings)。

### 第 2 步：连接你的 TRON 钱包并登录

* 打开 [TronSave 市场](https://tronsave.io/dashboard/seller/settings)并连接你的钱包。<!-- [NEEDS CONFIRMATION: wallet connection guide path — source linked to ../../faq/how-to-connect-wallet-in-tronsave] -->
* 点击 **Login TRONSAVE** 并签名消息以登录。

<figure><img src="../../.gitbook/assets/seller-desk.png" alt="Seller Desk"><figcaption></figcaption></figure>

### 第 3 步：质押能量/带宽（可选）

_如果你已有可用资源，可以跳过此步骤。_

如果你只持有 TRX，尚未质押能量或带宽，请在卖出前进行质押。你可以通过以下任一方式完成：

1. **通过 TronScan 质押**（[质押链接](https://tronscan.org/#/wallet/resources)）。
2. **直接在 TronSave 上质押** — 点击 **Stake more**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FfwUqlD1qDOSrUTfZTtw8%2Fimage.png?alt=media&#x26;token=838f5014-e16a-4ce9-b60d-c53e2253dc01" alt="" width="563"><figcaption></figcaption></figure>

选择**能量**或**带宽**，输入**质押数量**，然后点击 **Stake**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FQjYD2RABu1udoqXDWCEB%2Fimage.png?alt=media&#x26;token=9358a078-4e83-4e09-8834-b98ff51af4c8" alt="" width="560"><figcaption></figcaption></figure>

### 第 4 步：向 TronSave 授予代理（委托）权限

自动卖出需要权限才能代你从你的账户代理（委托）资源。<!-- [NEEDS CONFIRMATION: permission guide page path — source linked to ../permission] -->

### 第 5 步：编辑你的自动卖出条件

设置匹配规则以契合你的策略。

<figure><img src="../../.gitbook/assets/seller-automations.png" alt="Seller Automations"><figcaption></figcaption></figure>

<table>
  <thead>
    <tr><th>#</th><th>设置项</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td><code>Automatic matching</code></td>
      <td>自动匹配符合你条件的订单。</td>
    </tr>
    <tr>
      <td>2</td>
      <td><code>Earning Share</code></td>
      <td>你想获取的利润百分比。分成越高，你订单的匹配优先级越低。</td>
    </tr>
    <tr>
      <td>3</td>
      <td><code>Allow "Extend Order"</code></td>
      <td>允许买家创建延期订单。</td>
    </tr>
    <tr>
      <td>4</td>
      <td><code>Max Delegate Duration</code></td>
      <td>最长代理（委托）时长。默认为 30 天。</td>
    </tr>
    <tr>
      <td>5</td>
      <td><code>Maintain undelegate</code></td>
      <td>在账户中保持不代理（委托）的能量数量。这部分数量不会用于订单。</td>
    </tr>
    <tr>
      <td>6</td>
      <td><code>Min price</code></td>
      <td>你愿意冻结资源的订单的每日最低能量价格，单位为 SUN/天。</td>
    </tr>
    <tr>
      <td>7</td>
      <td><code>Min delegate amount</code></td>
      <td>可用于履行订单的最小资源数量。这有助于最大化使用你地址中的总资源。</td>
    </tr>
    <tr>
      <td>8</td>
      <td><code>Automatic Reclaim</code></td>
      <td><strong>TronSave：</strong>仅回收通过 TronSave 系统代理（委托）给他人的资源。<strong>All：</strong>在代理（委托）解锁后回收所有代理（委托）给他人的资源。</td>
    </tr>
  </tbody>
</table>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FkitgkPcW9Uq7tfxHss1M%2FFrame%20137.png?alt=media&#x26;token=7735a4ae-f7fe-4e03-ad0a-aa2a72dbcccb" alt=""><figcaption></figcaption></figure>

<table>
  <thead>
    <tr><th>#</th><th>设置项</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr>
      <td>9</td>
      <td><code>Automatic Vote</code></td>
      <td>自动为超级代表投票以赚取投票奖励。</td>
    </tr>
    <tr>
      <td>10</td>
      <td><code>Automatic Withdraw Reward</code></td>
      <td>自动领取并提取你的投票奖励。</td>
    </tr>
    <tr>
      <td>11</td>
      <td><code>Automatic Stake</code></td>
      <td>当你的余额达到设定的阈值时自动质押。系统会运行质押 2.0 交易为你获取能量。</td>
    </tr>
  </tbody>
</table>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FUthcklJHpefojpAGwtEC%2Fimage.png?alt=media&#x26;token=cd65d5e0-a268-4498-8921-b586fe104dff" alt=""><figcaption></figcaption></figure>

<table>
  <thead>
    <tr><th>#</th><th>设置项</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr>
      <td>12</td>
      <td><code>Payment Address</code></td>
      <td>接收自动卖出利润的地址。</td>
    </tr>
    <tr>
      <td>13</td>
      <td><code>Payment frequency</code></td>
      <td>结算的频率。每笔订单履行后**立即**结算（每笔交易手续费 0.3 TRX），或**每日**结算（零手续费）。</td>
    </tr>
  </tbody>
</table>

## 后续步骤

* 了解机制：[质押 2.0](../../concepts/staking-2.0.md) · [订单类型](../../concepts/order-types.md) · [定价与 APY](../../concepts/pricing-and-apy.md)
* 获取可卖出的资源：[通过质押 2.0 获取能量](staking-2.0.md)
