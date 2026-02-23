---
name: interview
description: >
  Conversational interview that builds a target model of what the user wants their vault to be.
  Use when user says "interview me about my vault", "what should my vault look like",
  "build a target model", "help me define my vault goals", or after running vault-org:analyze.
  Requires an analysis report as input. Produces a target model that feeds into vault-org:propose.
user_invocable: true
---

# Vault Interview

Conducts an open-ended conversation to understand what the user wants their vault to be. The analysis report provides context, but the user drives the conversation. The output is a target model document that gives the propose phase a clear destination.

## Prerequisites

An analysis report must exist (produced by `vault-org:analyze`). Look for `analysis-{vault-name}.md` in the working directory. If not found, tell the user to run `vault-org:analyze` first.

Read the analysis report before starting. You will reference it during the conversation, but it does not set the agenda.

## Interview Approach

This is a conversation, not a questionnaire. Follow these principles:

1. **Open-ended first**: Start with broad questions that let the user describe their world
2. **Listen, then probe**: Follow the user's thread -- ask why, ask for examples, ask what frustrates them
3. **Analysis as context**: When the user touches on something the analysis flagged, surface it naturally ("The analysis noticed X in that area -- does that match your experience?")
4. **No leading questions**: Do not push the user toward a specific organizational method or structure
5. **Synthesize as you go**: Periodically reflect back what you have heard ("So it sounds like you primarily use your vault for X and Y, and the main pain point is Z -- is that right?")

## Conversation Flow

### Phase 1: Anchoring (2-3 open-ended questions)

Start with broad questions to understand the user's relationship with their vault. Pick 2-3 that feel most natural based on the analysis:

- "Walk me through how you use your vault day to day. What does a typical session look like?"
- "What frustrates you most about your vault right now?"
- "When you cannot find something, what were you looking for and where did you expect it to be?"
- "What is the vault for? What role does it play in your work or life?"

Do not ask all of these. Pick the ones that make sense given what the analysis revealed. If the analysis found severe structural issues, the frustration question may be a good opener. If the vault is well-organized but missing metadata, the daily workflow question may surface more.

### Phase 2: Probing (follow the user's thread)

Based on what the user says, probe deeper. There is no fixed question set -- follow what matters to the user. Some areas you may explore:

- **Organization philosophy**: How do they think about categorizing knowledge? By project? By topic? By date? Intuitively?
- **Workflow patterns**: How do they create notes? How do they find them later? What connections matter?
- **Pain points**: What breaks? What takes too long? What do they avoid doing because it is too much effort?
- **Growth expectations**: How do they expect the vault to change over the next year? More note types? More automation?
- **Priorities**: If they could only fix one thing, what would it be?

When the user mentions something the analysis flagged, bring it up: "You mentioned you struggle finding meeting notes. The analysis found that meeting notes are spread across 4 different folders with no consistent naming. Is that the problem you are describing?"

### Phase 3: Synthesis (confirm understanding)

When you feel you have a clear picture, synthesize what you have heard back to the user. Cover:

1. **Their primary use cases** for the vault
2. **What matters most** to them (even if it contradicts "best practices")
3. **What they want to change** vs what they want to keep
4. **Their priorities** -- what to fix first

Ask: "Does this capture it? Anything I am missing or got wrong?"

Iterate until the user confirms.

## Output Format

Write the target model to `target-model-{vault-name}.md`:

```markdown
# Target Model: {vault-name}

## User Goals
What the vault is for and what role it plays in the user's work/life.

## Current Pain Points
What frustrates the user, in their own words. Reference specific analysis findings where relevant.

## Desired Organization
How the user wants to think about their vault structure. May include folder layout preferences, naming conventions, or general principles ("I want to find anything in 2 clicks").

## Metadata Preferences
What frontmatter fields matter to the user. What they do not care about. Format preferences.

## Workflow Requirements
How the user creates, finds, and connects notes. What automation they want. What manual process they prefer to keep.

## Priorities
Ordered list of what matters most. What to fix first. What can wait. What to leave alone.

## User Notes
Any additional context, preferences, or constraints the user mentioned during the interview. Include verbatim quotes where they clarify intent.
```

Not every section needs to be filled. If the user did not discuss metadata, leave that section brief. The target model reflects what the user actually said, not what you think they should want.

## Key Principles

- **The user is the authority on their own vault.** If they say deep nesting works for them, that is valid even if the analysis flagged it.
- **Do not prescribe.** The interview discovers intent. The propose phase translates intent into action.
- **Capture preferences, not just problems.** "I like how my Projects folder is organized" is as valuable as "I hate my tag system."
- **Short interview, rich output.** Most interviews should take 5-10 exchanges. Do not over-interrogate.

## Example

User: "Interview me about my vault" (with analysis-MyVault.md present)

Exchange 1: "Walk me through how you use your vault day to day."
User: "Mostly for work -- meeting notes, project tracking, and random research I do not want to lose."

Exchange 2: "When you say random research, where does that end up?"
User: "Honestly, everywhere. Sometimes in Projects, sometimes just in the root. That is probably a problem."

Exchange 3: "The analysis found 47 notes in the root folder with no clear category. Is that the research you are describing?"
User: "Yeah, exactly. I just create a note and forget to file it."

Exchange 4: "So it sounds like you have three main note types -- meetings, projects, and research -- and the research notes are the ones that get lost. The meetings and projects work okay?"
User: "Meetings are fine, projects are okay but messy inside. Research is the big problem."

Exchange 5: "If you could fix one thing, what would it be?"
User: "Make it so research notes have a home and I can actually find them later."

Result: A `target-model-MyVault.md` capturing that the user's priority is a research capture workflow, project folders need internal cleanup, and meetings are fine as-is.
