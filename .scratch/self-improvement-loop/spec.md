Status: ready-for-agent

# Self-Improvement Loop

## Problem Statement

The agent has 7 vault-specific skills for PARA operations, but no mechanism to learn from its own usage. When the user corrects the agent, discovers a better workflow, or the agent finds a non-obvious procedure, that knowledge evaporates at the end of the session. The agent has no way to persist preferences, refine its skills, or synthesize new procedures. Every session starts from the same static skill definitions.

## Solution

A self-improvement loop modeled after Hermes Agent's learning loop, adapted for the VS Code / Copilot agent architecture. Four sub-operations:

1. **Reflection** — The agent evaluates recent work after every vault skill invocation and on heuristic triggers (user correction, error recovery, complex workflows)
2. **User modeling** — Durable preferences and corrections persisted in `docs/agents/USER.md`
3. **Skill refinement** — Targeted patching of curated skills (with user confirmation) and synthesized skills (automatic)
4. **Skill synthesis** — Autonomous creation of new `z-*` skill files for repeatable workflows not covered by existing skills

## Architecture Decisions

- **Standalone reflect skill** (ADR-0002): Self-improvement logic lives in `.agents/skills/reflect/SKILL.md`, not embedded in each vault skill
- **Patch-only refinement** (ADR-0003): Curated skills are patched, never rewritten; synthesized skills are read-only to themselves
- **No cross-session bridge**: Reflection fires only within a session (post-operation + heuristic); cross-session persistence deferred
- **z-* naming convention**: Synthesized skills use `z-` prefix for visual distinction
- **USER.md at docs/agents/**: Four-section format (Preferences, Domain Context, Corrections, Routines)

## User Stories

1. As a user, I want the agent to reflect after every vault skill invocation, so that learnings are captured immediately.
2. As a user, I want the agent to reflect when I correct it mid-workflow, so that my corrections are persisted.
3. As a user, I want durable preferences about how I work stored in a USER.md file, so the agent remembers them across sessions.
4. As a user, I want the agent to propose patches to curated skills when a correction maps to an existing skill, so I can approve or reject before changes are applied.
5. As a user, I want the agent to autonomously create new `z-*` skills for complex workflows it discovers, so the vault's skill set grows organically.
6. As a user, I want the AGENTS.md to define the self-improvement loop as permanent agent behavior, not an optional feature.
7. As a user, I want the reflect skill to guard against nested reflections and skill self-deletion, so the system stays safe.

## Out of Scope

- Cross-session memory or FTS5 search
- Automated pruning of stale USER.md entries
- Skill synthesis from third-party sources or Skills Hub
- Multi-agent coordination
