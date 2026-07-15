---
name: commit
description: Stages and commits pending changes with a conventional commit message. Use when the user asks to "commit," "commit these changes," "make a commit," types `/commit`, or when another skill needs to commit before continuing.
---

# Commit

Stage and commit all pending changes. Do not push.

## Steps

### 1. Review

Run `git status` and `git diff` (staged and unstaged). Understand what changed — this drives the commit message.

### 2. Stage

If specific files are already staged, keep them. Otherwise:

```bash
git add -A
```

### 3. Commit

Write a conventional commit message derived from the diff — not from the user's request phrasing. Use the appropriate type (`feat`, `fix`, `refactor`, `chore`, etc.) and a concise subject. Add a short body only when the "why" isn't obvious from the subject.

Done when the commit is confirmed with a SHA.
