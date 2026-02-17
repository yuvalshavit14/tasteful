---
name: tasteful-sync
description: "Reconcile the project knowledge base with recent code changes. Use when the user says 'tasteful sync', 'sync knowledge base', 'update knowledge base', or after a round of implementation to catch up the docs with reality."
---

# Sync — Reconcile Knowledge Base with Codebase

You review recent code changes and update the project knowledge base to match reality. This ensures that `product-info.md`, `principles.json`, `ui-flows.json`, and `decisions-log.json` stay accurate as the codebase evolves.

## When to Use

- After `/tasteful-implement` completes (suggested automatically)
- After manual development work outside of tasteful
- Periodically to catch drift
- When onboarding a teammate to verify docs are current

## Process

### Step 1: Determine Scope

Check for a sync marker in the project's memory directory (`last-sync.json`). This file contains the commit hash from the last sync.

- **If marker exists**: scope is all changes since that commit
- **If no marker**: ask the developer:
  - "Full reconciliation" — compare the entire codebase against the knowledge base
  - "Since branch/commit X" — specific range
  - "Recent changes" — last 20 commits

### Step 2: Read Recent Changes

Use git to understand what changed within the scope:

```bash
# If marker exists
git log --stat <last-sync-hash>..HEAD
git diff --name-only <last-sync-hash>..HEAD

# Or for a full reconciliation, explore the codebase directly
```

### Step 3: Categorize Changes

Group the changed files by what knowledge base document they affect:

**Routes & Flows** → `ui-flows.json`
- New or modified `page.tsx` files under `app/`
- New or modified `route.ts` API files under `app/api/`
- Changes to middleware, auth guards, or navigation components
- Changes to layout files that affect page structure

**Patterns & Conventions** → `principles.json`
- Repeated new patterns across multiple files (e.g., all new routes use Zod validation)
- Changed conventions (e.g., switched from one pattern to another)
- New libraries or tools that imply new conventions

**Product & Architecture** → `product-info.md`
- New dependencies in `package.json`
- New database migrations (schema changes)
- New external service integrations
- Significant architectural changes (new directories, restructured code)
- Features that moved from "planned" to "built"

**Decisions** → `decisions-log.json`
- Significant code choices visible in diffs but not logged (e.g., chose library X over Y, refactored Z approach)

### Step 4: Read Current Knowledge Base

Read all knowledge base files from the project's memory directory:
- `product-info.md`
- `principles.json`
- `ui-flows.json`
- `decisions-log.json`

If `ui-flows.json` doesn't exist yet, note that it needs to be created.

### Step 5: Cross-Reference and Identify Updates

For each category from Step 3, compare against the current knowledge base:

**Gaps** — things in the codebase not reflected in the knowledge base:
- New routes not in ui-flows.json
- New patterns not in principles.json
- New tech/features not in product-info.md

**Stale info** — things in the knowledge base no longer true in the codebase:
- Routes that were removed or significantly changed
- Principles that the code no longer follows
- Tech stack entries that were replaced
- Product state descriptions that are outdated

**Implicit decisions** — significant choices made in code but not logged:
- Choosing one approach over another
- Architectural decisions visible in the diff

**Uncodified patterns** — decisions that should be principles:
- Read decisions-log.json and look for 2+ decisions with source "developer" on similar topics
- If the same type of question was answered the same way multiple times, flag it: "This decision was made N times — consider making it a principle via `/tasteful-learn`"

### Step 6: Present Findings

Present the proposed updates grouped by file. Be specific about what to add, update, or remove:

```
## Proposed Knowledge Base Updates

### ui-flows.json
- ADD: /create/map-characters route object (auth: required, needs story with characters)
- UPDATE: /browse route object — added "My Characters" tab
- REMOVE: /create/select-protagonist route object (merged into /create/map-characters)

### principles.json
- ADD: { domain: "API", principle: "Validate all request bodies with Zod schemas" }
- UPDATE: p-007 — Code now uses oklch colors in new components
- No removals

### product-info.md
- UPDATE: Tech Stack — add @playwright/test for dev testing toolbox
- UPDATE: Current Product State — template flow is fully built
- UPDATE: Core User Flows — add template browsing flow

### decisions-log.json
- ADD: { topic: "UX", decision: "Chose bottom sheet over modal for character picker" }

### New: ui-flows.json (needs creation)
- [Full flow map based on current routes]
```

### Step 7: Apply Updates

After the developer approves (or approves with modifications):

1. Read each file to be updated
2. Apply the approved changes using Edit or Write
3. For `ui-flows.json` creation, follow this JSON array format:

```json
[
  {
    "flow": "Story Creation",
    "routes": [
      {
        "path": "/create/select-gender",
        "auth": "none",
        "description": "Gender/pronoun selection for the child character",
        "data": "StoryContext (localStorage)",
        "navigatesTo": ["/create/select-age"]
      }
    ]
  }
]
```

Group routes by user flow, not alphabetically.

### Step 8: Save Sync Marker

Write the current HEAD commit hash to `last-sync.json` in the memory directory:

```json
{
  "commit": "[hash]",
  "date": "[YYYY-MM-DD]",
  "scope": "[what was reviewed]",
  "changes": "[brief summary of updates made]"
}
```

This allows the next `/tasteful-sync` to pick up from where this one left off.

## What This Skill Does NOT Do

- Does not make changes without developer approval
- Does not rewrite the entire knowledge base (incremental updates only)
- Does not replace `/tasteful-learn` for explicit principle corrections
- Does not replace `/tasteful-onboard` for initial setup
