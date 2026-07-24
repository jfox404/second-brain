---
name: archive-project
description: Archive a completed or stale project to /archive/. Use when the user wants to archive a project, or when the daily review skill suggests archiving a stale project with no recent captures for 30+ days.
argument-hint: "path=<relative-path>"
---

# Archive Project

See [shared patterns](../_shared/patterns.md) for trigger mode conventions, index note maintenance, and OKF frontmatter.

Move a project from `/projects/` to `/archive/`, reviewing for salvageable content before moving, and updating index notes.

## Archive trigger

Two modes:

- **Direct invocation** — user provides the project path or slug to archive.
- **Review suggestion** — the daily review detects a project with no recent captures (30+ days since last `[[wikilink]]` from a daily note) and suggests archiving. If the user agrees, the daily review delegates here.

Only projects (not areas or resources) can be archived. Area notes are never archived per the domain model.

## Process

### 1. Identify the project

The source is a project at `/projects/<slug>.md` or `/projects/<slug>/index.md` (folder). Determine:

- **Path** — from argument or user input
- **Slug** — directory name or filename without `.md`
- **Title** — from YAML frontmatter `title`
- **Format** — single file or folder

Validate that:
- The source exists under `/projects/`
- It has `type: project` in frontmatter
- It is not already archived (status should not be `archived`)

### 2. Review for salvageable content

Before archiving, review all notes in the project (the index note and any sub-notes in the folder) for knowledge worth preserving as evergreen reference material.

For each note, check:

- **Concept definitions** — is there a standalone concept, framework, or technique that belongs in `/resources/`?
- **Lessons learned** — are there retrospective insights worth keeping accessible?
- **Reusable patterns** — are there configurations, templates, or code snippets useful beyond this project?
- **Unique reference data** — are there links, comparisons, or research summaries worth keeping?

If any salvageable content is found, **do not move it automatically**. Instead, present to the user:

```
Salvage candidates from projects/<slug>:

  - "Concept X" — brief definition → propose as resources/concept-x.md
  - "Lessons learned" — retrospective insights → propose as resources/lessons-from-slug.md
  - "Useful script" — reusable pattern → propose as resources/slug-scripts.md

Proceed with these promotions? [y/n]
```

If the user agrees, create each promoted resource note:

1. Derive a slug from the content title
2. Write the file at `/resources/<slug>.md` with OKF frontmatter:

```yaml
---
title: <promoted title>
type: resource
description: <1-2 sentence summary>
tags: [<relevant tags>]
relationships:
  source: [[<original project>]]
timestamp: <current ISO datetime>
---
```

3. Include the body of the original content, edited for standalone readability
4. Replace content in the project note with a `[[wikilink]]` to the new resource note

### 3. Update project frontmatter

Set the project's frontmatter before moving:

```yaml
---
status: archived
archived: <YYYY-MM-DD>
---
```

Add `archived` date field; change `status` from `active`/`paused`/`completed` to `archived`.

### 4. Move to archive

- **Single file**: `foam note move projects/<slug>.md archive/<slug>.md` — rewrites all `[[wikilinks]]` vault-wide
- **Folder**: `mv projects/<slug> archive/<slug>` — preserves the folder structure and sub-notes

### 5. Update index notes

1. **`_projects.md`** — remove the entry for the archived project (or update its status, since the file still exists at its new path — but index notes list active projects, so remove it)
2. **`_archive.md`** (at vault root) — create or update an archive index note listing archived projects:

```markdown
# Archive

- [[Project Name]] — _Short description_
```

Scan `/archive/` for all project folders and files to build the list.

### 6. Update tasks.md

Remove or mark any tasks in `/tasks.md` that belong to the archived project. Change the heading from:

```
## Project: Name
```

to:

```
## Project: Name (archived)
```

to keep the record but signal it's no longer active.

### 7. Report back

```
Archived projects/<slug> → archive/<slug>
  - Resource promotions created: <count>
  - _projects.md updated
  - _archive.md updated
  - tasks.md updated
```

## Completion criteria

- Project moved to `/archive/`
- Frontmatter updated with `status: archived` and `archived` date
- Salvageable content promoted to resources (if user agreed)
- `_projects.md` updated (entry removed)
- `_archive.md` created/updated
- `tasks.md` updated with archive markers
- User has been told the result
