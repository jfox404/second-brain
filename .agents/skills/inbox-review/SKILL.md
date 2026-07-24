---
name: inbox-review
description: Process raw artifacts in /00 - Inbox/ that aren't daily notes. Read the file, propose a PARA destination, confirm with user, then extract content and archive. Use when the user has raw materials (transcripts, PDF notes, large reference docs) sitting in inbox, or when daily review flags unprocessed inbox artifacts.
argument-hint: "[filename]"
---

# Inbox Review

Process a raw artifact in `/00 - Inbox/` that is not a daily note (does not match `Daily Note - YYYY-MM-DD.md`).

The skill uses a proposal-review-execute pattern: a sub-agent reads the inbox file and drafts a proposal; the parent agent presents it to the user for approval; then executes.

## Trigger

Two modes:

1. **Manual**: Run `/inbox-review filename.md` to process a specific file.
2. **Daily review**: The daily review skill (step 9) lists unprocessed inbox artifacts and suggests running `/inbox-review`. When invoked that way, the named file is already known.

If no filename is provided, list all non-daily files in `/00 - Inbox/` and let the user pick one via question.

## Process

### 1. Read the inbox file

Read the full content of the target file at `/00 - Inbox/<filename>`.

### 2. Spawn proposal sub-agent

Launch a sub-agent with this brief:

> You are reviewing an inbox artifact at `/00 - Inbox/<filename>`.
>
> Read the file. Determine:
> 1. What type of content is this? (reference material, project notes, meeting transcript, area-related, etc.)
> 2. Which PARA bucket does it belong to? (`#project/<slug>`, `#area/<slug>`, or `#resource/<slug>`)
> 3. What is the best destination path and filename in the vault?
>    - For projects: `/01 - Projects/<slug>.md` or `/01 - Projects/<slug>/index.md`
>    - For areas: `/02 - Areas/<slug>.md`
>    - For resources: `/03 - Resources/<slug>.md`
> 4. Does the destination note already exist? If so, what new content from the inbox file should be merged in?
> 5. If the destination does not exist, what should the new note contain? (proposed frontmatter and body)
> 6. What content from the inbox file is noise and should be discarded?
>
> Return a concise proposal with the following structure:
>
> ```
> ## Proposal
>
> **Destination:** `/path/to/destination.md`
> **Action:** create | merge
> **Summary:** 1-2 sentence description of what the inbox file contains
>
> ### Content to extract
>
> - key point or section to include
> - another point
>
> ### If creating a new note
>
> Proposed frontmatter and body.
>
> ### If merging into existing note
>
> What sections/content to add.
>
> ## Questions for the user
>
> - Any specific questions about the proposal?
> ```

### 3. Present proposal to user

Use the question tool to ask the user if they approve:

- **Approve** — proceed to execute
- **Edit** — let the user specify changes
- **Skip** — leave the inbox file as-is

If the user wants edits, incorporate the feedback and re-present.

### 4. Execute

If approved:

1. **Create or update the destination note** — write extracted content to the PARA note.
2. **Archive the inbox file** — move `/00 - Inbox/<filename>` to `/04 - Archive/<filename>`.
3. **If creating a new note**, also update the relevant index note (`_projects.md`, `_areas.md`, or `_resources.md`) and `tasks.md` if action items were extracted.

If the inbox file contained action items (`- [ ]` or `[action:]`), extract them to the destination note's `## Tasks` section and to `tasks.md`.

### 5. Report

Summarize what was done:

- **Source:** inbox file name
- **Destination:** note path
- **Action:** created / merged
- **Archive:** path to archived file
- **Extractions:** count of action items, key decisions, notes promoted

## Archive path rules

| Inbox file type | Archive destination |
|---|---|
| `Daily Note - YYYY-MM-DD.md` | `/04 - Archive/daily/Daily Note - YYYY-MM-DD.md` |
| Any other file | `/04 - Archive/<filename>` |
