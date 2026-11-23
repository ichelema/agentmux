# AgentMux architecture — agreed design baseline

**Status:** derived from planning decisions; part of the owner-approved architecture baseline of ICH-588 (M0-03), under `governance 2.11-draft`. Architecture evolution follows `docs/DEVELOPMENT_WORKFLOW.md` §3 and approved ADRs.

## Goal and operating model

Linux-native terminal multiplexer/orchestrator for existing CLI agents (Claude Code, Codex, Gemini), regular shells and interactive TUI programs (including Vim). This is an application-level tool, not a patch to existing agent CLIs. Product sources in **C17**, with CMake/Ninja; some pinned third-party libraries may be implemented in other languages (notably Zig behind `libghostty-vt` C API).

## Executables

### `agentd` — persistent host-side daemon

- Runs on local or remote Linux target, independent of connected clients; supervision by external user service such as `systemd --user` with lingering where appropriate.
- Owns PTY masters, process/session lifecycle, per-session virtual terminal state and IPC server.
- Linux primitives: PTY APIs, `epoll`, Unix domain sockets, process/wait handling.
- Uses **libghostty-vt** in the daemon to interpret ANSI/VT output, handle terminal state, and produce snapshot/diff representation; exact C API, licensing and dependency/toolchain compatibility require milestone validation.
- Treats incoming terminal bytes and OSC events as untrusted. OSC 7501 may carry agent status indicators but is not trusted evidence of successful task completion; Kitty keyboard protocol support will need capability tests.
- Session state survives client detach and SSH disconnect while daemon and host processes keep running; **does not promise survival of daemon restart, host reboot, or process crash**.

### `agentctl` — interactive local client

- CLI/TUI with **Notcurses**, using planes for panes/splits/tabs and keyboard focus/input routing.
- Does not own the authoritative PTY or use `libghostty-vt` to reproduce terminal state. Receives screen snapshots and incremental updates from `agentd` for rendering.
- Supports first-launch setup for local/remote profiles; XDG/TOML configuration. Avoid storing secrets in config.
- Local target: Unix domain socket. Remote target: **OpenSSH** transport carrying the same application protocol to a bridge on the target/daemon socket (stdio bridge or tunnel implementation to be selected and specified before coding). Uses `~/.ssh/config`, OpenSSH authentication and host-key checks.

## Trust and interface map

```text
Local workstation                         Target Linux host (local or remote)
+-----------------------------+            +------------------------------------+
| agentctl                    |            | agentd                             |
| Notcurses UI / layout       |            | IPC endpoint and authorization     |
| config profiles / SSH       |            | session + process manager          |
+--------------+--------------+            | PTY masters + epoll                |
               | Unix socket / SSH bridge  | libghostty-vt instances            |
               +-------------------------->| virtual terminal snapshots/diffs   |
                                            +----------------+-------------------+
                                                             | PTY child I/O
                                            +----------------v-------------------+
                                            | shell / Vim / agent CLI processes   |
                                            +------------------------------------+
```

## IPC and compatibility

- Application-layer protocol works over local Unix socket and SSH-carried streams; versioned separately from the product release.
- Client/server handshake negotiates protocol version/capabilities. Both executables share a product version, while IPC versioning is independent.
- Security/limits for framing and message validation are mandatory. Concrete wire format, payload schemas, exact diff semantics and bridging strategy are **not yet approved** and must be specified in later atomic issues.

## Runtime ownership / behavior

- Exactly one daemon owns a given managed PTY/session at a time; client attach/detach must never close the authoritative PTY merely because the UI disconnects.
- `agentd` maintains VT state while no client is attached. Reattached clients must obtain a consistent snapshot, then follow diffs/events with appropriate ordering/resynchronization.
- User can use ordinary shells/Vim independent of agent integrations; agent status features are later milestone extensions.
- Session layout ownership is in `agentctl` initially; cross-client layout synchronization is not part of the approved baseline.

## Incremental milestones

| Milestone | Intent |
|---|---|
| M0 | repository, governance, architecture baseline, C17 build skeleton, test framework, CI |
| M1 | PTY ownership and lifecycle in daemon |
| M2 | libghostty-vt integration; terminal snapshots/diffs |
| M3 | Notcurses client, tabs/splits, input/focus |
| M4 | daemon/client attach-detach and persistence across client disconnect |
| M5 | local/remote target profiles and SSH transport |
| M6 | CLI agent integration and OSC 7501 status |

Milestone ordering is a plan and may be revised via approved issue/ADR; ensure each incremental deliverable is testable.

## Known constraints and unresolved design questions

1. Verify exact `libghostty-vt` C API, Zig build integration, status/Kitty features and license before dependency pinning.
2. Define IPC wire framing, error/version handling, snapshots/diffs, flow control and resource ceilings.
3. Choose an SSH bridge mode and authenticate/authorize the local daemon socket correctly on the remote host.
4. Define session identity, client concurrency and resize arbitration before implementation.
5. Decide threat mitigations for OSC, terminal escape responses, clipboard-like effects and resource pressure.
6. Define supported Linux distributions and toolchain versions; pin only after validating builds.

These are **open technical decisions**, not permission to improvise architectural changes during an atomic issue.
