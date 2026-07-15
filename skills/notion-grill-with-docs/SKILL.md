---
name: grill-with-docs-from-notion
description: Fetches a feature draft from the Notion "Features" database and hands it to /grill-with-docs for a relentless engineering interview. Use when the user wants to grill or sharpen a Notion feature before engineering begins, asks to "grill FEAT-12," "interview the design for <feature ID>," or types `/grill-with-docs-from-notion <ID>`.
---

# Grill With Docs From Notion

Fetch a feature draft from Notion and feed it to `/grill-with-docs` for a relentless interview that sharpens the design before engineering starts.

## Inputs

- **Spec ID** — the value of the Features database's **"Spec ID"** property (e.g. `FEAT-12`). The user supplies this. If they didn't, ask for it before doing anything else.

## Workflow

### 1. Locate the Features database

Resolve the database by name each run using `notion-search` — never hardcode an ID or URL. If the search returns more than one plausible match, list them and ask the user which one.

### 2. Fetch the draft

Fetch the Features database with `notion-fetch` to get its schema and a view URL, then locate the row whose **"Spec ID"** property equals the supplied value (query the view with `notion-query-database-view`, or search for the ID string, then confirm the match). Fetch that page in full with `notion-fetch` so you have its title, properties, and all raw notes.

- If no row matches, stop and tell the user the Spec ID wasn't found.

### 3. Hand off to /grill-with-docs

Invoke `/grill-with-docs`, passing it:

- The feature's **title** and **Spec ID**.
- The full **raw notes** from the Notion page.
- Any structured properties (owner, deadline, scope) surfaced by the schema.

From this point, `/grill-with-docs` owns the session — let it drive the interview.
