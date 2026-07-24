# Shared Patterns

Reference for skill authors. Skills that duplicate any pattern below should link here instead.

## Trigger modes

Skills that are invocable from both user command and the review pass use two modes:

| Mode | How | When |
|---|---|---|
| **Direct invocation** | User provides path/slug via argument hint | User-initiated |
| **Review suggestion** | Review pass detects the trigger condition, proposes action, user confirms | Review-initiated |

Pattern: ask the user or accept params, validate, propose a plan, confirm, execute, report.

## Slug derivation

Consistent across all creation and promotion:

```
lowercase(title) → replace spaces with hyphens → strip non-alphanumeric except hyphens → collapse repeated hyphens
```

## Index note conventions

Four index notes at vault root:

| File | Contents | Maintained by |
|---|---|---|
| `_projects.md` | `- [[Title]] — _description_ \`status: <status>\`` | Review pass (step 6) |
| `_areas.md` | `- [[Title]] — _description_` | Review pass (step 6) |
| `_resources.md` | `- [[Title]] — _description_` | Review pass (step 6) |
| `_archive.md` | `- [[Title]] — _description_` | Archive skill (step 5) |

When updating any index note: scan the corresponding directory, include every `.md` and subfolder `index.md`, read `title` and `status` from YAML frontmatter. Prefer updating existing entries; do not replace the whole file unless rebuilding from scratch.

## OKF-compliant frontmatter

Every promoted note (project, area, resource) follows Open Knowledge Format:

```yaml
---
title: <Title>
type: project|area|resource
description: <1-2 sentence summary>
tags: [tag1, tag2]
relationships:
  <relationship-type>: <target>
timestamp: <ISO datetime>
---
```

Project notes additionally carry `status`, `started`, and optionally `target`. Daily notes are lightweight (`date` only).

## File naming

| Note type | Single file | Folder |
|---|---|---|
| Daily | `/00 - Inbox/Daily Note - YYYY-MM-DD.md` | — |
| Project | `/01 - Projects/<slug>.md` | `/01 - Projects/<slug>/index.md` |
| Area | `/02 - Areas/<slug>.md` | `/02 - Areas/<slug>/index.md` |
| Resource | `/03 - Resources/<slug>.md` | `/03 - Resources/<slug>/index.md` |
| Archive | `/04 - Archive/<slug>.md` | `/04 - Archive/<slug>/index.md` |
