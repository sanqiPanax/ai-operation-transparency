# AI Operation Transparency

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Hermes Agent](https://img.shields.io/badge/Hermes%20Agent-skill-blueviolet)](https://hermes-agent.nousresearch.com)

> **Make every AI action visible.** A Hermes Agent skill that translates tool calls into plain language — live (toggleable) + post-task log (always on).

---

## Overview

When you say "organize my desktop," the AI executes dozens of file operations behind the scenes. What exactly did it do? This skill makes every step transparent.

**Two modes:**

| Mode | Default | What it does |
|------|---------|-------------|
| **Live Mode** 🟢 | Off (toggleable) | Before each batch of tool calls, the agent announces what it's about to do — **≤15 characters** per announcement |
| **Post-Task Log** 📋 | Always on | After task completion, a timestamped summary of every operation, grouped and translated into human language |

### Live Mode example

```
📡 Scanning all files on the desktop...
📡 Searching the web for "2026 best open source projects"...
📡 Moving photo.jpg to the "Pictures" folder...
```

### Post-Task Log example

```
📋 Operation Log
[14:23:01] Scanned desktop, found 47 files
[14:23:02] Created "Pictures" and "Documents" folders
[14:23:05] Moved 12 image files to "Pictures"
[14:23:08] Moved 35 documents to "Documents"
📌 Total: 47 operations | 10 seconds elapsed
```

---

## Why this exists

Most users interact with AI as a **black box** — input in, output out. This skill opens the box:

- **Security**: Know exactly what files were moved/deleted/modified
- **Trust**: See the AI's reasoning path before it acts
- **Learning**: New users understand what AI tools actually do under the hood
- **Debugging**: When something goes wrong, the log shows where

---

## Installation

### Prerequisites: Hermes Agent

This is a **Hermes Agent skill**. You need [Hermes Agent](https://hermes-agent.nousresearch.com) installed.

### Install from GitHub

```bash
# Clone the repo
git clone https://github.com/sanqiPanax/ai-operation-transparency.git

# Symlink or copy the skill into your Hermes skills directory
# Linux/macOS:
ln -s $(pwd)/ai-operation-transparency/SKILL.md ~/.hermes/skills/productivity/ai-operation-transparency/SKILL.md

# Or just copy:
cp ai-operation-transparency/SKILL.md ~/.hermes/skills/productivity/ai-operation-transparency/SKILL.md
```

### Load in Conversation

In any Hermes Agent session, load the skill:

```python
skill_view(name='ai-operation-transparency')
```

---

## How to Use

### Turn on Live Mode

Say this in **any language** during conversation:

> "Let me watch you work"
> "Transparency mode"
> "Show me what you're doing"
> "每一步都报一下"
> "让我看着你干"

### Turn off Live Mode

> "Stop reporting"
> "别报了"
> "Shut up and work"

### Review Post-Task Log

Post-task logs are **always generated** — just finish any task and look for the `📋 Operation Log` section at the end.

### Review Past Log

> "What did you just do?"
> "刚才你干了什么?"

---

## Language Support

The skill works in **any language** — just say your trigger phrase in the language you're using, and the agent will adapt:

| Trigger (EN) | Trigger (中文) | Trigger (日本語) |
|-------------|---------------|-----------------|
| "Let me watch" | "让我看着" | "見せて" |
| "Stop reporting" | "别报了" | "もういい" |
| "What did you do?" | "刚才干了什么?" | "何をした？" |

---

## The Translation Table

The agent translates raw tool calls into language a human can understand:

| Raw | Human translation |
|-----|------------------|
| `shutil.move('a.pdf', '/docs/')` | "Moved a.pdf to the Docs folder" |
| `terminal('pip install requests')` | "Installing requests library" |
| `web_search("latest AI news")` | "Searching the web for latest AI news" |
| `os.remove('temp.txt')` | "Deleted temp.txt" ⚠️ |

---

## Examples

- [Real-time mode walkthrough](examples/realtime-mode.md)
- [Post-task log walkthrough](examples/post-task-log.md)

---

## Roadmap

- [x] Live mode (toggleable)
- [x] Post-task log (always on)
- [x] Failure transparency (log failures honestly)
- [ ] Configurable verbosity levels
- [ ] Export log to file

---

## License

MIT

---

## Author

[sanqiPanax](https://github.com/sanqiPanax)

---

## FAQ

**Q: Does this consume extra tokens?**
A: Minimal (~50-150 tokens for post-task log, ~100-300 for live+log). The information is already in the agent's context — this skill just formats and summarizes it.

**Q: Does it slow down responses?**
A: No. The agent announces before tool calls (not during execution). No delay.

**Q: Can I use this with any AI model?**
A: This is a Hermes Agent behavioral skill — it works with any model Hermes runs on top of. It's about how the agent *reports* its actions, not a model-level feature.

**Q: Why would I want this?**
A: If you've ever wondered "what exactly did that AI just do to my files?" — this is for you.
