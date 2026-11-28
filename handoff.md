# Handoff — prossima sessione

Aggiornato il 2026-11-28. Questo file dice solo cosa fare adesso: la procedura di ripresa è in
[CLAUDE.md](CLAUDE.md), la memoria del progetto in [memory/memory.md](memory/memory.md).

## Stato

Lo slot WIP è libero: su Linear nessuna issue di Ethereal è aperta, e l'ultima issue chiusa
(ICH-588) ha il commento di chiusura CP06. Il progetto AgentMux ora si chiama Ethereal
(punto 17 di [memory/decisioni.md](memory/decisioni.md)): repository `ichelema/Ethereal`,
progetto Linear «Ethereal», bank Hindsight `Ethereal`, cartella `20261120_ethereal`. La
governance è `2.14-draft`.

## Prossimo passo

1. Esegui «Ripresa del lavoro» di `CLAUDE.md` e controlla che lo slot sia ancora libero.
2. Controlla che la sessione usi il bank `Ethereal`: `python
   ~/.claude/skills/trinity/hooks/hindsight/lib/hindsight_config.py --banks`, lanciato dalla root.
3. La prossima issue la sceglie l'utente. Il candidato di `docs/BOOTSTRAP_PLAN.md` è M0-04
   «CMake Foundation»: eseguibili minimi `agentd` e `agentctl` in C17 che compilano, senza
   sessioni e senza PTY. La spec deve fissare toolchain e versioni (`docs/ARCHITECTURE.md`,
   vincolo 6; `docs/DEVELOPMENT_WORKFLOW.md` §10).
4. Se l'utente sceglie M0-04, proponi il command qui sotto e fermati: lo invoca l'utente. Dopo il
   command fermati al gate CP01: mostra la spec all'utente e chiedi l'approvazione.

```text
/trinity:workflow:0_create-issue M0-04 «CMake Foundation»: eseguibili minimi agentd e agentctl in C17 con CMake/Ninja che compilano, senza sessioni, senza PTY, senza libghostty-vt, Notcurses o SSH (docs/BOOTSTRAP_PLAN.md, DEVELOPMENT_WORKFLOW §10-§11). La spec fissa toolchain e versioni (ARCHITECTURE vincolo 6). Spec e piano richiedono l'approvazione dell'utente prima di qualsiasi lavoro.
```

## Decisioni che servono all'utente

- Il bank Hindsight `agentmux` resta come backup della copia `Ethereal`. Quando il bank
  `Ethereal` funziona, l'utente decide se cancellarlo.
- L'integrazione GitHub di Linear: al merge la issue deve andare in In Review, non in Done.
  Conviene cambiarla prima del prossimo merge (punto 1 di
  [memory/punti-aperti.md](memory/punti-aperti.md)). Dopo la rinomina del repository, la prima
  PR mostra anche se l'integrazione collega ancora le PR alle issue.
- Il prefisso del branch di M0-04: `1_create-worktree` conosce solo `bug` e `improvments`
  (punto 3 di [memory/punti-aperti.md](memory/punti-aperti.md)).
- I termini non allineati dei documenti tradotti (punto 7 di
  [memory/punti-aperti.md](memory/punti-aperti.md), Low): correggerli in una issue a parte
  oppure lasciarli.
