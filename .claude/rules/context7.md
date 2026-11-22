Use Context7 MCP for these three areas, with the fixed IDs of each section:

- libghostty (`libghostty-vt`): the VT library selected for `agentd` in `docs/ARCHITECTURE.md`.
  See [libghostty](#libghostty).
- Notcurses (`dankamongmen/notcurses`): the TUI library selected for `agentctl` in
  `docs/ARCHITECTURE.md`. See [Notcurses](#notcurses).
- Linux system interfaces in C: the interfaces listed for `agentd` in `docs/ARCHITECTURE.md` (PTY
  APIs, `epoll`, Unix domain sockets, process/wait handling) and terminal I/O. See
  [Linux system interfaces](#linux-system-interfaces).

Use Context7 even when you think you know the answer: your training data may not reflect recent
changes.

## libghostty

For libghostty skip `resolve-library-id` and use this ID:

- `/websites/libghostty_tip_ghostty` (libghostty VT, <https://libghostty.tip.ghostty.org>):
  reference for the `libghostty-vt` C API — functions, parameters, types and examples.

This source documents the `tip` (development) version of Ghostty. Its API can differ from the
version that AgentMux pins. Once the project pins a `libghostty-vt` version, the headers of the
pinned version are authoritative: if Context7 and the pinned headers disagree, the headers win.

## Notcurses

For Notcurses skip `resolve-library-id` and use these IDs:

- `/dankamongmen/notcurses` (Notcurses on GitHub): main reference for implementation, functions,
  parameters and examples.
- `/websites/deepwiki_dankamongmen_notcurses` (Notcurses on DeepWiki): complementary reference to
  explore the architecture, understand the relations between components or analyze the internals.

The man pages installed with Notcurses are authoritative for signatures, return values, flags,
constants and pointer ownership. They match the installed version, while Context7 can index a
different version: if Context7 and the installed man pages disagree, the installed man pages win.

## Linux system interfaces

For Linux system interfaces skip `resolve-library-id` and use these IDs:

- `/websites/man7_linux_man-pages` (Linux man-pages, <https://man7.org/linux/man-pages/>): main
  reference for signatures, return values, `errno` values, flags and standards, for example
  `posix_openpt(3)`, `grantpt(3)` and `ptsname(3)`.
- `/websites/sourceware_glibc_manual_html_node` (GNU C Library Reference Manual): complementary
  reference for concepts, for example pseudo-terminal pairs (`openpty`, `forkpty`), controlling
  terminals and terminal modes.

Context7 indexes the latest published version of both sources. The man pages installed on the build
system match the installed kernel and glibc: if Context7 and the installed man pages disagree, the
installed man pages win.

## Steps

1. For the three areas above, use the fixed IDs of the matching section. For any other library,
   start with `resolve-library-id`, passing the library name and what to look up, unless the user
   provides an exact library ID in `/org/project` format
2. For any other library, pick the best match (ID format: `/org/project`) by exact name match,
   description relevance, source reputation and benchmark score
3. Call `query-docs` with the selected library ID, a query scoped to a single concept and
   `language: "C"` for the three areas above. Make a separate call per concept
4. Answer using the fetched docs
