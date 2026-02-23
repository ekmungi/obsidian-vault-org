---
name: analyze
description: >
  Assesses an Obsidian vault against best practices and identifies problems.
  Use when user says "analyze my vault", "what's wrong with my vault", "assess my vault",
  or after running vault-org:discover. Requires a discovery report as input.
  Produces a structured assessment that feeds into vault-org:propose.
user_invocable: true
---

# Vault Analysis

Evaluates a discovered vault against best practices. Recommendations are plugin-aware -- they adapt based on what the vault actually uses.

## Prerequisites

A discovery report must exist (produced by `vault-org:discover`). Look for `discovery-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:discover` first.

## Steps

### 2.1 Structural Assessment

Evaluate folder organization:
- **Depth issues**: Folders nested more than 3 levels deep (hard to navigate)
- **Misplaced files**: Files in the wrong PARA category (e.g., reference material in Projects)
- **Naming inconsistencies**: Mixed naming conventions (kebab-case, spaces, underscores)
- **Orphaned folders**: Folders that contain only subfolders, no actual notes
- **Organizational method**: Is the method consistent? Partially adopted? Mixed?

### 2.2 Metadata Consistency

Cross-reference frontmatter analysis from discovery:
- **Missing required fields**: Notes without tags, dates, or status when other notes have them
- **Inconsistent formats**: Mixed date formats (DD.MM.YYYY vs YYYY-MM-DD), tags as string vs list
- **Unused properties**: Frontmatter fields that appear in <5% of notes
- **Dataview compatibility**: If Dataview is installed, check that frontmatter supports common query patterns
- **Bases compatibility**: If Bases plugin is active, verify .base schemas match actual note properties

### 2.3 Plugin Redundancy and Gaps

Cross-reference installed plugins:
- **Redundant plugins**: Multiple plugins serving the same purpose (e.g., make.md + Bases)
- **Disabled but installed**: Plugins taking up space without being used
- **Missing capabilities**: Common needs not covered (e.g., no backup plugin, no template engine)
- **Configuration conflicts**: Plugin settings that contradict each other
- **Plugin-specific opportunities**: Features of installed plugins that aren't being utilized

### 2.4 Template Coverage

Evaluate templates against vault usage patterns:
- **Note types without templates**: If the vault has meeting notes, projects, contacts -- do templates exist for each?
- **Unused templates**: Templates that don't match any existing notes
- **Template quality**: Do templates include frontmatter? Are they consistent with each other?
- **Folder templates**: If Templater is installed, are folder templates configured for auto-apply?

### 2.5 Database Assessment

If Bases plugin or Dataview is present:
- **.base file integrity**: Do schemas match actual note properties?
- **Query patterns**: Are Dataview queries consistent? Do they reference properties that exist?
- **Dashboard opportunities**: Are there note collections that would benefit from database views?

## Output Format

Write the assessment as `analysis-{vault-name}.md` with:

1. **Summary** -- Top 3-5 issues ranked by impact
2. **Structural Issues** -- Table of problems with severity (Critical, High, Medium, Low)
3. **Metadata Issues** -- Inconsistencies with specific examples
4. **Plugin Recommendations** -- Table of add/remove/configure suggestions
5. **Template Gaps** -- What's missing
6. **Database Opportunities** -- What could benefit from structured views

Each issue must include:
- What the problem is
- Why it matters
- Which notes/folders are affected
- Suggested fix (brief -- detailed proposals come in Phase 3)

## Plugin Adaptation

Skip or adapt steps based on installed plugins:
- No Dataview? Skip Dataview compatibility checks in 2.2
- No Templater? Simplify template assessment in 2.4
- No Bases? Skip database assessment in 2.5
- Smart Connections available? Use semantic search to find misplaced files in 2.1

## Example

User: "Analyze my vault" (with discovery-MyVault.md present)

Result: An `analysis-MyVault.md` identifying that 40% of notes lack frontmatter, 3 plugins overlap, meeting notes have no template, and the Projects folder has inconsistent naming.
