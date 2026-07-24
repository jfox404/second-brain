# 02 — Daily review skill (capture enrichment)

**What to build:** A skill file at `.agents/skills/daily-review/SKILL.md` that the user invokes via Copilot agent mode to process unprocessed captures in today's daily note.

The skill instructs the AI to:
1. Read today's daily note (`/daily/YYYY-MM-DD.md`)
2. Find unprocessed captures (bullet lines starting with `- [HH:MM]` but not `- ✅ [HH:MM]`)
3. For each: assign PARA type tag (`#project/x`, `#area/x`, `#resource/x`), suggest `[[wikilinks]]` to related vault notes, extract `[action:]` items, mark with ✅
4. For URL captures: fetch linked content and synthesize a promoted resource note under `/resources/`
5. Update or create `## Tasks` sections in relevant project notes under `/projects/`
6. Update or create root `tasks.md` aggregating all action items (checkboxes, grouped by `## Project: Name`, with `[[wikilinks]]`)
7. Update or create index notes at vault root: `_projects.md`, `_areas.md`, `_resources.md`
8. Flag captures tagged with a new project tag that has no corresponding project note yet

**Blocked by:** 01 — Vault scaffold and sb CLI.

**Status:** ready-for-agent

- [ ] Skill file exists at `.agents/skills/daily-review/SKILL.md`
- [ ] Invoking the skill in Copilot agent mode enriches captures (✅, tags, links, actions)
- [ ] URL captures trigger resource note synthesis
- [ ] `tasks.md` is created/updated correctly
- [ ] Project `## Tasks` sections are maintained
- [ ] Index notes are created/updated
- [ ] Missing project notes are flagged
