# Vault Organization Framework

Detailed reference for the five-phase vault organization process. CLAUDE.md has the summary; this file has the full specification.

## Phase 1: Discovery (read-only)

Map the vault as-is. All steps are non-destructive.

| Step | What | Adapts To |
|------|------|-----------|
| 1.1 Folder map | 2+ levels deep, tree with file counts | Any vault structure |
| 1.2 Plugin inventory | Enabled, disabled, core plugins, versions | -- |
| 1.3 Template scan | What templates exist, contents, Templater vs core | Templater config, template folder path |
| 1.4 Frontmatter analysis | Properties, tags, aliases across all notes | Dataview fields, Bases schemas |
| 1.5 File type breakdown | .md, .canvas, .base, images, PDFs, excalidraw | Excalidraw, Bases, Canvas usage |
| 1.6 Link and tag analysis | Internal links, backlinks, orphans, tag frequency | Dataview inline fields, tag hierarchy |
| 1.7 Configuration audit | Plugin configs, theme, hotkeys, sync setup | Each installed plugin's data.json |
| 1.8 Health check | Empty folders, broken links, duplicates, stale notes | Smart Connections similarity data |

**Output**: `discovery/{vault-name}.md`

## Phase 2: Analysis

Evaluate what needs fixing. Recommendations are plugin-aware.

| Step | What | Plugin-Aware |
|------|------|-------------|
| 2.1 Structural assessment | Folder depth, misplaced files, naming inconsistencies | Folder conventions (PARA, Zettelkasten, etc.) |
| 2.2 Metadata consistency | Inconsistent frontmatter, missing fields, mixed formats | Dataview fields, Bases schemas, tag formats |
| 2.3 Plugin redundancy and gaps | Overlapping plugins, missing capabilities, unused plugins | Cross-reference plugin functionality |
| 2.4 Template coverage | Note types lacking templates, unused templates | Templater folder templates, note creation patterns |
| 2.5 Database assessment | .base file structure, query patterns, data integrity | Bases plugin schema, Dataview queries |

**Output**: `analysis/{vault-name}-assessment.md`

## Phase 3: Proposal (user approves each step)

Every change is proposed, explained, and approved before recording.

| Step | What | User Decides |
|------|------|-------------|
| 3.1 Folder structure | Reorganize, rename, merge, flatten | Before/after tree comparison |
| 3.2 Metadata schema | Standard frontmatter fields, tag taxonomy, naming conventions | Which fields, which format, which tags |
| 3.3 Template designs | One template at a time, walked through | Content, frontmatter, Templater logic |
| 3.4 Plugin config | Enable, disable, configure, remove | Per-plugin decision |
| 3.5 Health fixes | Clean empty folders, fix links, archive stale notes | Per-issue approval |
| 3.6 Database redesign | .base schemas, Dataview queries, dashboard structure | Schema and query review |

**Output**: `proposals/{vault-name}-proposal.md`

## Phase 4: Target State

Record everything approved as the target spec.

| Step | What |
|------|------|
| 4.1 Record decisions | Each approval becomes an entry in `spec/decisions.md` |
| 4.2 Write vault spec | Target folder structure, metadata schema, templates, plugin config |
| 4.3 Write migration plan | Ordered steps from current state to target state |

**Output**: `spec/{vault-name}-spec.md`, `spec/decisions.md`, `spec/{vault-name}-migration.md`

## Phase 5: Execution (user confirms each action)

Apply changes to the vault incrementally.

| Step | What | How |
|------|------|-----|
| 5.1 Folder restructuring | Create, rename, move, merge folders | Filesystem operations |
| 5.2 Frontmatter updates | Add/update/standardize properties across notes | Batch frontmatter edits |
| 5.3 Template deployment | Copy approved templates to vault template folder | File write to vault |
| 5.4 Plugin configuration | Update plugin settings, enable/disable | Edit plugin data.json / community-plugins.json |
| 5.5 Health cleanup | Remove empty folders, fix broken links, archive stale notes | Filesystem + content edits |
| 5.6 Database setup | Create/update .base files, Dataview queries | File write to vault |
| 5.7 Verification | Re-run discovery (Phase 1) to confirm target state achieved | Read-only scan |

**Output**: `execution/{vault-name}-log.md`

## Plugin Adaptation Matrix

The framework adapts based on which plugins are installed:

| Plugin | Phases Affected | How |
|--------|----------------|-----|
| **Dataview** | 1.4, 2.2, 3.2 | Frontmatter fields must support Dataview queries |
| **Templater** | 1.3, 2.4, 3.3, 5.3 | Templates use Templater syntax, folder templates auto-apply |
| **Bases** | 1.5, 2.5, 3.6, 5.6 | .base schemas define structured data views |
| **Smart Connections** | 1.8 | Semantic similarity finds duplicates and related notes |
| **Excalidraw** | 1.5 | .excalidraw files, scripts, drawings inventory |
| **Canvas** | 1.5 | .canvas files and node connections |
| **Icon Folder** | 1.1, 3.1, 5.1 | Icon mappings need updating when folders change |
| **Style Settings** | 1.7, 5.4 | Theme customization settings |

If a plugin is not installed, steps that depend on it are skipped or adapted.

## Vault Access Methods

1. **Smart Connections MCP** -- Semantic search across vault notes
2. **Filesystem** -- Direct file read/write for structure mapping and execution
3. **Plugin configs** -- Read `.obsidian/` directory for plugin settings
