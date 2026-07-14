# Skills Repo

This repo contains agent skills for use with Claude Code and other AI coding agents via the [skills CLI](https://github.com/vercel-labs/skills).

## Structure

Skills live under `skills/`, one subdirectory per skill, each containing a `SKILL.md` file. Uncategorized until volume justifies it.

## Writing Skills

Each `SKILL.md` requires YAML frontmatter with `name` and `description`. The description is the only thing the agent sees when deciding whether to load the skill, so it should be specific about what the skill does and when to invoke it.

A skill is a workflow, not a reference doc. Prefer steps with clear exit criteria over prose explanations.

## Installing

```bash
npx skills@latest add dascentral/skills
```
