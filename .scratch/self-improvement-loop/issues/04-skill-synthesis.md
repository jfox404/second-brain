# 04 — Skill synthesis (creating z-* skills)

**What to build:** Extend the reflect skill to autonomously synthesize new `z-*` skills when a workflow doesn't map to any existing skill. Implement the simplified skill format, naming convention, and guardrails (synthesized skills cannot delete or rename themselves).

**Blocked by:** 02 — User model writing, 03 — Skill refinement

**Status:** done

- [x] Implement synthesis trigger: workflow doesn't map to any existing skill AND meets complexity/correction threshold
- [x] Create z-* skill format: name, description, trigger, body (simpler than curated skills)
- [x] Implement writing mechanics: create `.agents/skills/z-*/SKILL.md` with frontmatter
- [x] Enforce guardrail: synthesized skills are read-only to themselves (can be patched by reflect, never deleted/renamed by agent)
- [x] Verify: agent executes a non-obvious multi-step procedure, reflect creates a new `z-*` skill, skill is valid markdown with correct format
