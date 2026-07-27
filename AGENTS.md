## Second Brain Vault

A personal knowledge system for turning scattered inputs into reusable thinking. Local-first, markdown-based, AI-assisted via Copilot agent mode.

**Prerequisite:** `foam-cli` installed globally (`npm install -g foam-cli`). The `sb` CLI delegates to `foam daily` for daily note creation.

**Install `sb` CLI:** Run `bin/install` — installs `foam-cli` (if missing) and symlinks `sb` to `~/.local/bin/sb`.

**Workflow:** capture → review → promote → manage

## Self-improvement loop

The agent runs a **reflection** step after every major operation (daily review, inbox review, archive, create-project) and as a self-nudge after any user correction. The reflect skill evaluates recent work and routes findings to `docs/agents/USER.md` (user model) or skill refinement/synthesis. See `.agents/skills/reflect/SKILL.md`.

Before any operation, if the user has a `USER.md` on record, read it. After any correction, write to it.

**Legacy vault location:** `/Users/soj3828/Library/CloudStorage/OneDrive-NorthwesternMutual/Knowledge Vaults/second-brain`

## Vault structure

| Directory | Purpose |
|---|---|
| `/00 - Inbox/` | Daily notes + raw artifacts (capture surface) |
| `/01 - Projects/` | Time-bound outcomes |
| `/02 - Areas/` | Ongoing responsibilities |
| `/03 - Resources/` | Reference/evergreen material |
| `/04 - Archive/` | Completed or stale projects + archived inbox files |
| `/04 - Archive/daily/` | Processed daily notes |
| `/assets/` | Images and attachments |

### Canonical PARA root folders (strict)

Only these exact root folders are valid PARA destinations:

- `00 - Inbox`
- `01 - Projects`
- `02 - Areas`
- `03 - Resources`
- `04 - Archive`

Agents must never create new PARA root variants (for example `02-AREAS`, `02 - AREAS`, `01-projects`, etc.). Always map imports and writes to the existing canonical folders above.

## Custom skills

In a fresh session, the first thing the agent should do is read the relevant skill file from `.scratch/second-brain/spec.md` and the ticket from `.scratch/second-brain/issues/`. The following skills have been specced:

- **Daily review skill** (`.agents/skills/daily-review/SKILL.md`) — enriches daily note captures with tags, links, actions; maintains tasks.md, project task sections, and index notes
- **Create project/area skill** (`.agents/skills/create-project/SKILL.md`) — creates project or area notes with selected sections
- **Promote to folder skill** (`.agents/skills/promote-project/SKILL.md`) — converts single file project to folder structure
- **Archive skill** (`.agents/skills/archive-project/SKILL.md`) — archives projects with salvage review
- **Inbox review skill** (`.agents/skills/inbox-review/SKILL.md`) — processes raw artifacts from `/00 - Inbox/` that aren't daily notes
- **Reflect skill** (`.agents/skills/reflect/SKILL.md`) — evaluates recent work for durable improvements: user model updates, skill patches, or skill synthesis

## Agent skills

### Issue tracker

Issues tracked as local markdown files under `.scratch/<feature>/`. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles use their default label strings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context layout — `CONTEXT.md` + `docs/adr/` at repo root. See `docs/agents/domain.md`.

When working in this vault, use the glossary defined in `CONTEXT.md` for all domain terms.
