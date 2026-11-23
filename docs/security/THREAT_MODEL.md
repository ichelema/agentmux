# AgentMux threat model — baseline

**Status:** part of the owner-approved architecture baseline of ICH-588 (M0-03), under `governance 2.12-draft`. Changes follow `docs/DEVELOPMENT_WORKFLOW.md` §3.

Risk-based modeling. Revisit boundaries when IPC/SSH, credentials, PTY ownership, terminal parsing, privileges, dependencies or sensitive surfaces change.

| Boundary | Assets / threats | Minimum controls |
|---|---|---|
| TB-01 client ↔ transport | Remote impersonation, interception | OpenSSH host-key verification, standard auth, no plaintext credentials |
| TB-02 transport ↔ daemon IPC | Unauthorized client, malformed frames | UDS permissions, Linux peer credentials (`SO_PEERCRED`), session authorization, version/size validation |
| TB-03 daemon ↔ child processes | Privilege escalation, FD leaks, resource exhaustion | Unprivileged execution, close/ownership of FDs, lifecycle cleanup, bounded sessions/buffers |
| TB-04 child PTY output ↔ VT parser/UI | Malformed ANSI/OSC, terminal escape abuse, memory abuse, false status | Treat output as hostile, parser tests/fuzz targets where justified, limits, sanitize response/actions, OSC not security evidence |

## Security gates

- Assess trust-boundary changes and input validation in issue plan/review.
- Use targeted negative/integration tests for risky interfaces; ASan/UBSan and CI checks per policy.
- Critical/High findings block merge and release; Medium/Low can be deferred only through explicitly approved linked issues.
- Avoid secrets in logs, Hindsight, agent prompts, handoffs and untrusted PR artifacts.
- Restrict who can modify protected CI/workflows or governance checks.

## Risks to evaluate in future issues

Session access control, cross-user socket access, remote bridge behavior, terminal escape effects, resource throttling, crash handling, simultaneous client resizing, child environment exposure, dependency provenance. No assertion that these controls are implemented yet.
