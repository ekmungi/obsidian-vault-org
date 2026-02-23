# Plugin Matrix

## Overview

Plugin recommendations for both vaults. Goal: consistent plugin configuration across personal and enterprise vaults, removing unused plugins, and ensuring each plugin serves a clear purpose.

## Plugin Inventory

| Plugin | Personal (Current) | Personal (Recommend) | Enterprise (Recommend) | Rationale | Config Sync? |
|--------|-------------------|---------------------|----------------------|-----------|-------------|
| templater-obsidian | Enabled | Keep | Yes | Core template engine. Powers all custom templates. Folder templates not yet configured -- should be. | Yes |
| dataview | Enabled | Keep | Yes | Inline queries, JS enabled. Powers dashboards and metadata views. European date format. | Yes |
| smart-connections | Enabled | Keep | Yes | Semantic search and MCP server for Jeeves integration. Tune re-indexing frequency. | Partial (model config may differ) |
| obsidian-excalidraw-plugin | Enabled | Keep | Yes | Visual diagrams and sketching. 8 downloaded scripts in personal vault. | Yes |
| table-editor-obsidian | Enabled | Keep | Yes | Markdown table editing. Essential for any vault with tables. | Yes |
| ai-meeting-notes | Enabled | Keep | Yes | Meeting transcription to markdown. Outputs to `03 RESOURCES/Meetings`. | Yes (output path) |
| outlook-meeting-notes | Enabled | Evaluate | Yes | Calendar integration for meeting notes. More relevant for enterprise (work calendar). May be redundant with ai-meeting-notes for personal use. | Yes |
| obsidian-style-settings | Enabled | Keep | Yes | Theme customization. Ensures visual consistency across vaults. | Yes |
| obsidian-icon-folder | Enabled | Keep | Yes | Colored PARA folder icons. Visual hierarchy for folder navigation. | Yes (icon mappings) |
| list-style | Enabled | Keep | Yes | Improved list rendering. Low overhead, consistent formatting. | Yes |
| obsidian-emoji-toolbar | Enabled | Remove | Remove | User preference is no emojis. Contradicts style guidelines. Remove from both vaults. | N/A |
| make-md | Disabled | Remove | Skip | Superseded by native Bases plugin. No reason to keep installed. | N/A |
| mcp-tools | Disabled | Evaluate | Evaluate | MCP integration for Obsidian. May overlap with smart-connections MCP. Evaluate whether it adds capabilities beyond what smart-connections provides. | TBD |
| remotely-save | Disabled | Skip | Skip | OneDrive handles vault sync. No need for a separate sync plugin. | N/A |
| bases (core) | Enabled | Keep | Yes | Native database views (.base files). Supersedes make-md. 4 databases in personal vault. | Yes |

## Actions

### Immediate (Personal Vault)
1. Remove `obsidian-emoji-toolbar` -- conflicts with no-emoji preference
2. Remove `make-md` -- superseded by Bases, currently disabled
3. Evaluate `outlook-meeting-notes` -- determine if it adds value beyond ai-meeting-notes for personal use
4. Evaluate `mcp-tools` -- determine if it adds value beyond smart-connections

### Enterprise Vault Setup
1. Install all "Yes" plugins from the matrix above
2. Configure identical settings for shared plugins (templater paths, dataview format, icon mappings)
3. Give `outlook-meeting-notes` priority -- work calendar integration is primary use case
4. Skip `remotely-save` -- enterprise OneDrive handles sync

### Configuration Sync Strategy
Plugins marked "Config Sync? Yes" should have identical configuration across both vaults. Strategy options:
- **Option A**: Maintain config files in this repo, manually copy to vaults
- **Option B**: Symlink shared plugin configs (fragile on Windows/OneDrive)
- **Option C**: Document the settings, manually replicate (simplest, most reliable)

Decision pending -- see `decisions/decisions.md`.
