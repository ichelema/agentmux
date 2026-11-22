# AgentMux — Product Vision

**Status:** Draft — requires human approval before becoming the authoritative product vision.

## Purpose

AgentMux is a lightweight, Linux-focused terminal-session manager designed to operate existing interactive CLI tools and, later, orchestrate existing AI coding agents. It separates long-lived terminal processes from their user interface so that local or remote sessions can continue when the client disconnects.

## Core principles

1. **Daemon owns execution:** `agentd` owns PTY masters, child process lifecycle, session identities and authoritative virtual terminal state.
2. **Client owns presentation:** `agentctl` provides an interactive TUI, initial setup and connection profiles, tabs, splits, focus and input routing.
3. **One mental model for local and remote:** `agentctl` connects to an `agentd` endpoint via a local Unix socket or an SSH-carried transport; the application protocol remains consistent.
4. **Reconnect without losing the session:** a disconnected client can reconnect while the daemon and host remain running; recovery after daemon failure or host reboot is **not** promised in the initial scope.
5. **Use libraries by responsibility:** `libghostty-vt` in `agentd` for VT/ANSI terminal emulation; Notcurses in `agentctl` for TUI rendering. Linux PTY and event facilities support the runtime.
6. **Agent integration is optional and incremental:** shells and Vim must work independently before adding support for existing tools such as Claude Code, Codex and Gemini CLI. OSC 7501 is a possible status signal, not proof of task completion.
7. **Small, reviewable increments:** specification-driven delivery via atomic Linear issues (`ICH-XXX`), independent review and human-controlled merge.

## Intended capabilities

- Start and manage independent PTY-backed interactive processes on Linux.
- Display multiple terminal sessions through tabs and split panes with correct input focus and resize behavior.
- Maintain session output and virtual screen state while no client is attached.
- Use the same client for local and SSH-remote endpoints; support reconnect and configuration profiles.
- Expose a versioned client/daemon protocol with explicit compatibility and capability negotiation.
- Later, observe and control existing CLI AI agents without modifying their internals.

## Explicit non-goals for the initial versions

- Reimplement all tmux functionality or depend on tmux, abduco or dtach for session persistence.
- Guarantee live process recovery after the daemon crashes or the Linux host reboots.
- Implement an LLM, replace the internals of existing AI CLI applications, or rely solely on terminal text/OSC status for semantic correctness.
- Offer multi-user remote terminal sharing, distributed high availability or production-grade cluster orchestration in the first milestones.
- Add extensive framework dependencies or complex UIs before the terminal-runtime prototype is validated.

## Success criteria

1. Two independent Linux PTY sessions can run Vim and a shell with functioning input and rendering.
2. A client can show both sessions in a vertical split, switch focus and resize without corrupting their terminal states.
3. After `agentctl` closes or SSH disconnects, sessions continue while `agentd` remains alive and can be reattached.
4. Local and remote connections use the same versioned application protocol with explicit error behavior.
5. The project follows the approved development workflow: Linear specification and plan approval, worktree isolation, CI evidence, independent review and human-approved merge.

## Scope boundaries and authoritative references

This document defines **why** AgentMux exists and what it aims to accomplish. `docs/REQUIREMENTS.md` defines requirements; `docs/ARCHITECTURE.md` defines the architecture; approved ADRs document architectural decisions; `docs/DEVELOPMENT_WORKFLOW.md` defines development governance; Linear contains issue-specific acceptance criteria and approved plans. Conflicts require human resolution, not unilateral agent assumptions.

## Milestone direction

M0: foundation and controlled pilot; M1: PTY runtime; M2: terminal emulation; M3: Notcurses TUI and layouts; M4: detach/reattach; M5: local/remote configuration and SSH; M6: existing CLI agent integration.

**Approval:** Pending. This document must not be represented as human-approved merely because it has been created.
