# Entity Graph Reference

Documents the entity relationship model for PARA-based Obsidian vaults and the
bi-directional linking requirements that make Dataview queries work.

---

## Standard PARA Entity Graph

```
PRODUCT (03. Resources/Products/)
  │ current-tech-version: [[Version]]
  │ current-commercial-version: [[Version]]
  ▼
VERSION (03. Resources/Products/)
  │ product: [[Product]]
  │ projects: ["[[Project]]"]

STAKEHOLDER (03. Resources/Stakeholders/)
  │ products: ["[[Product]]"]       ← stakeholder → product direction
  ▲
  │ stakeholders: ["[[Stakeholder]]"]   ← project → stakeholder direction
PROJECT (01. Projects/[Name]/[Name].md)
  │ product: "[[Product]]"
  │ related-versions: ["[[Version]]"]
  ▼
TASK (01. Projects/[Name]/tasks/[Task]/[Task].md)
  │ project: "[[Project]]"
  │ product: "[[Product]]"
  │ parent-task: "[[Parent Task]]"    ← sub-task only
  ▲
  │ projects: ["[[Project]]"]
  │ attendees: ["[[Stakeholder]]"]
MEETING (03. Resources/Meeting Notes/)
  │ products: ["[[Product]]"]
```

---

## Bi-Directional Linking Requirement

Dataview traversal requires links in **both directions**. Populating only one side causes
half of all queries to return empty results.

| Relationship | Direction 1 (source → target) | Direction 2 (target → source) |
|-------------|-------------------------------|-------------------------------|
| Project ↔ Stakeholder | `stakeholders: [[[S]]]` on project note | `products: [[[P]]]` on stakeholder note |
| Product ↔ Version | `current-version: [[V]]` on product note | `product: [[P]]` on version note |
| Project ↔ Version | `related-versions: [[[V]]]` on project note | `projects: [[[Proj]]]` on version note |
| Task → Project | `project: [[Proj]]` on task note | Dataview query on project hub |
| Task → Product | `product: [[P]]` on task note | Dataview query on product note |

### Acceptable Deferral

Populating all relationship arrays during initial migration is not always practical. It is
acceptable to:
1. Create all notes with empty arrays
2. Migrate content for all phases
3. Populate relationships in a dedicated "connections phase" at the end

Document this as a pending item in `Current State.md` so it is not forgotten.

---

## Lean Migration Principle (Versions)

For entities with long history (especially product versions), migrate lean by default:

| Status | Include? | How |
|--------|----------|-----|
| `current` | Yes | Create version note |
| `planned` | Yes | Create version note (status: planned) |
| `previous` | No | Preserve as Markdown table in parent product note |
| `deprecated` | No | Omit entirely unless user requests |

### Version History Table in Product Note

When not creating notes for previous versions, preserve history as a table inside the
product note:

```markdown
## Version History

| Version | Release | Status | Key Changes |
|---------|---------|--------|-------------|
| v1.4.2  | 2025-06 | Previous | IFU updates, bug fixes |
| v1.3.0  | 2025-01 | Previous | New detection model |
| v1.2.0  | 2024-08 | Previous | Initial commercial release |
```

This preserves historical context without creating notes that will never be opened or
queried by Dataview.

---

## Key Dataview Traversal Paths

These are the query patterns that depend on correct frontmatter population:

| Query goal | Dataview FROM + WHERE |
|-----------|----------------------|
| All tasks for a project | `FROM "01. Projects" WHERE type = "task" AND project = [[P]]` |
| Sub-tasks of a task | `FROM "...tasks/[Task]" WHERE type = "task" AND file.name != this.file.name` |
| All projects for a product | `FROM "01. Projects" WHERE type = "project" AND product = [[P]]` |
| Stakeholders on a product | `FROM "Stakeholders" WHERE contains(products, [[P]])` |
| Unprocessed meetings | `FROM "Meeting Notes" WHERE processed = false` |
| Overdue tasks | `FROM "01. Projects" WHERE type = "task" AND due-date < date(today) AND status != "done"` |

---

## Scoping Rules for Personal and Out-of-Scope Content

During migration, always filter content to work-relevant entities:

- Skip personal projects (travel, personal finance, language learning, etc.)
- Skip projects marked canceled with no useful content
- Skip internal tool or admin tasks that have no stakeholder visibility
- Skip version notes for products outside the vault's scope

Document all skip decisions explicitly in `Current State.md` with the reason.
