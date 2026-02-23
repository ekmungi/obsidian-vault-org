# vault-org

## What This Is

A standalone Claude Code plugin for discovering, analyzing, and restructuring Obsidian vaults. Runs independently -- no dependencies on other plugins. Produces standardized vault specs that downstream tools can consume.

## Five Skills

| Skill | Phase | What It Does |
|-------|-------|-------------|
| `vault-org:discover` | 1. Discovery | Maps folders, plugins, templates, metadata, health (read-only) |
| `vault-org:analyze` | 2. Analysis | Assesses problems, gaps, inconsistencies (plugin-aware) |
| `vault-org:propose` | 3. Proposal | Suggests improvements step by step, user approves each |
| `vault-org:spec` | 4. Target State | Records approved decisions, writes vault spec and migration plan |
| `vault-org:execute` | 5. Execution | Applies changes to the vault, user confirms each action |

Skills are sequential: each phase produces an artifact consumed by the next.

## Vault-Agnostic

Works on any Obsidian vault. Adapts based on installed plugins and existing structure. See `references/framework.md` for the plugin adaptation matrix.

## Artifact Chain

```
discover  ->  analysis  ->  proposal  ->  spec  ->  execute
  (map)      (problems)    (approved)   (target)   (apply)
                                            |
                                            v
                                    downstream tools
                                   (e.g., Jeeves, etc.)
```

The vault spec (`spec-{vault-name}.md`) is the primary output. It describes folder structure, metadata schema, templates, and plugin config in a standardized, machine-readable format that any other plugin or skill can parse.

## Key Rules

- **User approval required** for propose and execute phases
- **Plugin-aware** -- analysis and recommendations adapt to installed plugins
- **No dependencies** -- this plugin runs standalone, outputs feed other tools
- No emojis, BLUF format
