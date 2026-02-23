# vault-org

A Claude Code plugin for discovering, analyzing, and restructuring Obsidian vaults.

## What It Does

Walks you through organizing any Obsidian vault in five phases:

1. **Discover** -- Maps your vault's folders, plugins, templates, metadata, and health
2. **Analyze** -- Identifies problems, gaps, and inconsistencies
3. **Propose** -- Suggests improvements step by step (you approve each one)
4. **Spec** -- Records the target state as a machine-readable vault spec
5. **Execute** -- Applies changes to your vault (you confirm each action)

## Install

```bash
# Add the plugin
/plugin marketplace add ekmungi/obsidian-vault-org

# Install it
/plugin install vault-org@vault-org

# Restart your Claude Code session
```

## Usage

```
# Start with discovery
vault-org:discover

# Then analyze, propose, spec, execute -- in order
vault-org:analyze
vault-org:propose
vault-org:spec
vault-org:execute
```

Each skill produces an artifact consumed by the next. You can stop at any phase and resume later.

## Plugin-Aware

The framework adapts based on what's installed in your vault:

| Plugin | How It Adapts |
|--------|--------------|
| Dataview | Metadata analysis checks frontmatter supports queries |
| Templater | Template proposals use Templater syntax and folder templates |
| Bases | Database assessment covers .base schemas |
| Smart Connections | Health check uses semantic search for duplicates |
| Excalidraw | File inventory includes .excalidraw files |

Plugins not installed? Those steps are skipped automatically.

## Output

The primary output is a **vault spec** (`spec-{vault-name}.md`) -- a standardized, machine-readable description of your vault's structure, metadata schema, templates, and plugin config.

This spec can be consumed by other Claude Code plugins that need to understand your vault. For example, a personal assistant plugin could read the spec to know where to find meeting notes, what frontmatter fields to use, and which folders to search.

## Repo Structure

```
.claude-plugin/        Plugin manifest
skills/
├── discover/          Phase 1: Vault mapping
├── analyze/           Phase 2: Problem identification
├── propose/           Phase 3: User-approved proposals
├── spec/              Phase 4: Target state recording
└── execute/           Phase 5: Vault modification
references/            Framework docs and plugin knowledge
examples/              Example outputs from a real vault
```
