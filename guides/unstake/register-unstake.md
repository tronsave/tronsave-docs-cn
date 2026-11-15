---
description: 在 TronSave 上注册提前解质押，无需等待官方解质押期即可即时取回已质押的 TRX，需支付浮动服务费。
---

# 注册解除质押

**提前解质押**让你无需等待 TRON 官方解质押期，即可立即取回已质押的 TRX。它能让你更快获得流动性，并根据解质押金额收取浮动服务费。

## 1. 运作原理

按照以下步骤在 TronSave 上完成提前解质押注册。

### 步骤 1 – 连接钱包并登录

* 打开[提前解质押](https://tronsave.io/unstake)页面。
* 连接你想要解质押的钱包，然后点击 **Login with TronSave**。

### 步骤 2 – 阅读条款

* 仔细阅读所有**条款**。
* 在继续之前，勾选 "我已阅读并理解所有提前解 staked 的条款和条件" 复选框。

<figure><img src="../../.gitbook/assets/unstake-early.png" alt="Unstake Early"><figcaption></figcaption></figure>

### 步骤 3 – 计算可领取金额

* 点击 **Calculate Claimable**，预估在扣除所有费用后你将收到的金额。
* 点击 **Confirm** 按钮。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FDg2rjPhERnvBAGpQnMdx%2Fimage.png?alt=media&#x26;token=9d9b61b7-4d0d-43c7-bca8-388c5188b336" alt="" width="473"><figcaption></figcaption></figure>

### 步骤 4 – 提交注册请求

* 输入你的**接收地址**以进行确认。
* 然后点击 **Register Withdraw** 发送你的解质押请求。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FwVuRteXcNvv0tYH452Xk%2Fimage.png?alt=media&#x26;token=fec5a7b8-3536-4a14-ac3d-fc4ddcd553a7" alt="" width="563"><figcaption></figcaption></figure>

#### 此步骤的流动性检查

* 如果解质押金额超过当前系统流动性，你的请求需要管理员和供应商手动审批。审批通过后，你可以继续进行[步骤 5](register-unstake.md#step-5-check-balance)。
* <div data-gb-custom-block data-tag="hint" data-style="info" class="hint hint-info"><p>**建议：**加入我们的 Bot，以便在请求获批时即时收到通知。</p></div>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FaUNoQ28DQxk9g4XpcjvO%2Fimage.png?alt=media&#x26;token=004576ef-2d0b-45f8-b95f-0531e3977634" alt="" width="563"><figcaption></figcaption></figure>

* 如果系统流动性充足，你的请求将自动获批，你可以继续进行[步骤 5](register-unstake.md#step-5-check-balance)。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FUAgdqUNnu0jKvXWZ4WIw%2Fimage.png?alt=media&#x26;token=120505e1-57d2-4d21-9603-932ecc47c198" alt="" width="563"><figcaption></figcaption></figure>

### 步骤 5 – 检查余额

* 在继续之前，提取所有标记为 **To Be Withdrawn**（待提取）的金额。
* 请务必先从此地址**提取或转出所有其他代币**——任何留下的**非 TRX 代币**在此过程中**不会被计入或退还**。
* 如果你的余额超过解质押金额，请提取超出部分。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FlFD2lbVeDFyDVZrTy40B%2Fimage.png?alt=media&#x26;token=6e527f72-d57a-4b36-a597-bc620f37bee0" alt="" width="563"><figcaption></figcaption></figure>

### 步骤 6 – 更新权限

* 更新你的钱包权限，将 **Owner 和 Active 两个权限**都分配给 TronSave 官方机器人地址：

```
TXUwRhntqX3kyALhtpC74JP8Nt6m2VMiYC
```

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FWQJfQWXTVs1JL84OhFo0%2Fimage.png?alt=media&#x26;token=8766c72f-117f-441f-8423-6f7d24b54c88" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="warning" %}
此步骤需要约 **105 TRX** 的费用（权限更新费用，外加解质押和提取的网络费用）。

请仔细核对**授权地址**，并在完全理解整个流程后再确认。
{% endhint %}

### 步骤 7 – 确认并等待验证

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FaXsuXsq9gRuFO3PLSujV%2Fimage.png?alt=media&#x26;token=5b405e0b-15ef-4b49-b174-76cfbbe6b54d" alt="" width="563"><figcaption></figcaption></figure>

* 确认后，你的解质押请求将开始处理。
* 在 24 小时内，你的订单将尽快完成结算。

如果经过 24 小时处理后流动性仍然不可用，你的解质押订单将被挂到市场上，由供应商手动撮合。

{% hint style="info" %}
* 你可以**随时取消解质押请求**。
* 取消后，系统将**恢复你的钱包权限**。
{% endhint %}

## 2. 安全与注意事项

* 所有提前解质押交易都**可在 TRON 区块链上完全验证**。
* 在授予权限之前，请务必核实 **TronSave 官方地址**。为了更高的安全性，请先联系 TronSave 支持以确认真实性。
* 任何**超过自动审批阈值的解质押请求**都会被发送给管理团队进行人工审核和付款审批。
* 权限转移后，你将**无法再直接使用该钱包**。此步骤是 TronSave 代你完成解质押所必需的。

## 3. 承诺与支持

TronSave 承诺在可用流动性限额内**尽快**处理所有提前解质押请求。如果你的请求需要人工审批或遇到问题，请联系我们的官方支持渠道：

**Telegram 支持：** [@wantingtrx](https://t.me/wantingtrx)

## 后续步骤

* [质押 2.0](../../concepts/staking-2.0.md) · [能量与带宽](../../concepts/energy-and-bandwidth.md) · [术语表](../../concepts/glossary.md)
