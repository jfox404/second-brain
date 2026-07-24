## Parent

`.scratch/legacy-import/spec.md`

## What to build

The core vertical slice of the import skill: importing a single legacy file with straightforward frontmatter (`title`, `tags`, `created`) and no wikilinks, tag conflicts, or asset references.

The agent:
1. Reads the legacy file
2. Proposes an OKF frontmatter mapping to the user for approval
3. Determines the target PARA folder from the source path
4. Writes the transformed file to the primary vault
5. Confirms completion

## Acceptance criteria

- [ ] Agent can accept a `--path` pointing to a single legacy `.md` file
- [ ] Agent reads legacy frontmatter and proposes OKF transformation
- [ ] User approves or tweaks the mapping before write
- [ ] File lands in the correct PARA folder based on source path
- [ ] If the target folder doesn't exist in primary vault, the agent creates it (or asks user to)
- [ ] Transformed file has valid OKF frontmatter (`title`, `type`, `description`, `tags`, `relationships`, `timestamp`)
- [ ] Original legacy file is untouched
- [ ] Agent reports success and shows the written path

## Blocked by

None — can start immediately