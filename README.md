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

## How to leverage this system

**The core loop is three minutes a day.** Capture freely — no filing decisions. Then let the AI do the organizing.

### The daily habit

1. **Capture** throughout the day with `sb "thought"` — dump ideas, todos, links, meeting notes. No structure required.
2. **Review once daily** — open Copilot agent mode and say `"run the daily review"`. The AI:
   - Tags each capture with PARA type (`#project/x`, `#area/x`, `#resource/x`)
   - Links captures to related notes with `[[wikilinks]]`
   - Extracts `[action:]` items into project task sections and `tasks.md`
   - Fetches URLs and synthesizes resource notes
   - Marks processed captures with ✅
3. **That's it.** The AI handles organization, you handle thinking.

### AI workflow cheat sheet

| Prompt | What happens |
|---|---|
| `"run the daily review"` | Process all unprocessed captures in today's daily note |
| `"create a project called &lt;name&gt;"` | Interactive wizard for a new project note |
| `"create an area called &lt;name&gt; with sections overview, tasks, decisions"` | Non-interactive area creation |
| `"promote &lt;project&gt; to a folder"` | Convert single-file project to `index.md` + sub-notes |
| `"archive &lt;project&gt;"` | Move to Archive with knowledge salvage review |
| `"review the inbox"` | Process raw artifacts (transcripts, PDF notes, etc.) |

### The capture pipeline

```
Raw capture (sb "thought")          ← zero friction, no decisions
   │
   ▼
Enriched capture (✅)               ← AI adds tags, links, actions
   │
   ▼
Promoted note (standalone file)     ← AI extracts durable ideas
   │
   ▼
Project/area/resource folder        ← grows organically over time
   │
   ▼
Archive (if stale/completed)        ← periodic cleanup, AI suggests
```

**You never file anything.** You capture, AI promotes, you curate with simple prompts.

### PARA philosophy in practice

| Folder | What lives there | When it goes there |
|---|---|---|
| `00 - Inbox` | Daily notes, raw artifacts | All captures land here |
| `01 - Projects` | Time-bound outcomes with deadlines | AI promotes notable captures |
| `02 - Areas` | Ongoing responsibilities (health, career) | Created via skill, never archived |
| `03 - Resources` | Evergreen reference material | AI promotes URLs/book notes here |
| `04 - Archive` | Completed or stale work | Archived via skill, salvaged first |

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
