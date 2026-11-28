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
   messaggi dei commit `docs(ripresa)` restano in inglese. `CLAUDE.md`, `handoff.md`, `memory/`,
   `docs/` e `README.md` sono in italiano (i documenti dal punto 15). Le issue già chiuse
   (ICH-548…ICH-588) restano in inglese: sono state scritte con la label `lang:en`, che il
   progetto non ha più. Il cambio è entrato con il commit diretto
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
    Ethereal la regola sta in §4, §7 e §13 di `docs/DEVELOPMENT_WORKFLOW.md`, in
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
    `git:en`, inglese): per questo `.github/pull_request_template.md` resta in inglese. Cambia
    il punto 8 per i documenti. Il perimetro tradotto è `docs/` (ADR compresi), `README.md` e,
    su richiesta dell'utente dopo la review, `.claude/rules/context7.md`; la regola sta nel punto
    «Lingua» della sezione «Regole» di `CLAUDE.md`. Trinity non cambia: la sua regola sulla
    lingua (skill `linear`, sezione «Lingua») copre solo issue e Git, e nessun command, skill o
    agente di Trinity vuole i documenti in inglese. Il testo che ne risulta è la
    governance `2.13-draft`; `2.12-draft` indica il testo fino al commit `ea7acaa`. Eccezione a
    §12 di `docs/DEVELOPMENT_WORKFLOW.md`, scelta dell'utente: commit diretto su `main` senza
    issue e senza PR, dopo una review Fable senza finding Critical o High (4 Medium e 13 Low,
    corretti tranne quello sotto). La traduzione di `.claude/rules/context7.md` chiude il finding
    F-16. Resta aperto: alcune coppie di termini non sono ancora allineate tra i file (F-08, Low).
16. **2026-11-23 — Commenti nel codice in inglese.** Scelta dell'utente: i commenti nel codice
    seguono la label `git:` del progetto Linear, come commit e PR; con `git:en` sono in inglese.
    Motivo: il codice viaggia con commit e PR, e gli identificatori C e le API delle librerie
    sono in inglese. Prima nessuna regola, né in Ethereal né in Trinity, copriva i commenti nel
    codice. La regola sta nel punto «Lingua» della sezione «Regole» di `CLAUDE.md`; vale dal
    primo codice, M0-04. Commit diretto su `main`, su istruzione dell'utente. La governance resta
    `2.13-draft` per scelta dell'utente: la regola copre un caso nuovo e non cambia obblighi
    esistenti. `2.13-draft` indica quindi il testo dei commit `944b51b` e di questo commit.
17. **2026-11-28 — Il progetto AgentMux diventa Ethereal.** Scelta dell'utente. Negli
    identificatori il nome è `Ethereal` con la maiuscola: repository GitHub `ichelema/Ethereal`,
    bank Hindsight `Ethereal`, tag `repo:Ethereal`, progetto Linear «Ethereal». Lo shim di
    Hindsight ricava il bank dal nome del remote `origin` e mantiene la maiuscola. La cartella
    diventa `20261120_ethereal`. Cambiano anche il marcatore `agentmux.handoff.v1` →
    `ethereal.handoff.v1` e `.agentmux-local/` → `.ethereal-local/`; i componenti `agentd` e
    `agentctl` restano invariati. Le issue Done conservano titoli e testi storici con il nome
    vecchio, e i loro checkpoint usano il marcatore vecchio. Commit diretto su `main`, su
    istruzione dell'utente; la governance passa a `2.14-draft`.
18. **2026-11-28 — Quando consultare la memoria; governance `2.15-draft`.** Scelta dell'utente,
    dopo l'analisi dei transcript. In notcursesruby (69 sessioni dal 2026-11-13) il `recall`
    c'è in 12 delle 29 sessioni con `2_work-issue`, e 55 domande all'utente su 68 arrivano
    senza `recall` né letture di `memory/`. In Ethereal (10 sessioni) nessuna delle ultime 3 fa
    un `recall`. In nessuno dei due progetti `reflect` è mai stato usato. La sezione «Memoria»
    di `CLAUDE.md` ha ora la sottosezione «Quando consultare la memoria»:
    - blocco «Memoria consultata» quando si presenta il piano per CP02 e in `2_work-issue`;
    - `memory/decisioni.md`, pagina «Decisions» e `recall` prima di una domanda all'utente, ma
      non per le approvazioni;
    - `memory/insidie.md`, `memory/punti-aperti.md` e `recall` prima del primo tentativo di
      correzione di un fallimento inatteso;
    - pagine lette con `search_pages` e `get_page`;
    - `reflect` solo per modifiche di architettura e ADR.

    Il blocco «Memoria consultata» non va nel piano su Linear, perché il reviewer indipendente
    legge il piano e non deve ricevere fatti di Hindsight (punto 13). §7 di
    `docs/DEVELOPMENT_WORKFLOW.md` rimanda alla sottosezione. In Trinity `search_pages`,
    `get_page` e `get_mental_model` sono nell'allowlist MCP dal commit `ed31283`.
    `get_mental_model` non è nelle istruzioni: restituisce anche il `reflect_response`, circa
    25.000 caratteri per una pagina di 3.000. Eccezione a §12 di `docs/DEVELOPMENT_WORKFLOW.md`,
    scelta dell'utente: commit diretto su `main` senza issue, senza PR e senza review.
    `2.14-draft` indica il testo fino al commit `5b1130c`.
