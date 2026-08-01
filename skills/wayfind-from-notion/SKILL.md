---
name: wayfind-from-notion
disable-model-invocation: true
description: Fetch a Notion feature by Spec ID and wayfind its implementation.
---

# Wayfind from Notion

## Inputs

- **Spec ID** — the value of the Features database's **"Spec ID"** property (e.g. `FEAT-12`). If they didn't provide one, ask for it before doing anything else.

## Workflow

### 1. Fetch the feature

Invoke `/fetch-notion-feature` with the supplied Spec ID.

### 2. Hand off to /wayfinder

Invoke `/wayfinder`, passing it:

- The feature's **title** and **Spec ID**
- The full **raw notes** from the Notion page
- Any structured properties (owner, deadline, scope) surfaced by the schema

From this point, `/wayfinder` owns the session — let it drive the work.
