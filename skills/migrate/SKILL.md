---
name: migrate
description: This skill should be used when the user asks to "migrate from Notion", "import my Notion export", "resume migration", "migrate my tasks", "migrate my projects", "migrate my databases", or mentions continuing a multi-session vault migration already in progress. Imports structured content from Notion (or other PKM tools) into an Obsidian vault via phased, batch migration with human review at every step. Requires a vault with an established schema (CLAUDE.md).
version: 0.1.0
---

# Vault Migration

Imports structured content from Notion (or other PKM tools) into an Obsidian vault.
Migration is phased by entity type, batched within each phase, and always human-in-loop —
nothing is written without explicit user approval of representative samples first.

## Prerequisites

Before writing any files:

1. **Confirm vault schema** — A `CLAUDE.md` must exist defining frontmatter schemas, folder structure,
   and naming conventions. If missing, run `vault-org:spec` first.
2. **Locate export files** — Ask the user to confirm the path to the export directory. Notion
   exports one CSV per database.
3. **Agree on scope** — Identify what to migrate and what to skip (personal projects, canceled
   items, deprecated databases). Document exclusions in `Current State.md` so they are not
   re-examined in later sessions.
4. **Check platform** — On Windows enterprise machines, Python is often unavailable. Use
   PowerShell for all CSV parsing (see `references/csv-parsing.md`).
5. **OneDrive warning** — If the vault is OneDrive-synced, ask the user to pause sync before
   bulk write operations to prevent file conflicts.

---

## Phase Order

Migrate in dependency order. Never create a child entity before its parent exists — broken
wikilinks produce silent Dataview failures.

| Phase | Entity | Vault destination | Depends on |
|-------|--------|------------------|------------|
| 1 | Products | `03. Resources/Products/` | — |
| 2 | Versions | `03. Resources/Products/` | Products |
| 3 | Projects | `01. Projects/[Name]/[Name].md` | Products |
| 4 | Tasks | `01. Projects/[Project]/tasks/[Task]/` | Projects |
| 5 | Meetings | `03. Resources/Meeting Notes/` | Projects, Stakeholders |
| 6+ | Domain-specific | User Research, Literature, etc. | As applicable |

If the user asks to start with Tasks (Phase 4), explain that Projects must exist first for
`project: "[[Name]]"` links to resolve correctly.

---

## Migration Workflow

### Step 1: Parse and Summarise the CSV

Parse the export CSV. Present a summary table — do not write any files:

```
Database: Tasks (288 rows)

By status:
  131  Done       33  In Progress
   60  Not Started    40  Canceled
   16  Paused          6  Review

By project (top 5):
  [15]  Project A       [12]  Project B
  [117] (no project)

Orphaned records (no parent): 117
Suggested exclusions: canceled personal projects — skip?

Confirm scope and proceed to batch plan?
```

### Step 2: Agree on Architecture (once per entity type)

Before writing any notes for a new entity type, confirm the folder/file pattern. Show a
concrete example of the directory structure on disk. For Tasks, this is critical — the
pattern cannot be changed after dozens of notes are written.

See `references/task-architecture.md` for the recommended always-folder convention (hub note,
attachments/, completed/).

### Step 3: Present the Batch Plan

Propose batches of 10–20 task folders, fewer for complex entities such as Projects.
Group batches by logical domain (project, product area) not by status.

```
Batch plan — Tasks (160 in scope after exclusions):

Batch 1 — Project A: ~15 tasks (8 active + 7 completed)
Batch 2 — Project B: ~12 tasks
...
Batch N — Orphaned tasks: ~20 tasks (project assignment needed first)

Proceed with Batch 1?
```

### Step 4: Show Samples Before Writing

For each batch, show 3 representative notes in full before writing any files:

```
Sample 1 of 3 — Task Name:
━━━━━━━━━━━━━━━━━━━━━━━━━━
---
type: task
status: in-progress
priority: medium
project: "[[Project A]]"
product: "[[Product X]]"
parent-task: ""
due-date: 2026-03-31
owner: "[[Owner Name]]"
stakeholders: []
tags: [task]
---

# Task Name

## Context
Why this task exists.

## Sub-tasks
- [ ] Sub-item one
━━━━━━━━━━━━━━━━━━━━━━━━━━
[Samples 2 and 3...]

Approve and write remaining N notes?
```

Revise all samples if the user requests changes before writing. Explicit approval required.

### Step 5: Write the Batch

Write all notes for the approved batch. For folder-based entities (tasks, projects), create
the full folder structure — hub note, sub-folders, sub-notes — in one pass.

Confirm count after writing:

```
Batch 1 complete:
  Created: 8 active task folders in tasks/
  Created: 7 completed task folders in tasks/completed/
  Skipped: 2 (no content, trivial admin)

Proceed to Batch 2?
```

### Step 6: Handle Orphaned Records

Records without a valid parent entity (tasks with no project, versions with no product)
require explicit user decisions — never auto-assign or silently drop.

Present a grouped table with proposed assignments based on name/topic pattern matching:

```
Orphaned tasks — proposed assignments:

  "AI-ADR Update"       → Project B   (topic pattern match)
  "CADDIE IFU Review"   → Project A   (name prefix match)
  "Review Jun's email"  → unclear — skip?

Confirm, adjust, or skip?
```

Document all decisions in `Current State.md`.

### Step 7: Update Current State.md

Update `Current State.md` at the vault root after each batch. This file is the single
source of truth for cross-session continuity. See `references/cross-session.md` for the
required structure.

Key sections:
- Migration status table (all phases, current status)
- Remaining batch plan for the current phase
- Next batch specification (exact folder names, statuses, notes)
- Exact resume instruction for the next session

### Step 8: Post-Migration Verification

After the final batch, before declaring migration complete:

1. Read 5 random notes — verify frontmatter is correct
2. Test key Dataview queries from the vault schema — confirm results make sense
3. Check bi-directional links — both sides of key entity relationships must be populated
   (see `references/entity-graph.md`)
4. Search for `type: task` notes with empty `project:` field — identify stragglers
5. Update vault `CLAUDE.md` if any new schema properties were introduced during migration

---

## Examples

### Example 1: Starting a fresh Notion migration

User says: "Migrate my Notion databases. Exports are at C:/Users/me/Downloads/notion-export/"

Steps:
1. Read each CSV → present summary → confirm scope and exclusions
2. Phase 1: show 3 product note samples → user approves → write 10 product notes
3. Phase 2: propose lean version migration (current + planned only) → user agrees → write 15 version notes
4. Phase 3: present project batch plan → write project hubs by batch
5. Update `Current State.md` after each phase
6. Resume next session with "Resume Phase 4 — Tasks Batch 1"

Result: All entities in correct vault folders; `Current State.md` captures full progress.

### Example 2: Resuming a migration in progress

User says: "Resume migration — Tasks Batch 2"

Steps:
1. Read `Current State.md` — find batch plan and batch 2 scope
2. Skip to Step 3 — confirm batch 2 details, show 3 samples, get approval
3. Write batch 2 notes, update `Current State.md`

Result: Batch 2 written, progress updated, user prompted for batch 3.

### Example 3: Handling orphaned tasks

User says: "There are 117 tasks with no project in my export"

Steps:
1. Group orphaned tasks by name/topic pattern
2. Present assignment table with proposed project per group
3. For unclassifiable tasks, propose: skip or assign to a catch-all project
4. On confirmation, write notes to correct project task folders
5. Log all decisions in `Current State.md`

Result: All assignable orphans placed correctly; unassignable ones logged and skipped.

---

## Troubleshooting

**Notion relation fields contain extra path data**
Notion exports relations as `Name (path/id.csv)`. Strip with regex `^([^(]+?)(?:\s*\(|\s*$)` —
group 1 is the clean name. See `references/csv-parsing.md`.

**Python unavailable on enterprise Windows**
Use PowerShell with temp `.ps1` files — never inline PowerShell through bash.
See `references/csv-parsing.md`.

**Entity links not resolving in Dataview**
Check that the target note exists and the link uses the exact file name — no path prefix,
no extension. `[[Exact File Name]]` only.

**A property was added during migration that is not in the vault schema**
Update vault `CLAUDE.md` immediately. Note it as a pending template update in `Current State.md`.

**Context running out mid-migration**
Stop. Write `Current State.md` with full remaining batch detail. Tell the user to start a
fresh session with the exact resume phrase from that file.

---

## Additional Resources

- **`references/task-architecture.md`** — Always-folder convention, hub note schema, sub-task
  notes, completed/ pattern, three file types explained
- **`references/entity-graph.md`** — Entity relationship graph, bi-directional link requirements,
  Dataview traversal patterns, lean migration principle for versions
- **`references/csv-parsing.md`** — PowerShell CSV parsing template, Notion export quirks,
  column name mapping, relation field extraction pattern
- **`references/cross-session.md`** — `Current State.md` template with all required sections
