---
description: TronSave API 的版本历史与重大变更。
---

# 更新日志

本页面追踪 TronSave API 各代版本中的重要变更。目前共存在两代 API。

## API 版本代

<table><thead><tr><th width="96">版本代</th><th>基础路径</th><th width="141">状态</th><th>说明</th></tr></thead><tbody><tr><td><strong>v2</strong></td><td><code>https://api.tronsave.io/v2</code></td><td>当前</td><td>推荐用于所有新集成的版本代。<a href="api-reference/">API 参考</a>中的端点均面向 v2。</td></tr><tr><td><strong>v0</strong></td><td><code>https://api.tronsave.io/v0</code></td><td>旧版（仍受支持）</td><td>早期版本代，为向后兼容仍在受支持和维护中。没有弃用或停用日期。新集成应使用 v2。</td></tr></tbody></table>

{% hint style="info" %}
在 Nile 测试网上，请将主机替换为 `https://api-dev.tronsave.io`（例如 `https://api-dev.tronsave.io/v2`）。请参阅[快速开始 → 想先测试？](../getting-started/quickstart.md)。
{% endhint %}

{% hint style="warning" %}
v0 被视为旧版，但仍在受支持和维护中。没有弃用或停用日期。我们建议新集成迁移到 v2。
{% endhint %}

## 发布历史

### 未发布

* _占位内容 — 暂无确认的带日期条目。_

## 后续步骤

* [身份验证](authentication.md) · [API 参考](api-reference/)
* [开发者快速开始](quickstart.md)
