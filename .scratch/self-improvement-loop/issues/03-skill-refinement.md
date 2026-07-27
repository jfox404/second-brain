# 03 — Skill refinement (patching curated skills)

**What to build:** Extend the reflect skill to patch curated skills when a user correction maps to an existing skill's domain. Include user confirmation flow for curated changes. Implement patch-only mechanics (targeted old-string→new-string edits, never full rewrite).

**Blocked by:** 02 — User model writing (needs heuristic trigger infrastructure)

**Status:** ready-for-agent

- [ ] Implement skill-to-correction matching: does the correction topic match any existing skill's name/description?
- [ ] Implement patch-only refinement mechanics (targeted edit)
- [ ] Implement user confirmation flow for curated skill changes (surfaced for approval before applying)
- [ ] Reflect self-guard: if "last action was reflection" → stop (no nested loops)
- [ ] Verify: user corrects a workflow that matches an existing skill, agent proposes a patch, user confirms, skill is patched, change is minimal
