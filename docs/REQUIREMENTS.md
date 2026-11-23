# AgentMux product requirements — summary

**Status:** design summary, part of the owner-approved architecture baseline of ICH-588 (M0-03), under `governance 2.11-draft`; technical acceptance criteria must be elaborated in approved Linear issues/specs. Changes follow `docs/DEVELOPMENT_WORKFLOW.md` §3.

## Intended capabilities

- Linux client/daemon terminal multiplexing with persistent PTY-owning daemon.
- Interactive shells and full-screen terminal applications (e.g. Vim) with VT emulation.
- Local and remote interaction through a single versioned IPC contract, using OpenSSH for remote access.
- Client-side tabs/splits, pane focus and input routing with Notcurses.
- Terminal screen snapshots/diffs transmitted from daemon to client; reconnect after client/SSH disconnect while daemon remains alive.
- Optional CLI agent status integration (Claude Code, Codex, Gemini), including OSC 7501 when compatible and verified.
- Setup wizard and XDG/TOML configuration profiles; independent client/daemon protocol negotiation.

## Non-goals in initial milestones

- Reimplementing or modifying third-party CLI agent internals.
- Durable process/session restoration after daemon crash, reboot, or host failure.
- Implementing tmux, abduco or dtach as dependencies for detach/attach.
- Shipping terminal or SSH features during M0.
- Assuming OSC status reflects trustworthy task success.

## Cross-cutting requirements

Least privilege; secure IPC; limits for input and memory; explicit API contracts; C17 product builds; risk-based tests combining mocks, real PTYs and end-to-end integration; no secrets stored in logs or AI memory. Consult `docs/ARCHITECTURE.md` and `docs/security/THREAT_MODEL.md`.
