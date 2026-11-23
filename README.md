# AgentMux

**Status:** `governance 2.11-draft`, not frozen. This repository is **not** an implemented project.

AgentMux is a Linux application-level terminal session multiplexer and orchestrator for existing CLI AI agents, with a persistent PTY daemon and a C17 Notcurses client.

No code, CI, tests or branch protection exist yet.

## Documents

- [`CLAUDE.md`](CLAUDE.md): operating instructions for an agent starting in this repository (in Italian).
- [`docs/VISION.md`](docs/VISION.md): product vision, principles and non-goals.
- [`docs/REQUIREMENTS.md`](docs/REQUIREMENTS.md): product requirements summary.
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md): architecture baseline of `agentd` and `agentctl`.
- [`docs/adr/0001-application-level-daemon-client.md`](docs/adr/0001-application-level-daemon-client.md): ADR-0001, application-level PTY daemon and Notcurses client.
- [`docs/security/THREAT_MODEL.md`](docs/security/THREAT_MODEL.md): trust boundaries and minimum controls.
- [`docs/BOOTSTRAP_PLAN.md`](docs/BOOTSTRAP_PLAN.md): M0 bootstrap issues and controlled pilot.
- [`docs/DEVELOPMENT_WORKFLOW.md`](docs/DEVELOPMENT_WORKFLOW.md): development governance and safeguards.
- [`docs/specs/SESSION_CONTINUITY.md`](docs/specs/SESSION_CONTINUITY.md): cross-session resume, handoff format, recovery.
- [`.github/pull_request_template.md`](.github/pull_request_template.md): structure of the PR body.
- [`handoff.md`](handoff.md): next steps for the next agent session, nothing else (in Italian).
- [`memory/memory.md`](memory/memory.md): index of the persistent project memory (in Italian).
- [`.mcp.json`](.mcp.json) and [`.claude/rules/context7.md`](.claude/rules/context7.md): Context7 documentation server and the rule that tells agents when to use it.
