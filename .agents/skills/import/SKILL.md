---
name: import
description: Import a single legacy file into the primary vault with OKF frontmatter transformation
disable-model-invocation: true
argument-hint: path=<path>
---

# Import Skill — Single File Import

Import a single legacy `.md` file into the primary vault. The agent reads the legacy file, proposes an OKF frontmatter mapping, gets user approval, determines the target PARA folder from the source path, and writes the transformed file.

See [shared patterns](../_shared/patterns.md) for OKF frontmatter conventions.

## Usage

```
--path /path/to/legacy/vault/01 - Projects/My Note.md
```

## Step 1 — Validate the path argument

Check that `--path` is provided, the file exists, is a regular file, and ends with `.md`. If any check fails, report the error and stop.

If the path is valid, resolve it to an absolute path. Store as `LEGACY_FILE_PATH`.

## Step 2 — Read the legacy file

Read `LEGACY_FILE_PATH`. Store the full content.

## Step 3 — Parse legacy frontmatter

Check for YAML frontmatter (delimited by `---`). If present, parse these expected legacy fields and note any that are missing:

| Legacy field | Expected type | Notes |
|---|---|---|
| `title` | string | Display title |
| `type` | string | `project`, `area`, `resource`, or others |
| `created` | date string | `YYYY-MM-DD` or other format |
| `tags` | list | List of tag strings |
| `source` | string or list | Inbound references (wikilinks or URLs) |

Store parsed fields as `LEGACY_FRONTMATTER`. If no frontmatter exists, set all fields to empty/defaults and note this to the user.

## Step 4 — Derive missing fields

If `title` is missing, derive it from the filename (strip `.md`, replace hyphens/underscores with spaces, title-case).

If `type` is missing, ask the user or derive from the PARA folder the file resides in (e.g., files under `01 - Projects/` are `project` type).

If `created` is missing, use the file's modification time.

If `description` is missing (which it almost certainly will be — legacy vaults don't use this field), propose a 1-2 sentence summary for user approval (see Step 5).

## Step 5 — Propose OKF mapping

Build a proposed OKF frontmatter:

```yaml
---
title: <from legacy title>
type: <from legacy type>
description: <1-2 sentence summary — propose a draft>
tags: <from legacy tags>
relationships: <see below>
timestamp: <current ISO datetime or convert created to ISO>
---
```

**Relationships handling:**
- If `source` exists and contains wikilinks (e.g., `[[Note Title]]`), set `relationships.source` to those wikilinks.
- If `source` exists and contains plain text URLs, set `relationships.source` to the URLs.
- If `source` is absent, set `relationships: {}`.

**Timestamp conversion:**
- If `created` exists (e.g., `2024-01-15`), convert to ISO datetime: `2024-01-15T00:00:00+00:00`.
- If `created` is absent, use the current datetime in ISO format.

Present the proposed mapping to the user in a clear before/after format:

```
Legacy frontmatter:
  title: Build a Second Brain
  type: project
  created: 2024-01-15
  tags: [productivity, pkm]

Proposed OKF mapping:
  title: Build a Second Brain
  type: project
  description: A project to build a second brain using PARA and OKF.
  tags: [productivity, pkm]
  relationships: {}
  timestamp: 2024-01-15T00:00:00+00:00
```

Ask the user:
1. Do you approve this mapping? (yes / modify / cancel)
2. If "modify", ask which fields to change and accept new values.
3. If "cancel", report that the import was cancelled and stop.

## Step 6 — Determine the target PARA path

Resolve the target path by computing the relative path from the legacy vault root, then mapping it into the primary vault.

**Determine legacy vault root:**
Walk up from `LEGACY_FILE_PATH` looking for a directory that contains a `01 - Projects` folder. The first ancestor that has PARA folders is the legacy vault root.

If no vault root can be detected (no PARA folders found), use the directory containing `LEGACY_FILE_PATH` as the vault root.

**Compute relative path:**
`RELATIVE_PATH` = path of `LEGACY_FILE_PATH` relative to legacy vault root.

**Compute target path:**
`TARGET_PATH` = `<primary-vault-root>/<RELATIVE_PATH>`

The primary vault root is the repo root (`/home/jon/projects/second-brain`).

**Example:**
- Legacy file: `/home/jon/old-sb/01 - Projects/Foo.md`
- Legacy vault root: `/home/jon/old-sb`
- Relative path: `01 - Projects/Foo.md`
- Target path: `/home/jon/projects/second-brain/01 - Projects/Foo.md`

## Step 7 — Check target directory

Check if the target directory (parent of `TARGET_PATH`) exists in the primary vault.

If it does NOT exist:
- Inform the user: "The target directory `<parent>` does not exist in the primary vault."
- Ask: "Create it?" (yes / no)
- If yes, create the directory (using `mkdir -p`).
- If no, ask the user for an alternative target path, or cancel.

Also check if a file already exists at `TARGET_PATH`. If it does:
- Inform the user: "A file already exists at `<TARGET_PATH>`."
- Offer options: skip / rename / overwrite (the legacy file, not the existing one).
- Act on the user's choice. If rename, derive a unique name (e.g., append `-imported` before `.md`).

## Step 8 — Build the transformed file

Build the output markdown content:

```markdown
---
<approved OKF frontmatter>
---

<original body content (the part after frontmatter)>
```

If the legacy frontmatter had a `source` field with wikilinks, append a `## Sources` section at the end of the body:

```markdown
## Sources

- [[Referenced Note]]
```

(The `source` field is also preserved in `relationships.source` in frontmatter per Step 5.)

**Do not modify** the original legacy file at any point.

## Step 9 — Write the transformed file

Write the transformed content to `TARGET_PATH`.

## Step 10 — Confirm

Report success to the user:

> **Import complete.**
> - Source: `<LEGACY_FILE_PATH>`
> - Destination: `<TARGET_PATH>`
> - Frontmatter fields mapped: `<list of fields>`

## Edge cases

- **No frontmatter**: Derive `title` from filename, ask for `type`, use minimal OKF mapping.
- **Unknown legacy fields**: Carry them over into OKF frontmatter as-is (within OKF conventions) and note them to the user.
- **Multiple tags formats**: Legacy tags may be `[tag1, tag2]` or `[tag1, tag2]` (list) or `tag1, tag2` (comma-separated string). Normalize to YAML list format.
- **Non-PARA path**: If the file is not under a PARA folder, ask the user which PARA folder it should go into and derive the target path accordingly.
- **Date formats other than YYYY-MM-DD**: Convert to ISO datetime if possible; if format is unrecognizable, use the current datetime and note it to the user.

## Completion criteria

- Legacy file path validated and exists
- Legacy frontmatter parsed and all missing fields handled
- OKF mapping proposed to user and approved (possibly modified)
- Target PARA path determined from source path
- Target directory created if missing (user approved)
- File collision handled if present
- Transformed file written with valid OKF frontmatter (`title`, `type`, `description`, `tags`, `relationships`, `timestamp`)
- Original legacy file untouched
- Success reported with written path
