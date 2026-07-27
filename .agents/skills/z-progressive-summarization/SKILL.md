---
name: z-progressive-summarization
description: Apply layered summarization to vault notes on a cadence — layer 1 at promotion, layer 2 at ~1 week, layer 3 at ~1 month. Prevents notes from being stale blobs of raw text.
disable-model-invocation: true
---

# Progressive Summarization

Apply or advance summarization layers on vault notes. Layer 1 is applied at promotion time (by the daily review or create-project skill). Layers 2 and 3 are applied by a standalone pass invoked manually or on a cadence.

## Trigger

- **Layer 1**: Daily review step 3f applies it at promotion time (when a capture is promoted to a standalone note).
- **Layers 2 & 3**: Daily review step 11 scans all promoted notes and applies any due layers automatically. No separate invocation needed.
- **Manual**: User says "run progressive summarization", "deepen notes", "apply summarization layers" — runs the standalone pass immediately.

## Layer definitions

Each layer is tracked via a `summarized` field in the note's frontmatter:

| Value | Meaning | Applied when |
|---|---|---|
| (absent) | Not yet summarized | Note just created |
| `1` | Layer 1 applied | At promotion time |
| `2` | Layer 2 applied | ~7 days after `timestamp` |
| `3` | Layer 3 applied | ~30 days after `timestamp` |
| `skip` | User opted out | Manual skip per note |
| `max` | No further layers needed | User marked complete |

### Layer 1 — Extract (at promotion time)

Applied by the daily review after promoting a capture to a standalone file. Also applied by create-project skill when it creates a new note.

1. Read the full note content
2. Extract key claims, named entities, and domain terminology
3. Write a 1-2 sentence `description` in the frontmatter (if not already present)
4. Assign PARA type tag matching the `type` field
5. Write `summarized: 1` into frontmatter

### Layer 2 — Synthesize (~7 days)

Standalone pass. Scan all notes in `01 - Projects/`, `02 - Areas/`, `03 - Resources/` for notes where:

- `summarized` is `1` or absent AND
- `timestamp` is >= 7 days old (compare against note's timestamp or file modification time)

For each qualifying note:

1. Re-read the full note content
2. Write a one-paragraph synthesis of its core argument
3. If note has no `## Summary` section, append one at the end of the body with the synthesis
4. If note already has a `## Summary`, update it in-place
5. Scan captures from the same week (compare note's timestamp against daily notes in `00 - Inbox/`) for related content. If found, add a cross-reference line: `> This relates to [[other-note]] captured on YYYY-MM-DD.`
6. Set `summarized: 2` in frontmatter

### Layer 3 — Connect (~30 days)

Standalone pass. Scan all notes in `01 - Projects/`, `02 - Areas/`, `03 - Resources/` for notes where:

- `summarized` is `2` AND
- `timestamp` is >= 30 days old

For each qualifying note:

1. Re-read the note plus any notes it links to via `[[wikilinks]]` or `relationships` frontmatter
2. Write a broader synthesis that positions the note within the vault's graph — what broader topic does it belong to, what claims does it support or contradict, what gaps does it reveal
3. Note: do NOT claim contradictions the vault doesn't actually discuss — only link to what exists
4. Suggest 1-3 new `[[wikilinks]]` to otherwise-unrelated notes that share thematic overlap but are not yet connected
5. Append the broader synthesis under `## Connections` section (create if missing)
6. Set `summarized: 3` in frontmatter

## Procedure: Standalone pass

Run when the user invokes progressive summarization directly.

### 1. Identify notes due for summarization

Scan all `.md` files in `01 - Projects/`, `02 - Areas/`, `03 - Resources/`.

For each note, read frontmatter and determine which layer is due:

- If `summarized` is `skip` or `max`: skip
- If `summarized` is absent or `1` and `timestamp` (or `mtime`) is >= 7 days ago: **due for layer 2**
- If `summarized` is `2` and `timestamp` (or `mtime`) is >= 30 days ago: **due for layer 3`

Present the count to the user:

> Found N notes due for summarization: M for layer 2, P for layer 3.

Proceed without confirmation (or stop if user prefers to review per-note).

### 2. Apply layer 2

For each note due for layer 2, follow the **Layer 2 — Synthesize** procedure above.

### 3. Apply layer 3

For each note due for layer 3, follow the **Layer 3 — Connect** procedure above.

### 4. Report

Summarize what was done:

- **Layer 2 applied:** list of notes
- **Layer 3 applied:** list of notes
- **Skipped:** count of notes with `skip` or `max`
- **New connections suggested:** count of new `[[wikilinks]]` added

## Rules

- Never modify files in `.agents/`, `.claude/`, `.scratch/`, `assets/`, or `docs/`
- Never modify daily notes or inbox artifacts — summarization is for promoted notes only
- Respect `skip` and `max` frontmatter flags — do not re-summarize notes the user opted out of
- Layer 2 and 3 passes are idempotent: running them again on the same note should produce no change (the `summarized` field prevents re-processing)
- When suggesting new `[[wikilinks]]` in layer 3, prefer notes that are genuinely related by topic, not just co-occurring tags — the goal is discovery, not noise
