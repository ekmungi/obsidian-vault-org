---
name: analyze
description: >
  Assesses an Obsidian vault against best practices step by step, presenting findings
  after each assessment area for user review. Use when user says "analyze my vault",
  "what's wrong with my vault", "assess my vault", or after running vault-org:discover.
  Requires a discovery report as input. Produces a structured assessment that feeds
  into vault-org:interview.
user_invocable: true
---

# Vault Analysis

Evaluates a discovered vault against best practices, one assessment area at a time. Recommendations are plugin-aware -- they adapt based on what the vault actually uses.

## Prerequisites

A discovery report must exist (produced by `vault-org:discover`). Look for `discovery-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:discover` first.

## Workflow

Work through each assessment step sequentially. After completing each step:

1. **Present findings** -- Show what was found in that area, with specific examples
2. **Pause for reaction** -- Ask: "Any thoughts on these findings before I move to the next area?"
3. **Incorporate feedback** -- If the user comments, note their input alongside the findings
4. **Proceed** -- Move to the next step

This gives the user visibility into the analysis as it happens, rather than producing a monolithic report at the end.

### Skipping steps

Check plugin adaptation rules (below) before starting each step. If a step is irrelevant to the vault, tell the user you are skipping it and why, then move on.

## Assessment Steps

### 2.1 Structural Assessment

Evaluate folder organization:
- **Depth issues**: Folders nested more than 3 levels deep (hard to navigate)
- **Misplaced files**: Files in the wrong PARA category (e.g., reference material in Projects)
- **Naming inconsistencies**: Mixed naming conventions (kebab-case, spaces, underscores)
- **Orphaned folders**: Folders that contain only subfolders, no actual notes
- **Organizational method**: Is the method consistent? Partially adopted? Mixed?

Present: A table of structural issues found, each with severity and affected folders.

### 2.2 Metadata Consistency

Cross-reference frontmatter analysis from discovery:
- **Missing required fields**: Notes without tags, dates, or status when other notes have them
- **Inconsistent formats**: Mixed date formats (DD.MM.YYYY vs YYYY-MM-DD), tags as string vs list
- **Unused properties**: Frontmatter fields that appear in fewer than 5% of notes
- **Dataview compatibility**: If Dataview is installed, check that frontmatter supports common query patterns
- **Bases compatibility**: If Bases plugin is active, verify .base schemas match actual note properties

Present: A summary of metadata patterns found, specific inconsistencies with note-level examples.

### 2.3 Plugin Redundancy and Gaps

Cross-reference installed plugins:
- **Redundant plugins**: Multiple plugins serving the same purpose (e.g., make.md + Bases)
- **Disabled but installed**: Plugins taking up space without being used
- **Missing capabilities**: Common needs not covered (e.g., no backup plugin, no template engine)
- **Configuration conflicts**: Plugin settings that contradict each other
- **Plugin-specific opportunities**: Features of installed plugins that are not being utilized

Present: A table of plugin observations -- what overlaps, what is missing, what is underused.

### 2.4 Template Coverage

Evaluate templates against vault usage patterns:
- **Note types without templates**: If the vault has meeting notes, projects, contacts -- do templates exist for each?
- **Unused templates**: Templates that do not match any existing notes
- **Template quality**: Do templates include frontmatter? Are they consistent with each other?
- **Folder templates**: If Templater is installed, are folder templates configured for auto-apply?

Present: A list of template gaps and quality issues with specific examples.

### 2.5 Database Assessment

If Bases plugin or Dataview is present:
- **.base file integrity**: Do schemas match actual note properties?
- **Query patterns**: Are Dataview queries consistent? Do they reference properties that exist?
- **Dashboard opportunities**: Are there note collections that would benefit from database views?

Present: Database health findings and opportunities, with specific .base or query examples.

## Output Format

After all steps are complete, compile everything into `analysis-{vault-name}.md` with:

1. **Summary** -- Top 3-5 issues ranked by impact
2. **Structural Issues** -- Table of problems with severity (Critical, High, Medium, Low)
3. **Metadata Issues** -- Inconsistencies with specific examples
4. **Plugin Recommendations** -- Table of add/remove/configure suggestions
5. **Template Gaps** -- What is missing
6. **Database Opportunities** -- What could benefit from structured views
7. **User Notes** -- Any comments or context the user provided during the analysis

Each issue must include:
- What the problem is
- Why it matters
- Which notes/folders are affected
- Suggested fix (brief -- detailed proposals come in later phases)

## Plugin Adaptation

Skip or adapt steps based on installed plugins:
- No Dataview? Skip Dataview compatibility checks in 2.2
- No Templater? Simplify template assessment in 2.4
- No Bases? Skip database assessment in 2.5
- Smart Connections available? Use semantic search to find misplaced files in 2.1

## Example

User: "Analyze my vault" (with discovery-MyVault.md present)

Step-by-step interaction:
1. Run 2.1 -- present structural findings (3 folders nested 4+ levels, mixed naming)
2. User says: "The deep nesting in Projects is intentional, I like it that way"
3. Note user preference, move to 2.2 -- present metadata findings
4. Continue through each step...

Result: An `analysis-MyVault.md` that includes both the system findings and the user's comments, giving the next phase (interview) richer context to work with.
