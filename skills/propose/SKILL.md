---
name: propose
description: >
  Proposes vault improvements step by step, with user approval at each stage.
  Use when user says "propose changes", "fix my vault", "suggest improvements",
  or after running vault-org:analyze. Requires an analysis report as input.
  Produces an approved proposal that feeds into vault-org:spec.
user_invocable: true
---

# Vault Proposals

Walks the user through proposed improvements one at a time. Nothing is recorded without explicit user approval.

## Prerequisites

An analysis report must exist (produced by `vault-org:analyze`). Look for `analysis-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:analyze` first.

## Proposal Workflow

For each issue from the analysis, present:
1. **What**: The problem and proposed fix
2. **Why**: Impact on vault usability
3. **Before/After**: Concrete example of the change
4. **Effort**: How much work to apply (trivial, moderate, significant)

Then ask: "Approve, modify, or skip?"

- **Approve**: Record as-is in the proposal
- **Modify**: User adjusts the proposal, then approve
- **Skip**: Move to next issue, do not record

## Proposal Categories

Present proposals in this order (structural changes first, details last):

### 3.1 Folder Structure

For each structural change, show a before/after tree:

```
BEFORE:                          AFTER:
01 PROJECTS/                     01 PROJECTS/
├── AI Agents/                   ├── Jeeves/
├── Brainstorming/               │   ├── AI Agents/
├── Jeeves - Session Notes/      │   ├── Brainstorming/
├── Notes/                       │   └── Session Notes/
└── Job Search/                  └── Job Search/
```

Preserve the user's top-level organizational method. Only propose changes within it, not replacing it.

### 3.2 Metadata Schema

Propose a standard frontmatter schema. Show what it would look like:

```yaml
---
tags: []
created: YYYY-MM-DD
modified: YYYY-MM-DD
status: draft | active | complete | archived
type: note | meeting | project | reference
---
```

Adapt to installed plugins:
- Dataview installed? Ensure fields support `dataview` queries
- Bases installed? Ensure fields match .base schemas

### 3.3 Template Designs

Propose one template at a time. For each:
- Name and purpose
- Full template content (frontmatter + body)
- Which folder it auto-applies to (if Templater folder templates)
- How it relates to the metadata schema from 3.2

### 3.4 Plugin Configuration

For each plugin change:
- What to change and why
- The specific setting values
- Any dependencies (e.g., "enable X before configuring Y")

### 3.5 Health Fixes

For each health issue:
- What to fix (empty folder, broken link, etc.)
- The specific action (delete, rename, move, update link)
- Any risk ("this will break X if Y depends on it")

### 3.6 Database Design

For each proposed database or query:
- The .base schema or Dataview query
- What it displays and why
- Where it lives in the folder structure

## Output Format

Write approved proposals to `proposal-{vault-name}.md` with:

1. **Approved Changes** -- Numbered list of everything the user approved
2. **Skipped Items** -- Brief list of what was skipped (for reference)
3. **Dependencies** -- Which changes depend on others (order matters for execution)

## Key Principle

Never assume approval. If the user hasn't explicitly said yes to a change, it does not go in the proposal.
