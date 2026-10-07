# Project-specific engineering rules

This file is project-owned and intentionally preserved across platform updates.

Add only rules that are specific to this repository or its stack/domain. Prefer module-level `AGENTS.md` when a rule applies only to one subtree.

Do not duplicate the shared worktree/OpenSpec/merge/check/friction workflow from the root `AGENTS.md`.

## Development Backlog routing

Agent Control Room is an independent product. Its authoritative routing is the
committed `[development_backlog]` table in `.dev-platform.toml`: backlog
`lehard/development-backlog`, target `lehard/agent-control-room`, label
`project:agent-control-room`, default priority `P2`.

Read the effective routing with `python3 scripts/requirement_intake.py routing-parameters`
before creating a Requirement. Never use `project:dev-platform` for this product.

Optional external operator state uses `AGENT_CONTROL_ROOM_OPERATOR_CONFIG`,
separately from the platform source checkout's environment. Any explicitly
configured operator file must preserve this product's routing. Private fleet
inventory remains in `lehard/dev-platform-operator`, outside this repository.

## Explicit failures

Do not introduce fallbacks that mask missing or invalid input, configuration, or
environment variables. Required failures must identify the problem and exit
non-zero. Do not swallow exceptions or substitute degraded behavior.
