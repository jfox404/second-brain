# 05 — Auto-maintenance

**What to build:** An auto-maintenance capability embedded in the daily review and as a standalone linting pass that handles the four taxes of vault upkeep: tagging, linking, cleaning, and restructuring.

- **Tagging**: During daily review, AI assigns PARA type tags (`#project/x`, `#area/x`, `#resource/x`) to each capture automatically based on content analysis. No manual categorization.
- **Linking**: During daily review, AI scans the vault for notes related to each capture and suggests `[[wikilinks]]`. During the linting pass, AI discovers implicit connections between existing notes and suggests new links.
- **Linting**: Standalone pass (invoked on demand or triggered after N new captures) that detects:
  - Orphan notes (zero backlinks, not linked from any index note)
  - Dead wikilinks (links to non-existent files)
  - Notes with missing frontmatter fields
  - Notes in wrong PARA directories (type field doesn't match folder)
  - Duplicate or near-duplicate captures
- **Restructuring suggestions**: Flag captures with accumulated sub-notes for promotion to folder. Flag notes with 0 edits in 30+ days for archival review.

**Integration points:** Extends the daily review skill. New linting pass can be invoked via a CLI flag or as a standalone skill.

**Blocked by:** None

**Status:** done

**Implementation:**
- Created `.agents/skills/z-auto-maintenance/SKILL.md` — standalone linting skill with 9 procedures (scan, orphans, dead links, frontmatter validation, directory conformity, duplicates, restructuring suggestions, report, offer to fix)
- Extended daily-review skill with step 10 — auto-maintenance linting pass (subset: orphan detection, dead link check, frontmatter validation, directory conformity) that runs after inbox scan and before summary
