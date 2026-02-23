---
name: execute
description: >
  Applies approved vault changes step by step, with user confirmation at each action.
  Use when user says "execute the plan", "apply changes", "restructure my vault",
  or after running vault-org:spec. Requires a migration plan as input.
  Modifies the actual Obsidian vault and produces an execution log.
user_invocable: true
---

# Vault Execution

Applies changes to the Obsidian vault according to the migration plan. Every action requires user confirmation before execution.

## Prerequisites

A migration plan must exist (produced by `vault-org:spec`). Look for `migration-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:spec` first.

The user must confirm the vault path before any write operations begin.

## Execution Principles

1. **One action at a time**: Never batch multiple changes without confirmation
2. **Verify after each action**: Confirm the change was applied correctly
3. **Reversible first**: Execute low-risk, reversible changes before high-risk ones
4. **Stop on failure**: If an action fails, stop and diagnose before continuing
5. **Log everything**: Every action and its result goes in the execution log

## Execution Steps

### 5.1 Folder Restructuring

For each folder change in the migration plan:
- Show the user exactly what will happen (create, rename, move, delete)
- Wait for confirmation
- Execute the filesystem operation
- Verify the result

```
Action: Move "01 PROJECTS/Brainstorming/" to "01 PROJECTS/Jeeves/Brainstorming/"
Confirm? [yes/no]
```

### 5.2 Frontmatter Updates

For each note that needs frontmatter changes:
- Show the before/after frontmatter
- Wait for confirmation
- Edit the file (preserving all other content)
- Verify the frontmatter was updated correctly

For bulk updates (e.g., adding `type: note` to 30 files), present a summary and ask for batch approval:
```
Batch update: Add "type: note" to 30 files that currently lack a type field.
Show list? [yes] | Approve batch? [yes/no]
```

### 5.3 Template Deployment

For each template:
- Show the full template content
- Confirm the destination path in the vault's template folder
- Write the file
- If Templater is installed and folder templates were approved, update the Templater config

### 5.4 Plugin Configuration

For each plugin config change:
- Show the setting name, current value, and new value
- Explain what the change does
- Wait for confirmation
- Edit the plugin's `data.json` or `community-plugins.json`

**Important**: Plugin config changes may require reloading Obsidian. Note this in the log and remind the user.

### 5.5 Health Cleanup

For each health fix:
- Show the specific action (delete empty folder, fix broken link, etc.)
- Wait for confirmation
- Execute
- Verify

### 5.6 Database Setup

For each .base file or Dataview query:
- Show the schema or query
- Confirm the file path
- Write the file
- Verify it renders correctly (advise user to check in Obsidian)

### 5.7 Verification

After all changes are applied:
1. Re-run a lightweight version of `vault-org:discover` on the modified vault
2. Compare the new state against the vault spec (`spec-{vault-name}.md`)
3. Report any discrepancies
4. Update the vault spec if the user approves minor deviations

## Downstream Skill Update

After successful execution, prompt the user:

"Vault restructuring complete. The vault spec (`spec-{vault-name}.md`) reflects the new state. If you use downstream tools that read vault structure (e.g., Jeeves obsidian-knowledge), update them to point at this spec."

If the user confirms they use a specific downstream tool, offer to generate a vault knowledge reference in the format that tool expects. For example, for Jeeves, this might mean updating the obsidian-knowledge skill's references with the new folder structure, metadata schema, and template inventory.

## Output Format

Write the execution log to `execution-{vault-name}.md`:

```markdown
# Execution Log: {vault-name}

**Date**: DD.MM.YYYY
**Vault Path**: [path]
**Migration Plan**: migration-{vault-name}.md

## Actions

### Action 1: [description]
- **Step**: 5.1 (Folder Restructuring)
- **Decision**: DEC-001
- **Status**: Completed
- **Details**: [what was done]

### Action 2: ...

## Summary
- Total actions: N
- Completed: N
- Skipped: N
- Failed: N

## Post-Execution Notes
- [Any observations, warnings, or follow-ups]
```

## Troubleshooting

### File locked by Obsidian
If a file can't be modified because Obsidian has it open:
- Ask the user to close the specific note in Obsidian
- Or suggest closing Obsidian temporarily for bulk operations

### Plugin config changes not reflected
Plugin configurations require Obsidian to reload:
- Community plugins: Settings > Community Plugins > Reload
- Core plugins: May require restarting Obsidian
- Tell the user after any plugin config change

### Broken links after folder moves
When moving files, internal links (`[[...]]`) may break:
- Obsidian auto-updates links when moves happen within the app
- Filesystem moves (done by this skill) do NOT auto-update links
- After folder restructuring, scan for broken links and fix them in 5.5
