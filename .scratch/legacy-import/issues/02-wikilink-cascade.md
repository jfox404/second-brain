## Parent

`.scratch/legacy-import/spec.md`

## What to build

After importing a file, the agent scans its content for `[[wikilinks]]` to notes not present in the primary vault. For each orphan link, the agent offers to import the source file from the legacy vault. The user approves or skips per linked file.

Imported cascade files go through the same frontmatter transformation as the parent import (issue 01).

**Status:** done

## Acceptance criteria

- [x] Agent detects wikilinks in the imported file pointing to notes not in primary vault
- [x] Agent offers to import each linked file, one at a time
- [x] User can approve or skip per file
- [x] Cascade-imported files go through the same frontmatter transformation flow
- [x] After cascade, all wikilinks in the original file resolve correctly

## Blocked by

- `.scratch/legacy-import/issues/01-single-file-import.md`