---
name: daily-review
description: Enrich unprocessed captures for one daily note date at a time with tags, links, and actions; maintain tasks.md, project task sections, and index notes.
disable-model-invocation: true
argument-hint: "Run daily review for a single date (YYYY-MM-DD). If omitted, use today."
---

# Daily Review

See [shared patterns](../_shared/patterns.md) for index note conventions and OKF frontmatter.

Process unprocessed captures in exactly one daily note under `/00 - Inbox/Daily Note - YYYY-MM-DD.md` per run.

A **capture** is a bullet line in the `## Captures` section starting with `- [HH:MM]`. An **unprocessed** capture is one without the `✅` prefix (`- ✅ [HH:MM]`).

## Review scope

Resolve exactly one target daily note before starting:

1. If the user specifies a date argument (`YYYY-MM-DD`), use that date.
2. Otherwise, use today's date.
3. Process only that single note for this run.

If the target note does not exist, report it missing and stop.
If the target note exists but has no unprocessed captures/meetings to act on, report nothing to process and stop.

## Process

### 1. Identify target daily note

Resolve the target file as `/00 - Inbox/Daily Note - YYYY-MM-DD.md` for the selected date.

If the file is missing, report the missing date and stop.

### 2. Find unprocessed captures

In the target daily note, in the `## Captures` section find every bullet matching `- [HH:MM]` (raw captures) but NOT `- ✅ [HH:MM]` (already processed).

### 3. Enrich each capture

For every unprocessed capture in the target daily note, perform these steps **in order**:

#### a. Assign a PARA type tag

Choose one of `#project/<slug>`, `#area/<slug>`, or `#resource/<slug>` based on the capture content. Use an existing slug if one matches; invent a new one if needed.

- `#project/x` — time-bound outcome (build, write, ship, achieve by a date)
- `#area/x` — ongoing responsibility (health, career, finances, relationships, learning)
- `#resource/x` — reference material (article, book, video, concept, tool)

#### b. Suggest [[wikilinks]]

Search the vault for related notes. Add one or more `[[wikilinks]]` inline after the tag. Prefer existing notes; do not create new ones unless the link is essential.

#### c. Extract action items

If the capture contains `[action:]`, extract the action text. Each action item will later be compiled into `## Tasks` sections and `tasks.md`.

Formats recognized:
- `[action: do something]`
- `[action: do something by friday]`

#### d. Promote URL captures

If the capture contains a URL (http/https), fetch the linked content and synthesize a promoted resource note:

1. Fetch the content at the URL
2. Create a file at `/03 - Resources/<slug>.md` with OKF frontmatter:
   ```yaml
   ---
   title: <title from page or capture>
   type: resource
   description: <1-2 sentence summary>
   tags: [<relevant tags>]
   relationships:
     source: <URL>
   timestamp: <current ISO datetime>
   ---
   ```
3. Write a synthesis of the content in the body — key claims, quotes, structure — in your own words
4. Replace the URL in the capture with `[[<slug>]]`

#### e. Mark as processed

Change the capture prefix from `- ` to `- ✅ `.

### 4. Process meeting notes

In the `## Meetings` section of the target daily note, find every `### <Title> [[project-wikilink]]` entry.

For each meeting entry:

#### a. Parse structure

Identify these subsections by their bold headers:

- **Attendees:** — comma-separated names (one line)
- **Notes:** — bullet list following the header
- **Decisions:** — bullet list following the header
- **Actions:** — checkbox list (`- [ ]`) following the header

#### b. Identify project

Extract the project slug from the wikilink in the meeting title: `[[slug]]`. This determines the target project at `/01 - Projects/<slug>.md` (or `<slug>/index.md`).

#### c. Assess substance for promotion

Evaluate whether the meeting is substantive enough to promote to a standalone file. Consider:
- Has more than just a title (has notes, decisions, or actions)
- Contains decisions or action items
- Has multiple bullet points of content

If not substantive, leave the entry in place but still extract actions and decisions.

If substantive, promote (step d).

#### d. Promote to standalone file

Create a meeting note at `/01 - Projects/<slug>/YYYY-MM-DD-title-slug.md` with content:

```markdown
---
title: <Meeting Title>
type: meeting
project: <slug>
date: YYYY-MM-DD
attendees: [names]
tags: [project/<slug>]
relationships:
  source: [[YYYY-MM-DD]] (daily note for this meeting)
timestamp: <current ISO datetime>
---

**Attendees:** <names>

## Notes

- <bullets>

## Decisions

- <bullets>

## Actions

- [ ] <items>
```

Then replace the original entry in `## Meetings` with a wikilink:

```markdown
- [[YYYY-MM-DD-title-slug]]
```

#### e. Extract decisions to project

Add each decision bullet to the project note's `## Decisions` section (create if missing). Format as:

```markdown
- <decision> [[YYYY-MM-DD-title-slug]]
```

#### f. Extract relevant notes to project

Add a bullet linking to the meeting file under the project note's `## Meetings` section (create if missing):

```markdown
- YYYY-MM-DD — [[YYYY-MM-DD-title-slug]]
```

If the meeting was NOT promoted (too thin), instead add key notes directly to the project note's `## Notes` section as bullets with `[[wikilink to daily note]]` suffix.

#### g. Extract action items

Extract every `- [ ] <action text>` from the meeting's **Actions** section. Feed these into step 5 (Maintain project task sections) and step 6 (Update root tasks.md), using the project identified in step b.

### 5. Maintain project task sections

For every action item — both `[action:]` from captures (step 3c) and `- [ ]` from meeting **Actions** sections (step 4g) — determine the target project from context, the `#project/<slug>` tag, or the meeting's project wikilink.

For each project note at `/01 - Projects/<slug>.md` (or `01 - Projects/<slug>/index.md`):

- If the file does not exist, **flag it** (see step 8)
- If it exists, add or update its `## Tasks` section:
  - Add each action item as `- [ ] <action text> [[wikilink to source daily note]]`
  - Preserve any existing task items that are not yet checked off

### 6. Update root tasks.md

Create or update `/tasks.md` at vault root. Format:

```markdown
# Tasks

## Project: <Name>

- [ ] <action text> [[wikilink to source note]]
- [ ] <action text> [[wikilink to source note]]

## Project: <Name>

- [ ] <action text> [[wikilink to source note]]
```

- Group by project heading (`## Project: Name`)
- If an action item has no valid associated project note (missing, ambiguous, or unresolved project context), place it under `## Ad Hoc Requests` instead of creating a placeholder project heading
- Each task is `- [ ] description [[link]]`
- Preserve any existing unchecked tasks that were not part of this review pass
- Remove tasks that have been completed (`- [x]`) if the source capture has also changed

Use this additional section when needed:

```markdown
## Ad Hoc Requests

- [ ] <action text> [[wikilink to source note]]
```

### 7. Update index notes

Create or update these three index notes at vault root:

**`_projects.md`**
```markdown
# Projects

- [[Project Name]] — _Short description_ `status: active`
- [[Project Name]] — _Short description_ `status: paused`
```

**`_areas.md`**
```markdown
# Areas

- [[Area Name]] — _Short description_
```

**`_resources.md`**
```markdown
# Resources

- [[Resource Name]] — _Short description_
```

Scan each directory (`/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`) for `.md` files and subfolder index notes. Include every note found. Use the `status` from YAML frontmatter for projects. Prefer existing index notes — update them rather than replace them.

### 8. Flag missing project notes

If any capture was tagged `#project/<slug>` and there is no corresponding note at `/01 - Projects/<slug>.md` or `/01 - Projects/<slug>/index.md`, report this to the user:

> ⚠️ **Missing project note:** Captures tagged `#project/<slug>` but no project note exists. Create one with `/create-project`?

Collect all such flags and present them together at the end of the daily review. Also flag any meeting wikilink `[[slug]]` that points to a non-existent project note.

### 9. Scan inbox for unprocessed artifacts

After processing the target daily note, scan `/00 - Inbox/` for files that do NOT match the `Daily Note - YYYY-MM-DD.md` pattern (i.e., raw inbox artifacts).

For each such file, if it has been sitting unprocessed for more than 30 days (check file modification time), flag it prominently:

> ⚠️ **Stale inbox artifact:** `filename.md` has been in `/00 - Inbox/` since `<date>`. Process with `/inbox-review` or archive manually.

Also list any non-stale unprocessed inbox files for awareness:

> 📥 **Unprocessed inbox artifacts:** `filename.md` — use `/inbox-review` to process.

### 10. Summary

After processing the target daily note, provide a brief summary:

- **Daily note reviewed:** `YYYY-MM-DD`
- **Files modified:** list of files changed
- **Captures enriched:** count of captures processed
- **Meetings processed:** count of meetings processed
- **Actions extracted:** count of action items
- **Projects updated:** list of project notes with task/decision/meeting updates
- **Meetings promoted:** list of new standalone meeting files
- **Resources created:** list of new resource notes
- **Flags:** any missing project notes
- **Next step:** if another day needs review, rerun `/daily-review YYYY-MM-DD` for that specific date

### 11. Reflect

After completing the summary, run the **reflect skill** (`.agents/skills/reflect/SKILL.md`). It evaluates whether any user corrections, non-obvious procedures, or durable preferences were discovered during this run and routes findings to the user model or skill patches. If nothing notable, it reports "Reflection: nothing to persist."

## File naming

- Daily note: `/00 - Inbox/Daily Note - YYYY-MM-DD.md`
- Project: `/01 - Projects/<slug>.md` or `/01 - Projects/<slug>/index.md`
- Meeting: `/01 - Projects/<slug>/YYYY-MM-DD-title-slug.md`
- Area: `/02 - Areas/<slug>.md`
- Resource: `/03 - Resources/<slug>.md`
- Index: `_projects.md`, `_areas.md`, `_resources.md` (vault root)
- Tasks: `tasks.md` (vault root)

### Frontmatter patterns

**Daily note** (lightweight):
```yaml
---
date: YYYY-MM-DD
---
```

**Project/area/resource/meeting note** (OKF):
```yaml
---
title: <Title>
type: project|area|resource|meeting
description: <1-2 sentence summary>
tags: [tag1, tag2]
relationships:
  <relationship-type>: <target>
timestamp: <ISO datetime>
---
```
Projects also have `status`, `started`, `target` in frontmatter. Meetings also have `date`, `project`, `attendees` in frontmatter.
