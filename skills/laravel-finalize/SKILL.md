---
name: laravel-finalize
description: Pre-PR gate for a Laravel feature branch — confirms `/code-review`, checks Laravel best practices, and audits for leaked secrets.
disable-model-invocation: true
---

# Laravel Finalize

The last gate before `/pr`. Three checks, all against the branch's changes since it left the default branch: `/code-review` has run, the code follows `laravel-best-practices`, and nothing sensitive leaks into a **public** repo.

This skill reports; it does not change code.

## Step 1: Confirm Laravel application

Detect Laravel by checking for **either** an `artisan` file at the project root **or** `laravel/framework` under `require` in `composer.json`.

If neither is present, respond exactly:

> This is not a Laravel application.

Then stop.

## Step 2: Pin the changed surface

Resolve the default branch (`git symbolic-ref --short refs/remotes/origin/HEAD`, falling back to `main`) and the merge-base:

```bash
BASE=$(git merge-base HEAD <default-branch>)
```

Capture, once:

- `git log $BASE..HEAD --oneline` — the branch's commits.
- `git diff $BASE --name-only` plus `git diff --cached $BASE --name-only` — changed tracked files: committed, staged, and unstaged.
- `git ls-files --others --exclude-standard` — untracked files that would ship if added.

If all three are empty, report that there is nothing to finalize and stop. Done when `BASE` resolves and the file lists are captured.

## Step 3: Confirm `/code-review`

`/code-review` reviews commits only (`$BASE...HEAD`). If Step 2 found staged, unstaged, or untracked changes, ask the user to commit them first — a branch with no commits can't be reviewed at all. If they decline, continue and record the unreviewed changes as a gap for the report.

Look in this conversation for a completed `/code-review` run against this branch. If you don't find one, ask the user whether they've run it. On **no**, invoke the `code-review` skill with `$BASE` as the fixed point and let it finish before continuing.

Record the outcome: its hard violations (documented-standard breaches and spec gaps) and whether each is resolved. When the run isn't visible in this conversation, ask the user.

Done when the code-review outcome is known: run confirmed, outstanding hard violations listed (or none), and any gap recorded.

## Step 4: Spawn both audits in parallel

Issue both sub-agent calls together, in the foreground. Give each the `BASE` SHA, the commit list, and both file lists from Step 2. Each sub-agent is **read-only**: it reports findings and edits nothing.

### Best-practices sub-agent

Brief:

> Invoke the `laravel-best-practices` skill and apply it to every changed or untracked PHP and Blade file (`git diff $BASE -- <file>` for the hunks; read the whole file when context demands). Read only the rule files relevant to the changed surface. Read the project's `CLAUDE.md` / `AGENTS.md` first: a documented project convention overrides a generic rule and is never a finding. Work **consistency-first** — before flagging "should use X", check what sibling files already do; inconsistency with the codebase is worse than a suboptimal pattern. Skip anything Pint or Larastan already enforces.
>
> For each finding give: severity (🔴 must-fix / 🟠 should-fix / 🟡 nit), `path:line`, the issue in one sentence, and a concrete fix. Group by severity. Under 500 words.

### Secrets sub-agent

Brief:

> Assume this repository is **public**: anything committed is published, including content added in one commit and removed in a later one. Audit for leaked secrets and sensitive information across:
>
> - every commit on the branch, including merge resolutions — `git log -p --cc $BASE..HEAD` (history, not just the final diff);
> - staged changes — `git diff --cached`;
> - unstaged changes — `git diff`;
> - untracked files that are not gitignored.
>
> Hunt for:
>
> - Credentials: API keys, tokens, passwords, OAuth client secrets, webhook signing secrets, private keys, `APP_KEY`, connection strings with embedded passwords.
> - Config that inlines a value instead of reading `env()` — e.g. a literal key in `config/services.php`.
> - Env files: any `.env*` other than `.env.example` tracked or about to be; real values in `.env.example`.
> - Real personal data in seeders, factories, fixtures, or tests — real names, emails, phone numbers, addresses — where fake data belongs.
> - Internal infrastructure: private hostnames, IPs, internal URLs, server paths, account or tenant IDs.
> - Stray artifacts: database dumps, logs, `storage/` contents, `.DS_Store`, editor or debug files.
>
> If `gitleaks` is installed, also run `gitleaks git --log-opts="$BASE..HEAD"` and `gitleaks dir <path>` on each untracked file, and fold its hits into your review. Verify each hit: only demonstrably synthetic placeholders (`secret`, `password`, `xxxx`, a provider's documented example key) are not findings. A genuinely issued key is a finding even when it is test-mode (`sk_test_...`) or lives in a test.
>
> For each finding give: severity (🔴 leaked secret / 🟠 sensitive data / 🟡 hygiene), `path:line` and the commit SHA if it lives in history, what is exposed (redact the value itself), and the remediation. For anything 🔴 that is in a commit, state that the secret must be **rotated** and the history rewritten before pushing — deleting the line is not enough. Under 400 words; if clean, say so in one line.

## Step 5: Report

Present the results under three headings, sub-agent reports verbatim or lightly cleaned:

- `## Code review` — the recorded outcome, outstanding hard violations, and any gap.
- `## Laravel best practices`
- `## Secrets`

End with a one-line verdict: **Ready for `/pr`** when neither audit has a 🔴 finding and `/code-review` has no outstanding hard violations; otherwise **Not ready**, naming the blockers. Append any gap from Step 3. Then stop and wait for the user.
