---
name: fetch-notion-feature
description: Fetch a feature draft from the Notion Features database by Spec ID. Use when another skill needs a Notion feature row.
---

# Fetch Notion Feature

## Inputs

- **Spec ID** — the value of the Features database's **"Spec ID"** property (e.g. `FEAT-12`). If not provided, ask for it before doing anything else.

## Workflow

### 1. Locate the Features database

Resolve the database by name each run using `notion-search`. If the search returns more than one plausible match, list them and ask the user which one.

### 2. Fetch the draft

Fetch the Features database with `notion-fetch` to get its schema and a view URL, then locate the row whose **"Spec ID"** property equals the supplied value (query the view with `notion-query-database-view`, or search for the ID string, then confirm the match). Fetch that page in full with `notion-fetch` so you have its title, properties, and all raw notes.

- If no row matches, stop and tell the user the Spec ID wasn't found.
