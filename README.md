# Second Brain

A personal knowledge system for turning scattered inputs into reusable thinking. Local-first, markdown-based, AI-assisted via Copilot agent mode.

## Quick start

1. **Install** — run `bin/install` (installs `foam-cli` and symlinks the `sb` CLI to `~/.local/bin/sb`).
2. **Open this repo in VS Code** with the [Foam extension](https://foambubble.github.io/) installed
3. **Capture** — run `sb "thought"` from the terminal or `Cmd+Shift+P > Foam: Open Today's Note` in VS Code
4. **Review** — open Copilot agent mode and say "run the daily review"
5. **Organize** — invoke skills to create projects, promote to folders, or archive

## Workflow

```
Capture → Daily note (CLI or Foam wikilink)
   │
   ▼
Review  → AI enriches today's captures (tags, links, actions, URL synthesis)
   │
   ▼
Promote → AI extracts notable captures to permanent notes
   │
   ▼
Manage  → Create projects/areas, promote to folders, archive
```

## Vault structure

| Directory | Purpose |
|---|---|
| `00 - Inbox/` | Daily notes + raw artifacts — the default capture surface |
| `01 - Projects/` | Time-bound outcomes with tasks, goals, decisions |
| `02 - Areas/` | Ongoing responsibilities (health, career, etc.) |
| `03 - Resources/` | Reference/evergreen material |
| `04 - Archive/` | Completed or stale projects |
| `assets/` | Images and attachments |

Top-level files:
- `tasks.md` — aggregated action items across all projects
- `_projects.md`, `_areas.md`, `_resources.md`, `_archive.md` — auto-maintained index notes

## Skills

Skills are markdown files that instruct AI agents. Invoke them from Copilot agent mode:

| Skill | Location | What it does |
|---|---|---|
| Daily review | `.agents/skills/daily-review/` | Processes unprocessed daily note captures |
| Create project/area | `.agents/skills/create-project/` | Creates structured project or area notes |
| Promote to folder | `.agents/skills/promote-project/` | Converts single file to folder structure |
| Archive | `.agents/skills/archive-project/` | Archives projects with knowledge salvage |

## Domain glossary

See [`CONTEXT.md`](./CONTEXT.md) for the full glossary of terms used throughout this vault.

## Design spec

The full design decisions are documented in [`.scratch/second-brain/spec.md`](./.scratch/second-brain/spec.md). Implementation tickets are at [`.scratch/second-brain/issues/`](./.scratch/second-brain/issues/).
