## Parent

`.scratch/legacy-import/spec.md`

## What to build

When the user passes a directory path, the agent walks the directory, identifies every `.md` file, and processes them one at a time. Subdirectory structures under PARA folders are preserved.

The agent shows each file before import, gets approval, transforms it, and moves to the next. The user can skip individual files.

## Acceptance criteria

- [ ] Agent accepts a directory path and walks it for `.md` files
- [ ] Files are processed sequentially with user approval per file
- [ ] Subdirectory structure under PARA folders is preserved in the primary vault
- [ ] Non-markdown files (images, PDFs, etc.) are listed and offered for import
- [ ] User can skip individual files
- [ ] Agent reports a summary at the end (N imported, M skipped, any errors)
- [ ] Wikilink cascade (issue 02) triggers per imported file during the walk

## Blocked by

- `.scratch/legacy-import/issues/01-single-file-import.md`