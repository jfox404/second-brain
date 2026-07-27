---
name: z-weekly-weave
description: Create a weekly digest that weaves the past 7 days of captures into themes and cross-connections. Triggered by daily review once per week.
disable-model-invocation: true
---

# Weekly Weave

Create a digest note that connects the past week's captures into themes, highlights cross-connections, and links to relevant existing notes.

## Trigger

- Daily review step detects no digest exists for the current ISO week and suggests running the weave
- User says "run weekly weave", "weekly digest", "weave the week"

## Procedure

### 1. Check if digest already exists

Compute the current ISO week string: `weekly-YYYY-MM-DD` where the date is the Monday of the current week (use `date -I` or ISO week calculation).

Check if `03 - Resources/_digests/weekly-YYYY-MM-DD.md` exists. If it does, report "Weekly weave already exists for this week" and stop.

### 2. Collect captures from the past 7 days

Scan daily notes in `/00 - Inbox/` from the past 7 days (current date back 7 days). For each daily note:
1. Read all lines under `## 📓 Captures` (both processed `✅` and unprocessed)
2. Read all promoted meeting notes referenced from the daily note
3. Collect any action items extracted from those days

### 3. Group by theme

Analyze all collected captures and group them into 2-5 themes. Each theme should represent a coherent topic across multiple captures or days. Heuristics:
- Same `#project/x` or `#area/x` tag across multiple captures
- Repeated keywords or named entities
- Captures that reference the same existing note via `[[wikilink]]`

### 4. Create digest note

Create file `03 - Resources/_digests/weekly-YYYY-MM-DD.md`:

```markdown
---
title: Weekly Weave — YYYY-MM-DD
type: resource
description: Digest of captures from the week of YYYY-MM-DD, grouped by theme with cross-connections.
tags: [digest, weekly]
timestamp: <current ISO datetime>
---

# Weekly Weave — Week of YYYY-MM-DD

## Overview

<2-3 sentence overview of the week's themes and any notable patterns>

## Theme: <Name>

<1-2 paragraph synthesis of captures in this theme>

**Captures:**
- <date> — <capture summary> [[Daily Note - YYYY-MM-DD]]
- <date> — <capture summary> [[Daily Note - YYYY-MM-DD]]

**Related notes:** [[Note A]], [[Note B]]

## Theme: <Name>

...

## Cross-Theme Connections

<What connects across themes? Did a project capture relate to a resource note? Did an area note inform a project decision?>

## Action Items Carried Forward

<Any action items from the week that remain open, grouped by project>

- [ ] <action> [[source]]
```

### 5. Update captures with digest links

For each daily note that contributed captures to the digest, add a line at the top of its `## 📓 Captures` section:

```markdown
_Captures from this week are woven into [[weekly-YYYY-MM-DD]]._
```

### 6. Report

Report the digest was created with the number of themes and captures covered.
