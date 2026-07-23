# 01 — Vault scaffold and sb CLI

**What to build:** The vault directory structure, Foam configuration, and basic `sb` CLI script so that the user can capture thoughts from the terminal.

- Vault directories: `/daily/`, `/projects/`, `/areas/`, `/resources/`, `/archive/`, `/assets/`
- Foam config (`.vscode/foam.json` or equivalent) pointing daily notes to `/daily/YYYY-MM-DD.md`, wikilinks enabled
- `sb` bash CLI script installed to PATH with flags: `--open` (open daily note), `--editor` (multi-line), `--type meeting` (puts in ## Meetings), `--project x` (pre-tag), `--tag foo,bar`, `--no-timestamp`, `--help`
- Basic capture: `sb "thought"` appends `- [HH:MM] thought` to today's daily note under `## Captures`, creating the file and heading if missing

**Blocked by:** None — can start immediately.

**Status:** ready-for-agent

- [ ] Vault directories created
- [ ] Foam config added to `.vscode/` or equivalent
- [ ] `sb` CLI script installed, flags all work
- [ ] `sb "thought"` creates daily note and appends capture
- [ ] `sb --open` opens today's note
- [ ] `sb --type meeting "notes"` adds to ## Meetings section
