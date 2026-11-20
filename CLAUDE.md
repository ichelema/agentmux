# AgentMux agent operating instructions

Read `docs/DEVELOPMENT_WORKFLOW.md`, `docs/ARCHITECTURE.md`, `docs/specs/SESSION_CONTINUITY.md` and the active approved Linear issue before doing work. Repository documents and signed-off ADRs define architecture; Linear contains approved issue specification and plan; Git is ground truth for code. Hindsight is advisory technical memory, never a substitute for these sources.

**Status:** governance `2.10-draft`, tracked in the repository, **not** frozen. Before any implementation, verify human approval of specification, plan, credentials/permissions and workflow phase. Do not infer authorization from file presence.

## At session start (mandatory)

1. Discover the current `ICH-XXX` and its phase from authoritative Linear records or human instruction. If unavailable, request the issue; never guess.
2. Read the latest approved specification and plan; capture their revision identifiers and the applicable governance version.
3. Verify `git status`, active branch, HEAD commit, worktree path, and relevant remote/PR status. Check actual Git state rather than trusting a handoff.
4. Load only the relevant ADRs/specs/tests and retrieve targeted Hindsight knowledge for the affected components (if available). Verify memories against source of truth.
5. Read the last checkpoint/handoff from Linear/PR and compare every claim with Git, CI and issue status. Check unfinished operations before retrying. An independent reviewer skips this step: it does not read checkpoint comments (`docs/DEVELOPMENT_WORKFLOW.md` §4).
6. State resumable next action or blocker. Do not start a second active issue: WIP=1.

See `docs/specs/SESSION_CONTINUITY.md` for exact handoff contract.

## Hard constraints

- Work on one approved atomic issue at a time and only in its authorized worktree: branch `<prefix>/<issue-id>-<model>`, worktree `<repo-root>/.claude/worktrees/<prefix>+<issue-id>-<model>` (`docs/DEVELOPMENT_WORKFLOW.md` §1).
- No unauthorized scope expansion, architecture/security/permission changes, secret access, destructive operations, CI bypass, direct push to protected main, or agent merge. Single exception: a direct commit to `main` that contains only `RIPRESA.md`, with the message `docs(ripresa): ...` (`docs/DEVELOPMENT_WORKFLOW.md` §1). M0-06 must keep this exception compatible with the branch protection it configures, or revisit it.
- Independent reviewer: a read-only subagent with separate context on a detached review worktree, launched with the command the owner chooses: `3_independent-review … soft`, `4_review-fix-loop` or `issue-cycle review|loop` (`docs/DEVELOPMENT_WORKFLOW.md` §4). `3_independent-review` in `hard` mode in the implementer session is not independent. Read-only is procedural, not enforced. Max three ordinary review cycles in total.
- Critical/High unresolved findings block merge. Human controls approval and merge.
- Document/recover consequential state at milestones and interruptions. A new context must verify current source state rather than blindly trust a summary.
- Do not retain credentials or complete conversations in Hindsight or handoffs.

## Tooling guidance

C17 / CMake / Ninja for product; `agentd` owns Linux PTYs and `libghostty-vt`, while `agentctl` uses Notcurses for presentation. No PTY/VT/SSH implementation belongs to M0. Apply Matt Pocock agent skills selectively when available, not as a requirement for tool availability.
