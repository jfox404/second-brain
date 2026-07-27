---
name: reflect
description: Evaluate recent work for durable improvements — user model updates, skill patches, or skill synthesis opportunities.
disable-model-invocation: true
---

# Reflect

Run at the end of every major operation (daily review, inbox review, archive, create-project) and as a self-nudge after any user correction.

## Decision tree

Evaluate the operation just completed. Walk this tree in order:

### 1. Did the user correct you?

If the user corrected your approach, output, or behaviour:

- **Route: user model update.** Open `docs/agents/USER.md`. If the correction reveals a durable preference or communication pattern, add it to the Corrections section with enough context for a future agent to avoid the same mistake. If the correction already exists, skip.
- If the correction also implies a fix to a skill (the skill misled you), open the skill file and apply the **smallest patch** that prevents the same error. Route the patch through the Corrections entry too.

### 2. Did you discover a non-obvious procedure?

A non-obvious procedure is any sequence of ≥3 steps that (a) you would follow again in the same situation, (b) is not already documented in any skill, and (c) is not trivial (e.g., "open a file").

- **Route: skill synthesis.** Create a new skill at `.agents/skills/<name>/SKILL.md`. Include frontmatter with `name`, `description`, and `disable-model-invocation: true`. Write the procedure as ordered steps. Keep it to one page.
- If the procedure is a variation of an existing skill (e.g., a new branch of triage), route to **skill refinement** instead: open the existing skill and add the new branch.

### 3. Did you learn something durable about the user?

Something durable: a preference that would apply across sessions (e.g., "prefers Unix timestamps in frontmatter", "wants 2-line commit messages", "dislikes emoji in notes").

- **Route: user model update.** Add to the Preferences or Domain Context section of `docs/agents/USER.md`.

### 4. Nothing notable

Exit cleanly. Report: "Reflection: nothing to persist."

## Completion criterion

One of:
- `docs/agents/USER.md` updated with one or more new entries
- A skill file created or patched
- "Reflection: nothing to persist" reported