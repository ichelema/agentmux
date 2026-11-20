# RIPRESA — prompt per la prossima sessione

> Avvio: l'hook `SessionStart` di `.claude/settings.json` carica questo file nel contesto a ogni avvio e dopo
> `/clear`: basta scrivere «riprendi». Senza hook: «Leggi RIPRESA.md ed esegui il prompt». Scritto il 2026-11-20 su Linux, dopo la chiusura del pilota M0-01 (ICH-548, merge `9de59ec`). Il lavoro da
> fare è il **bootstrap M0** di `docs/BOOTSTRAP_PLAN.md`, una issue alla volta, con il workflow di Trinity.

## Stato e prossimo step (aggiornato il 2026-11-20, nessuna issue in corso)

- **M0-01 fatto:** ICH-548 «Repository Foundation / Controlled Pilot», PR #1, merge `9de59ec`. `main` contiene solo
  `README.md` e `.gitignore`. Worktree e branch rimossi. Chiusura e audit del pilota (AC-10) nei commenti di ICH-548.
- **Conflitti di governance trovati nel pilota** (Backlog, `related` a ICH-548):
  - ICH-557: command Trinity contro convenzioni AgentMux. **Decisione dell'utente del 2026-11-20: valgono le
    convenzioni Trinity** (branch `<prefix>/<issue-id>-<model>`, worktree `.claude/worktrees/<prefix>+<base-name>`,
    review lanciata dall'orchestratore su worktree detached `review+<base-name>`). Registrata su ICH-557.
  - ICH-558: l'automazione GitHub → Linear porta la issue a Done al merge, prima della chiusura CP06.
  - ICH-559: il reviewer legge i checkpoint dell'implementer nei commenti della issue.
- **Prossimo step: creare la issue M0-02 «Development Governance».** Nessuna issue è In Progress: lo slot WIP è
  libero. L'utente ha già scelto M0-02 come issue successiva (2026-11-20). Cosa fa la nuova sessione:
  1. esegue «Prima di iniziare» qui sotto e controlla che su Linear nessuna issue di Agentmux sia In Progress e che
     M0-02 non esista già; se esiste, non la crea di nuovo e riparte dal suo stato;
  2. lancia lei stessa il command `trinity:workflow:0_create-issue` (tool Skill) con la descrizione qui sotto;
  3. si ferma al gate CP01: mostra all'utente la spec della nuova issue e chiede l'approvazione. Niente piano,
     worktree o codice prima dell'approvazione.

  ```text
  /trinity:workflow:0_create-issue M0-02 Development Governance for project Agentmux (BOOTSTRAP_PLAN): track in the repository the seed governance documents (CLAUDE.md, docs/DEVELOPMENT_WORKFLOW.md, docs/specs/SESSION_CONTINUITY.md, .github/pull_request_template.md), aligned to the owner decision on ICH-557 (Trinity conventions for branch, worktree and review prevail), and resolve ICH-558 and ICH-559 in the same documents. Add .claude/worktrees/ to .gitignore. Use the AgentMux specification template (DEVELOPMENT_WORKFLOW "Specification template"). Spec and plan need owner approval before any implementation.
  ```

- **Decisioni dell'utente che servono per M0-02:**
  - prefisso del branch: `1_create-worktree` conosce solo `bug` e `improvments`; M0-02 non è né l'uno né l'altro;
  - impostazione dell'integrazione GitHub in Linear (ICH-558): al merge la issue deve andare in «In Review», non in
    «Done». La cambia solo l'utente, meglio prima del merge di M0-02.

## Contesto

- AgentMux: multiplexer e orchestratore di sessioni terminale per agent AI da CLI, su Linux. `agentd` (C17, PTY,
  `libghostty-vt`) e `agentctl` (C17, Notcurses). In M0 niente PTY, VT, Notcurses o SSH.
- Governance `2.9-draft`, non congelata. Documenti canonici: `CLAUDE.md`, `docs/DEVELOPMENT_WORKFLOW.md`,
  `docs/specs/SESSION_CONTINUITY.md`, `docs/ARCHITECTURE.md`, `docs/BOOTSTRAP_PLAN.md`, `docs/adr/`.
- Questi documenti sono ancora **file seed non tracciati** nel checkout principale (`CLAUDE.md`, `docs/`, `.github/`,
  `.mcp.json`, `.claude/`). Entrano in Git con M0-02 e M0-03, non prima e non fuori da una issue approvata.
  `.claude/settings.json` (hook di avvio che carica questo file, creato il 2026-11-20) va incluso in M0-02.
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
   eccezione** (scelta dell'utente del 2026-11-20): gli aggiornamenti di `RIPRESA.md` vanno su `main` con un commit
   diretto `docs(ripresa): ...` che contiene solo questo file.
5. Review indipendente: subagente read-only con contesto separato, su worktree detached; massimo 3 cicli per issue.
   Finding Critical/High bloccano il merge.
6. Commenti Linear, commit e PR in inglese (`lang:en`). `RIPRESA.md` resta in italiano.
7. Scrivi su Linear dalla sessione principale con `linear.py`: un subagente può essere bloccato dal classificatore
   dei permessi.
8. Scrivi un checkpoint `agentmux.handoff.v1` sulla issue a ogni checkpoint CP01–CP06, prima di chiudere la sessione,
   a contesto quasi pieno e su un blocco.
9. Nel checkout principale restano file seed non tracciati: dopo un merge che li aggiunge a `main`, il fast-forward
   fallisce finché l'utente non sposta le copie. Non usare `git pull` e non forzare.

## Procedura per ogni issue

1. `0_create-issue`, poi spec approvata (CP01) e piano approvato (CP02).
2. `1_create-worktree`, implementazione (`2_work-issue`), PR.
3. Review: `issue-cycle review` (un reviewer) o `loop` (due reviewer); la sceglie l'utente.
4. Merge dell'utente. Dopo il merge l'issue può già essere Done per l'automazione (ICH-558): controlla lo stato.
5. Chiusura: verifica `main`, registra lo SHA del merge, `5_remove-worktree`, commento di chiusura sulla issue.

## Chiusura del lavoro

Procedura da eseguire dopo ogni merge, anche da `5_remove-worktree` («Chiusura del lavoro nel progetto»):

1. Aggiorna la sezione «Stato e prossimo step» di questo file: issue chiusa con PR e SHA del merge, issue nate dal
   lavoro, decisioni dell'utente, prossimo step concreto e decisioni che servono.
2. Aggiorna la data nel titolo della sezione.
3. Commit diretto su `main` con il solo `RIPRESA.md` (`git add RIPRESA.md`, messaggio `docs(ripresa): ...` in
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
