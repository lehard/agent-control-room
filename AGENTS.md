# Repository Agent Rules

This repository uses the shared Dev Platform workflow with `workflow_profile=standard`, `harness_mode=platform`, `scm_provider=github`, `protected_main=true`, `publish_mode=pr` and `pr_merge_mode=manual`.

This file is the canonical repository-wide map of the cross-agent task protocol: sources of truth, task intents, always-on invariants, entrypoints and where the detailed contract lives. It is deliberately bounded. Detailed workflow rules live in the linked documents and are read when a task reaches that concern. Tool-specific instruction files reference this contract and must not fork or duplicate it.

## Sources of truth

These have different ownership; do not treat them as one flat precedence list:

- `AGENTS.md` and any applicable module-level `AGENTS.md` — how work may be performed (process, safety, Git, verification).
- `openspec/specs/` — accepted current behavior.
- `openspec/changes/<active>/` — the approved delta currently changing that behavior.
- Code — implementation of current specs plus approved active deltas.
- `docs/` — durable architecture/runbook/project guidance outside active feature contracts.
- `.claude/agents-board.json` — machine-local coordination only when the multi-agent profile is enabled; never a backlog.

Target behavior during an active change is `current specs + active delta`, subject to process/safety constraints. A safety/process rule is not silently bypassed because an OpenSpec artifact conflicts with it. Report the conflict.

## Task intents

Keep these intents distinct:

- **Discuss** a change: inspect, design and compare options; do not create durable task state merely because the discussion is substantial.

- **Fix/add to Backlog** when explicitly requested: create or reuse a Business Requirement through `python3 scripts/requirement_intake.py create ...` and stop. Fixation creates no OpenSpec change and performs no technical decomposition or implementation. Use this repository's committed `[development_backlog]` routing.

- **Quick execution**: a small direct request, including a regression repair that restores behavior unambiguously established by an accepted contract, remains a quick task and uses the normal start/check/finish path with no external issue and no ceremonial OpenSpec. Retain proportionate regression evidence; if it becomes material, enter the configured non-trivial intake path before broadening scope.

- **Fresh non-trivial execution**: create/reuse a Business Requirement, start its pre-authoring flow, drive evidence -> ADD -> intents -> handoff, author the resulting internal managed OpenSpec changes, link each child to the Requirement, start those managed tasks, and only then implement.
- **Execute an existing Business Requirement**: start the supplied `type:requirement` Issue with `python3 scripts/requirement_intake.py start --requirement owner/repo#N`, then resume through `python3 scripts/execute_requirement.py advance --requirement owner/repo#N`.
- **Direct technical managed/OpenSpec path**: an explicitly supplied managed task or explicit request for a technical managed task uses `managed_task.py create`, `execute_managed_task.py`, or `start_managed_task.py`.

Internal children carry `type:internal-change` and link to their parent Requirement. Read derived progress with `requirement_intake.py aggregate`; do not maintain a competing task ledger. After import, the child's OpenSpec package is canonical for implementation and verification.




Goal refinement is a selective layer before authoring, used only for explicit goal-backed work or a materially unclear non-trivial request. It creates no durable goal, backlog or plan artifact. See [docs/engineering/agent-workflow.md](docs/engineering/agent-workflow.md).

## Always-on invariants

- **No silent divergence.** If implementation reveals the agreed change is wrong, update the relevant OpenSpec artifact *before* changing direction. Never implement a different contract and repair the specification afterwards.
- **Verification is not a checkbox count.** A non-trivial OpenSpec change is complete only after project checks, semantic verification, a truthful `verification.md` receipt, archive through the lifecycle helper, and publication in that order.
- **Never fabricate a verification receipt.** The report must state what was actually checked and which method was used.
- **Managed contract conflicts stop.** Repair formal/schema mismatches in an imported package; a material product-contract conflict returns to the user.
- **Quick tasks do not silently grow.** If one expands into a material behavior, architecture, compatibility, data-contract or scope change, stop and enter requirement-first intake when operator integration is enabled; use direct managed fixation only for explicit technical intent.
- **Required GitHub checks are never bypassed.** Do not add agent/admin bypass to make autonomous publication succeed, and do not use the human user as a routine Git courier between finished work and GitHub.
- **Keep CI providers thin.** Put portable test, build, verification, release, and deploy behavior behind repository-owned executable entrypoints; use provider workflows for their native orchestration. See [docs/engineering/agent-workflow.md](docs/engineering/agent-workflow.md).
- **Report blockers.** If a required completion step is blocked, say so instead of reporting the task as done.
- **Resolve the friction checkpoint** before reporting a non-trivial task complete: `python3 scripts/agent_friction.py checkpoint --result none --review-note "Reviewed actual task path"`, or the id of a recorded event.
- **Review the whole Requirement before terminal delivery.** Record a bounded parent retrospective with `python3 scripts/requirement_retrospective.py checkpoint --requirement owner/repo#N --result none --review-note "Reviewed actual Requirement path"` or linked friction event ids. Cover pre-authoring and cross-child work; child retrospectives remain required. New lifecycle stages must record meaningful friction or expose it to the nearest enclosing retrospective.
- **Don't busy-poll background processes.** Wait out a long-running background command with one long timeout — for Codex, a single `write_stdin` call with `yield_time_ms` up to the runtime's max (around 300000) — instead of repeated short `sleep`/`ps` checks or empty stdin polls; poll again only once that wait elapses. Genuine interactive input is unaffected.

## Profile


`standard`: each task uses a feature branch created from freshly synchronized `main`. Worktrees and the agent board are not mandatory.

## Entrypoints

```bash
python3 scripts/agent_doctor.py
python3 scripts/start_task.py <slug> --task "<task>" --scope "<files/modules>"
python3 scripts/requirement_intake.py routing-parameters
python3 scripts/requirement_intake.py start --requirement owner/repo#N
python3 scripts/execute_requirement.py advance --requirement owner/repo#N
python3 scripts/orchestrate_pre_authoring.py --id requirement-N status
python3 scripts/managed_task.py create --bundle <directory>
python3 scripts/start_managed_task.py owner/repo#N
python3 scripts/execute_managed_task.py --bundle <directory>

python3 scripts/select_checks.py --execute
python3 scripts/openspec_lifecycle.py archive <change>
python3 scripts/finish_task.py --reconcile
python3 scripts/finish_task.py
python3 scripts/finish_task.py --status
```

`agent_doctor.py` is start-of-task hygiene and publication preflight. `start_task.py` synchronizes `origin` safely and never auto-merges divergent histories. `finish_task.py --reconcile` is the explicit managed-task recovery operation: it reports/merges current authoritative main only through normal Git history, refuses dirty/provenance-ambiguous/changed-remote state, and requires validation to be rerun. `finish_task.py` enforces OpenSpec lifecycle hygiene, re-fetches immediately before publication, refuses stale/diverged integration, never force-pushes, and is resumable: rerunning it after any interruption is the correct next step. `--status` never publishes, merges, or mutates task content; it freshly observes task/main ancestry so it can point to `--reconcile` before expensive validation.

`publish_mode=pr`: validated work is published as an exact-head PR; required GitHub checks gate merge. `pr_merge_mode=manual` leaves the PR for explicit review and merge.

## Where the detailed contract lives

| Concern | Canonical document |
| --- | --- |
| Maintaining agent-facing instructions, pointers and surface ownership | [docs/engineering/agent-instructions.md](docs/engineering/agent-instructions.md) |
| Task intake and intent transitions | [docs/engineering/task-intake.md](docs/engineering/task-intake.md) |
| ChatGPT Project authoring through connected GitHub | [docs/engineering/chatgpt-project-protocol.md](docs/engineering/chatgpt-project-protocol.md) |
| Task intake, goal refinement, start/publish lifecycle, worktrees, shared workspace, friction, completion | [docs/engineering/agent-workflow.md](docs/engineering/agent-workflow.md) |
| OpenSpec contract model, semantic verification, receipts, archive, version policy | [docs/engineering/openspec-workflow.md](docs/engineering/openspec-workflow.md) |
| Provider-local executor selection, escalation, delegated write containment | [docs/engineering/model-routing.md](docs/engineering/model-routing.md) |
| Optional engineering capability lifecycle and the browser verification adapter | [docs/engineering/engineering-capabilities.md](docs/engineering/engineering-capabilities.md), [docs/engineering/browser-verification.md](docs/engineering/browser-verification.md) |
| Project-specific engineering, stack and domain rules | [docs/engineering/project-rules.md](docs/engineering/project-rules.md) |
| Product/domain semantics, architecture invariants, anti-patterns or representative examples | [docs/context/README.md](docs/context/README.md) when that concern is reached |

Platform-managed scripts, docs and the self-contained CI workflow are versioned in this repository by Copier; upgrades arrive only through reviewed Copier update PRs.

## Ownership

Platform-owned files carry the shared lifecycle and are updated by Copier. Project-owned files carry this repository's domain, stack and operational rules. Keep subtree-specific rules in a module-level `AGENTS.md` next to the code they govern, and keep project-domain rules in project-owned docs rather than promoting them into this map. `docs/context/` is a project-owned, selectively loaded context surface.

OpenSpec-generated Claude/Codex skills are produced by the OpenSpec CLI; do not hand-edit them or treat them as platform-owned source.

## Optional engineering capabilities

Optional engineering capabilities are independent of `workflow_profile`. Use `python3 scripts/capability_manager.py list` to discover them and the same entrypoint for `create`, `enable`, `update`, `remove`, `audit`, and `sync`. `dev-platform/capabilities.toml` is the project opt-in; descriptors under `dev-platform/capabilities/` are canonical. Generated `.claude/.codex` skill surfaces are derived and must not be edited. OpenSpec-generated skills remain external to this lifecycle. The opt-in `browser-verification` capability adds bounded exploratory browser checks for web projects; see [docs/engineering/browser-verification.md](docs/engineering/browser-verification.md).
