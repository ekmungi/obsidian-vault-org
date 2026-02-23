# vault-org

A Claude Code plugin that walks you through organizing any Obsidian vault in six phases. It maps what you have, identifies problems, interviews you to understand what you want, proposes fixes aligned with your goals (you approve each one), records the target design, and then applies the changes -- all step by step, nothing without your say-so.

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

Claude will ask for your vault path, then map everything. From there, follow the six phases in order.

## The Six Phases

```
vault-org:discover --> vault-org:analyze --> vault-org:interview --> vault-org:propose --> vault-org:spec --> vault-org:execute
     (map)             (problems)             (goals)               (approve fixes)      (record plan)      (apply changes)
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

**What it does**: Reads the discovery report and evaluates your vault against best practices, one area at a time. Presents findings after each assessment area so you can review and comment before moving on. Adapts based on which plugins you have installed.

**Prerequisite**: You must have run `vault-org:discover` first. The `discovery-MyVault.md` file must be in your working directory.

**Invoke it**:

```
vault-org:analyze
```

**What the conversation looks like**: Claude works through five assessment areas sequentially:

1. **Structural assessment** -- folder depth, naming, orphaned folders
2. **Metadata consistency** -- frontmatter gaps, format mismatches
3. **Plugin redundancy and gaps** -- overlaps, unused plugins, missing capabilities
4. **Template coverage** -- note types without templates, quality issues
5. **Database assessment** -- .base integrity, query patterns (if applicable)

After each area, Claude presents what it found and pauses:

```
Structural Assessment:

| Issue | Severity | Affected |
|-------|----------|----------|
| Mixed naming conventions | Medium | 01 PROJECTS/, 03 RESOURCES/ |
| 4 folders nested 4+ levels | Low | 01 PROJECTS/Jeeves/... |
| 12 empty folders | Low | Various |

Any thoughts on these findings before I move to the next area?
```

You can comment ("the deep nesting is intentional") or say "continue" to move on. Your comments are captured in the final report.

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

## Phase 3: Interview

**What it does**: Has an open-ended conversation with you to understand what you want your vault to be. Uses the analysis findings as context, but you drive the conversation. Produces a target model that tells the proposal phase what to aim for.

**Prerequisite**: You must have run `vault-org:analyze` first. The `analysis-MyVault.md` file must be in your working directory.

**Invoke it**:

```
vault-org:interview
```

**What the conversation looks like**: Claude starts with broad, open-ended questions and follows your thread:

```
Walk me through how you use your vault day to day. What does a typical session look like?
```

You talk about your workflow. Claude listens, probes deeper, and occasionally connects your answers to analysis findings:

```
You mentioned you struggle finding meeting notes. The analysis found that meeting
notes are spread across 4 different folders with no consistent naming. Is that the
problem you are describing?
```

After 5-10 exchanges, Claude synthesizes what it heard:

```
So it sounds like:
- You primarily use the vault for work (meetings, projects, research)
- Meeting notes and project tracking work okay
- The main pain point is research notes -- they have no home and you can't find them later
- You'd like a simple capture workflow that files things automatically
- The deep folder nesting in Projects is intentional and you want to keep it

Does this capture it? Anything I'm missing or got wrong?
```

You confirm or correct, and Claude writes the target model.

**Output file**: `target-model-MyVault.md`

**Example prompt variations**:

```
vault-org:interview
```

```
Interview me about my vault
```

```
Help me define what I want my vault to look like
```

**Key difference from analysis**: The analysis tells you what is wrong according to best practices. The interview discovers what matters to you. Sometimes those overlap. Sometimes they don't -- you might be fine with something the analysis flagged. The interview captures that.

---

## Phase 4: Proposal

**What it does**: Walks through each issue from the analysis and proposes a fix, guided by your target model from the interview. You see every proposal one at a time and choose: approve, modify, or skip. Nothing is recorded without your explicit approval.

**Prerequisite**: You must have run `vault-org:interview` first. Both `target-model-MyVault.md` and `analysis-MyVault.md` must be in your working directory.

**Invoke it**:

```
vault-org:propose
```

**What the conversation looks like**:

Claude presents proposals in order -- structural changes first, then metadata, templates, plugins, health fixes, and databases. Proposals address your priorities (from the interview) first.

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

## Phase 5: Target State

**What it does**: Takes everything you approved in Phase 4 and produces three artifacts: a decision log, a vault spec, and a migration plan.

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

## Phase 6: Execution

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

After running all six phases, you have these files in your working directory:

| File | Phase | Purpose |
|------|-------|---------|
| `discovery-MyVault.md` | 1 | Complete vault map |
| `analysis-MyVault.md` | 2 | Problems and recommendations |
| `target-model-MyVault.md` | 3 | What the user wants (interview output) |
| `proposal-MyVault.md` | 4 | User-approved changes |
| `decisions-MyVault.md` | 5 | Decision log |
| `spec-MyVault.md` | 5 | Target state (primary output for downstream tools) |
| `migration-MyVault.md` | 5 | Ordered execution steps |
| `execution-MyVault.md` | 6 | Log of what was done |

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
  ├── analyze/SKILL.md    Phase 2: Identify problems (step by step)
  ├── interview/SKILL.md  Phase 3: Understand user goals
  ├── propose/SKILL.md    Phase 4: Propose fixes (user approves)
  ├── spec/SKILL.md       Phase 5: Record target state
  └── execute/SKILL.md    Phase 6: Apply changes
references/               Framework documentation
examples/                 Example outputs from a real vault audit
```
