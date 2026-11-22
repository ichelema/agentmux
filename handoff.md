# Handoff — prompt per la prossima sessione

> Avvio: l'hook `SessionStart` di `.claude/settings.json`, a ogni avvio e dopo `/clear`, indica questo file e lo fa
> leggere solo se chiedi di riprendere: basta scrivere «riprendi». Senza hook: «Leggi handoff.md ed esegui il prompt». Scritto il 2026-11-20 su Linux; ultimo aggiornamento il 2026-11-22, dopo la chiusura di M0-03 (ICH-588, merge `883f86a`). Il lavoro da
> fare è il **bootstrap M0** di `docs/BOOTSTRAP_PLAN.md`, una issue alla volta, con il workflow di Trinity.

## Stato e prossimo step (aggiornato il 2026-11-22, nessuna issue in corso)

- **M0-01 fatto:** ICH-548 «Repository Foundation / Controlled Pilot», PR #1, merge `9de59ec`.
- **M0-02 fatto:** ICH-566 «Track and align the development governance documents», PR #2, merge `ba53aa8` (head
  `fe89811`, recensita al ciclo 2; cicli usati 2 su 3). `main` traccia `CLAUDE.md`, `docs/DEVELOPMENT_WORKFLOW.md`,
  `docs/specs/SESSION_CONTINUITY.md`, `.github/pull_request_template.md` e `.claude/settings.json`; governance
  `2.10-draft`. Chiusura CP06 nel commento di chiusura di ICH-566. Worktree, branch locale e branch remoto rimossi.
- ICH-557, ICH-558 e ICH-559 sono risolte da PR #2: sono Done, con un commento di risoluzione ciascuna.
- **Rinomina del 2026-11-21:** `RIPRESA.md` ora si chiama `handoff.md`, con un commit diretto su `main` che
  aggiorna anche `CLAUDE.md`, `docs/DEVELOPMENT_WORKFLOW.md`, `README.md` e l'hook di `.claude/settings.json`. È
  un'eccezione alla regola 4, autorizzata dall'utente una sola volta durante il setup del progetto: d'ora in poi
  valgono tutte le regole. L'eccezione dei commit diretti vale ora per `handoff.md`, con lo stesso prefisso
  `docs(ripresa)`.
- **M0-03 fatto:** ICH-588 «Track and review the architecture baseline documents (M0-03)», PR #3, merge `883f86a`
  (head `c7ae1cc`, recensita al ciclo 1 con review `soft`, nessun finding; cicli usati 1 su 3). `main` traccia i sei
  documenti di architettura (baseline approvata dall'utente, ADR-0001 Accepted), `.mcp.json` e
  `.claude/rules/context7.md`. `hindsight.config.json` resta fuori dal repository ed è in `.gitignore` (OQ-2 di
  ICH-588). Chiusura CP06 nel commento di chiusura di ICH-588. Worktree di implementazione e di review, branch locale e
  branch remoto rimossi.
- **Note residue della review di ICH-588** (nessuna issue aperta, decide l'utente): parte del testo storico di
  `docs/BOOTSTRAP_PLAN.md` («Prerequisites», «Proposed M0 issue order») è superato; le righe di stato dei cinque
  documenti di baseline citano `governance 2.10-draft` e vanno aggiornate al prossimo cambio di versione.
- **Deviazione aperta (E6 di ICH-566, E5 di ICH-588):** l'integrazione GitHub di Linear porta ancora la issue a Done al
  merge, non a In Review. È successo con PR #2 e di nuovo con PR #3. La prova CP06 è il commento di chiusura, non lo
  stato Done. L'impostazione la cambia solo l'utente.
- **Lezione di M0-03:** in una sessione entrata nel worktree con `EnterWorktree`, il controllo di isolamento rifiuta i
  comandi Bash composti con `git` (cicli `for`, `cd` in subshell, variabili per il comando). Scrivi un comando semplice
  per chiamata, con path assoluti.
- **Lezione di M0-02:** non scrivere parole magiche (`Resolves`, `Fixes`, `Closes` seguite da un ID) nei messaggi di
  commit per issue diverse da quella attiva. Linear collega quelle issue alla PR e le porta In Progress; rimuovere il
  link non basta, perché ogni sync della PR lo ricrea. Nel corpo della PR usa una riga «Related» senza parola magica.
- **Rischio rinviato a M0-06** (spec-r2 di ICH-566, OQ-9): un agente può scrivere istruzioni in `handoff.md` senza
  review, e l'hook di avvio le fa eseguire alla ripresa (finding F6). M0-06 sceglie protezione di `main`, CODEOWNERS o
  una regola di diff visibile all'utente, e mantiene compatibile l'eccezione di `handoff.md` (regola 4).
- **Prossimo step: l'utente sceglie la prossima issue** (regola 1). Lo slot WIP è libero. `docs/BOOTSTRAP_PLAN.md`
  prevede M0-04 «CMake Foundation»: eseguibili minimi `agentd` e `agentctl` in C17 che compilano, nessuna sessione e
  nessun PTY. È la prima issue con codice: la spec deve fissare toolchain e versioni (`docs/ARCHITECTURE.md`, vincolo 6;
  `docs/DEVELOPMENT_WORKFLOW.md` §10). Cosa fa la nuova sessione:
  1. esegue «Prima di iniziare» qui sotto e controlla che su Linear nessuna issue di Agentmux sia In Progress;
  2. chiede all'utente la prossima issue. Se l'utente sceglie M0-04, controlla che la issue non esista già, propone
     il command qui sotto in un blocco di codice e si ferma: lo invoca l'utente (regola 10);
  3. dopo il command si ferma al gate CP01: mostra all'utente la spec e chiede l'approvazione.

  ```text
  /trinity:workflow:0_create-issue M0-04 CMake Foundation for project Agentmux (BOOTSTRAP_PLAN): minimal agentd and agentctl C17 executables that build with CMake and Ninja. No sessions, no PTYs, no libghostty-vt, Notcurses or SSH code. Decide and pin the toolchain versions for this step (ARCHITECTURE constraint 6, DEVELOPMENT_WORKFLOW §10). Use the AgentMux specification template (DEVELOPMENT_WORKFLOW "Specification template"). Spec and plan need owner approval before any implementation.
  ```

- **Decisioni dell'utente che servono:**
  - la prossima issue;
  - l'integrazione GitHub in Linear (OQ-2 di ICH-566, OQ-5 di ICH-588): al merge la issue deve andare in «In Review»,
    non in «Done»; meglio cambiarla prima del prossimo merge;
  - il prefisso del branch: `1_create-worktree` conosce solo `bug` e `improvments` (M0-02 e M0-03 hanno usato
    `improvments`).

## Contesto

- AgentMux: multiplexer e orchestratore di sessioni terminale per agent AI da CLI, su Linux. `agentd` (C17, PTY,
  `libghostty-vt`) e `agentctl` (C17, Notcurses). In M0 niente PTY, VT, Notcurses o SSH.
- Governance `2.10-draft`, non congelata. Documenti canonici: `CLAUDE.md`, `docs/DEVELOPMENT_WORKFLOW.md`,
  `docs/specs/SESSION_CONTINUITY.md`, `docs/ARCHITECTURE.md`, `docs/BOOTSTRAP_PLAN.md`, `docs/adr/`.
- I documenti di governance sono tracciati da M0-02, quelli di architettura da M0-03. Nel checkout principale non
  restano file seed non tracciati: `hindsight.config.json` è locale e ignorato da `.gitignore`.
- Le copie seed degli 8 file tracciati da M0-03 sono in `~/.claude/tmp/agentmux-seed-bak/ICH-588/`.
- Le copie seed dei 5 file tracciati da M0-02 sono in `~/.claude/tmp/agentmux-seed-bak/m0-02/`. Quella cartella
  contiene anche directory vuote con nomi sbagliati, create per errore il 2026-11-20: si possono rimuovere.
- Linear: team ICH, progetto **Agentmux** (label di progetto `lang:en`). GitHub: `ichelema/agentmux`, pubblico,
  `main` senza protezione e senza CI (arriva con M0-06).
- Le copie seed di `README.md` e `.gitignore` sono in `~/.claude/tmp/agentmux-seed-bak/`; il `README.md` seed ha la
  mappa dei documenti, utile per M0-02.

## Prima di iniziare

1. Leggi `CLAUDE.md` ed esegui la sua procedura di avvio (issue e fase da Linear, spec e piano approvati, stato Git,
   ultimo checkpoint). La procedura dettagliata è in `docs/specs/SESSION_CONTINUITY.md`.
2. Se c'è una issue In Progress nel progetto Agentmux, il prossimo step è il campo `next_allowed_action` del suo
   ultimo checkpoint `agentmux.handoff.v1`, verificato contro Git. Se questo file e Linear non coincidono, vale
   Linear per lo stato della issue e Git per il codice: segnala la differenza all'utente.
3. Recall mirato su Hindsight (`mcp__hindsight__recall`, tag `repo:agentmux`). Le memorie sono consigli: verificale.

## Regole vincolanti

1. WIP=1: una sola issue attiva. Non scegliere da solo la issue successiva: la sceglie l'utente.
2. Prima di implementare servono spec (CP01) e piano (CP02) approvati dall'utente. L'approvazione data in sessione
   basta: registrala tu sulla issue con un commento «written by Claude Code on the owner's instruction».
3. Convenzioni Trinity per branch, worktree e review (decisione su ICH-557).
4. L'agent non fa merge e non fa push su `main`. Push del branch di lavoro solo con refspec esplicito. **Unica
   eccezione** (scelta dell'utente del 2026-11-20): gli aggiornamenti di `handoff.md` vanno su `main` con un commit
   diretto `docs(ripresa): ...` che contiene solo questo file.
5. Review indipendente: subagente read-only con contesto separato, su worktree detached; massimo 3 cicli per issue.
   Finding Critical/High bloccano il merge.
6. Commenti Linear, commit e PR in inglese (`lang:en`). `handoff.md` resta in italiano.
7. Scrivi su Linear dalla sessione principale con `linear.py`: un subagente può essere bloccato dal classificatore
   dei permessi.
8. Scrivi un checkpoint `agentmux.handoff.v1` sulla issue a ogni checkpoint CP01–CP06, prima di chiudere la sessione,
   a contesto quasi pieno e su un blocco.
9. Nel checkout principale restano file seed non tracciati: dopo un merge che li aggiunge a `main`, il fast-forward
   fallisce finché le copie restano al loro posto. Non usare `git pull` e non forzare. Sposta le copie in
   `~/.claude/tmp/agentmux-seed-bak/<issue>/` solo con il consenso dell'utente (dato per M0-02 il 2026-11-20).
10. I command del workflow (`0_create-issue` … `5_remove-worktree`, `issue-cycle`) li invoca l'utente (scelta
    dell'utente del 2026-11-20). A fine fase l'agente scrive il prossimo command, con gli argomenti reali, in un
    blocco di codice, e si ferma. Non lancia i command con il tool Skill e non li esegue leggendo il file del command.

## Procedura per ogni issue

`<model>` è il modello scelto dall'utente; `<ICH-XXX>` è la issue attiva. L'utente invoca ogni command (regola 10).

```text
/trinity:workflow:0_create-issue <descrizione>
/trinity:workflow:1_create-worktree main <ICH-XXX> <model>
/trinity:workflow:2_work-issue <ICH-XXX> <model>
/trinity:workflow:3_independent-review <ICH-XXX> <model>
/trinity:workflow:4_review-fix-loop <ICH-XXX> <model>
/trinity:workflow:5_remove-worktree <prefisso>+<ICH-XXX>-<model>
```

0. Prima di ogni command spiega in breve cosa farà e perché, poi proponi il command in un blocco di codice.
1. `0_create-issue`, poi spec approvata (CP01) e piano approvato (CP02).
2. `1_create-worktree`, poi `2_work-issue`: implementazione e PR.
3. Review: `3_independent-review` (un reviewer) o `4_review-fix-loop` (due reviewer in parallelo, loop di fix); la
   sceglie l'utente.
4. Merge dell'utente. Finché OQ-2 non è applicato, dopo il merge l'issue è già Done per l'automazione: controlla
   lo stato e registra la deviazione E6 nel commento di chiusura.
5. Chiusura: verifica `main`, registra lo SHA del merge, `5_remove-worktree`, commento di chiusura sulla issue.

## Chiusura del lavoro

Procedura da eseguire dopo ogni merge, anche da `5_remove-worktree` («Chiusura del lavoro nel progetto»):

1. Aggiorna la sezione «Stato e prossimo step» di questo file: issue chiusa con PR e SHA del merge, issue nate dal
   lavoro, decisioni dell'utente, prossimo step concreto e decisioni che servono.
2. Aggiorna la data nel titolo della sezione.
3. Commit diretto su `main` con il solo `handoff.md` (`git add handoff.md`, messaggio `docs(ripresa): ...` in
   inglese), poi `git push origin main`. Prima controlla che `main` locale sia allineato a `origin/main`.
4. Retain su Hindsight delle decisioni e delle cause radice verificate (tag `claude-code`, `repo:agentmux`).

## Comandi

```bash
# Linear (da questa cartella; la chiave sta in skills/linear/.env)
cd /home/ichelema/.claude/skills/trinity/skills/linear
python scripts/linear.py query '{ issues(filter:{project:{name:{eq:"Agentmux"}}}){ nodes{ identifier title state{name} } } }'
python scripts/linear.py mutation 'mutation($input: CommentCreateInput!){ commentCreate(input:$input){ success comment{ url } } }' --variables @<file.json>

# GitHub
gh pr list -R ichelema/agentmux --state all

# Git: stato reale
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux worktree list
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux status --short
```
