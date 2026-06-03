---
description: TronSave API 的版本历史与重大变更。
---

# 更新日志

本页面追踪 TronSave API 各代版本中的重要变更。目前共存在两代 API。

## API 版本代

| 版本代 | 基础路径 | 状态 | 说明 |
| --- | --- | --- | --- |
| **v2** | `https://api.tronsave.io/v2` | 当前 | 推荐用于所有新集成的版本代。[API 参考](api-reference/README.md)中的端点均面向 v2。 |
| **v0** | `https://api.tronsave.io/v0` | 旧版（仍受支持） | 早期版本代，为向后兼容仍在受支持和维护中。没有弃用或停用日期。新集成应使用 v2。 |

{% hint style="info" %}
在 Nile 测试网上，请将主机替换为 `https://api-dev.tronsave.io`（例如 `https://api-dev.tronsave.io/v2`）。请参阅[快速开始 → 想先测试？](../getting-started/quickstart.md)。
{% endhint %}

{% hint style="warning" %}
v0 被视为旧版，但仍在受支持和维护中。没有弃用或停用日期。我们建议新集成迁移到 v2。
{% endhint %}

## 发布历史

<!-- [NEEDS CONFIRMATION: real changelog entries and dates — the entries below are placeholders and must be replaced with confirmed releases] -->

### 未发布

* _占位内容 — 暂无确认的带日期条目。_

<!--
Template for future entries:

### YYYY-MM-DD — vX.Y

**Added**
* ...

**Changed**
* ...

**Fixed**
* ...

**Deprecated / Removed**
* ...
-->

## 后续步骤

* [身份验证](authentication.md) · [API 参考](api-reference/README.md)
* [开发者快速开始](quickstart.md)
