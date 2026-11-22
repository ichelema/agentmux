# Handoff — prossima sessione

Aggiornato il 2026-11-22. Questo file dice solo cosa fare adesso: la procedura di ripresa è in
[CLAUDE.md](CLAUDE.md), la memoria del progetto in [memory/memory.md](memory/memory.md).

## Stato

Nessuna issue in corso: lo slot WIP è libero. L'ultima issue chiusa è M0-03 (ICH-588, merge
`883f86a`).

## Prossimo passo

1. Esegui «Ripresa del lavoro» di `CLAUDE.md`. Controlla su Linear che nessuna issue di Agentmux sia In
   Progress.
2. Chiedi all'utente la prossima issue. `docs/BOOTSTRAP_PLAN.md` prevede M0-04 «CMake
   Foundation»: eseguibili minimi `agentd` e `agentctl` in C17 che compilano, senza sessioni e
   senza PTY.
3. Se l'utente sceglie M0-04, controlla che la issue non esista già. Proponi il command qui sotto e
   fermati: lo invoca l'utente.
4. Dopo il command fermati al gate CP01: mostra la spec all'utente e chiedi l'approvazione.

M0-04 è la prima issue con codice: la spec deve fissare toolchain e versioni
(`docs/ARCHITECTURE.md`, vincolo 6; `docs/DEVELOPMENT_WORKFLOW.md` §10).

```text
/trinity:workflow:0_create-issue M0-04 CMake Foundation for project Agentmux (BOOTSTRAP_PLAN): minimal agentd and agentctl C17 executables that build with CMake and Ninja. No sessions, no PTYs, no libghostty-vt, Notcurses or SSH code. Decide and pin the toolchain versions for this step (ARCHITECTURE constraint 6, DEVELOPMENT_WORKFLOW §10). Use the AgentMux specification template (DEVELOPMENT_WORKFLOW "Specification template"). Spec and plan need owner approval before any implementation.
```

## Decisioni che servono all'utente

- La prossima issue.
- L'integrazione GitHub di Linear: al merge la issue deve andare in In Review, non in Done.
  Conviene cambiarla prima del prossimo merge (punto 1 di
  [memory/punti-aperti.md](memory/punti-aperti.md)).
- Il prefisso del branch di M0-04: `1_create-worktree` conosce solo `bug` e `improvments` (punto 3
  di [memory/punti-aperti.md](memory/punti-aperti.md)).
