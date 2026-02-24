# Task Architecture Reference

Detailed guidance on the task folder convention used during migration and ongoing task creation.

---

## Always-Folder Convention

Every task gets a folder, even simple ones. Never create a flat task `.md` file.

**Why:** A task starting as a single note may grow into sub-tasks, reference docs, and
attachments. Starting flat means restructuring mid-work. Starting with a folder means it
can grow without ever moving files.

### Folder Structure

```
01. Projects/[Project]/tasks/
├── [Task Name]/
│   ├── [Task Name].md          ← hub note (type: task) — Dataview-visible
│   ├── [Sub-task Name].md      ← sub-task note (optional, only for complex sub-items)
│   └── attachments/            ← ALL supporting files: PDFs, images, exports
└── completed/
    └── [Done Task Name]/
        └── [Done Task Name].md
```

### Three File Types in a Task Folder

| File | `type:` | Dataview-visible | Purpose |
|------|---------|-----------------|---------|
| `[Task Name].md` (hub) | `task` | Yes | Status, owner, sub-item checklist, sub-task query |
| `[Sub-task].md` | `task` | Yes | Complex sub-items needing own tracking (rare) |
| Reference notes | `note` or blank | No | Working notes, research, context — invisible to queries |
| `attachments/` contents | — | No | PDFs, images, exported files |

---

## Task Hub Note Schema

```yaml
---
type: task
status: todo              # todo | in-progress | review | blocked | done | delayed
priority: medium          # high | medium | low
project: "[[Project Name]]"
product: "[[Product Name]]"
parent-task: ""           # blank for top-level; "[[Parent Task]]" for sub-tasks
due-date:
start-date:
owner: "[[Owner Name]]"
stakeholders: []
tags: [task]
---
```

The `parent-task:` property is not in the original vault schema template — add it to vault
`CLAUDE.md` when introducing this architecture.

---

## Task Hub Note Body

```markdown
# Task Name

## Context
Why this task exists. What the goal is. What triggers or decisions led here.

## Sub-tasks
- [ ] Simple sub-item (checkbox only — no separate note needed)
- [ ] Another simple sub-item

## Sub-task Notes
```dataview
TABLE status, due-date
FROM "01. Projects/[Project]/tasks/[Task Name]"
WHERE type = "task" AND file.name != this.file.name
SORT status ASC
```

## Notes
Free-form working notes. Not tracked by Dataview.
```

---

## Sub-task Notes

Create a sub-task `.md` file (with `type: task`) only when the sub-item needs its own:
- Status tracking
- Owner or stakeholder assignment separate from the parent
- Due date or priority
- Its own nested sub-items

For simple checklist items, keep them as checkboxes in the hub note body. Do not create
a sub-task note for every checkbox.

Sub-task notes use the same hub note schema with `parent-task: "[[Parent Task Name]]"` populated.

---

## Completed Tasks

| Task type | Convention |
|-----------|-----------|
| Simple done task (single page, no sub-notes) | `tasks/completed/[Task Name]/[Task Name].md` |
| Trivial admin item (receipt, one-liner, no context) | Checkbox only in project hub — no folder |
| Complex done task (had sub-notes or reference material) | `tasks/completed/[Task Name]/` folder with all contents |

---

## Naming Tasks During Migration

When combining two Notion tasks into one Obsidian task folder (e.g., merging a parent task
and an orphaned subtask), use the parent task name for the folder. Add the merged content as
a sub-item checkbox or sub-task note inside.

When a Notion task has inline sub-items listed in its description, bring them in as:
- Checkboxes in the hub note (for simple items)
- Sub-task notes (for items requiring independent tracking)
