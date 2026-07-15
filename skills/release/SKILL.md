---
name: release
description: Creates a new GitHub release with auto-generated notes from commit history since the last tag. Use when the user asks to "create a release," "tag a release," "ship a new version," "cut a release," or types `/release`. For a read-only preview without tagging, pass `--preview` or use when the user asks to "preview release notes."
---

# Create Release

Create a new GitHub release, or preview what the next release would look like.

## Usage

```text
/release [major|minor|patch] [--preview]
```

If `--preview` is passed, run Steps 1–4 only and display the projected notes — no tagging, pushing, or publishing. Works on any branch.

If no bump type is provided, determine it from commit analysis in Step 2 without pausing to ask.

## Step 0: Verify branch (skip if `--preview`)

```bash
git branch --show-current
```

If the result is anything other than `main`, abort:

> Releases can only be created from the `main` branch. You are currently on `<branch-name>`. Please switch to `main` and try again.

## Step 1: Gather context

```bash
git tag --sort=-version:refname | head -20
```

Identify the most recent semver tag (format `vX.Y.Z`). If no tags exist, the initial version is `v0.1.0`.

Fetch all commits since that tag:

```bash
git log <last-tag>..HEAD --pretty=format:"%H|||%s|||%b" --no-merges
```

## Step 2: Analyze commits and determine version bump

If a bump type argument was provided, use it. Otherwise categorize each commit using these rules, in priority order:

- **Major** — any commit with `BREAKING CHANGE` in the body, or `!` after the type (e.g. `feat!:`)
- **Minor** — type `feat`, or freeform commits that clearly introduce new functionality
- **Patch** — types `fix`, `perf`, `refactor`, or freeform commits that are bug fixes, dependency updates, or maintenance

## Step 3: Calculate the next version

Apply the bump to the last tag:

- `major`: increment X, reset Y and Z to 0
- `minor`: increment Y, reset Z to 0
- `patch`: increment Z only

## Step 4: Generate release notes

```markdown
## What's Changed

### New Features

- Description of feature (commit sha short)

### Bug Fixes

- Description of fix (commit sha short)

### Improvements & Refactoring

- Description of improvement (commit sha short)

### Maintenance

- Description of maintenance work (commit sha short)

### Other Changes

- Any commits that don't fit the above categories
```

Guidelines:

- Write for a technical audience but avoid raw commit message text where it's cryptic.
- For conventional commits, use the description after the type prefix as the basis.
- For freeform commits, summarize the intent based on the message.
- Group related commits under a single bullet where it makes sense.
- Omit categories with no entries.
- Omit merge commits.

## Step 5: Display and finish

**If `--preview`**, display the following and stop:

```text
Projected version: vX.Y.Z → vA.B.C
Suggested bump: <major|minor|patch>

Release Notes:
---
[generated notes]
---

Run `/release` to create this release.
```

**Otherwise**, display the full plan, then immediately proceed to tag and release:

```text
Version: v1.2.3 → v1.3.0
Tag: v1.3.0

Release Notes:
---
[generated notes]
---

This will:
  1. Create and push git tag v1.3.0
  2. Create GitHub release "v1.3.0" with the above notes
```

Create and push the tag:

```bash
git tag v1.3.0
git push origin v1.3.0
```

Create the GitHub release:

```bash
gh release create v1.3.0 \
  --title "v1.3.0" \
  --notes "<generated release notes>"
```

Confirm success by showing the release URL returned by `gh`.
