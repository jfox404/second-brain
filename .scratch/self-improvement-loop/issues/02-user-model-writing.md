# 02 — User model writing

**What to build:** Extend the reflect skill to write durable facts to USER.md when it detects a user correction or expressed preference during reflection. Add heuristic trigger definitions to AGENTS.md so reflection fires on user correction mid-session.

**Blocked by:** 01 — Reflect skill scaffold + AGENTS.md + USER.md

**Status:** done

- [x] Define heuristic triggers in AGENTS.md: user correction, 5+ tool calls, error recovery, non-obvious procedure → invoke reflect
- [x] Extend reflect skill's decision tree to route user corrections and expressed preferences to USER.md writes
- [x] Implement USER.md write mechanics (append or replace entry by section)
- [x] Verify: user corrects agent during a workflow, USER.md gains a durable entry
