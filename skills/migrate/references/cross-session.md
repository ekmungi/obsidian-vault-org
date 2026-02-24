# Cross-Session Continuity Reference

The `Current State.md` file is the single mechanism for preserving migration context
across sessions. Large migrations span multiple sessions — without this file, each new
session starts cold with no knowledge of decisions made, batches completed, or scope
agreed in earlier sessions.

---

## Where to Write It

Create `Current State.md` at the vault root (same level as `CLAUDE.md` and `Dashboard.md`).

```
Vault Root/
├── CLAUDE.md
├── Dashboard.md
├── Current State.md    ← here
├── 00. Inbox/
├── 01. Projects/
└── ...
```

---

## Required Structure

```markdown
# Current State — [Vault Name] Migration

**As of:** YYYY-MM-DD
**Session:** [What was completed this session — e.g., "Phases 3, 4, 5 complete"]
**Next session:** [Exact trigger phrase — e.g., "Resume Phase 6 — Tasks Batch 2"]

---

## How to Resume in a New Session

1. Claude Code auto-reads CLAUDE.md — vault structure, schemas, workflows are loaded
2. Read this file for full context
3. Say "[exact trigger phrase]" to continue

**Export file paths:**
- [Database name]: `[full path to CSV]`

**Platform notes:**
- [e.g., "Use PowerShell for CSV parsing — Python not available on this machine"]
- Command: `powershell.exe -ExecutionPolicy Bypass -File '[path]'`

---

## Migration Status

| Phase | Content | Status | Notes |
|-------|---------|--------|-------|
| 1 | Products | ✅ Done | N notes created |
| 2 | Versions | ✅ Done | N notes; history tables in product notes |
| 3 | Projects | ✅ Done | N hub notes; N archived |
| 4 | Tasks | 🔄 In Progress | Batch 1 done; Batches 2-N pending |
| 5 | Meetings | ⏳ Pending | |
| 6 | User Research | ⏳ Pending | |
| 7 | Connections audit | ⏳ Pending | Populate stakeholders/products arrays |

---

## Phase [N] — Batch Plan ([X] tasks remaining)

| Batch | Projects/domain | Est. tasks |
|-------|----------------|-----------|
| **[N+1] — NEXT** | [Project group] | ~N |
| [N+2] | [Project group] | ~N |
| [N+3] | Orphaned tasks | ~N |

---

## Phase [N] — [Next Batch Name] (READY TO WRITE)

### Active task folders to create in `tasks/`

| Folder name | Status | Notes |
|-------------|--------|-------|
| `[Task Name]/` | todo | [any relevant note] |
| `[Task Name]/` | in-progress | Inline sub-tasks: "[sub-task 1]", "[sub-task 2]" |

### Completed task folders to create in `tasks/completed/`

`[Done Task 1]`, `[Done Task 2]`, `[Done Task 3]`

### Tasks moved to another project

- `[Task Name]` (status) → [Target Project]
*(Will be written in Batch N)*

### Discarded

- `[Task Name]` — reason (e.g., "no content, vague") → skip
- `[Task Name]` — reason → `04. Archive/Projects/`

---

## Architecture Decisions (agreed this session)

Document any decisions that aren't in CLAUDE.md yet:

- **Task folder convention:** Always-folder — every task gets a folder from creation
- **Attachments:** All supporting files go in `attachments/` subfolder
- **`parent-task:` property:** New — add to CLAUDE.md task schema (pending)
- **Lean versions:** Current + planned only; previous → Version History table in product note

---

## Pending Manual Actions

- [ ] [Action that Claude cannot do — e.g., rename a folder in Explorer]
- [ ] Update `Template - Task.md` with new schema properties
- [ ] Delete `00. Inbox/[folder]/` after verifying migrated content

---

## Scope Exclusions (agreed — do not revisit)

- **Personal projects skipped:** [list]
- **Canceled tasks with no content:** omitted entirely
- **Orphaned tasks assigned:** [list of decisions made]

---

## CLAUDE.md — Pending Updates

Decisions made this session that need adding to CLAUDE.md:

1. **Add `parent-task:` to task schema:**
   ```yaml
   parent-task: ""    # blank for top-level tasks; "[[Parent Task]]" for sub-tasks
   ```
2. **Document task folder convention:** always-folder, attachments/, completed/
```

---

## Update Frequency

- After **every batch** — update migration status table and next batch spec
- After **every session** — update the resume instruction and session summary
- When a **new decision** is made — document it in Architecture Decisions immediately

Do not wait until the end of a session to write this file. Update it as decisions are made
so that a context-truncated session can always resume cleanly.

---

## Freshness Rule

The resume instruction must be specific enough that a new session with zero prior context
can proceed without asking the user any clarifying questions. If the resume instruction is
vague (e.g., "continue tasks"), it will not work.

Good: `"Resume Phase 6 — Tasks Batch 2 (FY26 CAMs Alignment)"`
Bad: `"Continue migrating tasks"`
