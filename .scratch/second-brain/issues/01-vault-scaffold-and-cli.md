# 01 — Vault scaffold and sb CLI

**What to build:** The vault directory structure, Foam configuration, `foam-cli` installed, and a thin `sb` CLI wrapper so that the user can capture thoughts from the terminal.

Prerequisite: `npm install -g foam-cli`

- Vault directories: `/00 - Daily/`, `/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, `/04 - Archive/`, `/assets/`
- Foam template at `.foam/templates/daily-note.md` with frontmatter (`date:`) and sections (`## Today's Focus`, `## Captures`, `## Meetings`)
- Foam config pointing daily notes to `/00 - Daily/YYYY-MM-DD.md`, wikilinks enabled
- `sb` bash CLI wrapper that delegates to `foam daily --create --path-only` for note creation, then appends the capture line. Flags: `--open` (open daily note), `--editor` (multi-line), `--type meeting`, `--project x`, `--tag foo,bar`, `--no-timestamp`, `--help`
- Basic capture: `sb "thought"` → `foam daily --create --path-only` gets the path, then appends `- [HH:MM] thought` under `## Captures`

**Blocked by:** None — can start immediately.

**Status:** ready-for-agent

- [ ] `foam-cli` installed globally
- [ ] Vault directories created
- [ ] Daily note template at `.foam/templates/daily-note.md`
- [ ] Foam config added
- [ ] `sb` CLI wrapper script installed, flags all work
- [ ] `sb "thought"` creates daily note and appends capture
- [ ] `sb --open` opens today's note
- [ ] `sb --type meeting "notes"` adds to ## Meetings section
