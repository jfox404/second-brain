# 07 — Connection surfacing

**What to build:** Three complementary patterns for proactive rediscovery of old knowledge — capture-time recall, weekly weave, and serendipity prompt.

**Capture-time recall:** When a new capture enters the system (via `sb` CLI or manual daily note edit), the daily review checks for related old notes in the vault. Surfaces up to 3 related notes inline with the capture review, showing title, description, and a one-sentence "why this relates." If the user confirms a link, a `[[wikilink]]` is added to both the capture and the old note.

**Weekly weave:** A weekly pass (invoked manually or suggested by the self-improvement loop after 7 days of captures) that:
  - Reads all captures from the past 7 days
  - Groups them by theme
  - Writes a digest note at `03 - Resources/_digests/weekly-YYYY-MM-DD.md` with:
    - A brief overview of the week's themes
    - Each theme with links to its source captures and relevant existing notes
    - Highlights cross-theme connections
  - Updates the captures with `[[wikilinks]]` to the digest

**Serendipity prompt:** On an irregular cadence (triggered by the self-improvement loop or on user request), AI selects a note not touched in N months (default: 90 days). Presents it to the user: "You haven't looked at [[note]] since [date]. Here's what it's about: [one sentence]. Worth a refresh?" User actions: open, link to current work, mark for archival, dismiss.

**Blocked by:** 05 (auto-maintenance linking infrastructure)

**Status:** ready-for-agent
