---
name: z-serendipity-prompt
description: Surface a forgotten note not touched in 90+ days and offer to re-read, link, archive, or dismiss. Triggered by auto-maintenance or user request.
disable-model-invocation: true
---

# Serendipity Prompt

Surface a forgotten note — one not touched in 90+ days — and offer the user a lightweight interaction to reconnect with it.

## Trigger

- Auto-maintenance step detects overdue notes and offers to run a serendipity prompt
- User says "show me something I forgot", "serendipity prompt", "surface a forgotten note"
- Self-improvement loop triggers after major operation completion

## Procedure

### 1. Find candidates

Scan all `.md` files in `01 - Projects/`, `02 - Areas/`, `03 - Resources/`. For each note:
1. Read `timestamp` from frontmatter (fall back to file modification time via `git log --oneline -1` or `stat`)
2. Calculate days since that timestamp
3. Filter for notes where days >= 90 (configurable threshold)
4. Exclude: digest notes (`_digests/`), index notes, and notes where user has interacted recently (recent commits touching that file)

Sort candidates by days-since-timestamp descending (stalest first). Pick the top candidate that hasn't been shown before in this session (avoid repeating).

### 2. Prepare prompt

Read the candidate note. Compose:

> 🕰️ **Forgotten note:** [[Note Title]]
> Last touched: <date> (N days ago)
> Description: <1-2 sentence description from frontmatter or body>
> Worth a refresh?

### 3. Offer actions

Present options for the user to choose:

1. **Open** — open the note for reading
2. **Link to current work** — add a `[[wikilink]]` from this note to whatever the user is currently capturing or reviewing
3. **Mark for archival** — set `status: ready-for-archive` in frontmatter
4. **Dismiss** — mark with `serendipity: dismissed YYYY-MM-DD` in frontmatter so it won't be surfaced again for another 90 days
5. **Skip this one** — pick the next candidate

### 4. Apply user's choice

Do what the user selected. If they dismissed it, add the frontmatter field. If they linked, add the link. If they archived, defer to the archive skill or set the flag.

### 5. Repeat or stop

After handling, ask if the user wants another serendipity prompt. Continue until they decline or no more candidates exist.

## Config

The staleness threshold (default 90 days) can be overridden:
- `serendipity-threshold: <days>` in note frontmatter for per-note control
- Pass `--days N` when invoking manually
