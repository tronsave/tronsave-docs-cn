---
description: 使用 SaveSender 在一个批次中向多个接收地址发送 TRX 或 TRC20 代币，集成能量和带宽以降低手续费。
---

# 批量发送代币

**SaveSender** 能够高效且低成本地向大量接收地址发送代币。它专为 TRON 网络打造，集成了能量和带宽，因此相比普通的链上转账，你支付的 TRX 手续费要低得多。

## 你能获得什么

* **完全免费使用** — 发送代币不收取任何平台费用。
* **专为 TRON 打造** — 针对 TRX 和 TRC20 代币进行了优化。
* **最高节省 70% 手续费** — 通过集成的能量和带宽实现。
* **CSV 上传** — 准备一份接收地址和金额的列表。
* **快速** — 几分钟内即可发送到数百个地址。

## 如何批量发送 TRC20 代币

下面的流程涵盖了 TRC20 空投（同样适用于多笔转账）。

### 第 1 步：打开工具

前往 [tronsave.io](https://tronsave.io/)，打开 **Tools** 菜单，点击 [**Bulk Send Token**](https://tronsave.io/tools/bulk-send)。

<figure><img src="../../.gitbook/assets/tools-bulk-send.png" alt="Tools Bulk Send"><figcaption></figcaption></figure>

### 第 2 步：准备你的 CSV

创建一个 CSV 文件，列出每个接收地址的钱包地址和代币金额，例如 `TAbc123xyz, 100.5`。请仔细核对地址。

{% hint style="info" %}
* 每一行必须包含一个**地址**和一个**金额**，以逗号分隔，例如 `TAbc123xyz, 100.5`。
* 不允许重复地址。
{% endhint %}

### 第 3 步：连接你的钱包

连接你的钱包，并确保其中持有足够的 TRX 用于支付手续费，以及足够数量的你打算发送的代币。

### 第 4 步：选择代币并上传数据

1. 选择代币。
2. 上传你的 CSV。
3. 核对预览。

<figure><img src="../../.gitbook/assets/tools-bulk-send-token.png" alt="Tools Bulk Send Token"><figcaption></figcaption></figure>

### 第 5 步：授权代币（TRC20）

对于 TRC20 代币，在转账之前你必须先授权该代币。系统会自动预估授权交易所需的能量。

请先购买能量，然后执行 **Approve** 步骤。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2Fh8NTj1m75HHotVNKOHnn%2Fimage.png?alt=media&#x26;token=9c05694b-b981-4213-b793-85ad582f4bc2" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FiaEdlwcSjGNAoYxA24jc%2Fimage.png?alt=media&#x26;token=1de42071-a627-4ba9-b22c-88b4047e944a" alt=""><figcaption></figcaption></figure>

### 第 6 步：发送代币

代币授权完成后，系统会自动进入 **Send** 步骤。

TronSave 会预估你的交易所需的能量。为了节省手续费，请点击 **Buy Energy**。

* 等待 15–30 秒，让能量被代理（委托）到你的钱包。
* 然后确认以继续进行批量发送。

{% hint style="success" %}
在此处购买能量可以降低 TRX gas 成本，并保持发送过程顺畅。
{% endhint %}

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2Fw0GNCVrptQXHPrMRiW3y%2Fimage.png?alt=media&#x26;token=a1cdf5a4-ae98-4727-8987-843e01e14e92" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FdUJNiJ9DrzzsNloEl6A5%2Fimage.png?alt=media&#x26;token=bbf5ace1-2edf-4780-a0af-c002b795beb8" alt=""><figcaption></figcaption></figure>

### 第 7 步：在 TRONSCAN 上验证

查看 [TRONSCAN](https://tronscan.org/) 以确认转账成功。

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FEF51LAO6FoFMLzNg7MTx%2Fimage.png?alt=media&#x26;token=76e8df2e-875c-4548-90fc-2323db626453" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1055070949-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh0rlDWTk05Qu3lvB5Gve%2Fuploads%2FnTNHCoHIJaJajCq3F28h%2Fimage.png?alt=media&#x26;token=50b8b5de-24d3-4eef-a020-5f1177647e96" alt=""><figcaption></figcaption></figure>

## 后续步骤

* [购买能量和带宽](../buy/) — 为你的批量发送提供动力的资源。
* [能量和带宽](../../concepts/energy-and-bandwidth.md) — 为什么这些资源能降低转账手续费。
