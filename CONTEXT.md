# Second Brain

A personal knowledge system for turning scattered inputs into reusable thinking. Local-first, markdown-based, AI-assisted.

## Language

**Capture**:
A single thought or piece of information saved to the system. Captures live in daily notes until processed.
_Avoid_: Note, snippet, bookmark

**Daily note**:
A markdown file at `/00 - Inbox/Daily Note - YYYY-MM-DD.md` that serves as the default capture surface. Each capture is a timestamped bullet line.
_Avoid_: Journal, log

**Daily review**:
A manual AI-assisted pass over unprocessed captures in today's daily note. Enriches them in-place with tags, wikilinks, and action markers.
_Avoid_: Processing, grooming

**Capture trigger**:
The mechanism that writes a thought to the daily note — a CLI command (`sb "..."`) or a VS Code Foam wikilink.
_Avoid_: Input method, capture tool

**Inbox**:
The `/00 - Inbox/` folder — a unified staging area for both daily notes and raw artifacts. Sits before the PARA system in the workflow. Content enters here, gets processed (daily review or inbox review), then graduates to its PARA home or archive.
_Avoid_: Dump, backlog

**Inbox artifact**:
A file in `/00 - Inbox/` that is not a daily note. A meeting transcript, PDF notes, a long-form reference doc — too large for a single capture bullet. Processed via inbox review, which extracts signal into the appropriate PARA note and archives the original.
_Avoid_: Attachment, document, blob

**Inbox review**:
The process of examining an inbox artifact, extracting its signal into the appropriate PARA note, and archiving the original. Uses a proposal sub-agent to draft the destination and content, then presents it to the user for approval before executing. One artifact at a time.
_Avoid_: Bulk processing, filing

**Archival**:
Moving a completed/stale project to `/04 - Archive/`, or moving a processed inbox item out of `/00 - Inbox/`. The archive skill reviews projects for salvageable content before moving. Inbox artifacts archive flat to `/04 - Archive/` root; processed daily notes go to `/04 - Archive/daily/`.
_Avoid_: Deletion, cleanup

**PARA**:
The four note types — **P**roject (time-bound outcome, can archive), **A**rea (ongoing responsibility, never archived), **R**esource (reference/evergreen material), **A**rchive (completed/stale projects). Implemented as OKF `type` field in frontmatter, not as a rigid folder hierarchy (though folders happen to match).
_Avoid_: Folders as the source of truth (type is the frontmatter field; folders are for navigation)

**Project note**:
Markdown file or folder under `/01 - Projects/` representing a time-bound outcome. Can contain sections: overview, tasks, goals, open loops, decisions, related notes, stakeholders. AI selects which sections to include on creation.
_Avoid_: Just "project"

**Area note**:
Markdown file or folder under `/02 - Areas/` representing an ongoing responsibility (health, career, finances, etc.). Same project structure but no goals section, and never archived.
_Avoid_: Category, bucket

**URL capture**:
A capture containing a URL. The daily review fetches the linked content and synthesizes it into a promoted resource note.
_Avoid_: Link, bookmark

**Action item**:
A capture tagged with `[action:]` that represents something to do. Lives in the daily note but is compiled into project task sections and a central `tasks.md`.
_Avoid_: Task, todo, to-do

**Promotion**:
When the daily review extracts a capture from a daily note into its own standalone file under the appropriate type folder. The AI decides when a capture is durable, complex, or novel enough to promote. For URL captures, the AI fetches the linked content and synthesizes the promoted note.
_Avoid_: Extraction, filing

**OKF frontmatter**:
YAML frontmatter on every promoted note following the Open Knowledge Format conventions — `title`, `type`, `description`, `tags`, `relationships`, `timestamp`. Daily notes are lightweight.
_Avoid_: Metadata

**Vault**:
The entire repository: all markdown files, assets, and configuration. Git-tracked, local-first.
_Avoid_: Workspace, knowledge base, wiki
