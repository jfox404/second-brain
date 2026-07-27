# 04 — Promote-to-folder and archive skills

**What to build:** Two skill files for project lifecycle management.

**Promote-to-folder skill** (`.agents/skills/promote-project/SKILL.md`):
- Converts a single project/area/resource file to a folder: `projects/<slug>.md` → `projects/<slug>/index.md`
- Moves any related notes (wiki-referenced or co-located) into the folder using `foam note move` to automatically rewrite all `[[wikilinks]]`
- AI also suggests promotion when it detects accumulated sub-notes during daily review

**Archive skill** (`.agents/skills/archive-project/SKILL.md`):
- Moves a project from `/01 - Projects/` to `/04 - Archive/`
- Before moving, reviews all project notes for knowledge worth promoting to a resource note
- Proposes resource promotions to the user
- Updates index notes (`_projects.md`, `_areas.md`)
- AI also suggests archiving stale projects during daily review (no recent captures for 30+ days)

**Blocked by:** 03 — Create project/area skill (needs valid project structure to promote/archive).

**Status:** done

- [x] `promote-project` skill exists and converts file to folder
- [x] Related notes are moved into the folder
- [x] `[[wikilinks]]` are updated in other notes
- [x] `archive-project` skill exists and moves to `/04 - Archive/`
- [x] Archive skill reviews for salvageable content before archiving
- [x] Index notes updated after both operations
