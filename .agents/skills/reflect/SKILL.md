---
name: reflect
description: Evaluate recent work for durable improvements — user model updates, skill patches, or skill synthesis opportunities.
disable-model-invocation: true
---

# Reflect

Fire when any heuristic trigger in AGENTS.md fires. See the "Self-improvement loop" section for the full list of triggers.

## Decision tree

Evaluate the operation just completed. Walk this tree in order:

### 1. Did the user correct you?

If the user corrected your approach, output, or behaviour — even mid-operation (see "User correction" heuristic trigger in AGENTS.md):

- **Route: user model update.** Open `docs/agents/USER.md`. Add a Corrections entry with the date, operation context, and enough detail for a future agent to avoid the same mistake. See [USER.md write mechanics](#usermd-write-mechanics) for the entry format.
- If the correction also implies a fix to a skill (the skill misled you), open the skill file and apply the **smallest patch** that prevents the same error. Route the patch through the Corrections entry too.

### 2. Did you discover a non-obvious procedure?

A non-obvious procedure is any sequence of ≥3 steps that (a) you would follow again in the same situation, (b) is not already documented in any skill, and (c) is not trivial (e.g., "open a file").

- **Route: skill synthesis.** Create a new skill at `.agents/skills/<name>/SKILL.md`. Include frontmatter with `name`, `description`, and `disable-model-invocation: true`. Write the procedure as ordered steps. Keep it to one page.
- If the procedure is a variation of an existing skill (e.g., a new branch of triage), route to **skill refinement** instead: open the existing skill and add the new branch.

### 3. Did you learn something durable about the user?

Something durable: a preference or expressed preference that would apply across sessions (e.g., "prefers Unix timestamps in frontmatter", "wants 2-line commit messages", "dislikes emoji in notes").

- **Route: user model update.** Open `docs/agents/USER.md`. Add a Preferences or Domain Context entry with the date and context. See [USER.md write mechanics](#usermd-write-mechanics) for the entry format.

### 4. Nothing notable

Exit cleanly. Report: "Reflection: nothing to persist."

## USER.md write mechanics

### Corrections

Entry format:
```markdown
- **YYYY-MM-DD:** During `<operation>`: <what the user corrected>. <Guidance to avoid repeating>.
```

- Append new entries at the bottom of the Corrections section.
- If an identical entry already exists (same date, same correction), skip it.

### Preferences

Entry format:
```markdown
- **YYYY-MM-DD:** <preference description with enough context for cross-session reuse>.
```

- Append new entries at the bottom of the Preferences section.
- If an identical entry already exists, skip it.
- If the user *contradicts* an existing preference, replace that entry rather than appending.

### Domain Context

Entry format:
```markdown
- **YYYY-MM-DD:** <context detail>.
```

- Append new entries at the bottom of the Domain Context section.
- If the user explicitly contradicts a prior domain context entry, replace the contradicted entry.

## Completion criterion

One of:
- `docs/agents/USER.md` updated with one or more new entries
- A skill file created or patched
- "Reflection: nothing to persist" reported