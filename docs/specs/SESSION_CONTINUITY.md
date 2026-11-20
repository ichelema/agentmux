# Session continuity and resumable agent execution

**Status:** part of `governance 2.10-draft`; operational specification exercised in the pilot ICH-548, not yet automated. This protocol is mandatory for starting a new agent session.

## What survives between sessions

- **Git:** source changes, branch/worktree, commits, PR diff are authoritative for implementation state. Uncommitted files may exist; inspect `git status` and do not silently discard them.
- **Linear:** issue identity, approved spec and plan revisions, current phase, approvals, dependency/blocked status, retry and review-cycle budget, links to PR and last durable checkpoint.
- **GitHub:** CI/PR/review evidence tied to exact commit SHA.
- **Hindsight:** reusable long-term technical memory only; may be unavailable; never used as authoritative task-state or approval record.
- **Checkpoint handoff:** concise operational snapshot published to the issue or equivalent persistent authorized location. Its claims are advisory until verified against sources above.

Do **not** assume ChatGPT/agent conversational memory, hidden chain of thought, worktree naming or a local YAML file alone provides reliable resumption.

## Start-of-session algorithm

1. Determine issue ID from explicit user instruction or approved Linear execution slot. If ambiguous, stop. Do not select the next backlog issue autonomously.
2. Read `CLAUDE.md`, relevant workflow and architecture sections. Read spec revision and separately approved plan revision from Linear; confirm governance version pinned at start.
3. Verify actual repository and worktree: location, branch, `HEAD`, `git status`, untracked/uncommitted work, remote divergence, outstanding cherry-pick/rebase/merge. Verify issue/branch linkage.
4. Read last checkpoint and PR/CI state, and cross-check it against actual Git and Linear. Treat mismatches as potential recovery blockers; do not overwrite newer work.
5. Load relevant ADR/contracts/test scope; make targeted Hindsight `recall` queries based on issue/component. Verify retrieved facts against source.
6. Determine exact checkpoint (CP01–CP06), consumed correction/review counts and permitted next action. Check whether previous side-effect operations already happened before retrying.
7. Produce short resume assessment: verified state; remaining AC/finding; next operation; pending human gate. Execute only if all required approvals and safety checks hold.

## End-of-session / interruption checkpoint

Record when: checkpoint reached, session about to end, context saturation, tool error requiring replacement, blocker or review handoff. Prefer an issue comment/structured status in Linear, linked to commit and PR. The first line of each implementer checkpoint comment is exactly `**Implementer checkpoint (agentmux.handoff.v1) — reviewers do not read this comment.**`; reviewers skip every comment that starts with this marker line. Optionally use an ephemeral local handoff for convenience, never as the only copy.

Minimum record:

```yaml
schema: agentmux.handoff.v1
issue: ICH-XXX
checkpoint: CP03
phase: implementation
governance_version: 2.10-draft
spec_revision: "<approved-revision-id>"
plan_revision: "<approved-revision-id>"
branch: improvments/ICH-XXX-opus
worktree: "<repo-root>/.claude/worktrees/improvments+ICH-XXX-opus"
head_commit: "<full-git-sha>"
worktree_clean: false
pr: "<url-or-null>"
acceptance_criteria:
  AC-01: passed
  AC-02: pending
verification:
  - command: "ctest --test-dir build --output-on-failure"
    result: "failed (example)"
    evidence: "<CI link or sanitized log reference>"
review:
  cycles_used: 0
  open_findings: []
recovery:
  root_cause: "<classified cause or null>"
  corrections_used_for_root_cause: 0
blocking_decisions: []
next_allowed_action: "<one concrete, authorized action>"
last_updated: "<timestamp-with-timezone>"
```

Values are **illustrative**. Do not report checks as passed unless measured, and never fabricate SHA/revision/approval or timestamp.

## Security and reliability

- Exclude secrets, auth tokens, personal credentials, sensitive terminal contents and verbatim agent prompts.
- Keep review finding IDs and cycle counter stable across agent restarts; keep recovery counters tied to root cause.
- Verification must precede execution when the last handoff may be stale.
- If prior agent disappeared mid-mutation (PR creation, Linear edit), inspect remote state to avoid duplicates.
- A reviewer starts from a **separate** clean context and independently reads the canonical spec, approved plan, approval records, diff, test evidence and Hindsight (if available); it must not receive implementer's conversational scratchpad and does not read checkpoint comments (marker line above).
- Hindsight may help recover technical gotchas but **must not** serve as the checkpoint database or a mechanism to bypass issue approvals.

## Planned automation vs available today

An orchestration script could later implement `resume` / `checkpoint` and validate states across Git, Linear, GitHub and Hindsight. **No script or cross-tool integration exists in this pack**. Until built, the steps are an explicit agent operating procedure, requiring real access to the named sources. If a connector is absent, ask for required context or stop at the gate; do not invent it.
