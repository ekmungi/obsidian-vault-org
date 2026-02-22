# Obsidian Vault Organization - Project Instructions

## What This Is

A planning and configuration repo for organizing two Obsidian vaults (personal + enterprise) that both follow the PARA method. This is NOT a code project -- it contains Markdown documentation, Obsidian templates, plugin configurations, and decision logs.

## Context

Anant is migrating from Notion to Obsidian. Two vaults need consistent structure, templates, and plugin configuration. The personal vault exists (46 .md files, basic PARA structure). The enterprise vault is being set up.

## Vault Paths (Dynamic)

Vault paths vary depending on which machine is active (work laptop vs personal). Do NOT hardcode paths. When vault access is needed, resolve dynamically:

- **Personal vault**: Typically at `C:\Users\ekmun\OneDrive\Obsidian_Vault\Mungi_Personal`
- **Enterprise vault**: TBD (path not yet established)

## Structure

```
obsidian-vault-org/
├── CLAUDE.md            # This file -- project instructions
├── README.md            # Project overview and goals
├── .gitignore           # Standard + Obsidian-specific ignores
├── audit/               # Current-state audits of each vault
├── templates/           # Obsidian templates (designed here, deployed to vaults)
├── plugins/             # Plugin recommendations and config
└── decisions/           # Decision log
```

## PARA Method

Both vaults use numbered PARA folders for consistent sort order:
- `00 INBOX` -- Capture zone, unsorted items
- `01 PROJECTS` -- Active projects with clear outcomes and deadlines
- `02 AREAS` -- Ongoing responsibilities without end dates
- `03 RESOURCES` -- Reference material, topics of interest
- `04 ARCHIVE` -- Completed or inactive items from any category

## Working Conventions

- **No code**: This repo contains Markdown, YAML, and Obsidian template files only
- **Decision log**: Record structural decisions in `decisions/decisions.md`
- **Templates**: Design templates in `templates/`, then manually copy to each vault's template folder
- **Audits**: Keep vault audits current as changes are made

## Relationship to Jeeves

The Jeeves plugin (at `C:\Users\ekmun\Dev\jeeves\`) has an `obsidian-knowledge` skill that consumes vault structure. Decisions made here (folder structure, metadata conventions, template frontmatter) affect how Jeeves agents search and read vault content. Update Jeeves's skill when vault conventions change.

## Key Preferences

- No emojis in templates or documentation
- BLUF (Bottom Line Up Front) format for notes
- European date format (DD.MM.YYYY) in Dataview
- Immutable Obsidian config: design templates here first, then deploy (don't edit in-vault)
