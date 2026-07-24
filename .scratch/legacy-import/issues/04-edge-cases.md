## Parent

`.scratch/legacy-import/spec.md`

## What to build

Three edge cases that the import skill must handle when they arise during a file import:

1. **Tag reconciliation**: When a legacy tag doesn't match the vault glossary in `CONTEXT.md`, the agent warns and asks: rename to a glossary term, keep as-is, or skip the tag.

2. **Source field transformation**: Legacy notes may have a `source` field in frontmatter listing provenance files. The agent converts this to a "Sources" section with wikilinks at the end of the note body, and removes it from frontmatter.

3. **Asset copying**: When an imported file references an asset (`![image](assets/foo.png)`), the agent detects the reference, locates the file in the legacy vault's `/assets/`, copies it to the primary `/assets/`, and confirms the path still resolves.

## Acceptance criteria

- [ ] Tag conflict is detected and presented to user with rename/keep/skip options
- [ ] Renamed tag is written to OKF frontmatter with the glossary term
- [ ] `source` field is converted to a "Sources" section with wikilinks
- [ ] Source field is removed from frontmatter after conversion
- [ ] Referenced assets are detected, copied, and paths verified
- [ ] Missing assets are reported (warn, don't fail)

## Blocked by

- `.scratch/legacy-import/issues/01-single-file-import.md`