# Import legacy vault

Status: `done`

## Problem Statement

The user has a previous second-brain vault (VS Code + Foam + PARA, same toolchain) with ~50 notes in OKF-like but non-conformant frontmatter. This vault (the primary vault) is empty of PARA content. The user needs to bring that content over with frontmatter transformation, wikilink preservation, and tag reconciliation — all guided by an AI agent, not a fully automated script.

## Solution

An AI-assisted import skill that reads a file or directory from a legacy vault, transforms its frontmatter to OKF, copies it into the corresponding PARA folder in the primary vault, handles wikilink cascading (offers to import linked notes), reconciles tags against the vault glossary, and copies referenced assets. The user approves each step.

No changes to the `sb` CLI. The skill is invoked directly by the AI agent (opencode, Claude Code, Cline, etc.).

## User Stories

1. As a vault owner, I want to import a single legacy note into the primary vault, so that I can bring over one piece of content at a time.
2. As a vault owner, I want to import an entire legacy vault directory, so that I can migrate all my old content in one session.
3. As a vault owner, I want the skill to auto-transform legacy frontmatter to OKF, so that my imported notes match the vault's conventions.
4. As a vault owner, I want the skill to determine the target PARA folder from the source path, so that notes land in the right place.
5. As a vault owner, I want the skill to detect when an imported note wikilinks to notes not yet in the primary vault, so that I can optionally import those linked notes too.
6. As a vault owner, I want the skill to warn me if an imported tag conflicts with the vault glossary, so that I can decide whether to rename, keep, or skip it.
7. As a vault owner, I want the skill to preserve subdirectory structure when importing a folder, so that project folders with multiple notes and artifacts stay organized.
8. As a vault owner, I want the skill to copy referenced assets (e.g., images), so that media embeds continue to work.
9. As a vault owner, I want files to land directly in their PARA folder (not inbox), so that the import is efficient and doesn't create extra review work.
10. As a vault owner, I want the skill to present each file for approval before writing, so that I can catch surprises.
11. As a vault owner, I want imported files that already exist in the primary vault to be flagged, so that I can decide how to handle the collision (skip, rename, overwrite).
12. As a vault owner, I want the skill to handle the legacy `source` field by converting it to a "Sources" section with wikilinks at the end of the note, so that provenance information is preserved in standard form.

## Implementation Decisions

- **Pure skill, no `sb` changes**: The import is an AI skill, invoked via the agent (e.g., "run import-skill --path ~/old-sb"). No modifications to the `sb` CLI.
- **Route determination: path wins**: The target PARA folder is determined from the legacy file's path, not its frontmatter. A legacy file at `01 - Projects/Foo.md` lands in primary `01 - Projects/Foo.md`.
- **Landing zone: direct to PARA**: Files skip the inbox and land directly in their PARA folder.
- **Frontmatter transformation: AI-guided**: The agent reads legacy frontmatter and proposes an OKF mapping. Expected legacy fields: `title`, `type`, `created`, `tags`, `source`. The `source` field (inbound links) becomes a "Sources" section with wikilinks at the end. Missing `description` is left blank or derived by the agent with user approval.
- **Wikilink cascade: ask first**: When a note contains wikilinks to notes not in the primary vault, the agent offers to import each one. User approves or skips per file.
- **Preserve subdirectories**: Folder hierarchies under a PARA directory are maintained.
- **Tag reconciliation: warn and ask**: If a legacy tag doesn't match the vault glossary, the agent warns and asks the user to rename, keep, or skip.
- **Assets: copy**: Referenced assets from the legacy `/assets/` folder are copied to primary `/assets/`.
- **Filename collisions**: Since the primary vault is currently empty, collisions are unlikely. When they occur, the agent flags them and offers skip / rename / overwrite.
- **Invocation pattern**: The skill takes a single `--path` argument (file or directory). For a directory, it walks files breadth-first and processes each one sequentially with user approval.

## Testing Decisions

Testing is manual through skill invocation. No automated test infrastructure exists for skills in this vault. The skill should be exercised by running it against a test legacy vault (a copy or small subset) before production use.

## Out of Scope

- Full automation / unattended import (every step requires user approval)
- `sb import` CLI command
- Import from non-Foam systems (Notion, Roam, Obsidian, etc.)
- Bulk tag renaming across the vault
- Deduplication of near-duplicate notes
- Import of `.obsidian/`, `.vscode/`, or other editor config from legacy vault
- Git history preservation from legacy vault

## Further Notes

The import skill operates in a single session. For a vault of ~50 notes, the user should expect the full import to take one focused session with the AI agent. Larger legacy vaults can be imported in batches by running the skill on subdirectories.