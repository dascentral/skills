---
name: spec-from-notion
description: Turns a draft feature in the Notion "Features" database into a structured spec on disk. Use when the user asks to "load spec FEAT-12," "plan the feature from Notion," "write the spec for <feature ID>," or types `/spec-from-notion <ID>`.
---

# Spec From Notion

Turn a raw draft in the Notion "Features" database into a reviewed spec checked into the repository.

## Inputs

- **Spec ID** — the value of the Features database's **"Spec ID"** property (e.g. `FEAT-12`). The user supplies this. If they didn't, ask for it before doing anything else.

## Workflow

### 1. Locate the Features database

Resolve the database by name each run using `notion-search` — never hardcode an ID or URL. If the search returns more than one plausible match, list them and ask the user which one.

### 2. Find the draft by its Spec ID

Fetch the Features database with `notion-fetch` to get its schema and a view URL, then locate the row whose **"Spec ID"** property equals the supplied value (query the view with `notion-query-database-view`, or search for the ID string, then confirm the match). Fetch that page in full with `notion-fetch` so you have its title, properties, and all raw notes.

- If no row matches, stop and tell the user the Spec ID wasn't found. Do not guess.

### 3. Triage the draft

Read the raw notes critically. Surface gaps, ambiguities, and assumptions across these four areas:

- What problem is this solving, and for whom?
- Scope boundaries — what's explicitly in and out.
- Constraints (deadlines, compatibility, performance) and edge cases.
- How success is measured.

Ask questions one cluster at a time and wait for answers. Proceed to step 4 when every area above has a concrete answer, or the user confirms the notes are sufficient.

### 4. Generate the spec

Invoke the `agent-skills:spec` skill to produce a structured specification from the triaged draft. Feed it the feature's intent, notes, and the answers gathered in step 3. Step 4 is complete when the spec skill returns a structured document.

### 5. Write the spec to disk

Save it under `docs/specs/` (create the directory if it doesn't exist) with the filename:

```text
docs/specs/YYYY-MM-DD-feature-name.md
```

- `YYYY-MM-DD` is today's date.
- `feature-name` is a kebab-cased slug derived from the draft's title (fall back to a short description if the title is unusable). Lowercase, words separated by hyphens, no special characters.

Confirm the file was written before continuing.

### 6. Update the Notion record to "In Review.

Only after the file exists on disk, use `notion-update-page` with the `update_properties` command to set the record's **Status** property to **In Review**.

- The update tool requires exact property names — rely on the schema you fetched in step 2 to confirm the property is named "Status" and the option is "In Review." If either differs, ask the user rather than guessing.

### 7. Report

Tell the user the spec's path, the slug used, and confirm the Notion status was updated.
