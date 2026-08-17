# Skills Repo

Skills for Claude Code and other AI coding agents, installed via the [skills CLI](https://github.com/vercel-labs/skills). Each skill is one directory under `skills/` containing a `SKILL.md`.

## Writing a Skill

1. Create a directory under `skills/` and add a `SKILL.md` with YAML frontmatter (`name`, `description`).
2. The `description` is the skill's **context pointer** — the only thing the agent sees when deciding whether to load it. Write it as trigger conditions, not a summary: what the skill does and every distinct situation that should invoke it.
3. Write the body as steps with clear exit criteria, not prose. A skill is a workflow the agent runs, not a reference doc it consults.
4. Test by invoking the skill and checking that the agent takes the same process each run.
