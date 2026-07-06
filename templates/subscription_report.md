# 📡 RSS 订阅报告

**日期**: {{date}}
**总计**: {{count}} 个订阅源

---

## 订阅源列表

{{#feeds}}
### {{name}}
- **URL**: `{{url}}`
- **类型**: {{type}}
- **分类**: {{category}}
- **文章数**: {{entry_count}}
- **健康状态**: {{health_status}}
  - 上次刷新: {{last_polled_at}}
  - 连续失败: {{consecutive_failures}}
  - 自动禁用: {{auto_disabled}}
{{/feeds}}

---

## 健康摘要

| 状态 | 数量 |
|------|------|
| ✅ 正常 | {{healthy_count}} |
| ⚠️ 有失败记录 | {{warning_count}} |
| ❌ 自动禁用 | {{disabled_count}} |