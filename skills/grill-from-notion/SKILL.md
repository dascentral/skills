---
name: grill-from-notion
description: Fetches a feature draft from the Notion "Features" database and hands it to /grill-with-docs for a relentless engineering interview. Use when the user wants to grill or sharpen a Notion feature before engineering begins or types `/grill-from-notion <ID>`.
---

# Grill from Notion

## Inputs

- **Spec ID** — the value of the Features database's **"Spec ID"** property (e.g. `FEAT-12`). If they didn't provide one, ask for it before doing anything else.

## Workflow

### 1. Fetch the feature

Invoke `/fetch-notion-feature` with the supplied Spec ID.

### 2. Hand off to /grill-with-docs

Invoke `/grill-with-docs`, passing it:

- The feature's **title** and **Spec ID**
- The full **raw notes** from the Notion page
- Any structured properties (owner, deadline, scope) surfaced by the schema

From this point, `/grill-with-docs` owns the session — let it drive the interview.
