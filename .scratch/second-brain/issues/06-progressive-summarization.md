# 06 — Progressive summarization

**What to build:** A cadence-based summarization pipeline that prevents notes from being stale blobs of raw text. AI applies layers of summarization at configurable intervals.

**Layer 1 (at capture/promotion time):** AI extracts key claims, named entities, and terminology from the note. Writes a 1-2 sentence `description` in the frontmatter. Tags with PARA type.

**Layer 2 (at ~1 week):** AI re-reads the note and writes a one-paragraph synthesis of its core argument. Appends a `## Summary` section if the note doesn't have one. Checks if the content relates to other notes captured in the same week.

**Layer 3 (at ~1 month):** AI re-reads the note plus any notes linked to it. Writes a broader synthesis that positions the note within the vault's graph. Suggests new `[[wikilinks]]` to otherwise-unrelated notes.

**Mechanics:**
- A frontmatter field `summarized: <layer>` tracks which layer has been applied
- The daily review or a standalone pass checks which notes are due for the next layer
- User can skip/reschedule per note via a `summarize: skip` frontmatter flag
- Cadence is configurable (default: layer 2 at 7 days, layer 3 at 30 days)

**Blocked by:** 05 (auto-maintenance linting pass provides the pass infrastructure)

**Status:** ready-for-agent
