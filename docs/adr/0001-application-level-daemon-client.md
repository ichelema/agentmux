# ADR-0001 — Application-level PTY daemon and Notcurses client

**Status:** Accepted — owner sign-off through ICH-588 (M0-03), specification approved on 2026-11-22, effective at the owner merge.

## Context

AgentMux needs independent terminal child processes, local/remote attachment and preservation of PTY state after UI or SSH disconnection without relying on tmux/abduco/dtach.

## Decision

Use two Linux executables in C17: persistent PTY-owning `agentd`, and Notcurses-based `agentctl`. `agentd` performs ANSI/VT parsing using pinned `libghostty-vt` through its C interface and exposes a versioned IPC protocol over Unix socket or SSH-carried stream. `agentctl` handles UI layout and rendering.

## Consequences

- Clear responsibility and trust boundaries, daemon independently supervised.
- Must build reliable IPC snapshot/diff, reconnect/resync, resource management and auth.
- Daemon lifetime, not user interface lifetime, bounds persistence; daemon restart recovery is excluded initially.
- Third-party library integration and exact APIs must be validated before implementing relevant milestones.

## Alternatives considered

Embedded terminal emulator in client; terminal passthrough to terminal emulator; tmux/abduco/dtach dependency. Rejected for the selected architecture.
