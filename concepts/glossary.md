---
description: TronSave 文档中通用的标准化术语。
---

# 术语表

| 术语 | 定义 |
| --- | --- |
| **能量（Energy）** | 执行智能合约（例如 USDT TRC‑20 转账）时消耗的 TRON 资源。通过质押 TRX 或在 TronSave 上租赁获得。 |
| **带宽（Bandwidth）** | 按交易字节大小消耗的 TRON 资源。每笔交易都需要一定的带宽。 |
| **TRX** | TRON 网络的原生代币。 |
| **SUN** | TRX 的最小单位。**1 TRX = 1,000,000 SUN。** 所有价格均以 SUN 计价。 |
| **质押 2.0（Stake 2.0）** | TRON 产生能量/带宽的质押（冻结）机制。是 TronSave 供给的基础。 |
| **代理（委托，Delegation）** | 在链上将能量/带宽从一个账户授予另一个账户。订单成交后会将资源代理给 `receiver`（接收地址）。 |
| **供应商/卖家（Provider / Seller）** | 质押 TRX 并将所产生的能量租出（代理）以赚取 APY 的用户。 |
| **买家（Buyer）** | 从市场租赁能量/带宽的用户。 |
| **订单簿（Order book）** | 决定市场价格的实时买/卖订单集合。 |
| **内部账户（Internal Account）** | 由 TronSave 托管的余额，你向其中存入 TRX；API 可以自动从中扣款。与 API 密钥关联。 |
| **API 密钥（API Key）** | 与你的内部账户关联的私密标识符，用于授权 API 请求。 |
| **签名交易（Signed Transaction）** | 一种替代的鉴权方式，即每次购买时你用自己的钱包对一笔 TRX 付款进行签名（完全自托管）。 |
| **`unitPrice`** | 每个资源单位的价格（以 SUN 计），或一个档位：`SLOW` / `MEDIUM` / `FAST`。 |
| **`resourceType`** | `ENERGY` 或 `BANDWIDTH`。默认为 `ENERGY`。 |
| **`durationSec`** | 租赁时长（以秒计）。默认 `259200`（3 天）。 |
| **`receiver`** | 接收所租资源的 TRON 地址。 |
| **`requester`** | 下单/代表订单的地址（你内部账户的 `representAddress`）。 |
| **`fulfilledPercent`** | 订单成交状态：`0` 待处理，`1–99` 部分成交，`100` 完全成交。 |
| **APR / APY** | 供应商收益。公式参见 [定价与 APY](pricing-and-apy.md)。 |
| **WTRX** | Wrapped TRX（包装 TRX），用于某些 [SDK](../developers/sdk/wtrx.md) 付款流程。 |
| **ZapBuy** | 通过直接向机器人地址发送 TRX 来购买能量；固定 1 小时租赁。 |
| **Fast Charge** | 用于快速能量充值的 API 流程（预估 → 创建 → 跟踪 → 确认）。 |
| **Nile** | TronSave 使用的 TRON 测试网（`api-dev.tronsave.io`）。 |

> 术语标准：在指代 TRON 资源时，将 **能量（Energy）** 和 **带宽（Bandwidth）** 首字母大写书写；**TRX** 和 **SUN** 全大写；字段名使用 `code` 格式。
