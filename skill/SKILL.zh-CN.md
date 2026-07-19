---
name: feed-all
description: 通过 feed-all MCP 服务执行跨 Host 的 RSS、Atom、网页订阅、刷新、搜索和按需阅读。
tags: [rss, atom, webpage, mcp, codex, claude]
metadata:
  canonical_skill: feed-all
  compatibility_alias: rss-plugin-installer
  mcp_server: feed-all
---

# feed-all Agent Skill

使用 `feed-all` MCP 服务维护长期订阅并获取紧凑条目。运行时
`tools/list` 是权威能力来源。

## 意图路由

| 意图 | 工具 |
| --- | --- |
| 查找 Feed | `rss_discover` 或 `rss_fetch` |
| 建立订阅 | `rss_subscribe` |
| 刷新来源 | `rss_refresh` |
| 列出或搜索条目 | `rss_list_entries` 或 `rss_search` |
| 阅读选中内容 | `rss_get_entry` |
| 诊断来源 | `rss_check_health` |

## 默认工作流

```text
发现 -> 验证 -> 订阅 -> 刷新 -> 筛选
  -> 读取选中条目 -> 总结或提醒
```

先读取元数据，使用小 limit。只对选中并需要分析的条目读取链接正文。
成功阅读后再标记已读。

## 持久化和安全

- 来源元数据、条目元数据、链接和阅读状态会持久化。
- 已发布 0.2.x 服务可能缓存按需读取的正文。
- 正文保留上限属于规划内容，不能写成当前 API 已提供的能力。
- 不绕过 CAPTCHA、认证、付费墙、robots 策略或反爬挑战。
- 不订阅私有或未获授权的来源。

## Agent 职责

Skill 不运行 scheduler 或 Digest 循环。Host Agent 可以在用户配置的定时
任务中调用 `rss_refresh`，之后完成筛选、总结和提醒。用户 profile 可用于
排序，但修改 profile 必须经过用户确认。

旧的 `rss-plugin-installer` 名称是本 skill 的兼容别名。
