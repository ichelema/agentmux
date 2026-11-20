# AgentMux Development Workflow

**Status:** `governance 2.9-draft` — consolidated candidate for Controlled Pilot, not yet frozen. Intended first stable governance release: `1.0.0`, after pilot and explicit human approval. This document describes **policy**, not proof that automated protections already exist.

## 1. Authority, state and non-negotiable invariants

Human owner approves specifications, lightweight plans, architecture/permission/scope changes, exceptional deferrals, governance revisions and merges. AI may implement, test, commit and prepare PRs only inside authorized bounds. Git repository is authoritative for code, architecture documents, ADRs and governance. Linear is authoritative for issue-specific approved specification, approved plan, approvals, status, dependencies and human decisions. GitHub provides PR/change/CI evidence. Hindsight is advisory knowledge, never normative.

- One active execution slot (WIP=1), including remediation/review/integration. Future issues may be specified; exceptions require human-approved suspension and explicit reassignment.
- One worktree and matching branch per issue, created by the Trinity command `1_create-worktree`: branch `<prefix>/<base-name>` and worktree `<repo-root>/.claude/worktrees/<prefix>+<base-name>`, where `<prefix>` is `bug` or `improvments` (from the issue type or label) and `<base-name>` is `<issue-id>-<model>`. Never trust a handoff instead of verifying real Git state.
- No autonomous merge, direct protected-branch push, force push, unauthorized destructive changes, secret reads, privilege escalation or policy/CI bypass. Single exception to the direct push rule: the resume prompt `RIPRESA.md` is updated by a direct commit to `main` that contains only that file, with the message `docs(ripresa): ...`. M0-06 must keep this exception compatible with the branch protection it configures, or revisit it.
- All work must be traceable: Linear issue → approved spec/plan revisions → branch/worktree → commits/PR → review → human merge → post-merge CI → Linear completion.
- Independent review runs as a read-only reviewer subagent with separate context, launched by the orchestrator on a detached review worktree (§4); max three normal review cycles across an issue, not three per agent restart.
- Critical/High findings block merge; Medium/Low residual issues require explicit human-approved deferral into linked Linear issues.

## 2. Delivery lifecycle and gates

| Checkpoint | Mandatory evidence / authority |
|---|---|
| CP01 Specification | Human approves Atomic issue scope, non-goals, AC and verifications; revision pinned |
| CP02 Plan | Human approves five-part lightweight plan and classification A0/A1/A2 |
| CP03 Implementation | Assigned agent works only in authorized worktree, implements AC, provides tests/build evidence |
| CP04 Independent Review | Separate reviewer validates code against spec, applicable ADRs, risk, security and test coverage; max 3 cycles |
| CP05 PR Ready | Required CI and review checks pass, findings handled, human sees scope/evidence |
| CP06 Merge + Closure | Human authorizes and executes merge; `main` CI verified; worktree cleaned safely; closure comment on the issue (Definition of Done) records the closure and releases the slot; then the agent moves the issue to Done |

Do not report every shell command. Report milestone gates and blockers, conflicts, permission/architecture/security exceptions. After an interrupted session, resume from last verified checkpoint; never infer a successful merge or test from a conversational summary.

### Definition of Ready

- Atomic objective, explicit in/out scope, dependencies, applicable references, AC with verification, risks and open questions resolved or explicitly tracked.
- Approved specification baseline and subsequent separately approved implementation plan.
- Authorized worktree/branch and available execution slot.
- Required environment/tool access confirmed; safety and security constraints identified.

### Specification template (Linear)

Objective; References; Scope; Out of Scope; Preconditions; Expected Behavior; Error Handling & Edge Cases; Interfaces & Constraints; AC-01...; Verification mapped to each AC; Open Questions; Approval and version/revision. Shared technical contracts live in `docs/specs/` as appropriate; avoid duplicate issues/spec files.

### Lightweight implementation plan (Linear)

Five required sections: (1) Approach, (2) affected files/modules, (3) verification plan mapped to AC, (4) risks, (5) architectural impact A0/A1/A2 plus dependencies. Plan approved separately after specification. Do not silently replan.

### Definition of Done

Approved AC satisfied; risk-adapted quality checks pass; review independent and no unresolved blocking findings; human-approved PR merged. After merge, verify `main` CI and clean the worktree, then post a closure comment on the issue. The closure comment records the merge SHA, the post-merge check of `main`, the worktree removal and the slot release; it is the authoritative CP06 evidence and the slot-release signal. The Linear state Done alone does not prove closure. The owner configures the Linear GitHub integration to move an issue to In Review at merge; the agent moves the issue to Done after the closure comment. No redundant final report.

## 3. Architecture and decision changes

- A0: compatible local implementation changes within approved plan may proceed autonomously.
- A1: interface/API contract changes require human authorization; ADR if architecturally material.
- A2: boundary, ownership, persistence, security or dependency architecture changes require an approved ADR **before** coding.
- A scope blocker stops implementation for decision. Related defects and improvements are proposed as linked Linear issues and deferred (Triage & Defer). Security Critical/High affecting the modification blocks progression.
- No automatic edits to approved spec/plan to justify implementation drift. Changes are revisioned and re-approved before resumption.

## 4. Agent roles and separation

**Implementer:** source modification, build/tests, limited remediation, PR preparation, issue progress updates within authorized worktree. Cannot sign off own review or merge.

**Independent Reviewer:** separate session/context. The orchestrator launches the reviewer as a read-only subagent with the Trinity command `3_independent-review` (mode `review`, one reviewer) or `4_review-fix-loop` (mode `loop`, two parallel reviewers); the owner chooses the mode, and `issue-cycle` runs the same modes. Each reviewer works on a detached worktree `<repo-root>/.claude/worktrees/review+<base-name>` at the PR head SHA, which the orchestrator removes after the review. A subagent launched this way satisfies the separate session/context requirement; the owner does not start a reviewer session by hand. Read-only is a procedure, not a technical control: the reviewer has shell access. The reviewer reads approved specification, applicable ADRs, exact commit/diff, CI evidence and independently retrieved relevant Hindsight. Must not receive implementer conversation, modify production code or rely on implementer test claims without examination. Report stable finding ID, severity, location, problem, evidence, expected behavior and status. `No Findings` is valid only after full review.

Each review cycle is a complete assessment of a particular commit. Corrections trigger further review; total 3 ordinary cycles. After the limit, stop/escalate. A newly modified commit must not be merged without independent validation even if review budget exhausted: explicit human exception/decision required; agent must not silently start cycle 4.

## 5. Risk-based quality and security

Baseline: build, unit tests, static checks as selected, compiler warnings and required CI. For PTY, native memory, IPC, SSH, sessions and VT input, add actual PTY/process tests, end-to-end tests, negative inputs, resource limits, sanitizers/fuzzing where warranted. Mocks/stubs for pure logic, protocols and client layout; real PTYs for terminal/process behavior; E2E local attach/detach and remote once implemented. Use bounded timeouts and nondisclosing test fixtures.

Threat boundaries: client/SSH ↔ daemon IPC ↔ PTY/process ownership ↔ untrusted ANSI/OSC/VT. Unix socket perms, peer credentials, protocol/frame limits, least privilege, FD hygiene and secure SSH identity are requirements, not assumed implementation. Revisit threat model for boundary changes. Unresolved Critical/High blocks merge/release. Approved residual Medium/Low become linked issues.

## 6. Retry, recovery and interrupted execution

Classify failures as transient, execution, context or structural. Transient tool/network: at most **3 total** attempts (initial + two retries) with simple short backoff, provided re-execution is safe/idempotent; check effects of prior requests before retrying GitHub/Linear mutations. Build/test: at most **2 correction attempts per root cause** within approved plan, with meaningful diagnosis each time. Context loss: one reconstruction from authoritative sources; then escalate. Permission/architecture/scope violations: **no automatic retry**. Counters persist across agent/session replacement; renaming an error/command does not reset them.

On interruption or blocker: leave code/worktree safe, persist checkpoint/handoff referencing actual Git commit, current issue and plan/spec revisions, evidence, attempts and next permitted step; never store secrets. New agent executes `docs/specs/SESSION_CONTINUITY.md`, verifies source state, and resumes only within authorization. No autonomous deletion/revert of uncommitted work. This is different from **Revert-first** for confirmed significant post-merge regressions, which uses human-controlled revert PR and corrective issue/tests, with WIP suspension as required.

## 7. Context and Hindsight

L0 mandatory: `CLAUDE.md`, applicable workflow sections, current approved Linear issue/spec/plan, actual branch/worktree/commit. L1 relevant: impacted ADRs/contracts/modules/tests plus targeted Hindsight `recall`. L2 on demand: related code, Git history, issues, additional targeted memory. Avoid full-document dumps unless needed. Hindsight bank `agentmux` is proposed; use `retain` for verified, reusable technical findings/gotchas with source references, `recall` to recover context, `reflect` for patterns if available. Do not blindly treat generated memory as authoritative or record secrets/conversation dumps. Reviewer runs independent recall and excludes implementer opinions on current PR. A knowledge capture check at meaningful checkpoints asks whether there was a reusable, verifiable discovery; no forced writing.

## 8. Documentation governance

Canonical docs: `docs/VISION.md` (vision), `docs/REQUIREMENTS.md` (requirements), `docs/ARCHITECTURE.md` (current architecture), `docs/adr/` (architectural choices), `docs/specs/` (shared technical contracts), `docs/security/THREAT_MODEL.md` (security model), this document (workflow), `CLAUDE.md` (compact agent instructions). Linear for issue-specific specification/plan and status; GitHub/Git for implementation/CI. One fact, one authoritative home; cross-reference rather than copy. Update docs in same PR when contract/policy/requirement changes. Unresolved conflicts between authoritative sources require human decision, not agent guessing.

## 9. WIP, slots and enforcement

Use a technically atomic execution-slot mechanism when available; checking worktrees alone is inadequate. Slot is held through implementation, review, merge and post-merge verification, and is released only by the CP06 closure comment (§2), never by a Linear state change alone. An authorized suspension can release/reassign it; ensure at most one active workstream. State registry, if implemented, is operational only; reconcile with Linear and Git. Verify issue/branch identity and reviewed commit matches PR HEAD. Protected GitHub branch/rulesets should require PR + CI, prevent agent direct push/merge/force push and protect CI configuration; human decisions cannot be replaced by a CI heuristic. Enforcement scripts and CI checks are *planned*, not claimed installed. Bootstrapping these controls requires human-authorized administrative setup.

## 10. CI, dependencies and releases

Product C17; CMake/Ninja; pinned Clang primary and GCC secondary; clang-tidy, CTest, ASan/UBSan as appropriate; GitHub Actions planned `ci.yml`, `integration.yml`, `release.yml`. Pin third-party libraries/toolchains and Actions SHAs in controlled manifest. Validate Zig/libghostty-vt compatibility and Notcurses optional features. Separate ordinary PR CI from PTY E2E/integration and human-approved release publish. Release at approved milestones: shared product version for `agentd`/`agentctl`, separate IPC major/minor handshake/capabilities. Traceable tags, CI-produced artifacts and SHA256. Controlled dependency changes: inventory version/license/provenance; review monthly, promptly triage security; proposed Linear issue, approvals, risk tests, independent review, no automatic updates.

## 11. Bootstrap and pilot

M0 staged: administrative repo/Linear provisioning, development governance, architecture baseline, C17 skeleton, test infrastructure, CI. No PTY, libghostty-vt, Notcurses or SSH implementation in M0. See `docs/BOOTSTRAP_PLAN.md`. Pilot uses a minimal real Atomic issue, human-approved spec/plan, worktree, independent review, PR, human merge and post-merge verification. Consolidate and fix policy contradictions before governance freeze. Pilot not yet executed, no approval implied.

## 12. Governance versions and changes

Draft series `2.9-draft` is a planning identifier, **not** SemVer 2.9 of frozen governance. After pilot + human approval, freeze first governance `1.0.0`; this version is independent of AgentMux product version and IPC version. Stable governance: PATCH for editorial clarification without changed obligations; MINOR for compatible new controls; MAJOR for fundamental gate/permission/workflow changes.

All normative changes: separate `ICH-XXX` issue/proposal, impact analysis, approved scope/plan, authorized worktree and PR, independent review, human approval/merge, proportionate validation (editorial consistency, targeted control test, or focused/full pilot for structural change). `main` is normative after merge. Existing active issues remain pinned to their approved governance version absent explicit human migration; urgent security protection requires explicit decision. No automatic changes to enforcement that safeguards the same PR.

## 13. Practical pre-pilot conflict resolutions

- Single active WIP is compatible with emergency work only via human-authorized suspension and slot transfer.
- Review cycle cap does not authorize merge of unreviewed new code; exception escalates.
- Minimal DoD does not omit post-merge `main` CI/Linear closure required before slot release.
- Hindsight helps technical recall but cannot assert current PR correctness for independent reviewer.
- Bootstrap provisioning is human-authorized before tools exist; a planned gate is not a currently enforceable gate.
- New session inherits verified issue state and consumed retry/review counters; never silently resets them.

## 14. Unimplemented enforcement and open approvals

This file does not create GitHub rulesets, Linear statuses, Hindsight installation/bank, scripts, review check machinery or SSH/CI access. Their actual availability must be checked during pilot. Architecture, bootstrap issues and governance freeze require explicit human acceptance. In absence of an objectively verifiable approval or gate state, **stop and ask** rather than assume success.
