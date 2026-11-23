# Handoff — prossima sessione

Aggiornato il 2026-11-23. Questo file dice solo cosa fare adesso: la procedura di ripresa è in
[CLAUDE.md](CLAUDE.md), la memoria del progetto in [memory/memory.md](memory/memory.md).

## Stato

Lo slot WIP è libero: su Linear nessuna issue di Agentmux è aperta, e l'ultima issue chiusa
(ICH-588) ha il commento di chiusura CP06. La governance è `2.12-draft` (commit `62604aa`).

## Prossimo passo

1. Esegui «Ripresa del lavoro» di `CLAUDE.md` e controlla che lo slot sia ancora libero.
2. Controlla `gpt-bridge:reviewer`. La mod crea il gemello all'avvio della sessione, quindi ora
   deve avere la sezione «Memoria del progetto» di `agents/reviewer.md` di Trinity (commit
   `e85f52b`). Fai la prova con un subagente che risponde senza usare tool.
3. Primo lavoro, scelto dall'utente il 2026-11-23 (punto 15 di
   [memory/decisioni.md](memory/decisioni.md)): traduci in italiano tutti i documenti del
   repository e cambia le regole che li vogliono in inglese. Commit, PR e commenti sulla PR
   restano nella lingua della label `git:` del progetto Linear (oggi `git:en`, inglese).
   - Trova prima le regole. In AgentMux l'unica regola che cita l'inglese è in `CLAUDE.md`,
     sezione «Regole», punto «Lingua». La sezione «Lingua» della skill `linear` di Trinity copre
     issue e Git (commit, PR, commenti sulla PR, CHANGELOG), non i documenti. Controlla anche gli
     altri command e skill di Trinity, per esempio `github-pr-release`.
   - Perimetro da fissare nella spec: `docs/` (ADR compresi) e `README.md`.
     `.github/pull_request_template.md` è il corpo della PR: segue la label `git:`.
   - Identificatori, marcatori e chiavi restano identici: `agentmux.handoff.v1`, «written by
     Claude Code on the owner's instruction», `**CP06 closure — ICH-XXX`.
   - Il punto 4 di [memory/punti-aperti.md](memory/punti-aperti.md) tocca la traduzione: parte
     del testo storico di `docs/BOOTSTRAP_PLAN.md` è superata.
   - È un cambio di governance: nuova versione `2.13-draft` e §12 di
     `docs/DEVELOPMENT_WORKFLOW.md` (issue, PR e review indipendente), salvo un'eccezione
     dell'utente.
4. Se l'utente sceglie la issue, proponi il command qui sotto e fermati: lo invoca l'utente. Dopo
   il command fermati al gate CP01: mostra la spec all'utente e chiedi l'approvazione.
5. Dopo questa issue viene M0-04 «CMake Foundation» (`docs/BOOTSTRAP_PLAN.md`): eseguibili
   minimi `agentd` e `agentctl` in C17 che compilano, senza sessioni e senza PTY. La spec deve
   fissare toolchain e versioni (`docs/ARCHITECTURE.md`, vincolo 6;
   `docs/DEVELOPMENT_WORKFLOW.md` §10).

```text
/trinity:workflow:0_create-issue Documenti di AgentMux in italiano: traduci dall'inglese all'italiano docs/ (ADR compresi) e README.md, e cambia le regole che vogliono i documenti in inglese (in AgentMux e, se l'utente lo approva, in Trinity). Commit, PR e commenti sulla PR restano nella lingua della label git: del progetto Linear (oggi git:en). Identificatori, marcatori e chiavi restano identici (agentmux.handoff.v1, "written by Claude Code on the owner's instruction", "**CP06 closure — ICH-XXX"). È un cambio di governance: nuova versione 2.13-draft (DEVELOPMENT_WORKFLOW §12). Spec e piano richiedono l'approvazione dell'utente prima di qualsiasi lavoro.
```

## Decisioni che servono all'utente

- Per la traduzione dei documenti: una issue (§12) oppure un'eccezione con commit diretto su
  `main`.
- Se la regola sui documenti in inglese sta in Trinity, vale per tutti i progetti: cambiarla per
  tutti o solo per AgentMux.
- L'integrazione GitHub di Linear: al merge la issue deve andare in In Review, non in Done.
  Conviene cambiarla prima del prossimo merge (punto 1 di
  [memory/punti-aperti.md](memory/punti-aperti.md)).
- Il prefisso del branch delle prossime issue: `1_create-worktree` conosce solo `bug` e
  `improvments` (punto 3 di [memory/punti-aperti.md](memory/punti-aperti.md)).
