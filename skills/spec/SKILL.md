---
name: spec
description: >
  Records approved vault proposals into a target spec, decision log, and migration plan.
  Use when user says "record the spec", "finalize the plan", "write the spec",
  or after completing vault-org:propose. Requires an approved proposal as input.
  Produces artifacts consumable by vault-org:execute and by downstream skills (e.g., Jeeves obsidian-knowledge).
user_invocable: true
---

# Vault Specification

Converts approved proposals into three artifacts: a target vault spec, a decision log, and a migration plan. These artifacts serve two purposes:
1. Drive execution (Phase 5)
2. Feed downstream tools that need to understand the vault (e.g., Jeeves, other Claude Code plugins)

## Prerequisites

An approved proposal must exist (produced by `vault-org:propose`). Look for `proposal-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:propose` first.

## Outputs

### 4.1 Decision Log

Append each approved change as a decision entry:

```markdown
## DEC-NNN: [Short title]

**Date**: DD.MM.YYYY
**Status**: Approved

**Decision**: [What was decided]
**Context**: [Why -- from the analysis]
**Impact**: [What changes]
```

Write to `decisions-{vault-name}.md`.

### 4.2 Vault Spec

A single document describing the target state of the vault. This is the primary artifact consumed by downstream tools.

Structure:

```markdown
# Vault Spec: {vault-name}

## Folder Structure
[Target tree with descriptions]

## Metadata Schema
[Standard frontmatter fields, types, allowed values]

## Templates
[List of templates with purpose and location]

## Plugin Configuration
[Enabled plugins with key settings]

## Databases
[.base schemas and Dataview queries]

## Conventions
[Naming, tagging, linking, archiving rules]
```

Write to `spec-{vault-name}.md`.

**This file must be machine-readable.** Use consistent Markdown structure so that other skills can parse it. Downstream tools (like Jeeves's obsidian-knowledge skill) read this spec to understand how the vault is organized, what metadata fields to expect, and where to find specific note types.

### 4.3 Migration Plan

An ordered list of steps to go from current state to target state. Each step references a decision and specifies the exact action.

```markdown
# Migration Plan: {vault-name}

## Step 1: [Action] (DEC-001)
- What: [Specific filesystem/content changes]
- Risk: [Low/Medium/High]
- Reversible: [Yes/No]
- Verify: [How to confirm it worked]

## Step 2: ...
```

Order steps so that dependencies are satisfied (e.g., create folders before moving files into them).

Write to `migration-{vault-name}.md`.

## Downstream Skill Integration

The vault spec is designed to be consumed by other plugins and skills. When the spec is complete, inform the user:

"The vault spec is ready. If you use a plugin like Jeeves that reads vault structure, you can point its obsidian-knowledge skill (or equivalent) at `spec-{vault-name}.md` to give it full vault context."

The spec intentionally uses a standardized format so that any tool can parse:
- Folder paths and their purposes
- Metadata field names, types, and allowed values
- Template names and locations
- Plugin capabilities available in the vault

## Example

User: "Write the spec" (with proposal-MyVault.md present)

Result: Three files -- `decisions-MyVault.md` (decision log), `spec-MyVault.md` (target state), `migration-MyVault.md` (ordered execution steps). The spec is immediately usable by downstream skills.
