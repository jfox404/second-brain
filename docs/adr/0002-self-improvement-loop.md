# 0002 — Self-improvement loop with standalone reflect skill

**Status:** accepted

The agent learns from its own usage through a self-improvement loop with four sub-operations: skill synthesis, skill refinement, user modeling, and reflection. Reflection is a standalone meta-skill (`.agents/skills/reflect/SKILL.md`) rather than logic embedded in each vault skill, keeping the improvement logic in one place. Triggers are post-operation (after every vault skill) and heuristic (5+ tool calls, user correction, error recovery). Cross-session bridging is deferred — the loop operates within a single session only. The agent's AGENTS.md permanently instructs it to run reflection after every skill invocation and on heuristic triggers. This exists at the boundary between "doing the work" and "getting better at the work" — a meta-layer that observes production without mixing into it.
