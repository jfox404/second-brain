---
name: reflect
description: Evaluate recent work for durable improvements — user model updates, targeted skill patches (with user confirmation for curated skills), or skill synthesis opportunities. Includes self-guard against nested reflection.
disable-model-invocation: true
---

# Reflect

## Self-guard

If the current context is already inside a reflection invocation (the reflect skill is already on the call stack — i.e., the heuristic trigger fired while reflect was still running), stop immediately and report: "Reflection: self-guard triggered (nested reflection prevented)." Do not proceed through the decision tree.

Fire when any heuristic trigger in AGENTS.md fires. See the "Self-improvement loop" section for the full list of triggers.

## Decision tree

Evaluate the operation just completed. Walk this tree in order:

### 1. Did the user correct you?

If the user corrected your approach, output, or behaviour — even mid-operation (see "User correction" heuristic trigger in AGENTS.md):

- **Route: user model update.** Open `docs/agents/USER.md`. Add a Corrections entry with the date, operation context, and enough detail for a future agent to avoid the same mistake. See [USER.md write mechanics](#usermd-write-mechanics) for the entry format.

- **Route: skill refinement (if correction implies a skill fix).** If the correction suggests a skill misled you or a skill's instructions need clarifying:

  1. **Match correction to a skill.** Read the frontmatter of all skills under `.agents/skills/*/SKILL.md`. Compare the correction topic against each skill's `name` and `description` fields. If the topic clearly matches one skill, that's the target and continue to step 2. If no match:
     - Route to user model update (above).
     - Additionally, check whether the corrected workflow is a non-obvious procedure (≥3 steps, not already documented in any skill, not trivial). If it meets the threshold, also route to **skill synthesis** — create a `z-*` skill capturing the corrected workflow. This handles the correction-threshold case: a user correction about an undocumented complex procedure should still produce a skill.

  2. **Formulate a patch.** Identify the smallest targeted edit that prevents the same error: the exact old string (current text) and new string (replacement) in the target skill file. The patch must be minimal — never rewrite or restructure the skill file. Only fix the specific passage that misled you.

  3. **Confirm with user.** Before applying any change to a curated skill (any skill that is NOT a `z-*` synthesized skill), surface the proposed patch:
     - Which skill file is being patched
     - The old string and new string
     - Ask: "I propose patching `<skill name>` with this change. Apply it?"
     - Wait for user confirmation before applying the edit.
     - For `z-*` synthesized skills, apply the patch autonomously (no confirmation needed).

  4. **Apply the patch.** If confirmed, use a targeted edit (old-string→new-string) to apply the change. Never rewrite the entire file. Route the patch through the Corrections entry in USER.md too.

### 2. Did you discover a non-obvious procedure?

A non-obvious procedure is any sequence of ≥3 steps that (a) you would follow again in the same situation, (b) is not already documented in any skill, (c) is not trivial (e.g., "open a file"), and (d) was not already routed through a user correction (Step 1). If the user corrected this workflow, Step 1 handled it (including the correction-threshold synthesis path) — do not double-route.

- **Route: skill synthesis.** Synthesize a new `z-*` skill at `.agents/skills/z-<name>/SKILL.md`. Use the simplified [z-* Skill Format](#z--skill-format). Synthesized skills are read-only to themselves (see [guardrails](#guardrails)).
- If the procedure is a variation of an existing skill (e.g., a new branch of triage), route to **skill refinement** instead. Use the same patch mechanics as Step 1: formulate a targeted edit, confirm with user (for curated skills), then apply.

### 3. Did you learn something durable about the user?

Something durable: a preference or expressed preference that would apply across sessions (e.g., "prefers Unix timestamps in frontmatter", "wants 2-line commit messages", "dislikes emoji in notes").

- **Route: user model update.** Open `docs/agents/USER.md`. Add a Preferences or Domain Context entry with the date and context. See [USER.md write mechanics](#usermd-write-mechanics) for the entry format.

### 4. Nothing notable

Exit cleanly. Report: "Reflection: nothing to persist."

## z-* Skill Format

Synthesized skills use a simplified format compared to curated skills. Every `z-*` skill file contains:

**Frontmatter** (minimal):
```yaml
---
name: z-<descriptive-name>
description: One-line summary of when and why to use this workflow.
disable-model-invocation: true
---
```

**Body** (one page maximum):
- **Trigger** — 1-2 sentences describing when the agent should fire this skill. Use the vocabulary of the procedure itself, not abstract categories.
- **Procedure** — Numbered steps in order. Each step ends with a checkable completion criterion.
- **Rules (optional)** — Constraints or invariants the agent must follow while executing.

### Writing mechanics

1. Create directory `.agents/skills/z-<name>/`
2. Write `.agents/skills/z-<name>/SKILL.md` with the frontmatter and body above
3. Verify the file is valid markdown with correct YAML frontmatter (no unclosed code fences, valid YAML)

## Guardrails

The following guardrails apply to all `z-*` synthesized skills:

- **No self-deletion.** The agent must never delete a `z-*` skill directory or its `SKILL.md`. If a `z-*` skill is no longer useful, flag it for the user rather than removing it.
- **No renaming.** The agent must never rename a `z-*` skill or move it to a different path. If a name is wrong, flag it for the user.
- **Autonomous patching.** The reflect skill may patch `z-*` skills autonomously (no user confirmation) via targeted edits, following the same patch mechanics as Step 1.3.
- **`z-*` skills reference this guardrail section.** The guardrails in this section are the single source of truth. Every `z-*` skill implicitly inherits them — no need to repeat them in individual `z-*` files.

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
- A skill file created or patched (including `z-*` skills)
- "Reflection: nothing to persist" reported
- "Reflection: self-guard triggered (nested reflection prevented)" reported