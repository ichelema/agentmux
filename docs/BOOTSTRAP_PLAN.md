# M0 staged bootstrap and controlled pilot

**Status:** plan only; no Linear issues or repository protections have been provisioned.

## Prerequisites (human-owned)

Approve seed documents and project architecture; provision/authorize GitHub and Linear; establish agent credentials and permission boundaries; establish independent reviewer environment; confirm what checks/automation can actually be enforced. The seed repository cannot claim a review gate or branch protection until configured and tested.

## Proposed M0 issue order (planning labels, not Linear IDs)

| Order | Issue title | Verifiable deliverable | Important exclusion |
|---|---|---|---|
| M0-01 | Repository Foundation / Controlled Pilot | Minimal valid repo, README and `.gitignore`, reviewed PR/merge | No CMake, PTY or app features |
| M0-02 | Development Governance | Consolidated workflow, agent instructions and templates tracked in repo | No new policy without approval |
| M0-03 | Architecture Baseline | Vision/requirements/architecture/ADRs reviewed | No integration code |
| M0-04 | CMake Foundation | Minimal `agentd` and `agentctl` C17 executables compile | No actual sessions/PTYS |
| M0-05 | Test Infrastructure | CTest, representative test, sanitizer wiring | No SSH E2E |
| M0-06 | GitHub Actions CI | PR build/test/checks, protected main configured and validated | No release publication |

The planning labels `M0-xx` must be replaced by Linear-issued `ICH-XXX` IDs. Split any issue if it is too large to be atomic. Note: the initial repo seed and governance documents themselves may need to be introduced administratively before the pilot PR. The human must approve how to avoid treating preseeded files as a completed pilot deliverable. **A pilot consisting only of adding README/.gitignore is meaningful only if those files have not already been integrated in the baseline.** A clean alternative is to provision an empty Git repository first, then run the pilot, then add the consolidated documentation in M0-02/M0-03.

## Pilot sequence

1. Approve atomic issue specification with AC and explicit out-of-scope.
2. Approve independent lightweight plan.
3. Acquire exclusive execution slot; create matching worktree/branch.
4. Run Implementer with approved context; collect limited test evidence.
5. Run read-only Independent Reviewer against exact commit and AC; remediate within three cycles.
6. Require human review/approval and merge; verify main CI and Linear closure; release slot.
7. Audit WIP exclusivity, permissions, review isolation, traceability, gate effectiveness and session continuity by deliberately stopping and resuming a non-destructive session during the pilot.
8. Record discovered governance conflicts and human-approved corrections; freeze `1.0.0` only after explicit sign-off.

## Session continuity pilot acceptance criteria

- Checkpoint retains issue, approved revision IDs, commit SHA, test evidence, budget counters and next action.
- New independent agent session reconstructs state without needing original chat history.
- New session notices altered Git state and refuses to overwrite conflicting or uncommitted changes.
- Hindsight recall is verified and does not override an ADR/spec/Linear approval.
- Reviewer receives its own context; no implementer scratchpad is transferred.

## Notes on enforcement

Atomic WIP lock, GitHub rulesets, protected CI, reviewer check, dependency locks and automated Hindsight memory access are M0 implementation/configuration tasks. Until they exist, use human supervision, explicit checks and restricted permissions; never represent procedural protection as technically enforced.
