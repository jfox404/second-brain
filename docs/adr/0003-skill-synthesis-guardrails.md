# 0003 — Skill synthesis guardrails

**Status:** accepted

To prevent dangerous feedback loops, the reflect skill operates under three guardrails. First, skill refinement is patch-only — the reflect skill applies targeted old-string→new-string edits to curated skills, never full rewrites, preserving what works. Synthesized skills (`z-*`) can be patched too but cannot be deleted or renamed by the agent. Second, the reflect skill gates on "was the last thing I did a reflection?" — if yes, it stops, preventing nested reflection spirals. Third, curate skill refinements require user confirmation before applying; synthesized skills and user model updates execute automatically. This trades some autonomy for safety — the agent can improve itself freely as long as it stays within the guardrail boundaries, and escalation to the user is reserved for changes to human-authored artifacts.
