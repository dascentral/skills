---
name: grill-from-notion
disable-model-invocation: true
description: Fetch a Notion feature by Spec ID and grill it via /grill-with-docs.
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
