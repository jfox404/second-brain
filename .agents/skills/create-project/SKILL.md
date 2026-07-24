---
name: create-project
description: Create a project or area note with selected sections. Use when the user wants to create a new project, start a new area of responsibility, or when the daily review skill discovers a capture tagged with a new #project/x or #area/x tag that has no corresponding note and proposes creating one.
argument-hint: "name=<title> type=project|area format=file|folder sections=overview,tasks,goals,open-loops,decisions,related,stakeholders tags=tag1,tag2 related=Note Title"
---

# Create Project/Area

See [shared patterns](../_shared/patterns.md) for OKF frontmatter conventions and slug derivation.

Two branches — **wizard mode** when the user provides no arguments (asks interactively), **argument mode** when they do.

## Common invariant (both modes)

Every note gets this OKF-compliant frontmatter:

```yaml
---
title: <Title>
type: project|area
description: <1-2 sentence summary>
status: active
started: <YYYY-MM-DD>
tags: []
relationships: {}
timestamp: <YYYY-MM-DDTHH:MM:SS±HH:MM>
---
```

Projects also get `target: <YYYY-MM-DD>` (optional — ask or infer from context). For area notes the goals section is never included.

## Branch 1 — Wizard mode (no args)

When the user invokes the skill without providing arguments:

1. **Ask for the note title** — a short descriptive name.
2. **Ask the type** — project (time-bound outcome) or area (ongoing responsibility).
3. **Ask for format** — single file (`01 - Projects/<slug>.md`) or folder (`01 - Projects/<slug>/index.md`).
4. **Ask for sections** — present the list: overview, tasks, goals, open loops, decisions, related notes, stakeholders. Let the user pick any subset. (Area notes cannot include goals per the invariant above.)
5. **Ask for tags** (optional) — comma-separated list.
6. **Ask for related notes** (optional) — comma-separated wikilink titles.
7. **If type is project, ask for a target date** (optional).
8. Create the note (see Common creation steps below).
9. Confirm the path and content.

## Branch 2 — Argument mode (called by daily review skill or with params)

When arguments are passed or a calling skill provides the fields:

Accept these named parameters:

| Parameter | Description | Required |
|---|---|---|
| `name` | Note title | Yes |
| `type` | `project` or `area` | Yes |
| `format` | `file` or `folder` (default: `file`) | No |
| `sections` | Comma-separated list of sections to include | No (default: overview, tasks, goals for projects, no goals for areas) |
| `tags` | Comma-separated tags | No |
| `related` | Comma-separated wikilink titles (maps to `relationships` in frontmatter) | No |
| `target` | Target completion date for projects | No |

Apply defaults for any missing parameter and proceed to creation.

## Common creation steps

1. **Derive the slug** from the title: lowercase, spaces to hyphens, strip non-alphanumeric except hyphens.
2. **Determine the directory** — `01 - Projects` for project type, `02 - Areas` for area type.
3. **Build the frontmatter** with today's date as `started`.
4. **Build the body** with the selected sections (see Section templates below).
5. **Create the file:**
   - **Single file**: `foam note create --title "<Title>" --dir <dir>` then overwrite the file with the full frontmatter + body. (Foam creates the file and indexes it; you replace the placeholder content.)
   - **Folder**: `mkdir -p <dir>/<slug>` then write `<dir>/<slug>/index.md` with the full frontmatter + body. Do not use `foam note create` for folders — foam creates flat files only.
6. **Report back**: tell the user the note was created and at what path.

## Section templates

Each section is a second-level heading with placeholder content:

```
## Overview

Brief description of the project or area.

## Tasks

- [ ] Task 1
- [ ] Task 2

## Goals

- Goal 1
- Goal 2

## Open Loops

- Question or unresolved item

## Decisions

- **Decision**: Description. *Context*: Why.

## Related Notes

- [[Note Title]] — how it relates

## Stakeholders

- Person or role
```

Only include the sections the user (wizard) or caller (argument mode) selected.

## Completion criteria

- Note file or folder created at the correct path
- Frontmatter has `title`, `type`, `description`, `status: active`, `started`, `tags`, `relationships`, `timestamp` with today's date
- Project notes include `target` if specified
- Area notes never include a Goals section
- Only the selected sections appear in the body
- Tags and related notes are in the frontmatter (`tags`, `relationships`) if provided
- User has been told the path
