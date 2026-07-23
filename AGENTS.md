## Second Brain Vault

A personal knowledge system for turning scattered inputs into reusable thinking. Local-first, markdown-based, AI-assisted via Copilot agent mode.

**Prerequisite:** `foam-cli` installed globally (`npm install -g foam-cli`). The `sb` CLI delegates to `foam daily` for daily note creation.

**Install `sb` CLI:** Add `bin/` to your PATH or symlink: `ln -sf "$PWD/bin/sb" ~/.local/bin/sb`

**Workflow:** capture → review → promote → manage

## Vault structure

| Directory | Purpose |
|---|---|
| `/daily/` | Daily notes (capture surface) |
| `/projects/` | Time-bound outcomes |
| `/areas/` | Ongoing responsibilities |
| `/resources/` | Reference/evergreen material |
| `/archive/` | Completed or stale projects |
| `/assets/` | Images and attachments |

## Custom skills

In a fresh session, the first thing the agent should do is read the relevant skill file from `.scratch/second-brain/spec.md` and the ticket from `.scratch/second-brain/issues/`. The following skills have been specced:

- **Review skill** (`.agents/skills/review/SKILL.md`) — enriches daily note captures with tags, links, actions; maintains tasks.md, project task sections, and index notes
- **Create project/area skill** (`.agents/skills/create-project/SKILL.md`) — creates project or area notes with selected sections
- **Promote to folder skill** (`.agents/skills/promote-project/SKILL.md`) — converts single file project to folder structure
- **Archive skill** (`.agents/skills/archive-project/SKILL.md`) — archives projects with salvage review

## Agent skills

### Issue tracker

Issues tracked as local markdown files under `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles use their default label strings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context layout — `CONTEXT.md` + `docs/adr/` at repo root. See `docs/agents/domain.md`.

When working in this vault, use the glossary defined in `CONTEXT.md` for all domain terms.
