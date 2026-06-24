---
name: ai-operation-transparency
description: "Use when the user wants to see what the AI is actually doing — real-time tool announcements (toggleable) + post-task operation log with timestamps, both in plain language."
version: 1.0.0
author: bro (sanqiPanax)
license: MIT
metadata:
  hermes:
    tags: [transparency, logging, trust, user-experience, observable-ai]
    related_skills: []
---

# AI Operation Transparency — 看得见的AI

> "What is the AI actually doing right now?" — This skill makes every step visible, in plain language.

- **Live Mode** (🟢 toggleable): Before each batch of tool calls, the agent announces what it's about to do in ≤15 chars of human language.
- **Post-Task Log** (✅ always on): After a task completes, a timestamped summary of every operation, grouped and translated into human language. Logs are also **saved to files** for later review.

Two golden rules:
1. **Speak human** — not `shutil.move('a.pdf', '/docs/')`, but "moved a.pdf to the Docs folder"
2. **Be brief** — Live ≤15 chars/entry, Log ≤30 chars/entry

## Live Mode (User-Toggleable)

**Turn on:** User says "let me watch you work", "transparency mode", "show me what you're doing" in any language.

**Agent behavior change:**
- Before every **batch of tool calls**, output one short plain-language line
- Format: `📡 [action description]` — max 15 characters
- Examples:

```
📡 Scanning all files on the desktop...
```
```
📡 Searching the web for "2026 best open source projects"...
```
```
📡 Moving photo.jpg to the "Pictures" folder...
```

**Turn off:** User says "stop reporting", "shut up and work", "turn off transparency", etc.

> Agent note: Check for mode-switch intent on every user message. Do not self-disable.

## Post-Task Log (Always On)

Appended automatically at task end:

```
---
📋 Operation Log
[14:23:01] Scanned desktop, found 47 files
[14:23:02] Created "Pictures" and "Documents" folders
[14:23:05] Moved 12 image files to "Pictures"
[14:23:08] Moved 35 documents to "Documents"
[14:23:10] Deleted 0 files
📌 Total: 47 operations | 10 seconds elapsed
```

**Agent rules:**

1. Log one entry per **logical phase** (not per atomic tool call)
2. At task end, output all entries with timestamps in plain language
3. Timestamps precise to seconds
4. Short (1-3 steps) → just 1-2 lines
5. Long (20+ steps) → **merge** — "Moved 35 documents" not 35 individual lines

## Log Storage (File Persistence)

In addition to appearing in the conversation, every post-task log is **saved to a file** so you can review past operations.

### File Location

```
{HERMES_HOME}/logs/operations/
```

Default on each platform:

| Platform | Path |
|----------|------|
| **Linux / macOS** | `~/.hermes/logs/operations/` |
| **Windows** | `~/AppData/Local/hermes/logs/operations/` |

### File Naming

Each task creates one file with a task-based name:

```
2026-06-24_15-30-00_your-request-summary.md
```

The summary part is a short slug of what the task was about (auto-generated from the user's request):

```
2026-06-24_15-30-00_search-ai-naming-research.md
2026-06-24_16-45-12_organize-desktop.md
2026-06-25_09-12-33_fix-bug-in-auth-module.md
```

### File Content

Each saved file contains the full operation log with a header:

```markdown
# AI Operation Log
**Task:** organize desktop
**Started:** 2026-06-24 15:30:00
**Duration:** 11 seconds
**Total operations:** 47

---

📋 Operation Log
[15:30:01] Scanned desktop, found 47 files
[15:30:02] Created "Pictures" and "Documents" folders
[15:30:05] Moved 12 image files to "Pictures"
[15:30:08] Moved 35 documents to "Documents"
[15:30:10] Deleted 0 files
📌 Total: 47 operations | 11 seconds elapsed
```

### Save Procedure (Agent Instructions)

When a task ends with a post-task log:

1. Construct the log content (same as what you show in chat)
2. Use `write_file(path=...)` to save it to `{HERMES_HOME}/logs/operations/YYYY-MM-DD_HH-MM-SS_task-slug.md`
3. Derive the task slug from the user's request — short, lowercase, hyphens for spaces. Max 40 chars.

### Reading Old Logs

User can ask:
- "What did I do yesterday?" → `search_files(pattern='2026-06-24*', path='~/AppData/Local/hermes/logs/operations/')`
- "Show me the log from when I organized my desktop" → search within the directory
- "What was the last thing I did?" → `ls -t` on the operations directory, read the newest file

### When NOT to Save

- **Trivial single-step tasks** ("what's the weather", "translate this") — no log file needed
- **User explicitly says** "don't save that"
- **Sensitive operations** the user marks as private — agent can ask or respect context

## Human Translation Table (Agent Internal Reference)

| Raw Action | Human Translation |
|-----------|------------------|
| `read_file()` | "Reading XXX file" |
| `write_file()` | "Writing to XXX file" |
| `terminal('pip install ...')` | "Installing XXX dependency" |
| `terminal('git clone ...')` | "Cloning XXX repo from GitHub" |
| `web_search()` | "Searching the web for XXX" |
| `browser_navigate()` | "Opening XXX webpage" |
| `execute_code()` | "Running a Python script" |
| `search_files()` | "Searching the filesystem" |
| `patch()` | "Modifying part of XXX file" |
| `delegate_task()` | "Spawning a sub-agent to do XXX" |
| `memory(action='add')` | "Remembering XXX" |
| `terminal(command='rm ...')` | "Deleting XXX" ⚠️ |

Never expose raw tool names. Every entry is a complete, short sentence that the user can understand without technical knowledge.

## Failure Handling

Tool calls can fail (timeout, blocked API, bot detection). Log failures honestly — don't hide them:

| Scenario | Log entry |
|---------|-----------|
| Search blocked | `📡 Tried to search XXX, blocked by target site` |
| Tool timeout | `📡 Tried to search XXX, request timed out` |
| Strategy switch | `📡 Search engine A failed, switched to strategy B` |
| All failed | `📡 All search methods failed, using internal knowledge` |

Post-Task Log also reflects failures:
```
[14:57:30] Bing keyword matching was terrible, Scholar/arXiv blocked
[14:57:35] Switched strategy: used training data
📌 Total: 6 search attempts | 5 failed, 1 partial success
```

**Failure is information.** The user deserves to know "I tried but it didn't work."

## When to Use

- User says "let me watch you work", "transparency", "log", "show me what you're doing"
- User asks "what did you just do" (triggers post-task log review)
- Non-technical users unfamiliar with AI capabilities
- Operations involving file moves, deletes, modifications (irreversible actions)
- User explicitly asks for step-by-step reporting

## Don't Use For

- **Pure conversational Q&A (no tool calls)**
- User explicitly says "just do it, don't explain"
- Very short single-step operations ("check the weather")
- User keeps saying "I know, stop reporting"

## Mode Switching

The agent detects mode switches from **natural language**. No special commands needed:

| User Says | Behavior |
|-----------|----------|
| "Let me watch you work" | Turn on Live Mode |
| "Show me what you're doing" | Turn on Live Mode |
| "Transparency on" | Turn on Live Mode |
| "Stop reporting" | Turn off Live Mode |
| "Shut up and work" | Turn off Live Mode (log stays on) |
| "What did you just do" | Output post-task log only |

## Common Pitfalls

1. **Live mode spam** — One notification every second will annoy the user.
   - **Merge strategy**: Group related operations → "Moving 47 files..."
   - Report by **logical phase**, not by individual tool call

2. **Post-task log too long** — Auto-merge when exceeding 20 entries.
   - Don't list "moved a.docx" "moved b.docx" → merge to "Moved 12 documents"

3. **Agent forgetting it's in Live Mode** — If user hasn't said to stop, keep it on.

4. **Abrupt cancellation** — When user says "stop", don't output log. Just stop.

5. **Inconsistent timestamp format** — Always use `[HH:MM:SS]`. No mixing `14:23` with `2:23 PM`.

6. **Log directory doesn't exist** — Agent must create it. Use `write_file` which auto-creates parent directories.

## Comparison: On vs Off

| | No Transparency | Post-Task Log Only | Live + Log |
|--|----------------|-------------------|------------|
| User sees | "Done" | "Done" + operation list | Step-by-step + final list |
| Trust | Low | Medium | High |
| Token overhead | 0 | ~50-150 | ~100-300 |
| Disk usage | 0 | ~0.5-2KB per task | ~0.5-2KB per task |
| Best for | High-trust/routine | File ops/first time | Sensitive ops/teaching |

## Verification Checklist

- [ ] Live Mode: brief human announcement before each tool batch (≤15 chars)
- [ ] Post-Task Log: appended at task end, has timestamps
- [ ] Language: plain human language, no raw tool names
- [ ] Merged: similar operations grouped, no line-by-line spam
- [ ] Toggle: user says stop → stops, user says start → starts
- [ ] Failures: logged honestly, not hidden
- [ ] File saved: log written to `{HERMES_HOME}/logs/operations/YYYY-MM-DD_HH-MM-SS_task.md`
- [ ] Trivial tasks skipped: no file saved for single-step ops
