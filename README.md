# Obsidian Vault Organization

Planning and configuration repo for organizing two Obsidian vaults (personal + enterprise) with consistent PARA structure, templates, and plugin configuration.

## Goals

1. **Audit** current vault state and identify problems
2. **Decide** on structure, templates, and plugin standards
3. **Design** templates and configurations in this repo
4. **Deploy** to both vaults with consistent structure

## Current State

**Phase**: Audit and Planning (bootstrapping)

| Vault | Status | Files | Audit |
|-------|--------|-------|-------|
| Personal | Exists, basic PARA structure | 46 .md, 4 .base | [Completed](audit/personal-vault.md) |
| Enterprise | Not yet set up | -- | [Pending](audit/enterprise-vault.md) |

## PARA Method

Both vaults use numbered PARA folders:
- `00 INBOX` -- Unsorted capture
- `01 PROJECTS` -- Active projects with outcomes and deadlines
- `02 AREAS` -- Ongoing responsibilities
- `03 RESOURCES` -- Reference material
- `04 ARCHIVE` -- Completed or inactive items

## Repo Structure

```
obsidian-vault-org/
├── audit/               Current-state vault audits
├── templates/           Obsidian templates (designed here, deployed to vaults)
├── plugins/             Plugin matrix and configuration
└── decisions/           Decision log
```

## Key Documents

- [Personal Vault Audit](audit/personal-vault.md) -- Folder structure, file counts, plugin inventory, observations
- [Enterprise Vault Audit](audit/enterprise-vault.md) -- Pending
- [Plugin Matrix](plugins/plugin-matrix.md) -- Plugin recommendations for both vaults
- [Decision Log](decisions/decisions.md) -- Structural and configuration decisions

## Relationship to Jeeves

The [Jeeves plugin](https://github.com/ekmungi/jeeves) has an `obsidian-knowledge` skill that reads from these vaults. Decisions made here (folder structure, metadata conventions, template frontmatter) affect how Jeeves agents search and synthesize vault content. When vault conventions change, update the Jeeves skill accordingly.

## Phase Plan

| Phase | Focus | Status |
|-------|-------|--------|
| 1. Audit | Document current state of both vaults | In progress |
| 2. Decide | Structure, templates, plugins, metadata conventions | Not started |
| 3. Design | Create templates, configure plugins, document workflows | Not started |
| 4. Deploy | Apply templates and config to both vaults | Not started |
