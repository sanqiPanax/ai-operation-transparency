# Live Mode Example: Organizing a Desktop

## Before

User: "Organize my desktop"

Agent (Live Mode OFF, default):
```
✅ Desktop organized.
```

**User sees**: "Done." No idea what happened.

---

## After (Live Mode ON)

User: "Let me watch you work" → **Live Mode ON**

User: "Organize my desktop"

```
📡 Scanning all files on the desktop...
📡 Moving image files to "Pictures" folder...
📡 Moving documents to "Documents" folder...
📡 Moving zip files to "Archives" folder...
---
📋 Operation Log
[14:23:01] Scanned desktop, found 47 files
[14:23:02] Created "Pictures", "Documents", "Archives" folders
[14:23:05] Moved 12 image files to "Pictures"
[14:23:08] Moved 35 documents to "Documents"
[14:23:10] Moved 3 archives to "Archives"
[14:23:12] Deleted 0 files
📌 Total: 50 operations | 11 seconds elapsed
```

**User sees**: Every step in real-time + a full summary. Trust through transparency.

---

## Real Conversation Log (from development)

```
User: "帮我整理一下桌面"
📡 正在扫描桌面上所有文件...
📡 正在把图片分类到"图片"文件夹...
📡 正在把文档分类到"文档"文件夹...

📋 操作记录
[14:23:01] 扫描了桌面，发现47个文件
[14:23:02] 新建了"图片"和"文档"两个文件夹
[14:23:05] 把12个图片文件移到了"图片"文件夹
[14:23:08] 把35个文档移到了"文档"文件夹
[14:23:10] 删除了0个文件
📌 合计：47次操作 | 用时10秒
```
