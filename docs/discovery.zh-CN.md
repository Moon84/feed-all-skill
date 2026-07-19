# 外部新闻发现

订阅和发现是两种不同的行为。

```text
订阅 = 长期、稳定、用户确认的来源
发现 = 临时、广泛、用于补充召回的候选
```

规划流程：

```text
查询 -> provider -> 标准化候选 -> URL/标题去重
     -> Agent 相关性判断 -> 用户确认
     -> 保存链接或建立订阅
```

外部结果默认只包含标题、URL、来源、发布时间和 snippet。不应静默写入订阅
数据库，也不应保存为全文文章。

规划中的 provider 可以包括 Google News RSS、Tavily 或 NewsAPI。provider 是
可选的发现适配器，不能替代可信的官方订阅。

规划工具名为 `rss_search_external`、`rss_save_discovered_link` 和
`rss_subscribe_discovered_source`。它们不是 0.2.x 的已发布 MCP 工具。
