# 03 — Create project/area skill

**What to build:** A skill file at `.agents/skills/create-project/SKILL.md` that the user invokes (or the daily review skill invokes) to create a new project or area note.

Supports two modes:
- **Wizard mode** (no arguments): asks for name, type (project/area), file or folder, which sections to include (overview, tasks, goals, open loops, decisions, related notes, stakeholders — no goals for areas)
- **Argument mode** (called by daily review skill or with CLI arguments): accepts the same fields as parameters

Creates the note with YAML frontmatter: `title`, `type`, `status` (active), `started`, `target` (projects only), `tags`, `related`. Uses `foam note create --title "..." --dir "01 - Projects" --property type=project --property status=active` for note creation. Creates as single file or folder with `index.md`.

**Blocked by:** 01 — Vault scaffold and sb CLI.

**Status:** done

- [x] Skill file exists at `.agents/skills/create-project/SKILL.md`
- [x] Wizard mode creates project note with selected sections
- [x] Argument mode creates project non-interactively
- [x] Area notes created without goals section
- [x] File vs folder choice works
- [x] YAML frontmatter is correct
