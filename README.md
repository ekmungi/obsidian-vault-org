# vault-org

A Claude Code plugin that walks you through organizing any Obsidian vault in five phases. It maps what you have, identifies problems, proposes fixes (you approve each one), records the target design, and then applies the changes -- all step by step, nothing without your say-so.

The final output is a machine-readable vault spec that other Claude Code plugins can consume. For example, a personal assistant plugin can read the spec to know where your meeting notes live, what frontmatter fields to expect, and which folders to search.

## Install

```
/plugin marketplace add ekmungi/obsidian-vault-org
/plugin install vault-org@vault-org
```

Restart your Claude Code session after installing.

## Quick Start

Open any project in Claude Code (it does not have to be the vault itself) and run:

```
vault-org:discover
```

Claude will ask for your vault path, then map everything. From there, follow the five phases in order.

## The Five Phases

```
vault-org:discover  -->  vault-org:analyze  -->  vault-org:propose  -->  vault-org:spec  -->  vault-org:execute
     (map)                (problems)              (approve fixes)        (record plan)        (apply changes)
```

Each phase produces a Markdown file that feeds into the next. You can stop at any phase and pick up later -- the artifacts persist in your working directory.

---

## Phase 1: Discovery

**What it does**: Scans your vault and produces a complete map -- folders, plugins, templates, metadata, file types, links, tags, config, and health issues. Read-only, nothing is changed.

**Invoke it**:

```
vault-org:discover
```

**Claude will ask**: "What is the path to your Obsidian vault?"

**You respond with the path**:

```
My vault is at C:\Users\me\OneDrive\Obsidian_Vault\MyVault
```

**What you get back**: A structured report covering 8 areas:

1. Folder tree (2+ levels deep, with file counts)
2. Plugin table (every plugin, enabled/disabled/core, version)
3. Template catalog (what templates exist, what's in them)
4. Frontmatter analysis (which properties are used, how often, inconsistencies)
5. File type counts (.md, .canvas, .base, images, PDFs, etc.)
6. Link and tag map (internal links, orphaned notes, tag frequency)
7. Configuration snapshot (Dataview settings, Templater config, theme, hotkeys)
8. Health issues (empty folders, broken links, duplicates, stale notes)

**Output file**: `discovery-MyVault.md` in your working directory.

**Example prompt variations** (all trigger this skill):

```
Discover my vault at D:\Obsidian\Work
```

```
Map my personal vault -- it's at C:\Users\me\Obsidian_Vault\Personal
```

```
Audit my vault
```

```
Scan my Obsidian vault and tell me what's in it
```

---

## Phase 2: Analysis

**What it does**: Reads the discovery report and evaluates your vault against best practices. Identifies structural problems, metadata inconsistencies, plugin redundancies, template gaps, and database opportunities. Adapts based on which plugins you have installed.

**Prerequisite**: You must have run `vault-org:discover` first. The `discovery-MyVault.md` file must be in your working directory.

**Invoke it**:

```
vault-org:analyze
```

**What you get back**: A prioritized assessment with severity ratings:

- **Summary**: Top 3-5 issues ranked by impact
- **Structural issues**: Folder depth problems, misplaced files, naming inconsistencies
- **Metadata issues**: Missing frontmatter, mixed date formats, orphaned tags
- **Plugin recommendations**: What to add, remove, or reconfigure
- **Template gaps**: Note types that lack templates
- **Database opportunities**: Collections that would benefit from structured views

**Output file**: `analysis-MyVault.md`

**Example prompt variations**:

```
vault-org:analyze
```

```
Analyze my vault -- what needs fixing?
```

```
What's wrong with my vault?
```

**How plugin-awareness works**: If you have Dataview installed, the analysis checks whether your frontmatter actually supports Dataview queries. If you have Templater, it checks whether folder templates are configured. If you don't have these plugins, those checks are skipped. You don't need to tell Claude what plugins you have -- it already knows from Phase 1.

---

## Phase 3: Proposal

**What it does**: Walks through each issue from the analysis and proposes a fix. You see every proposal one at a time and choose: approve, modify, or skip. Nothing is recorded without your explicit approval.

**Prerequisite**: You must have run `vault-org:analyze` first.

**Invoke it**:

```
vault-org:propose
```

**What the conversation looks like**:

Claude presents proposals in order -- structural changes first, then metadata, templates, plugins, health fixes, and databases.

For a folder restructure, you see a before/after comparison:

```
Proposal 1: Consolidate Jeeves-related subfolders

BEFORE:                              AFTER:
01 PROJECTS/                         01 PROJECTS/
├── AI Agents/                       ├── Jeeves/
├── Brainstorming/                   │   ├── AI Agents/
├── Jeeves - Session Notes/          │   ├── Brainstorming/
├── Meeting Transcription/           │   └── Session Notes/
├── Notes/                           ├── Notes/
└── Job Search/                      └── Job Search/

Why: 4 subfolders are Jeeves-related but scattered. Grouping them reduces clutter.
Effort: Moderate (move 4 folders, update internal links).

Approve, modify, or skip?
```

You respond:

```
Approve
```

Or:

```
Modify -- keep "Meeting Transcription" outside the Jeeves folder, I use it for non-Jeeves meetings too
```

Or:

```
Skip
```

For a metadata schema proposal:

```
Proposal 4: Standardize frontmatter across all notes

Proposed schema:
---
tags: []
created: YYYY-MM-DD
status: draft | active | complete | archived
type: note | meeting | project | reference | contact
---

Why: Currently 60% of notes have no frontmatter. Dataview queries fail silently on notes
without the expected fields. This schema covers all note types found in your vault.

Approve, modify, or skip?
```

For a template proposal:

```
Proposal 6: Meeting notes template

---
tags: [meeting]
created: <% tp.date.now("YYYY-MM-DD") %>
status: active
type: meeting
attendees: []
---

# <% tp.file.title %>

## Agenda
-

## Notes
-

## Action Items
- [ ]

Auto-applies to: 03 RESOURCES/Meetings/ (via Templater folder template)

Approve, modify, or skip?
```

**Output file**: `proposal-MyVault.md` (contains only approved changes)

**Example prompt variations**:

```
vault-org:propose
```

```
Propose changes for my vault
```

```
What should I fix? Walk me through it.
```

---

## Phase 4: Target State

**What it does**: Takes everything you approved in Phase 3 and produces three artifacts: a decision log, a vault spec, and a migration plan.

**Prerequisite**: You must have run `vault-org:propose` first.

**Invoke it**:

```
vault-org:spec
```

**What you get**: Three files:

**1. Decision log** (`decisions-MyVault.md`):

```markdown
## DEC-001: Consolidate Jeeves subfolders under 01 PROJECTS/Jeeves/

Date: 23.02.2026
Status: Approved
Decision: Move AI Agents, Brainstorming, and Session Notes under a Jeeves parent folder.
Context: 4 Jeeves-related folders were scattered across 01 PROJECTS root.
Impact: Reduces top-level clutter, groups related work.

## DEC-002: Standardize frontmatter schema

Date: 23.02.2026
Status: Approved
Decision: All notes get tags, created, status, and type fields.
...
```

**2. Vault spec** (`spec-MyVault.md`) -- the primary artifact:

```markdown
# Vault Spec: MyVault

## Folder Structure
00 INBOX/                    Capture zone
01 PROJECTS/                 Active projects
  ├── Jeeves/                Jeeves-related work
  ├── Job Search/            Job search materials
  └── ...
02 AREAS/                    Ongoing responsibilities
03 RESOURCES/                Reference material
04 ARCHIVE/                  Completed items

## Metadata Schema
| Field   | Type     | Values                                  | Required |
|---------|----------|-----------------------------------------|----------|
| tags    | list     | free-form                               | Yes      |
| created | date     | YYYY-MM-DD                              | Yes      |
| status  | string   | draft, active, complete, archived       | Yes      |
| type    | string   | note, meeting, project, reference, contact | Yes   |

## Templates
| Name           | Location                    | Auto-applies To          |
|----------------|-----------------------------|--------------------------|
| Meeting Notes  | 03 RESOURCES/Templates/     | 03 RESOURCES/Meetings/   |
| Project Brief  | 03 RESOURCES/Templates/     | 01 PROJECTS/             |
| ...            | ...                         | ...                      |

## Plugin Configuration
...

## Conventions
- Tag format: lowercase, hyphenated (e.g., #meeting-notes)
- File naming: sentence case with spaces
- Archive: move to 04 ARCHIVE/{original-category}/ when done
```

This file is machine-readable. Other plugins parse it to understand your vault.

**3. Migration plan** (`migration-MyVault.md`):

```markdown
## Step 1: Create 01 PROJECTS/Jeeves/ folder (DEC-001)
- What: mkdir "01 PROJECTS/Jeeves/"
- Risk: Low
- Reversible: Yes
- Verify: Folder exists

## Step 2: Move subfolders into Jeeves/ (DEC-001)
- What: Move AI Agents/, Brainstorming/, Session Notes/ into Jeeves/
- Risk: Medium (internal links may break)
- Reversible: Yes (move back)
- Verify: Files accessible, no broken links

## Step 3: Add frontmatter to 28 notes missing it (DEC-002)
- What: Insert standard frontmatter block at top of each file
- Risk: Low
- Reversible: Yes (remove frontmatter)
- Verify: All .md files have frontmatter
...
```

**Example prompt variations**:

```
vault-org:spec
```

```
Write the spec and migration plan
```

```
Finalize the plan from what I approved
```

---

## Phase 5: Execution

**What it does**: Follows the migration plan step by step, applying each change to your actual vault. Every action is shown to you before it happens. You confirm each one.

**Prerequisite**: You must have run `vault-org:spec` first.

**Invoke it**:

```
vault-org:execute
```

**What the conversation looks like**:

```
Starting execution for MyVault.
Vault path: C:\Users\me\OneDrive\Obsidian_Vault\MyVault
Migration plan: 12 steps

Step 1 of 12: Create folder "01 PROJECTS/Jeeves/"
Action: Create new directory at 01 PROJECTS/Jeeves/
Risk: Low | Reversible: Yes

Proceed? [yes/no]
```

You say `yes`, Claude creates the folder, confirms it exists, then moves to Step 2.

For bulk operations like frontmatter updates:

```
Step 3 of 12: Add frontmatter to 28 notes (DEC-002)

This will add the following frontmatter to 28 files that currently have none:
---
tags: []
created: [file creation date]
status: draft
type: note
---

Show full file list? Or approve batch?
```

You can say:

```
Show the list first
```

Or:

```
Approve batch
```

After all steps complete, Claude re-scans the vault to verify:

```
Verification complete.
- 12/12 actions succeeded
- Folder structure matches spec
- All notes have frontmatter
- 2 broken links detected (from folder moves) -- fixing now...

Vault restructuring complete. The vault spec (spec-MyVault.md) reflects the new state.

Do you use any downstream tools that read vault structure?
If so, point them at spec-MyVault.md for full vault context.
```

**Output file**: `execution-MyVault.md` (log of every action taken)

**Example prompt variations**:

```
vault-org:execute
```

```
Apply the changes to my vault
```

```
Execute the migration plan
```

**Tips**:
- Close notes in Obsidian before bulk folder moves to avoid file locks
- After plugin config changes, reload Obsidian (Settings > Community Plugins > Reload)
- Folder moves via filesystem don't auto-update Obsidian links -- the execution phase handles broken link repair automatically

---

## Artifacts Summary

After running all five phases, you have these files in your working directory:

| File | Phase | Purpose |
|------|-------|---------|
| `discovery-MyVault.md` | 1 | Complete vault map |
| `analysis-MyVault.md` | 2 | Problems and recommendations |
| `proposal-MyVault.md` | 3 | User-approved changes |
| `decisions-MyVault.md` | 4 | Decision log |
| `spec-MyVault.md` | 4 | Target state (primary output for downstream tools) |
| `migration-MyVault.md` | 4 | Ordered execution steps |
| `execution-MyVault.md` | 5 | Log of what was done |

## Using the Vault Spec with Other Plugins

The vault spec is designed to be consumed by any Claude Code plugin that needs vault context. For example, if you use a personal assistant plugin:

```
# In your assistant plugin's skill, reference the vault spec:
"Read spec-MyVault.md to understand the vault's folder structure,
metadata schema, and template inventory before searching for notes."
```

The spec tells downstream tools:
- Where each type of content lives (meetings in `03 RESOURCES/Meetings/`)
- What frontmatter fields to expect (`tags`, `created`, `status`, `type`)
- What templates are available and where
- Which plugins are active and how they're configured

## Running on Multiple Vaults

Run the full pipeline on each vault separately:

```
vault-org:discover
> My personal vault is at C:\Users\me\Obsidian\Personal

vault-org:discover
> My work vault is at C:\Users\me\Obsidian\Enterprise
```

This produces `discovery-Personal.md` and `discovery-Enterprise.md`. Continue through each phase independently per vault.

## Repo Structure

```
.claude-plugin/           Plugin manifest
skills/
  ├── discover/SKILL.md   Phase 1: Map the vault
  ├── analyze/SKILL.md    Phase 2: Identify problems
  ├── propose/SKILL.md    Phase 3: Propose fixes (user approves)
  ├── spec/SKILL.md       Phase 4: Record target state
  └── execute/SKILL.md    Phase 5: Apply changes
references/               Framework documentation
examples/                 Example outputs from a real vault audit
```
