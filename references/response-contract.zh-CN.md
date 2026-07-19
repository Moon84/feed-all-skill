# 响应契约

已发布工具返回可由 JSON 表示的对象。支持便携封装的成功响应如下：

```json
{
  "success": true,
  "render_type": "rss.entry_list",
  "data": {},
  "markdown": ""
}
```

失败响应如下：

```json
{
  "success": false,
  "error": "human-readable error"
}
```

`data` 是机器可读契约，`markdown` 是没有专用渲染器的 Host 的回退内容。
Agent 不能假设每个 Host 都渲染 Markdown，也不能假设未来工具一定使用相同
的 render type。

## 当前常见类型

包括 `rss.feed`、`rss.feed_list`、`rss.entry_list`、`rss.entry_detail`、
`rss.discovery`、`rss.refresh_result`、`rss.source_health`、
`rss.entry_state`、`rss.opml_result` 和 `rss.op_result`。

## 紧凑性

优先使用列表响应。Host Agent 应设置小 limit，按时间或来源过滤，只对选中
并需要分析的条目请求正文。
