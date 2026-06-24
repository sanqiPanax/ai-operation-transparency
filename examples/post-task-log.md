# Post-Task Log Example: Searching & Failing

Post-task logs are **always on** — even when the search goes wrong. Failures are logged honestly.

## Scenario: Multiple search engines blocked

Agent tries to find research on a topic. Google, Scholar, Bing all fail.

```
📡 Google blocked... trying Bing...
📡 Bing also blocked... trying DuckDuckGo...
📡 DuckDuckGo lite not returning results...
📡 Trying Jina search...
📡 Jina requires API key...
📡 Switching strategy: using internal knowledge
---
📋 Operation Log
[14:56:12] Tried Google search → blocked by captcha
[14:56:15] Tried Google Scholar → blocked
[14:56:20] Tried Bing international → irrelevant results
[14:56:30] Tried DuckDuckGo lite → no results returned
[14:56:40] Tried Jina search → requires API key
[14:56:45] Switched strategy: using training data
📌 Total: 6 search attempts | 5 failed, 1 partial success
```

**User sees**: Not just "I couldn't find it" — but *why* and *what was tried*.

---

## Scenario: File operation (delete)

```
📋 Operation Log
[15:01:02] Listed files in temp folder, found 23
[15:01:03] Moved 20 cache files to Recycle Bin
[15:01:04] Kept 3 config files (recently modified)
📌 Total: 23 operations | 2 seconds elapsed
```

**User sees**: "You want me to clean temp? Here's exactly what I did. 3 files kept because they were recently modified — just in case."

---

## Scenario: Code generation

```
📋 Operation Log
[15:10:01] Read existing file app.py (342 lines)
[15:10:05] Searched for "requests library API" for reference
[15:10:10] Wrote new function fetch_data() to app.py
[15:10:12] Ran syntax check → passed
📌 Total: 4 operations | 11 seconds elapsed
```

**User sees**: The agent didn't just "write code" — it researched, wrote, and verified.
