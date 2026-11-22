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
   di ICH-588): è un file locale.
7. **2026-11-22 — Riordino dei documenti di sessione.** `handoff.md` contiene solo il prossimo
   passo. Storia, decisioni, insidie e punti aperti stanno in `memory/`, con l'indice
   `memory/memory.md`. `CLAUDE.md` contiene solo struttura, ripresa, workflow, regole, memoria e
   chiusura. L'eccezione `docs(ripresa)` copre ora `handoff.md` e `memory/`: il §1 di
   `docs/DEVELOPMENT_WORKFLOW.md` è allineato. Motivo: `handoff.md` accumulava la storia del lavoro
   ed era difficile da usare. Il riordino è un commit diretto su `main`, eccezione una tantum
   dell'utente. L'identificatore di governance resta `2.10-draft`, come per la rinomina `879e99e`.
8. **2026-11-22 — Lingua.** Scelta dell'utente: solo le issue sono in inglese (label di progetto
   `lang:en`). `CLAUDE.md`, `handoff.md` e `memory/` sono in italiano. Commenti, commit e PR
   restano in inglese, perché i command di Trinity usano la lingua del progetto Linear. Anche i
   messaggi dei commit `docs(ripresa)` restano in inglese (scelta dell'utente).
