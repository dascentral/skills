---
name: pr
description: Opens a GitHub pull request. Use when the user asks to "open a PR," "create a pull request," "ship this as a PR," "submit for review," or types `/pr`. Commits and pushes first if needed.
---

# PR

Open a pull request. Commit and push first if the branch needs it.

## Steps

### 1. Check for uncommitted changes

Run `git status`. If there are uncommitted changes, invoke the `commit` skill before continuing.

### 2. Push if needed

Check whether the branch has an upstream and whether any local commits are ahead of it. If the branch is unpushed or has commits the remote doesn't:

```bash
git push --set-upstream origin <branch>
```

### 3. Open PR

```bash
gh pr create --title "<commit subject>" --body ""
```

Derive the title from the most recent commit subject. Include a body only when there are multiple commits or the scope warrants a brief summary.

Done when the PR URL is shown.
