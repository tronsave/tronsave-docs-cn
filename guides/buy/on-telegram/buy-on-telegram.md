---
description: 使用待处理订单或您的内部账户，从 TronSave Telegram 机器人购买能量和带宽。
---

# 在 Telegram 上购买

在 Telegram 机器人中，使用您的 TronSave 内部账户购买资源有两种方式：创建**待处理订单**并稍后付款，或直接从已充值的内部账户购买。

{% hint style="info" %}
购买前请确保您已[创建 Telegram 账户](create-account.md)并[充值 TRX](deposit-trx.md)。有关订单类型之间的区别，请参阅[订单类型](../../../concepts/order-types.md)。
{% endhint %}

## 待处理订单 — 先下单，后付款

待处理订单让您可以先下单，然后通过向机器人返回的地址充值确切的总金额来付款。

### 第 1 步：打开机器人

在 Telegram 中打开 [@BuyEnergyTronsave\_bot](https://t.me/BuyEnergyTronsave_bot)。

### 第 2 步：下单

按以下格式发送购买命令：

```
/energy [target-address] [resource-amount] [duration]
/bandwidth [target-address] [resource-amount] [duration]
```

{% hint style="info" %}
使用 `/available` 查看可用资源，使用 `/calc` 在购买前计算费用。
{% endhint %}

**示例：**

```
/energy TWkuSK363HQLtRFCBsNeeiSL7EXy5s6dGL 100000 3h   // 100K Energy for 3 hours
/bandwidth TWkuSK363HQLtRFCBsNeeiSL7EXy5s6dGL 2000 1   // 2000 Bandwidth for 1 day
```

**时长格式：**

* 最短 `1h`（1 小时），最长 `30`（30 天）。
* 小时：数字后跟 `h`（例如 `1h`、`12h`）。
* 天：仅数字（例如 `1`、`20`）。

### 第 3 步：充值总支付金额

向机器人提供的充值地址充值确切的**总支付金额**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FcwytOcj2yQXg3wmY9m4p%2Fimage.png?alt=media&#x26;token=c4b0b4f9-feb9-4b8b-98e2-9a58d28bbed1" alt=""><figcaption></figcaption></figure>

### 第 4 步：订单已创建

在 20 秒内收到全额付款后，您的订单即成功创建。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2Fvm6CrYmAMOn4dGgFmzdC%2Fimage.png?alt=media&#x26;token=7697bff8-06d7-47ac-b9f8-4c2d10defd42" alt=""><figcaption></figcaption></figure>

## 使用内部账户购买

直接从您已充值的内部账户购买，可选择**快速购买**或**自定义购买**。

### 快速购买

**第 1 步：** 点击**购买资源**按钮。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FZpiLNa2a7D5vV93adg0o%2Fimage.png?alt=media&#x26;token=551763f2-451e-45bf-b81a-27ec3ef2bbf8" alt=""><figcaption></figcaption></figure>

**第 2 步：** 输入将接收资源的 TRON 钱包地址。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FYQQxSueby1aZ28jq16FC%2Fimage.png?alt=media&#x26;token=eca628a0-323b-4aed-addb-52183feb9414" alt=""><figcaption></figcaption></figure>

**第 3 步：** 选择**能量**或**带宽**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FPfakK7PRiemDiaR6WSOC%2Fimage.png?alt=media&#x26;token=edc4428d-12af-48d2-a438-7ca79ffda90b" alt=""><figcaption></figcaption></figure>

**第 4 步：** 填写订单详情：

* 选择**时长**（例如 1 小时、3 天）。
* 点击您想购买的**数量**。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FcjEXXyJ7FtRnBfmQSqfW%2Fimage.png?alt=media&#x26;token=000b5d38-f625-4d86-a680-993ac5cd2816" alt=""><figcaption></figcaption></figure>

**第 5 步：** 点击**确认**以创建订单。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2Foi1h9CnKKQNSnGF4U1LD%2Fimage.png?alt=media&#x26;token=c881adfc-ab82-4ddb-8835-64c440cd57f7" alt=""><figcaption></figcaption></figure>

### 自定义购买

启动自定义购买有两种方式。

**方式 1：** 直接在购买表单中点击**自定义购买**按钮。网页界面已集成到 TronSave Telegram 机器人中。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FUgFvrvMUxdgzohZ8uKsE%2Fimage.png?alt=media&#x26;token=5f7af6a3-b516-4e06-8e70-9f81d8be05ed" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FQt9x9eNvl2yoVEhn2olo%2Fimage.png?alt=media&#x26;token=7839247c-022b-41e2-a9dd-e1a82849b8f1" alt=""><figcaption></figcaption></figure>

**方式 2：** 选择**购买能量**，在机器人内打开预集成的自定义购买界面。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FDvPusqgE3AoE72S94TpF%2Fimage.png?alt=media&#x26;token=a47de1f2-3dd3-4627-861f-513c46d46b08" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FQt9x9eNvl2yoVEhn2olo%2Fimage.png?alt=media&#x26;token=7839247c-022b-41e2-a9dd-e1a82849b8f1" alt=""><figcaption></figcaption></figure>

在自定义界面中，输入**目标地址**、**数量**、**价格**和**时长**，然后点击**创建订单**以提交。

## 后续步骤

* 初次使用机器人？从[创建 Telegram 账户](create-account.md)和[充值 TRX](deposit-trx.md)开始。
* 在[订单类型](../../../concepts/order-types.md)中了解每种订单的行为方式。
