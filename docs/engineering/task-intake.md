
# Portable task intake

This is the project-local contract for turning a user request into work. It
does not require an operator Backlog, GitHub Project, fleet registry, or
operator credentials.

## Intent boundary

- **Discuss**: inspect and compare without creating durable task state.
- **Fix / plan a non-trivial change**: author a local OpenSpec
  proposal/spec/design/tasks package and stop for acceptance.
- **Quick execution**: make a small, bounded change through the normal
  task/check/finish workflow without ceremonial OpenSpec. This includes a
  regression repair that restores behavior unambiguously established by an
  accepted spec or equivalent durable contract; retain proportionate regression
  evidence.
- **Non-trivial execution**: agree the local OpenSpec package before
  implementation, then keep it canonical through verification and archive.

Use repository evidence to resolve factual ambiguity. Ask the user only when a
material product or scope choice remains. For a reasonable test seam, a quick
regression repair demonstrates the defect before repair, passes after it, and
reruns the original failure path. Otherwise, state the limitation and actual
alternative check truthfully. Do not let a quick task silently grow into a
material behavior, architecture, compatibility, data-contract, or scope
change: write/accept the local OpenSpec package first.

## Commands

```bash
openspec new change <change>
python3 scripts/start_task.py <change> --task "OpenSpec <change>" --scope "<paths>"
python3 scripts/select_checks.py --execute
python3 scripts/openspec_lifecycle.py check
python3 scripts/finish_task.py
```

The generated repository contains its own lifecycle scripts and CI workflow.
Provider adapters create or reuse the configured review object; GitLab stops at
green exact-head CI for human merge/acceptance. Do not require access to this
platform repository or to an operator installation to perform ordinary work.
## Readable managed work identity

Requirement creation exposes `Work identity: BR-N`, derived from its Issue
number. Normal child linking, handoff materialization and import allocate and
read back `BR-N/Tn`, retaining the exact private `Requirement: owner/repo#N`
line. Ordinal reservations remain in the parent's existing Issue body when
checklist links are removed; child Issue claims support interrupted-write
recovery. Do not edit or delete those reservations when reordering the
checklist. Conflicting claims, duplicate ordinals and lost identity claims observable on
readback fail closed;
inspect both Issue records before retrying. GitHub body edits have no atomic
compare-and-swap: avoid concurrent manual prose edits while linking. A change
observed before replacement stops the operation, but an unseen edit overwritten
between that read and replacement cannot be detected. No separate registry is used.

Only validated BR tokens may supplement opaque public technical provenance.
This permission does not disclose the private Backlog name, URL, exact Issue
reference or Requirement prose. Native GitHub Assignee and project-owned area
and optional kind labels continue to own responsibility and taxonomy. Branch
and PR propagation is described in the next section.

## Readable managed publication identity

Through platform-owned start helpers, a linked managed child with validated canonical identity `BR-7/T2` starts on
`agent/br-7-t2-<change>`. Its change directory and worktree path still use the
change slug. Resume retains the registered branch, including legacy branches;
branch spelling never substitutes for canonical task provenance.

Child PR titles carry `[BR-7/T2]`. Shared Requirement PR titles carry `[BR-7]`
and the standard identity block lists every included child. Publication retries
repair the leading title prefix and the bounded `dev-platform:br-identity`
block while preserving the rest of an existing PR's title and body. The same
early shared draft PR grows by exact-head fast-forward publication. Shared
candidate refs, canonical manifest paths and opaque exact lineage retain their
ownership rules; readable manifest fields are additive presentation only.

Only validated BR tokens may cross the private/public boundary. Canonical
claims and committed provenance must agree before publication; parent prose,
private repository names, references and URLs are not presentation sources.
Unlinked tasks keep their existing branch and PR behavior. Project-owned
publication entrypoints remain owned by the project; shared orchestration passes
safe title/body arguments, and the portable `managed_work_identity.presentation`
helper supports adopting the same idempotent repair contract in a custom
publisher. Legacy custom start signatures remain callable for unlinked tasks
and recorded legacy branches. Creating a fresh linked branch stops before
creation until the owning helper accepts the optional `branch_name` keyword;
this prevents silently losing BR identity without changing worktree paths. Copier continues to preserve project-owned harness files and agent
instructions; this identity does not introduce product taxonomy.
