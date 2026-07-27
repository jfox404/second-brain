# 01 — Vault scaffold and sb CLI

**What to build:** The vault directory structure, Foam configuration, `foam-cli` installed, and a thin `sb` CLI wrapper so that the user can capture thoughts from the terminal.

Prerequisite: `npm install -g foam-cli`

- Vault directories: `/00 - Daily/`, `/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, `/04 - Archive/`, `/assets/`
- Foam template at `.foam/templates/daily-note.md` with frontmatter (`date:`) and sections (`## Today's Focus`, `## Captures`, `## Meetings`)
- Foam config pointing daily notes to `/00 - Daily/YYYY-MM-DD.md`, wikilinks enabled
- `sb` bash CLI wrapper that delegates to `foam daily --create --path-only` for note creation, then appends the capture line. Flags: `--open` (open daily note), `--editor` (multi-line), `--project x`, `--tag foo,bar`, `--no-timestamp`, `--help`
- Basic capture: `sb "thought"` → `foam daily --create --path-only` gets the path, then appends `- [HH:MM] thought` under `## Captures`

**Blocked by:** None — can start immediately.

**Status:** done

- [x] `foam-cli` installed globally (via `bin/install`)
- [x] Vault directories created (convention: `00 - Inbox/` instead of `00 - Daily/` per template)
- [x] Daily note template at `.foam/templates/daily-note.md`
- [x] Foam config in `.vscode/settings.json`
- [x] `sb` CLI wrapper script installed, flags all work
- [x] `sb "thought"` creates daily note and appends capture
- [x] `sb --open` opens today's note
- ~~[ ] `sb --type meeting "notes"` adds to ## Meetings section~~ *(removed — meetings captured via snippet instead)*
