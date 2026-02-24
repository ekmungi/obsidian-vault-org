# vault-org

## What This Is

A standalone Claude Code plugin for discovering, analyzing, and restructuring Obsidian vaults. Runs independently -- no dependencies on other plugins. Produces standardized vault specs that downstream tools can consume.

## Seven Skills

| Skill | Phase | What It Does |
|-------|-------|-------------|
| `vault-org:discover` | 1. Discovery | Maps folders, plugins, templates, metadata, health (read-only) |
| `vault-org:analyze` | 2. Analysis | Assesses problems step by step, user reviews each area (plugin-aware) |
| `vault-org:interview` | 3. Interview | Conversational interview to build a target model of what the user wants |
| `vault-org:propose` | 4. Proposal | Suggests improvements guided by target model, user approves each |
| `vault-org:spec` | 5. Target State | Records approved decisions, writes vault spec and migration plan |
| `vault-org:execute` | 6. Execution | Applies changes to the vault, user confirms each action |
| `vault-org:migrate` | 7. Migration | Imports content from Notion (or other PKMs) via phased batch migration with human review |

The first six skills are sequential: each phase produces an artifact consumed by the next.
`vault-org:migrate` is a standalone import skill — use it after execute to populate the vault with content from external databases.

## Vault-Agnostic

Works on any Obsidian vault. Adapts based on installed plugins and existing structure. See `references/framework.md` for the plugin adaptation matrix.

## Artifact Chain

```
discover -> analyze -> interview -> propose ->  spec  -> execute
  (map)    (problems)  (goals)    (approved)  (target)   (apply)
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
