# Personalization

The host Agent may read a user-provided Markdown profile to rank entries and
form discovery queries. The profile is user-owned and must not be silently
rewritten by the skill.

Recommended sections:

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

The safe feedback loop is:

```text
Agent proposes preference change -> user confirms -> host updates profile
                                 -> future ranking uses the confirmed profile
```

EntroFeed may derive durable interests or knowledge artifacts from confirmed
feedback. `feed-all` remains responsible for source and entry operations.
