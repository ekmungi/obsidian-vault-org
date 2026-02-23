---
name: discover
description: >
  Maps an Obsidian vault's folder structure, plugins, templates, metadata, and health.
  Use when user says "discover my vault", "map my vault", "audit my vault", "scan my vault",
  or when connecting a new Obsidian vault for the first time.
  Produces a structured discovery report that feeds into vault-org:analyze.
user_invocable: true
---

# Vault Discovery

Produces a complete map of an Obsidian vault. Read-only -- no changes are made to the vault.

## Prerequisites

The user must provide the vault path. If not provided, ask for it. Validate that the path exists and contains a `.obsidian/` directory before proceeding.

## Steps

### 1.1 Folder Map

Scan the vault's top 2+ levels of folder structure. Produce a tree with file counts per folder.

```
Vault Root/
├── Folder A/          (12 files)
│   ├── Subfolder/     (3 files)
│   └── Subfolder/     (7 files)
└── Folder B/          (5 files)
```

Note the organizational method if recognizable (PARA, Zettelkasten, flat, date-based, etc.).

### 1.2 Plugin Inventory

Read `.obsidian/community-plugins.json` for the enabled plugin list. Then scan `.obsidian/plugins/` for all installed plugins (including disabled ones). For each plugin, read its `manifest.json` to get name, version, and description.

Also read `.obsidian/core-plugins.json` for enabled core plugins.

Produce a table:

| Plugin | Type | Status | Version | Description |
|--------|------|--------|---------|-------------|

### 1.3 Template Scan

Determine the template folder by reading `.obsidian/templates.json` (core templates) and `.obsidian/plugins/templater-obsidian/data.json` (Templater config, if installed).

List all template files with a brief summary of their contents. Note whether Templater folder templates are configured.

### 1.4 Frontmatter Analysis

Sample frontmatter from all .md files in the vault (or a representative subset for large vaults). Identify:
- Which properties are used (tags, aliases, dates, status, custom fields)
- Frequency of each property
- Format inconsistencies (e.g., mixed date formats, tags as list vs string)
- Dataview inline fields if Dataview is installed

### 1.5 File Type Breakdown

Count files by extension across the vault:

| Type | Count | Examples |
|------|-------|----------|
| .md | | |
| .canvas | | |
| .base | | |
| .excalidraw | | |
| Images (.png, .jpg, etc.) | | |
| PDFs | | |
| Other | | |

### 1.6 Link and Tag Analysis

Scan .md files for:
- Internal links (`[[...]]`) -- count total links, identify most-linked notes
- Orphaned notes (no incoming or outgoing links)
- Tag inventory with frequency
- Tag hierarchy (nested tags like `#project/active`)

If Smart Connections MCP is available, use semantic search to identify thematically related but unlinked notes.

### 1.7 Configuration Audit

Read key configuration files from `.obsidian/`:
- `app.json` -- core app settings
- `appearance.json` -- theme, font, CSS
- `hotkeys.json` -- custom keybindings
- Plugin-specific `data.json` for each enabled plugin (especially Dataview, Templater, Smart Connections)

Summarize notable settings (date format, template paths, enabled features).

### 1.8 Health Check

Identify:
- Empty folders (no files, no subfolders)
- Broken internal links (link targets that don't exist)
- Duplicate file names
- Very large files (>100KB markdown)
- Stale notes (if frontmatter includes dates, identify notes not modified in 6+ months)
- Attachments not referenced by any note

## Output Format

Write the discovery report as a structured Markdown document. Save it to the working directory as `discovery-{vault-name}.md`.

The report must be machine-readable so that `vault-org:analyze` can consume it. Use consistent heading structure and Markdown tables throughout.

## Vault Access Methods

Try in this order:
1. **Filesystem** -- Direct file reads via Read/Glob/Grep tools (primary method)
2. **Smart Connections MCP** -- Semantic search for link analysis (if available)

## Example

User: "Discover my vault at C:\Users\me\Obsidian\MyVault"

Result: A `discovery-MyVault.md` file with folder map, plugin inventory, template catalog, metadata schema, file types, link graph, config snapshot, and health issues.
