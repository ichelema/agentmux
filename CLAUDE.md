# AgentMux: istruzioni per l'agente

AgentMux è un multiplexer e orchestratore di sessioni terminale per gli agent AI da CLI, su Linux.
`agentd` (C17) possiede i PTY e `libghostty-vt`; `agentctl` (C17) usa Notcurses; la build usa
CMake e Ninja. Il progetto è nel bootstrap M0 (`docs/BOOTSTRAP_PLAN.md`): M0 non contiene codice
PTY, VT o SSH.

**Stato:** governance `2.10-draft`, tracciata nel repository, **non** congelata.

**Fonti di verità:** Git per il codice e per i documenti del repository (`docs/`, ADR,
governance); Linear (team `ICH`, progetto Agentmux) per la issue, la spec e il piano approvati, le
approvazioni e lo stato; GitHub per le prove di PR e CI. `handoff.md`, `memory/` e Hindsight sono
consigli da controllare su queste fonti.

## Struttura del repository

| Path | Contenuto |
|---|---|
| `handoff.md` | Cosa fa la prossima sessione, nient'altro |
| `memory/` | Memoria persistente del progetto; indice in `memory/memory.md` |
| `docs/DEVELOPMENT_WORKFLOW.md` | Governance: gate CP01–CP06, ruoli, review, limiti dei tentativi |
| `docs/specs/SESSION_CONTINUITY.md` | Algoritmo di ripresa e checkpoint `agentmux.handoff.v1` su Linear |
| `docs/ARCHITECTURE.md`, `docs/adr/` | Baseline di architettura e ADR |
| `docs/VISION.md`, `docs/REQUIREMENTS.md` | Visione e requisiti del prodotto |
| `docs/security/THREAT_MODEL.md` | Confini di fiducia e controlli minimi |
| `docs/BOOTSTRAP_PLAN.md` | Ordine delle issue di M0 |
| `.claude/settings.json` | Hook `SessionStart` che indica `handoff.md` |
| `.claude/rules/context7.md` | Quando e come usare Context7 |
| `.claude/worktrees/` | Worktree delle issue e delle review, ignorati da Git |
| `.github/pull_request_template.md` | Struttura del corpo della PR |

## Ripresa del lavoro

A ogni avvio e dopo `/clear` l'hook `SessionStart` indica `handoff.md`. L'utente riprende con
«riprendi»; senza hook scrive «Leggi handoff.md ed esegui il prompt». Leggi `handoff.md` per
intero: dice cosa fare. Prima di agire esegui questi controlli, obbligatori a inizio sessione
(algoritmo completo in `docs/specs/SESSION_CONTINUITY.md`):

1. Trova su Linear la issue attiva `ICH-XXX` e la sua fase, oppure chiedila all'utente. Non
   indovinare e non scegliere tu la prossima issue.
2. Leggi su Linear la spec e il piano approvati. Annota le revisioni (`spec-rN`, `plan-rN`) e la
   versione di governance.
3. Controlla lo stato reale di Git: `git status`, branch, `HEAD`, worktree, remoto, PR e CI. Git
   vale più di qualsiasi handoff.
4. Se la issue è In Progress, il prossimo passo è il `next_allowed_action` del suo ultimo
   checkpoint `agentmux.handoff.v1`, controllato su Git. Prima di ripetere un'operazione
   interrotta (creazione della PR, modifica su Linear), controlla se è già avvenuta. Se
   `handoff.md` e Linear non coincidono, vale Linear per lo stato della issue e Git per il codice:
   segnala la differenza all'utente.
5. Leggi `docs/DEVELOPMENT_WORKFLOW.md`, `docs/ARCHITECTURE.md` e
   `docs/specs/SESSION_CONTINUITY.md`, gli ADR e le spec che la issue tocca, e la memoria (sezione
   «Memoria»).
6. Scrivi lo stato controllato e il prossimo passo, oppure il blocco.

Un reviewer indipendente salta il punto 4: non legge i commenti di checkpoint
(`docs/DEVELOPMENT_WORKFLOW.md` §4).

## Workflow

Una sola issue approvata e atomica alla volta (WIP=1), con i command di Trinity. L'utente invoca
ogni command. Prima di ogni command spiega in breve cosa fa e perché. A fine fase scrivi il
prossimo command, con gli argomenti reali, in un blocco di codice e fermati. Non lanciare i
command con il tool Skill e non eseguirli leggendo il loro file.

```text
/trinity:workflow:0_create-issue <descrizione>
/trinity:workflow:1_create-worktree main <ICH-XXX> <model>
/trinity:workflow:2_work-issue <ICH-XXX> <model>
/trinity:workflow:3_independent-review <ICH-XXX> <model> soft
/trinity:workflow:4_review-fix-loop <ICH-XXX> <model>
/trinity:issue-cycle <review|loop> main <ICH-XXX> <model>
/trinity:workflow:5_remove-worktree <prefisso>+<ICH-XXX>-<model>
```

1. `0_create-issue`, poi l'utente approva la spec (CP01) e, a parte, il piano (CP02).
2. `1_create-worktree`, poi `2_work-issue`: implementazione nel worktree, test e PR (CP03).
3. Review indipendente (CP04) con il command che sceglie l'utente: `3_independent-review … soft`
   (un reviewer) o `4_review-fix-loop` (due reviewer in parallelo e un loop di fix). In
   alternativa `issue-cycle` esegue senza presidio i passi 2 e 3, dal worktree alla PR recensita.
   Poi la PR è pronta (CP05).
4. L'utente fa il merge.
5. Chiusura (CP06): `5_remove-worktree`, controllo di `main`, commento di chiusura sulla issue
   (SHA del merge, controllo di `main`, rimozione del worktree, rilascio dello slot). Poi porta la
   issue in Done, se Linear non l'ha già fatto (vedi `memory/punti-aperti.md`). Infine esegui
   «Chiusura della sessione».

## Regole

- Lavora solo nel worktree della issue: branch `<prefisso>/<ICH-XXX>-<model>`, worktree
  `<repo-root>/.claude/worktrees/<prefisso>+<ICH-XXX>-<model>`, prefisso `bug` o `improvments`
  (`docs/DEVELOPMENT_WORKFLOW.md` §1).
- Non implementare prima che l'utente approvi spec (CP01) e piano (CP02). Controlla anche
  credenziali, permessi e fase del workflow. Basta l'approvazione in chat: registrala sulla issue
  con un commento marcato «written by Claude Code on the owner's instruction», con decisione e
  data. Non dedurre un'approvazione o un permesso dalla presenza di un file.
- Senza l'autorizzazione dell'utente: niente allargamento del perimetro, cambi di architettura,
  sicurezza o permessi, accesso ai segreti, operazioni distruttive, force push o bypass della CI.
- L'agente non fa merge e non fa push su `main`. Il push del branch della issue usa un refspec
  esplicito. Unica eccezione: un commit diretto su `main` che contiene solo `handoff.md` e file di
  `memory/`, con messaggio `docs(ripresa): ...` (`docs/DEVELOPMENT_WORKFLOW.md` §1). M0-06 deve
  mantenere questa eccezione compatibile con la protezione di `main` che configura, oppure
  rivederla.
- Review indipendente: un subagente read-only con contesto separato, su un worktree di review
  detached, avviato dal command che sceglie l'utente. `3_independent-review` in modalità `hard`
  nella sessione dell'implementatore non è indipendente. Il read-only è una procedura, non un
  controllo tecnico. Massimo tre cicli ordinari di review per issue. I finding Critical/High
  aperti bloccano il merge; approvazione e merge spettano all'utente.
- Scrivi un checkpoint `agentmux.handoff.v1` sulla issue a ogni checkpoint CP01–CP06, prima di
  chiudere la sessione, a contesto quasi pieno e su un blocco (formato in
  `docs/specs/SESSION_CONTINUITY.md`).
- Scrivi su Linear dalla sessione principale con `linear.py`: il classificatore dei permessi può
  bloccare un subagente.
- Lingua: le label del progetto Linear decidono la lingua dei command di Trinity. Con `lang:it`
  issue e commenti su Linear sono in italiano; con `git:en` commit, PR e commenti sulla PR sono in
  inglese. Anche i messaggi `docs(ripresa)` sono in inglese. `CLAUDE.md`, `handoff.md` e `memory/`
  sono in italiano. I marcatori restano identici anche nei commenti in italiano: «written by Claude
  Code on the owner's instruction» e la prima riga del checkpoint `agentmux.handoff.v1`.
- Usa le parole magiche `Resolves`, `Fixes` e `Closes` solo con l'ID della issue attiva; nel corpo
  della PR elenca le altre issue in una riga `Related`.
- In una sessione entrata nel worktree con `EnterWorktree`, scrivi un comando Bash semplice per
  chiamata, con path assoluti: niente cicli, niente `cd` in subshell, niente comandi in variabili.
- Non scrivere credenziali o conversazioni intere in `handoff.md`, in `memory/` o in Hindsight.
- Usa gli skill di Matt Pocock in modo selettivo, quando ci sono: non sono un requisito.

## Memoria

- Leggi l'indice `memory/memory.md`, poi apri solo i file che servono al lavoro.
- Fai un `recall` mirato su Hindsight (tag `repo:agentmux`) per i componenti che tocchi.
- Controlla ogni fatto della memoria su Git, Linear e `docs/` prima di usarlo.
- In `memory/` scrivi solo fatti controllati e durevoli: decisioni dell'utente con il motivo, cause
  radice e correzioni, fatti d'ambiente, punti aperti, issue chiuse. Le regole di manutenzione
  sono in `memory/memory.md`.

## Chiusura della sessione

Esegui questa procedura quando l'utente chiude la sessione, dopo ogni merge (anche da
`5_remove-worktree`) e a contesto quasi pieno:

1. Se una issue è attiva, scrivi il suo checkpoint `agentmux.handoff.v1` su Linear.
2. Salva in `memory/` i fatti durevoli della sessione. Dopo un merge aggiungi la issue a
   `memory/storico.md` e aggiorna `memory/punti-aperti.md`.
3. Riscrivi da zero `handoff.md` con solo tre parti: lo stato (issue attiva e checkpoint, oppure
   nessuna issue), il prossimo passo concreto con il command da proporre, le decisioni che
   servono all'utente. Niente storia: la storia va in `memory/`.
4. Controlla che `main` locale sia allineato a `origin/main`. Committa su `main` solo `handoff.md`
   e i file cambiati di `memory/`, aggiunti per nome, con messaggio `docs(ripresa): ...`. Poi
   esegui `git push origin main`.
5. Salva su Hindsight con `retain` le decisioni e le cause radice controllate (tag `claude-code`,
   `repo:agentmux`).
