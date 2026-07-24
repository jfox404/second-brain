Status: ready-for-agent

# Second Brain — Personal Knowledge System

## Problem Statement

Previous attempts at personal knowledge management systems failed due to two main reasons: (1) capture backlog paralysis — unprocessed captures accumulated faster than they could be reviewed, creating guilt and abandonment, and (2) poor retrieval — saved information was hard to find later, making the effort of saving feel pointless.

The system needs to be local-first, markdown-based, AI-assisted, and require minimal maintenance.

## Solution

A VS Code + Foam-based second brain vault with three layers: (1) a frictionless capture surface (CLI + daily notes), (2) a daily review that enriches captures in-place, and (3) a lightweight PARA-based organization layer enforced by the AI, not by manual filing.

## User Stories

1. As a user, I want to run `sb "thought"` from the terminal, so that the thought is appended to today's daily note with a timestamp.
2. As a user, I want to create a daily note automatically when it doesn't exist, so that I never have to think about file creation.
3. As a user, I want to open a daily note from VS Code via Foam's daily note feature, so that I can capture or review without leaving my editor.
4. As a user, I want to create Foam wikilinks from within a daily note to link to related notes, so that connections grow naturally.
5. As a user, I want to invoke a daily review skill that processes unprocessed captures in today's daily note, so that I can enrich my notes without manual work.
6. As a user, I want the daily review skill to suggest wikilinks between my captures and existing vault notes, so that the graph becomes more connected.
7. As a user, I want the daily review skill to assign PARA type tags (`#project/x`, `#area/x`, `#resource/x`, `#archive/x`) to captures, so that they're categorized without manual decisions.
8. As a user, I want the daily review skill to extract action items marked `[action:]` from my captures and compile them into project task sections and a central `tasks.md`, so that I can plan from both per-project and aggregate views.
9. As a user, I want the daily review skill to promote noteworthy captures to standalone files under the appropriate type folder, so that durable ideas have their own permanent home.
10. As a user, I want the daily review skill, when it encounters a URL in a capture, to fetch the linked content and synthesize a promoted resource note, so that article/book highlights are captured without extra effort.
11. As a user, I want the daily review skill to mark processed captures with a ✅ prefix so that I can see what's been reviewed at a glance.
12. As a user, I want promoted notes to have OKF-compliant YAML frontmatter (`title`, `type`, `description`, `tags`, `relationships`, `timestamp`), so that AI agents can parse my vault.
13. As a user, I want to search my vault via VS Code's full-text search and find any note, so that retrieval is fast.
14. As a user, I want to browse the Foam graph to discover connections between notes, so that serendipitous discovery happens naturally.
15. As a user, I want vault notes organized under `/00 - Daily/`, `/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, and `/04 - Archive/`, so that the file tree is navigable.
16. As a user, I want a `tasks.md` at vault root that aggregates all action items from across the vault, grouped by project, so that I have a single planning surface.
17. As a user, I want the daily review skill to maintain a `## Tasks` section inside each project note, so that project-level task visibility is preserved.
18. As a user, I want the `sb` CLI to be installable from a single command, so that setup is trivial.
19. As a user, I want a skill to create a project or area note, so that I can establish structure for a new initiative.
20. As a user, I want the project creation skill to ask which sections to include (overview, tasks, goals, open loops, decisions, related notes, stakeholders), so that each project note is tailored to its needs.
21. As a user, I want the project creation skill to accept arguments so it can be invoked non-interactively (e.g., by the daily review), so that AI can create projects without blocking on input.
22. As a user, I want the daily review to flag captures tagged with a new `#project/x` that has no corresponding project note, and suggest creating one, so that project discovery is automatic.
23. As a user, I want project notes to have YAML frontmatter with `status` (active/paused/completed/archived), `started`, and `target`, so that lifecycle metadata is agent-readable.
24. As a user, I want a project to start as a single file and be upgradable to a folder when it accumulates artifacts, so that simple projects stay simple and complex ones can grow.
25. As a user, I want a skill that promotes a single-file project to a folder structure, so that the upgrade path is clean.
26. As a user, I want AI to suggest upgrading a project to a folder when enough promoted notes accumulate, so that organization scales naturally.
27. As a user, I want a skill to archive a project that moves it to `/04 - Archive/`, so that completed work is separated from active projects.
28. As a user, I want the archive skill to review the project for content worth promoting to a resource note before archiving, so that valuable knowledge isn't lost.
29. As a user, I want AI to suggest archiving projects with no recent activity, so that stale projects don't clutter active views.
30. As a user, I want area notes to follow the same pattern as projects but without a goals section, and they are never archived, so that ongoing responsibilities have a permanent home.
31. As a user, I want resource notes to be creatable both by the AI daily review (from URL captures) and manually via a skill/template, so that all reference material has a consistent structure.
32. As a user, I want resource notes to have AI-determined sections based on source type (article, book, video, concept), so that structure fits the content.
33. As a user, I want resources to be flat files by default and upgradeable to folders (like projects), so that complex topics can grow.
34. As a user, I want the AI to suggest archiving outdated resources, so that stale reference material doesn't accumulate.
35. As a user, I want daily notes to have YAML frontmatter with `date`, so that the date is machine-readable.
36. As a user, I want daily notes to have sections: `## Today's Focus`, `## Captures`, and `## Meetings`, so that the note has structure without being rigid.
37. As a user, I want meeting notes to start as content in the daily note's `## Meetings` section and be promoted to standalone files by the daily review if substantive, so that capture stays fast.
38. As a user, I want the `sb` CLI to support flags: `--open` (open daily note), `--editor` (multi-line input), `--type meeting` (adds to ## Meetings), `--project x` (pre-tags), `--tag foo,bar` (custom tags), `--no-timestamp`, so that the CLI covers common scenarios.
39. As a user, I want `tasks.md` to use markdown checkboxes (`- [ ]`), `[[wikilinks]]` back to source notes, and be grouped by project heading, so that I can track and navigate tasks.
40. As a user, I want auto-generated index notes (`_projects.md`, `_areas.md`, `_resources.md`, `_archive.md`) listing all notes of each type with status, updated during review/archive passes, so that I have navigable overviews.
41. As a user, I want when a note upgrades to a folder, its `index.md` to serve as that folder's MOC listing sub-notes, so that folder-level navigation is automatic.

## Implementation Decisions

- **Capture path**: `sb "thought"` delegates to `foam daily --create --path-only` for note creation, then appends `- [HH:MM] thought` to `/00 - Daily/YYYY-MM-DD.md` under `## Captures`.
- **CLI flags**: `--open` (opens daily note in $EDITOR), `--editor` (opens $EDITOR for multi-line), `--type meeting` (appends to ## Meetings), `--project x` (pre-tags `#project/x`), `--tag foo,bar` (custom tags), `--no-timestamp`, `--help`. Implemented as a bash script.
- **Daily note template**: Frontmatter with `date: YYYY-MM-DD`. Sections: `## Today's Focus`, `## Captures`, `## Meetings`. Created on first capture of the day.
- **Meeting notes**: Start in the daily note's `## Meetings` section. The daily review promotes substantive meetings to standalone files.
- **Review marker**: Processed captures get a `✅` prefix: `- ✅ [HH:MM] thought`. The daily review only processes captures without the marker.
- **Daily review**: A skill file at `.agents/skills/daily-review/SKILL.md`. Invoked manually by the user. Uses AI (GitHub Copilot) to process unprocessed captures.
- **Review actions**: For each unprocessed capture, the AI: (1) adds PARA type tag, (2) suggests `[[wikilinks]]` to existing notes, (3) extracts `[action:]` items, (4) promotes URL captures by fetching and synthesizing, (5) marks with ✅.
- **Task aggregation**: The daily review maintains `## Tasks` sections in project notes and a root `tasks.md` that aggregates all actions with `[[wikilinks]]` back to source notes. `tasks.md` format: checkboxes `- [ ]`, grouped by `## Project: Name`.
- **Index notes**: `_projects.md`, `_areas.md`, `_resources.md`, `_archive.md` at vault root. Simple link lists with status badges. Updated at the end of each daily review; `_archive.md` is updated by the archive skill.
- **Folder index.md**: When a note upgrades to a folder, `index.md` serves as the MOC for that folder, listing sub-notes. Maintained by the daily review.
- **Vault structure**: `/00 - Daily/`, `/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, `/04 - Archive/`, `tasks.md`, `_projects.md`, `_areas.md`, `_resources.md`, `_archive.md` (root). Assets in `/assets/`.
- **Frontmatter**: Promoted notes get OKF-compliant YAML frontmatter: `title`, `type`, `description`, `tags`, `relationships`, `timestamp`.
- **Resource notes**: Created by the daily review (from URL captures) or manually via skill/template. AI determines sections based on source type (article, book, video, concept). Flat files by default, upgradeable to folders. AI suggests archiving outdated resources.
- **Vault structure**: `/00 - Daily/`, `/01 - Projects/`, `/02 - Areas/`, `/03 - Resources/`, `/04 - Archive/`, `tasks.md` (root). Assets in `/assets/`.
- **Frontmatter**: Promoted notes get OKF-compliant YAML frontmatter: `title`, `type`, `description`, `tags`, `relationships`, `timestamp`.
- **Foam config**: Daily notes at `/00 - Daily/YYYY-MM-DD.md`. Wikilinks enabled. Graph view configured to color by `type` frontmatter field.
- **Project creation skill**: A skill at `.agents/skills/create-project/SKILL.md`. Supports wizard mode (ask which sections to include) and argument mode for non-interactive use. Available sections: overview, tasks, goals, open loops, decisions, related notes, stakeholders.
- **Project note lifecycle**: Frontmatter has `status` (active/paused/completed/archived), `started`, `target`. Projects start as single files under `/01 - Projects/<slug>.md`. When promoted notes accumulate, AI or user invokes a promote-to-folder skill.
- **Promote-to-folder skill**: A skill at `.agents/skills/promote-project/SKILL.md`. Converts a project file to `projects/<slug>/index.md`, moves associated notes into the folder using `foam note move` (which auto-rewrites all wikilinks).
- **Archive skill**: A skill at `.agents/skills/archive-project/SKILL.md`. Moves project to `/04 - Archive/`. Before archiving, reviews project content and proposes resource note promotion for salvageable knowledge. AI suggests archiving for projects with prolonged inactivity.
- **Area notes**: Same structure as projects but no goals section. Never archived. Under `/02 - Areas/`.
- **Git**: Local-only, no remote configured. No auto-commit or sync.

## Testing Decisions

No formal testing. The user will validate the system by using it.

## Out of Scope

- Mobile capture (no app or mobile CLI)
- Cloud sync or remote backup
- Voice note capture
- PDF/image annotation
- Integration with external task systems (Todoist, Linear, etc.)
- Multi-user or shared vault
- Automated daily review scheduling
- Web UI or dashboard

## Further Notes

Setup prerequisite: VS Code with Foam extension, `foam-cli` installed globally (`npm install -g foam-cli`). The `sb` CLI is a thin bash wrapper around `foam daily`. The daily review skill assumes GitHub Copilot is available in VS Code.
