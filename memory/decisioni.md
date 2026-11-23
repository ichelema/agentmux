# Decisioni

Ogni voce: data, decisione, motivo o fonte. Le più recenti in fondo. Le regole operative che ne
derivano sono in [`CLAUDE.md`](../CLAUDE.md).

1. **2026-11-20 — L'utente invoca i command del workflow.** A fine fase l'agente scrive il prossimo
   command con gli argomenti reali e si ferma. Non usa il tool Skill e non esegue il file del
   command.
2. **2026-11-20 — Basta l'approvazione in chat.** L'utente non scrive commenti di approvazione su
   Linear. L'agente registra ogni approvazione sulla issue con un commento marcato «written by
   Claude Code on the owner's instruction», con decisione e data. Motivo: scrivere i commenti a
   mano rallenta troppo il lavoro.
3. **2026-11-20 — Commit diretti su `main` per il file di ripresa.** È l'unica eccezione al divieto
   di push su `main`: un commit che contiene solo il file di ripresa, con messaggio inglese
   `docs(ripresa): ...`. Registrata su ICH-557; primo commit `2361f8e`. Il punto 7 la estende a
   `memory/`.
4. **2026-11-20 — Convenzioni Trinity (ICH-557, risolta da PR #2).** Branch, worktree e review
   seguono i command Trinity: il testo è in `docs/DEVELOPMENT_WORKFLOW.md` §1 e §4.
5. **2026-11-21 — `RIPRESA.md` diventa `handoff.md`** (commit `879e99e`). Il commit è diretto su
   `main` e tocca anche altri file: eccezione una tantum dell'utente durante il setup. Lo stesso
   giorno l'hook `SessionStart` smette di iniettare il file e lo indica soltanto (commit
   `315a842`).
6. **2026-11-22 — `hindsight.config.json` resta fuori dal repository** ed è in `.gitignore` (OQ-2
   di ICH-588): è un file locale. Superata il 2026-11-23 dal punto 12.
7. **2026-11-22 — Riordino dei documenti di sessione.** `handoff.md` contiene solo il prossimo
   passo. Storia, decisioni, insidie e punti aperti stanno in `memory/`, con l'indice
   `memory/memory.md`. `CLAUDE.md` contiene solo struttura, ripresa, workflow, regole, memoria e
   chiusura. L'eccezione `docs(ripresa)` copre ora `handoff.md` e `memory/`: il §1 di
   `docs/DEVELOPMENT_WORKFLOW.md` è allineato. Motivo: `handoff.md` accumulava la storia del lavoro
   ed era difficile da usare. Il riordino è un commit diretto su `main`, eccezione una tantum
   dell'utente. Il testo di governance che ne risulta ha l'identificatore `2.11-draft` (punto 9).
8. **2026-11-22 — Lingua.** Scelta dell'utente: due label di progetto separano le lingue. `lang:it`
   mette in italiano issue e commenti su Linear; `git:en` mette in inglese commit, PR e commenti
   sulla PR. Le regole stanno nella sezione «Lingua» della skill `linear` di Trinity (commit
   `48afcfe`). Motivo: scegliere in modo separato la lingua delle issue e quella di GitHub. Anche i
   messaggi dei commit `docs(ripresa)` restano in inglese. `CLAUDE.md`, `handoff.md` e `memory/`
   sono in italiano. Le issue già chiuse (ICH-548…ICH-588) restano in inglese: sono state scritte
   con la label `lang:en`, che il progetto non ha più. Il cambio è entrato con il commit diretto
   su `main` `bffbbd7`, su istruzione dell'utente.
9. **2026-11-23 — Governance `2.11-draft`.** Una review GPT dei documenti di sessione ha trovato
   che `2.10-draft` indicava due testi diversi, contro il §12 di `docs/DEVELOPMENT_WORKFLOW.md`.
   Scelta dell'utente: il testo con il riordino, l'eccezione `memory/`, la lingua e le correzioni
   della review ha l'identificatore `2.11-draft`, anche nelle righe di stato dei quattro documenti
   di baseline che lo citano. `2.10-draft` indica il testo fino al commit `990819a`; le issue
   chiuse restano fissate alla loro versione. Le correzioni delle review GPT e Fable sono entrate
   con un solo commit diretto su `main`, su istruzione dell'utente. I commit già pubblicati
   `5ad5099`, `049af9d` e `bffbbd7` portano ancora `2.10-draft`: il loro testo è intermedio.
10. **2026-11-23 — Workflow completo.** Scelte dell'utente dopo la stessa review: la catena dei
    command comprende `2_work-simplify` e `2_work-verify` (facoltativo); le due modalità di
    `3_independent-review` avviano entrambe un subagente (`hard` Fable, `soft` Sonnet);
    `issue-cycle` si propone solo dopo CP02, per una sola issue, e CP05 si controlla a parte. Il
    testo è in `CLAUDE.md` e nel §4 di `docs/DEVELOPMENT_WORKFLOW.md`.
11. **2026-11-23 — Hook `SessionStart` invariato.** L'hook di `.claude/settings.json` indica
    `$CLAUDE_PROJECT_DIR/handoff.md`. In una sessione avviata dentro il worktree di una issue
    indicherebbe la copia ferma al punto in cui è nato il branch (finding N10 della review Fable).
    Scelta dell'utente: lasciare l'hook com'è, perché le sessioni partono dalla root e `CLAUDE.md`
    dice di leggere `handoff.md` solo da `<repo-root>`. Superata il 2026-11-23 dal punto 14.
12. **2026-11-23 — Configurazione condivisa tra le macchine.** Scelta dell'utente:
    `hindsight.config.json` entra in Git, e il contenuto di `.claude/settings.local.json`
    (`enabledMcpjsonServers` con `context7`) passa in `.claude/settings.json`. Motivo: i due file
    servono anche sulle altre macchine. Prima del commit il controllo non ha trovato segreti. Il
    repository è pubblico: in questi file non vanno mai segreti o dati personali.
    `.claude/settings.local.json` resta in `.gitignore` per le impostazioni di una sola macchina.
    Il cambio è solo di configurazione: `CLAUDE.md` e `docs/` non cambiano, quindi la governance
    resta `2.11-draft`. Commit diretto su `main`, su istruzione dell'utente.
13. **2026-11-23 — Il reviewer indipendente non usa Hindsight; governance `2.12-draft`.** Scelta
    dell'utente: il reviewer non usa Hindsight in nessun modo e legge solo la memoria in file nel
    suo worktree di review, come ipotesi da controllare sul codice. I file di memoria che la PR
    cambia fanno parte del changeset. Motivo: Hindsight non garantisce che il reviewer scarti le
    memorie sulla PR in review. In Trinity la regola sta nel corpo di `agents/reviewer.md`
    (commit `af2072b`, `f77d009` ed `e85f52b`): in Claude Code 2.1.330 una skill precaricata con
    `skills:` non si può nascondere alla sessione principale. La review Fable ha mostrato che il
    divieto esplicito serve, perché il reviewer può arrivare a Hindsight con la skill e `curl`. In
    AgentMux la regola sta in §4, §7 e §13 di `docs/DEVELOPMENT_WORKFLOW.md`, in
    `docs/specs/SESSION_CONTINUITY.md` e nella sezione «Memoria» di `CLAUDE.md`. Commit diretto su
    `main` `62604aa`, su istruzione dell'utente, dopo una review Fable senza finding Critical o
    High. `2.11-draft` indica il testo fino al commit `02097a2`.
14. **2026-11-23 — L'hook `SessionStart` rimanda a «Ripresa del lavoro».** Il commit `02097a2`,
    fatto dalla sessione di notcursesruby su richiesta dell'utente (lo dice quella sessione),
    cambia il testo dell'hook: non fa più leggere `handoff.md` ed eseguire il prompt, ma rimanda
    alla sezione «Ripresa del lavoro» di `CLAUDE.md`. Motivo, dal messaggio del commit: il prompt
    di `handoff.md` saltava i controlli obbligatori che la sezione chiede prima di agire.
    Condizione e matcher dell'hook non cambiano. `CLAUDE.md` descrive l'hook nuovo dal commit
    `62604aa`.
15. **2026-11-23 — Documenti in italiano.** Scelta dell'utente: tutti i documenti del repository
    passano dall'inglese all'italiano, e le regole che li vogliono in inglese cambiano. Commit, PR
    e commenti sulla PR restano nella lingua della label `git:` del progetto Linear (oggi
    `git:en`, inglese). Cambia il punto 8 per i documenti. Da applicare: è il primo lavoro di
    `handoff.md`.
