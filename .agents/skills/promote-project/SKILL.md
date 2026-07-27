---
name: promote-project
description: Promote a single-file project, area, or resource note to a folder structure. Use when the user wants to upgrade a note to a folder, or when the daily review skill discovers accumulated sub-notes and suggests promoting.
argument-hint: "path=<relative-or-absolute-path>"
---

# Promote to Folder

See [shared patterns](../_shared/patterns.md) for trigger mode conventions, slug derivation, and index note maintenance.

Convert a single-file note (`01 - Projects/<slug>.md`) to a folder (`01 - Projects/<slug>/index.md`), moving related notes inside and rewriting all `[[wikilinks]]`.

## Promotion trigger

This skill runs in two modes:

- **Direct invocation** — user provides the path to a note to promote.
- **Review suggestion** — the daily review detects a note with accumulated sub-notes (multiple `[[wikilinks]]` pointing into its slug directory that don't exist yet) and suggests promotion. If the user agrees, the daily review delegates here.

## Process

### 1. Identify the source note

The source is a single `.md` file in `/01 - Projects/`, `/02 - Areas/`, or `/03 - Resources/`. Determine:

- **Path** — from the argument or user-provided input
- **Slug** — the filename without `.md`: `01 - Projects/my-project.md` → `my-project`
- **Type directory** — `projects`, `areas`, or `resources` (from the parent folder)
- **Title** — from YAML frontmatter `title` field

Validate the file exists and is not already a folder (`01 - Projects/slug/index.md` is already promoted — skip).

### 2. Find related notes

Search the vault for notes that reference the source note via `[[slug]]` or `[[Title]]`. Use `foam links` to get inbound and outbound links.

Also scan for co-located notes in the same directory with the same slug prefix (e.g. `01 - Projects/my-project-backend.md` alongside `01 - Projects/my-project.md`).

Compile a list of related notes — these are candidates to move into the folder.

### 3. Present the plan

Show the user:

```
Plan: Promote 01 - Projects/<slug>.md to 01 - Projects/<slug>/index.md

Related notes to move:
  - 01 - Projects/related-note.md → 01 - Projects/slug/related-note.md
  - 02 - Areas/some-topic.md → 01 - Projects/slug/some-topic.md  (cross-type)
```

Ask the user to confirm the move. Let the user remove any related notes from the move list.

### 4. Create the folder and index

1. Create the target directory: `mkdir -p <type-dir>/<slug>`
2. Copy the source note content: `cp <type-dir>/<slug>.md <type-dir>/<slug>/index.md`
3. Remove the original file: `rm <type-dir>/<slug>.md`

### 5. Move related notes

For each related note in the confirmed list:

- **Same type directory**: `foam note move <rel-path> <type-dir>/<slug>/<rel-slug>.md` — this rewrites all `[[wikilinks]]` in the vault pointing to the old location.
- **Cross-type** (e.g., area note into a project folder): copy the file instead of moving it, since the note belongs to another type. Place a `[[wikilink]]` in the index pointing to the original location.

### 6. Update the index.md

After moving related notes, update `<type-dir>/<slug>/index.md` to serve as a **Map of Content** (MOC) for the folder. Append a `## Sub-notes` section:

```markdown
## Sub-notes

- [[related-note]] — Brief description
- [[another-note]] — Brief description
```

Preserve all existing sections from the original note; add `## Sub-notes` at the end.

### 7. Update index notes

Refresh the type index note (`_projects.md`, `_areas.md`, or `_resources.md`) at vault root — the [[wikilink]] target stays the same (the slug), so no entry changes are needed unless the description changed.

### 8. Report back

```
Promoted <type>/<slug>.md to <type>/<slug>/index.md
  - Related notes moved: <count>
  - [[wikilinks]] rewritten: <count> (handled by foam note move)
  - MOC updated in index.md
```

### 9. Reflect

After completing the report, run the **reflect skill** (`.agents/skills/reflect/SKILL.md`). It evaluates whether any user corrections, non-obvious procedures, or durable preferences were discovered during this run and routes findings to the user model or skill patches. If nothing notable, it reports "Reflection: nothing to persist."

## Completion criteria

- Source file removed, folder with `index.md` created
- All confirmed related notes moved into the folder
- `[[wikilinks]]` in other vault notes rewritten to point to new paths
- `index.md` has a `## Sub-notes` section listing moved notes
- User has been told the result
