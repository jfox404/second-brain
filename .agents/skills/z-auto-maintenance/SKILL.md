---
name: z-auto-maintenance
description: Lint and maintain vault health — orphan detection, dead link checking, frontmatter validation, directory conformity, duplicate flagging, and restructuring suggestions. Run standalone or invoked by daily review.
disable-model-invocation: true
---

# Auto-maintenance

Invoke standalone when the user asks for vault health check, linting, or cleanup. Also invoked as a step in daily review (step 4).

## Trigger

- User says "run auto-maintenance", "lint the vault", "check vault health", "find orphans", "clean up dead links"
- Daily review step 4 calls into this skill after capture enrichment

## Procedure

### 1. Scan vault layout

Collect an inventory of every `.md` file in the vault, organized by PARA folder:

| Folder | Expected `type` | Notes |
|---|---|---|
| `/00 - Inbox/` | (none — raw captures) | Daily notes + inbox artifacts. Skip from most checks. |
| `/01 - Projects/` | `project` or `meeting` | |
| `/02 - Areas/` | `area` | |
| `/03 - Resources/` | `resource` | |
| `/04 - Archive/` | `project` (archived) | |

Exclude skill files under `.agents/`, `.claude/`, `.scratch/`, `docs/`, `assets/`, and root files (README, AGENTS, CONTEXT).

### 2. Orphan detection

For each note in `01`, `02`, `03`:
1. Read the file's content
2. Search the entire vault (excluding `.agents/`, `.claude/`, `.scratch/`, and the note itself) for `[[Slug]]` or `[[Title]]` or `[[filename]]` references to this note
3. Also check if the corresponding index note (`_projects.md`, `_areas.md`, `_resources.md`) lists this note
4. If zero references found, flag as **orphan**

Completion: produce a list of orphan paths with their title and type.

### 3. Dead wikilink detection

For every `.md` file (excluding `.agents/`, `.claude/`, `.scratch/`):
1. Find all `[[...]]` references
2. For each reference, resolve to a file path:
   - If the text inside `[[...]]` is a filename without extension, look for `<text>.md` or `<text>/index.md`
   - If it's a Title with spaces, derive the slug via the slug derivation pattern (see _shared/patterns.md)
3. Check if the resolved path exists on disk
4. If not, flag as **dead link**

Completion: produce a list of source files and the dead wikilinks they contain.

### 4. Frontmatter validation

For each note in `01`, `02`, `03` (not in `00 - Inbox/` or `04 - Archive/`):
1. Check it has YAML frontmatter delimited by `---`
2. Check required fields exist: `title`, `type`, `description`, `timestamp`
3. Check `type` matches the VALUES in step 1's table
4. Check `timestamp` is valid ISO datetime
5. Check `tags` is present (even if empty)

Completion: produce a list of notes with missing or malformed frontmatter, grouped by issue.

### 5. Directory conformity

For each note in `01`, `02`, `03`:
1. Read the `type` field from frontmatter
2. Verify the folder matches the expected `type` per the table in step 1
3. If mismatch, flag **directory mismatch** — recommend moving the note or correcting the type

Completion: produce a list of notes whose `type` contradicts their PARA folder.

### 6. Duplicate detection

For each `.md` file in `01`, `02`, `03`:
1. Compare its `title` against all other notes' titles using normalized similarity:
   - Lowercase both, strip punctuation, collapse whitespace
   - If match ratio > 80% (same words in different order counts), flag as **potential duplicate**
2. Also check for notes with identical `description` or very similar body text patterns

Completion: produce a list of note pairs that may be duplicates, with similarity score.

### 7. Restructuring suggestions

Scan `01 - Projects/`:
1. For each project file (not folder/index), count how many `.md` files reference it via `[[wikilink]]` or have it in their `relationships` frontmatter
2. If count >= 5, suggest **promotion to folder** (see promote-project skill)

Scan all PARA notes:
1. Check `status: completed` or `status: archived` in frontmatter
2. Check file modification time via `git log --oneline -1 -- <path>` or `stat`
3. If completed and no edits in 30+ days, suggest **archival** (see archive skill)

Completion: produce a list of promotion candidates and archival candidates.

### 8. Report

Present findings grouped by category. Omit any category with zero findings. Use emoji markers:

```
🔍 **Orphans** (3)
- [[Note A]] — /01 - Projects/note-a.md (0 references)
- [[Note B]] — /02 - Areas/note-b.md (0 references)

🔗 **Dead links** (1)
- /01 - Projects/foo.md → [[nonexistent-note]]

⚠️ **Frontmatter issues** (2)
- /03 - Resources/bar.md: missing `description`
- /01 - Projects/baz.md: `timestamp` not valid ISO datetime

📂 **Directory mismatches** (1)
- /03 - Resources/thing.md has `type: project` — belongs in 01 - Projects/

👯 **Potential duplicates** (1)
- [[Note C]] and [[Note D]] — 85% title similarity

📦 **Promotion candidates** (1)
- [[Note E]] — 7 backlinks, eligible for folder promotion

🗄️ **Archival candidates** (1)
- [[Note F]] — completed, 45 days since last edit
```

### 9. Offer to fix

After presenting the report, offer to address each category:

> I found N issues. Want me to fix any of these categories?
> 1. 🔗 Fix dead links (remove or relocate)
> 2. ⚠️ Patch frontmatter (add missing fields)
> 3. 📂 Move directory mismatches
> 4. 📦 Promote [[Note E]] to folder
> 5. 🗄️ Archive [[Note F]]

Apply each fix the user confirms. For dead links: if the target is clearly a renamed note, replace with the correct wikilink; otherwise remove the `[[...]]` and leave plain text. For frontmatter: infer missing fields from content where possible. For directory mismatches: move the file to the correct folder and update all wikilinks.

### 10. Offer serendipity prompt

After fixes are applied, scan for notes with `timestamp` >= 90 days old (or file `mtime`). If any exist, offer:

> 🕰️ **Serendipity prompt available:** Found N notes not touched in 90+ days. Surface one?

If the user confirms, run the **z-serendipity-prompt** skill (`.agents/skills/z-serendipity-prompt/SKILL.md`).

## Rules

- Never modify files in `.agents/`, `.claude/`, `.scratch/`, `assets/`, or `docs/` during auto-maintenance
- Never touch daily notes in `00 - Inbox/` during linting (that's the daily review's domain)
- Present before fixing — always show the report first, then ask before applying changes
- Skip `.gitkeep` files and any file matching `_projects.md|_areas.md|_resources.md|_archive.md|tasks.md` in lint checks (these are structurally "supposed to" reference other notes and follow their own conventions)
