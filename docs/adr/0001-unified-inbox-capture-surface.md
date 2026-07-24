# 0001 — Unified inbox capture surface

**Status:** accepted

Daily notes (`Daily Note - YYYY-MM-DD.md`) and raw artifacts (transcripts, PDF notes, long-form reference docs) share `/00 - Inbox/` as a single staging area, replacing the old `/00 - Daily/` folder. Daily notes are processed by daily review (enriching individual capture bullets); raw artifacts are processed by inbox review (proposal sub-agent → user approval → extraction to PARA → archive). Both graduate out of inbox on their own timeline — daily notes immediately, artifacts when the user has capacity.

This exists at the boundary between "capture" and "organize" — raw inputs land in one place, then split by size and urgency into two different review pipelines. The alternative was keeping `/00 - Daily/` for captures and scattering large artifacts into PARA folders immediately (premature filing) or dumping them in a second folder (fragmented staging). One inbox keeps the capture ritual simple: everything goes to the same place.
