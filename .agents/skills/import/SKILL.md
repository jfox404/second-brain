---
name: import
description: Import single or bulk legacy files into the primary vault with OKF frontmatter transformation
disable-model-invocation: true
argument-hint: path=<file-or-directory>
---

# Import Skill — Single File & Bulk Directory Import with Wikilink Cascade, Tag Reconciliation, and Asset Copy

Import legacy `.md` files into the primary vault. Accepts a single file or a directory path. For a single file, reads the legacy file, proposes an OKF frontmatter mapping, gets user approval, determines the target PARA folder from the source path, and writes the transformed file. For a directory, walks all `.md` files recursively, processes them sequentially with user approval per file, and preserves subdirectory structure under PARA folders.

After each import, the agent scans for `[[wikilinks]]` to notes not yet in the primary vault and offers to cascade-import each linked file from the legacy vault.

See [shared patterns](../_shared/patterns.md) for OKF frontmatter conventions.

## Usage

```
--path /path/to/legacy/vault/01 - Projects/My Note.md
```

```
--path /path/to/legacy/vault/01 - Projects/
```

## Step 1 — Validate the path argument

Check that `--path` is provided and the path exists. If not, report the error and stop.

Resolve the path to an absolute path.

**Check if path is a file or directory:**
- If it's a regular file ending with `.md`, proceed to **Step 2 (Single-file import flow)** below.
- If it's a directory, proceed to **Bulk Directory Import** (Section B below).
- If it's a regular file but not `.md`, report: "The specified file is not a `.md` file. Only markdown files can be imported." and stop.
- If it's something else (symlink, etc.), report the error and stop.

Store the resolved path as `LEGACY_PATH`.

---

# Section B — Bulk Directory Import

Use this flow when `LEGACY_PATH` is a directory. After bulk import completes, the session ends — there is no return to the single-file flow.

## Step B1 — Walk the directory for files

Walk `LEGACY_PATH` recursively for all files. Separate them into:

- **Markdown files**: All files ending with `.md` under `LEGACY_PATH` (recursive). Store as `MD_FILES`.
- **Non-markdown files**: All other files (images, PDFs, etc.). Store as `NON_MD_FILES`.

Report the counts to the user:

> Found `<N>` markdown files and `<M>` non-markdown files in `<LEGACY_PATH>`.

## Step B2 — Offer to import non-markdown files

List the non-markdown files to the user:

> **Non-markdown files found:**
> - `<file1>` (e.g., `assets/foo.png`)
> - `<file2>` (e.g., `docs/schema.pdf`)

For each non-markdown file, ask: "Import this file? It will be copied as-is to the corresponding path in the primary vault." (yes / skip). 
- If yes, copy the file to the primary vault at the path relative to `LEGACY_VAULT_ROOT` (same subdirectory structure, as resolved in Step 6 for markdown files).
- If no, skip it and move to the next.

Collect skipped non-markdown files for the final summary.

## Step B3 — Determine legacy vault root

Determine the legacy vault root from `LEGACY_PATH`:

Walk up from `LEGACY_PATH` looking for a directory that contains a `01 - Projects` folder. The first ancestor that has PARA folders is the legacy vault root.

If no vault root can be detected (no PARA folders found up to filesystem root), use `LEGACY_PATH` itself as the vault root.

Store as `LEGACY_VAULT_ROOT`.

## Step B4 — Process markdown files sequentially with approval

For each file in `MD_FILES` (process in sorted order for determinism):

1. Show the user the path relative to `LEGACY_VAULT_ROOT` (this matches the target path in Step 6):
   > **File `<N>` of `<TOTAL>`: `<relative_path>`**
   > Source: `<full_path>`

2. Ask: "Import this file?" (yes / skip)
   - If "skip", record it as skipped and move to the next file.
   - If "yes", run the **Single-file import flow** (Steps 2 through 11) on this file, with one modification to Step 6: use `LEGACY_VAULT_ROOT` as the vault root (it was already determined in Step B3).

3. After each file is imported, run **Wikilink Cascade** (Steps 12-16) on the imported file, offering to cascade-import any orphan wikilinks.

4. Record the outcome (imported / skipped / error) for the final summary.

## Step B5 — Report bulk import summary

After all files have been processed, present a summary:

> **Bulk import complete.**
> - Directory: `<LEGACY_PATH>`
> - Total markdown files found: `<N>`
> - Imported: `<count>`
> - Skipped: `<count>`
> - Errors: `<count>`
> - Non-markdown files imported: `<count>`
> - Non-markdown files skipped: `<count>`

If there were any errors, list each one:

> Errors:
> - `<file>`: `<error description>`

---

**To continue below:** After Section B completes, the session is done. The steps below (Steps 2 through 16) describe the single-file import and wikilink cascade flows, which are invoked per file during the bulk directory walk.

## Step 2 — Read the legacy file

Read the file at the current legacy path. Store the full content.

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
tags: <from legacy tags, after tag reconciliation>
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

**Tag reconciliation:** Before including legacy tags in the proposed mapping, reconcile each tag against the vault glossary per the [Tag Reconciliation](#tag-reconciliation) flow below.

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

**Determine legacy vault root (if not already set by bulk import):**
Walk up from the current file path looking for a directory that contains a `01 - Projects` folder. The first ancestor that has PARA folders is the legacy vault root.

If no vault root can be detected (no PARA folders found), use the directory containing the current file as the vault root.

During bulk import, `LEGACY_VAULT_ROOT` was already determined in Step B3 — use that value directly (skip re-detection).

**Compute relative path:**
`RELATIVE_PATH` = path of current file relative to legacy vault root.

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
<approved OKF frontmatter — source field removed from frontmatter>
---

<original body content (the part after frontmatter)>
```

**Source field transformation:** Apply the [Source Field Transformation](#source-field-transformation) flow to convert `source` to a Sources section in the body and remove it from frontmatter.

**Asset detection:** Scan the body content for asset references (`![alt](path/to/asset)` or `[link](path/to/asset)` pointing to files under an `assets/` directory). These will be processed in the [Asset Copying](#asset-copying) step.

**Do not modify** the original legacy file at any point.

## Step 9 — Write the transformed file

Write the transformed content to `TARGET_PATH`.

## Step 10 — Asset copying

If the body content referenced any assets (detected in Step 8), process each one per the [Asset Copying](#asset-copying) flow below.

## Step 11 — Confirm

Report success to the user:

> **Import complete.**
> - Source: `<current file path>`
> - Destination: `<TARGET_PATH>`
> - Frontmatter fields mapped: `<list of fields>`

---

## Wikilink Cascade

After the file is imported, scan its body for `[[wikilinks]]` to notes not present in the primary vault. For each orphan link, offer to import the source file from the legacy vault. User approves or skips per linked file. Cascade-imported files go through the same frontmatter transformation flow as the parent import.

## Step 12 — Scan imported content for wikilinks

Read the content of `TARGET_PATH` and extract all `[[wikilink targets]]`:

- Use a regex pattern: `\[\[([^\]]+?)(?:\|([^\]]+))?\]\]` to capture the link target and optional display text.
- For each match, extract the **link target** (the part before `|`, or the whole inner text if no pipe).
- Normalize targets by stripping leading/trailing whitespace.
- Collect a **unique set** of wikilink targets. Store as `WIKILINKS`.

**Example extraction:**
- `[[Build a Second Brain]]` → target: `Build a Second Brain`
- `[[Foo Project|Foo]]` → target: `Foo Project` (display text `Foo` is ignored for resolution)

## Step 13 — Check each wikilink against the primary vault

For each target in `WIKILINKS`:

1. Determine the expected filename. Convert the target to a slug (lowercase, spaces to hyphens, strip non-alphanumeric except hyphens, collapse repeated hyphens) and search for these file patterns in the primary vault:
   - `<slug>.md` in any PARA folder
   - `<slug>/index.md` in any PARA folder
2. Also search for a file whose frontmatter `title` field matches the target exactly (case-insensitive).

Use `find` or glob to search the primary vault root (`/home/jon/projects/second-brain`):

```bash
find /home/jon/projects/second-brain -name "<slug>.md" -o -name "<slug>/index.md" 2>/dev/null
```

A simpler alternative: check if the file `<target>.md` exists in any of the PARA directories (`/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, `/04 - Archive/`).

Collect the set of targets not found in the primary vault as `ORPHAN_WIKILINKS`.

If `ORPHAN_WIKILINKS` is empty:
- Report: "All wikilinks in the imported file resolve to existing notes."
- Proceed to completion.

## Step 14 — Present orphan wikilinks for user review

Present the list of orphan wikilinks to the user:

> **Orphan wikilinks found** in the imported file (notes not in primary vault):
> - [[Target One]]
> - [[Target Two]]
>
> Import cascade: for each orphan link, I can try to find the source file in the legacy vault and import it using the same frontmatter transformation.

Ask: "Proceed with cascade import?" (yes / skip all / pick individually)

- If "skip all", report the unresolved wikilinks to the user and proceed to completion.
- If "yes", iterate over each orphan link (in any order).
- If "pick individually", ask the user to select which targets to process, then iterate over the selected subset.

## Step 15 — Cascade-import each orphan wikilink

For each orphan target to process:

**Find the legacy source file:**
Search the legacy vault root for a file matching the target:
- Search for `<target>.md` recursively under the legacy vault root.
- Also try slug variations (lowercase, hyphens).

If no file is found:
- Report: "Could not find a legacy source file for [[Target]] in `<legacy-vault-root>`."
- Ask: "Provide the path manually, or skip?" (provide path / skip)
- If "skip", move to the next orphan target.

If one file is found:
- Report: "Found candidate: `<path>`"
- Ask: "Import this file?" (yes / skip)

If multiple files are found:
- Report all candidates.
- Ask: "Which one to import? (enter number, or 'skip')"

**If approved**, run the **same frontmatter transformation flow** (Steps 2 through 11) on the found legacy file:
- Step 2 — Read the legacy file
- Step 3 — Parse legacy frontmatter
- Step 4 — Derive missing fields
- Step 5 — Propose OKF mapping
- Step 6 — Determine target PARA path
- Step 7 — Check target directory
- Step 8 — Build transformed file
- Step 9 — Write transformed file
- Step 10 — Asset copying
- Step 11 — Confirm (for this cascade file)

**Recursive cascade:** After each cascade-import completes, that file may also contain wikilinks to notes not in the primary vault. Ask the user: "The cascade-imported file also contains orphan wikilinks. Cascade further?" (yes / no). If yes, recursively run Steps 12-15 on the cascade-imported file. Track depth to avoid cycles — maintain a set of already-imported files and skip any file that has already been imported in this session.

## Step 16 — Final verification

After all cascade imports are complete:

1. Re-read the original imported file at `TARGET_PATH`.
2. Check that every `[[wikilink]]` in the file now resolves to a note in the primary vault.
3. Report:

> **Cascade import complete.**
> - Originally imported: `<TARGET_PATH>`
> - Orphan wikilinks found: `<count>`
> - Cascade-imported files: `<list of files>`
> - Skipped: `<list of skipped targets>`
> - Unresolved wikilinks: `<list of unresolved targets>`

If any wikilinks remain unresolved, note them to the user for manual handling.

## Edge cases

### Tag Reconciliation

When a legacy tag doesn't match the vault glossary in `CONTEXT.md`, follow this flow during Step 5:

1. **Detect tags:** Collect all tags from legacy frontmatter. Legacy tags may be a YAML list, a comma-separated string, or a single string — normalize to a list of individual tag strings before proceeding.
2. **Check against glossary:** Read `CONTEXT.md` at the repo root. Look for a glossary section (typically a list of terms with definitions). For each legacy tag, check if it matches a glossary term (case-insensitive) or is commonly understood.
3. **Identify conflicts:** If a tag doesn't match any glossary term, flag it as a conflict.
4. **Present to user:**
   > Tag conflict: `<legacy_tag>` is not in the vault glossary.
   > Options: (r)ename to a glossary term, (k)eep as-is, (s)kip this tag
5. **Handle user choice:**
   - **Rename:** Ask the user which glossary term to use. Replace the tag in the OKF frontmatter.
   - **Keep:** Include the tag as-is in the OKF frontmatter.
   - **Skip:** Remove the tag from the OKF frontmatter entirely.
6. **Proceed** with the reconciled tags in the proposed OKF mapping.

### Source Field Transformation

When the legacy frontmatter includes a `source` field:

1. **Parse source content:**
   - If a string, treat as a single source.
   - If a list, treat as multiple sources.
2. **Detect source type:** For each source entry, determine if it's a wikilink (`[[Note Title]]`), a plain URL (`https://...`), or plain text.
3. **Transform:**
   - **Wikilinks:** Add to `relationships.source` in frontmatter AND append to a `## Sources` section at the end of the body.
   - **URLs:** Add to `relationships.source` in frontmatter AND append as a markdown link in the Sources section.
   - **Plain text:** Add to `relationships.source` in frontmatter AND append as-is in the Sources section.
4. **Remove `source` from frontmatter:** The `source` field is NOT carried over into the OKF frontmatter (it has been transformed into `relationships.source` and the body Sources section).

### Asset Copying

When an imported file references assets (detected during Step 8):

1. **Detect asset references:** Scan the body content for:
   - Image embeds: `![alt](path/to/asset)`
   - File links: `[label](path/to/asset)`
   - Focus on references under an `assets/` directory.
2. **Check each asset:** For each detected reference:
   - Look for the asset file in the legacy vaults `assets/` folder (relative to legacy vault root).
   - Check if the asset already exists in the primary vaults `assets/` folder.
3. **Copy if needed:** If the asset exists in the legacy vault and not in the primary vault, ask the user for approval before copying.
4. **Missing assets:** If an asset reference points to a file that doesn't exist in the legacy vault, warn the user but don't block the import.
5. **Path verification:** After copying, verify the asset file exists at the target path and confirm to the user.

### General Edge Cases

- **No frontmatter**: Derive `title` from filename, ask for `type`, use minimal OKF mapping.
- **Unknown legacy fields**: Carry them over into OKF frontmatter as-is (within OKF conventions) and note them to the user.
- **Multiple tags formats**: Legacy tags may be `[tag1, tag2]` (list) or `tag1, tag2` (comma-separated string). Normalize to YAML list format.
- **Non-PARA path**: If the file is not under a PARA folder, ask the user which PARA folder it should go into and derive the target path accordingly.
- **Date formats other than YYYY-MM-DD**: Convert to ISO datetime if possible; if format is unrecognizable, use the current datetime and note it to the user.
- **Circular cascade**: If two notes wikilink to each other and neither is in the primary vault, importing one triggers an offer to import the other. The already-imported set prevents re-importing the same file twice.
- **Self-referencing wikilinks**: A file that wikilinks to itself should be ignored during cascade (the file already exists in the primary vault — it was just imported).
- **Wikilinks to non-note targets**: Some wikilinks may reference headings (`[[Note#Section]]`) or blocks (`[[Note^block-id]]`). Resolve the base target (`Note`) for cascade purposes; the anchor is resolved naturally once the note exists.

## Completion criteria

### Single-file import
- Legacy file path validated and exists
- Legacy frontmatter parsed and all missing fields handled
- OKF mapping proposed to user and approved (possibly modified)
- Tags reconciled against vault glossary (user decides per conflict)
- Target PARA path determined from source path
- Target directory created if missing (user approved)
- File collision handled if present
- Source field transformed to `relationships.source` + body Sources section
- Referenced assets detected, offered for copy, and copied if approved
- Transformed file written with valid OKF frontmatter (`title`, `type`, `description`, `tags`, `relationships`, `timestamp`)
- Original legacy file untouched
- Success reported with written path

### Bulk directory import
- Agent accepts directory path and walks it recursively for all files
- Non-markdown files listed and offered for import individually
- Legacy vault root determined from directory path
- Markdown files processed sequentially with user approval per file
- Subdirectory structure under PARA folders preserved in the primary vault
- User can skip individual files
- Wikilink cascade runs per imported file during the walk
- Bulk summary reported (N imported, M skipped, any errors)

### Wikilink cascade
- Wikilinks detected in imported file pointing to notes not in primary vault
- Each orphan link offered for import, one at a time
- User can approve or skip per linked file
- Cascade-imported files go through same frontmatter transformation flow
- Recursive cascade offered when cascade-imported files also contain orphan links
- Cycle prevention via already-imported set
- After cascade, all wikilinks in original file verified and unresolved ones reported

### Edge cases
- Tag conflicts detected, presented with rename/keep/skip options
- Renamed tag written to OKF frontmatter with glossary term
- Source field converted to `relationships.source` + body Sources section
- Source field removed from OKF frontmatter after transformation
- Referenced assets detected, copied with approval, and paths verified
- Missing assets reported as warnings (don't fail the import)
