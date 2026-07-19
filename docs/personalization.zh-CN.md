# 个性化

Host Agent 可以读取用户提供的 Markdown profile，用于条目排序和发现查询。该
profile 属于用户，skill 不能静默改写。

建议章节：

```text
Role
Tracking Goals
High-Value Signals
Preferred Sources
Excluded Topics
Discovery Policy
Output Preference
Retention Preference
```

安全反馈流程：

```text
Agent 提议偏好变化 -> 用户确认 -> Host 更新 profile
                  -> 后续排序使用已确认偏好
```

EntroFeed 可以根据已确认反馈派生长期兴趣或知识产物。`feed-all` 仍只负责
来源和条目操作。
