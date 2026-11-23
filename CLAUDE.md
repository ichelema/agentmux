# AgentMux: istruzioni per l'agente

AgentMux è un multiplexer e orchestratore di sessioni di terminale per gli agent AI da CLI, su
Linux. Architettura e toolchain sono in `docs/ARCHITECTURE.md`. Il progetto è nel bootstrap M0
(`docs/BOOTSTRAP_PLAN.md`): M0 non implementa PTY, `libghostty-vt`, Notcurses o SSH
(`docs/DEVELOPMENT_WORKFLOW.md` §11).

**Stato:** governance `2.11-draft`, tracciata nel repository, **non** congelata.

**Fonti di verità:** Git per il codice e per i documenti del repository (`docs/`, ADR,
governance); Linear (team `ICH`, progetto Agentmux) per la issue, la spec e il piano approvati, le
approvazioni e lo stato; GitHub per le prove di PR e CI. `handoff.md`, `memory/` e Hindsight sono
consigli da controllare su queste fonti.

## Struttura del repository

`<repo-root>` è il checkout di `main`: `/mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux`.
Leggi e scrivi `handoff.md` e `memory/` solo lì: le copie nei worktree delle issue sono ferme al
punto in cui è nato il branch.

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
| `.mcp.json` | Server MCP del progetto (Context7) |
| `.claude/worktrees/` | Worktree delle issue e delle review, ignorati da Git |
| `.github/pull_request_template.md` | Struttura del corpo della PR |

## Ripresa del lavoro

A ogni avvio e dopo `/clear` l'hook `SessionStart` indica `handoff.md`. L'utente riprende con
«riprendi»; senza hook scrive «Leggi handoff.md ed esegui il prompt». Leggi
`<repo-root>/handoff.md` per intero: dice cosa fare. Prima di agire esegui questi controlli,
obbligatori a inizio sessione (algoritmo completo in `docs/specs/SESSION_CONTINUITY.md`):

1. Trova la issue di lavoro. È la issue che `handoff.md` o l'utente indicano, oppure una issue di
   Agentmux che:
   - è In Progress o In Review;
   - è in Backlog o Todo con un checkpoint `agentmux.handoff.v1` o con il record di approvazione
     della sua spec (CP01) o del suo piano (CP02): è tra la specifica e l'implementazione;
   - è la issue dell'ultima PR mergiata e non ha ancora il commento di chiusura CP06. Elenca le PR
     con `gh pr list -R ichelema/agentmux --state merged --limit 100 --json number,headRefName,mergedAt` e
     prendi quella con `mergedAt` più recente: l'ID della issue è nel nome del branch.

   Lo slot WIP lo liberano solo il commento CP06 o una sospensione autorizzata dall'utente, non il
   nome dello stato. Se il caso non è chiaro, fermati e chiedi all'utente.
2. Se non c'è una issue di lavoro, la prossima issue la sceglie l'utente: chiedigliela, non
   indovinare. Salta i punti 3 e 5.
3. Leggi su Linear la spec e il piano della issue. Annota le revisioni approvate (`spec-rN`,
   `plan-rN`) e la versione di governance. Se manca ancora l'approvazione della spec (CP01) o del
   piano (CP02), il prossimo passo è quel gate.
4. Controlla lo stato reale di Git: `git status`, branch, `HEAD`, worktree, remoto, PR e CI. Git
   vale più di qualsiasi handoff.
5. Leggi l'ultimo checkpoint `agentmux.handoff.v1` della issue e confronta ogni sua affermazione
   con Git, PR, CI e Linear. Se coincidono, il prossimo passo è il suo `next_allowed_action`.
   Prima di ripetere un'operazione interrotta (creazione della PR, modifica su Linear), controlla
   se è già avvenuta. Se `handoff.md` e Linear non coincidono, vale Linear per lo stato della
   issue e Git per il codice: segnala la differenza all'utente.
6. Leggi `docs/DEVELOPMENT_WORKFLOW.md`, `docs/ARCHITECTURE.md` e
   `docs/specs/SESSION_CONTINUITY.md`, gli ADR, le spec e i test che il lavoro tocca, e la memoria
   (sezione «Memoria»).
7. Scrivi lo stato controllato e il prossimo passo, oppure il blocco.

Un reviewer indipendente salta il punto 5: non legge i commenti di checkpoint
(`docs/DEVELOPMENT_WORKFLOW.md` §4).

## Workflow

Una sola issue approvata e atomica alla volta (WIP=1), con i command di Trinity. L'utente invoca
ogni command. Prima di ogni command spiega in breve cosa fa e perché. A fine fase scrivi il
prossimo command, con gli argomenti reali, in un blocco di codice e fermati. Non lanciare i
command con il tool Skill e non eseguirli leggendo il loro file di tua iniziativa.

```text
/trinity:workflow:0_create-issue <descrizione>
/trinity:workflow:1_create-worktree main <ICH-XXX> <model>
/trinity:workflow:2_work-issue <ICH-XXX> <model>
/trinity:workflow:2_work-simplify <ICH-XXX> <model>
/trinity:workflow:2_work-verify <ICH-XXX> <model>
/trinity:workflow:3_independent-review <ICH-XXX> <model> <hard|soft>
/trinity:workflow:4_review-fix-loop <ICH-XXX> <model>
/trinity:workflow:5_remove-worktree <prefisso>+<ICH-XXX>-<model>
/trinity:issue-cycle <review|loop> main <ICH-XXX> <model>
```

1. `0_create-issue`, poi l'utente approva la spec (CP01) e, a parte, il piano (CP02).
2. `1_create-worktree`, poi `2_work-issue`: implementazione nel worktree, test e PR (CP03).
3. `2_work-simplify`, che `2_work-issue` propone come passo successivo. Poi `2_work-verify`,
   facoltativo, per provare il programma in esecuzione.
4. Review indipendente (CP04), con il command che sceglie l'utente: `3_independent-review` (un
   reviewer; `hard` usa Fable, `soft` usa Sonnet) o `4_review-fix-loop` (due reviewer in
   parallelo e un loop di fix). I cicli di review si contano sulla issue, non sul singolo
   command: prima di un command di review controlla quanti cicli restano dei tre. Il limite di
   tre cicli per issue prevale sul contatore del command, e un round di `4_review-fix-loop` vale
   un ciclo. Quando i cicli finiscono, completa il triage e la pulizia dei worktree di review, poi
   fermati prima di un nuovo round: fix e finding residui li decide l'utente. Dopo un fix il nuovo
   head richiede un altro ciclo.
5. PR pronta (CP05), un gate a sé: CI richiesta passata (oggi la CI non esiste: scrivilo, non
   dichiararla passata), finding gestiti, review indipendente sull'head attuale della PR, perimetro
   e prove mostrati all'utente. Un head con fix non recensiti e senza cicli rimasti richiede una
   decisione esplicita dell'utente.
6. L'utente fa il merge.
7. L'utente invoca `5_remove-worktree`. Alla fine il command esegue «Chiusura della sessione», che
   contiene anche la chiusura CP06.

`issue-cycle` esegue senza presidio i passi 2–4 per una issue. Proponilo solo dopo CP02 e per una
sola issue. Quando l'utente lo invoca, segui il command anche dove ti fa leggere i file degli step.
Se il lavoro esce dal piano approvato, fermati: il piano nuovo va approvato di nuovo.

Dopo `issue-cycle` e dopo `4_review-fix-loop` controlla CP05 come al punto 5:

- `issue-cycle` può aprire la PR con test falliti e, in modalità `review`, non rifà la review
  dopo i fix;
- `4_review-fix-loop` conta fino a tre round per ogni invocazione, fixa i `LOW` del terzo round
  senza un nuovo round e crea da solo le issue per i finding `MEDIUM`. Per la governance il rinvio
  di un finding Medium o Low va approvato dall'utente: mostragli le issue create e chiedi
  l'approvazione.

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
  detached, avviato dal command che sceglie l'utente. Le due modalità di `3_independent-review`
  avviano entrambe un subagente e scelgono solo il modello. Il read-only è una procedura, non un
  controllo tecnico. Massimo tre cicli ordinari di review per issue. I finding Critical/High
  aperti bloccano il merge; approvazione e merge spettano all'utente.
- Scrivi un checkpoint `agentmux.handoff.v1` sulla issue a ogni checkpoint CP01–CP06, prima di
  chiudere la sessione, a contesto quasi pieno e su un blocco (formato in
  `docs/specs/SESSION_CONTINUITY.md`).
- Scrivi su Linear dalla sessione principale con `linear.py`: il classificatore dei permessi può
  bloccare un subagente.
- Lingua: le label del progetto Linear decidono la lingua dei testi che restano (sezione «Lingua»
  della skill `linear` di Trinity). `lang:it` mette in italiano issue e commenti su Linear;
  `git:en` mette in inglese commit, PR e commenti sulla PR. Anche i messaggi `docs(ripresa)` sono
  in inglese. `CLAUDE.md`, `handoff.md` e `memory/` sono in italiano. I marcatori restano identici
  anche nei commenti in italiano: «written by Claude Code on the owner's instruction», la prima
  riga del checkpoint `agentmux.handoff.v1` e l'inizio `**CP06 closure — ICH-XXX` della prima
  riga del commento di chiusura.
- Usa una parola magica di Linear, di chiusura (`Fixes`, `Closes`, `Resolves`…) o di collegamento
  (`Refs`, `Part of`, `Related to`…), solo con l'ID della issue attiva. Nel titolo della PR e nei
  commit cita solo la issue attiva. Le altre issue vanno nel corpo della PR, in una riga
  `Related: ICH-…`, che non è una parola magica.
- In una sessione entrata nel worktree con `EnterWorktree`, scrivi un comando Bash semplice per
  chiamata, con path assoluti: niente cicli, niente `cd` in subshell, niente comandi in variabili.
- Non scrivere credenziali o conversazioni intere in `handoff.md`, in `memory/` o in Hindsight.
- Usa le skill di Matt Pocock in modo selettivo, quando ci sono: non sono un requisito.

## Memoria

- Leggi l'indice `<repo-root>/memory/memory.md`, poi apri solo i file che servono al lavoro.
- Se Hindsight è disponibile, fai un `recall` mirato (tag `repo:agentmux`) per i componenti che
  tocchi. Se non risponde, prosegui e dillo.
- Controlla ogni fatto della memoria su Git, Linear e `docs/` prima di usarlo.
- In `memory/` scrivi solo fatti controllati e durevoli, in italiano: decisioni dell'utente con il
  motivo, cause radice e correzioni, fatti d'ambiente, punti aperti, issue chiuse.
- Non scrivere in `memory/` lo stato della issue attiva (va nel checkpoint su Linear), il prossimo
  passo (va in `handoff.md`), segreti o conversazioni.
- Aggiorna il file del tema e non creare duplicati. Per un tema nuovo crea un file e aggiungi una
  riga all'indice. Quando un fatto cambia, correggi la riga: la storia resta in Git.

## Chiusura della sessione

Esegui questa procedura quando l'utente chiude la sessione, dopo ogni merge (la esegue anche
`5_remove-worktree`) e a contesto quasi pieno.

1. Se la sessione è dentro il worktree di una issue, esci con `ExitWorktree(action: "keep")`:
   worktree, branch e modifiche restano.
2. Prima di scrivere un file controlla il checkout di `main`: esegui `git -C <repo-root> fetch`,
   poi controlla che sia su `main` e senza modifiche locali. Se `main` locale è indietro, allinealo
   con `git -C <repo-root> merge --ff-only origin/main`. Controlla anche i commit locali non
   pubblicati con `git -C <repo-root> log --name-only --format='%h %s' origin/main..main`: sono
   ammessi solo commit `docs(ripresa)` che toccano solo `handoff.md` e `memory/`. Se un controllo
   o il fast-forward fallisce, salta i punti 4–7: scrivi solo il checkpoint del punto 3 e segnala
   il blocco.
3. Se una issue è attiva, scrivi il suo checkpoint `agentmux.handoff.v1` su Linear quando non c'è
   un merge, oppure quando CP06 non si può completare ora (per esempio manca `5_remove-worktree`):
   resta il punto di ripresa.
4. Dopo un merge completa CP06 prima del resto. Se il commento di chiusura esiste già, non
   ripeterlo.
   - Controlla lo SHA del merge su `origin/main` e che `main` locale lo contenga.
   - Controlla `main` dopo il merge. Oggi la CI non esiste: scrivilo, non dichiararla passata.
   - Controlla che worktree e branch della issue non esistano più.
   - Scrivi sulla issue il commento di chiusura. La prima riga inizia con
     `**CP06 closure — ICH-XXX`; seguono SHA del merge, controllo di `main`, rimozione del
     worktree, issue nate dal lavoro, rilascio dello slot e, se Linear ha già portato la issue a
     Done, la deviazione (punto 1 di `memory/punti-aperti.md`).
   - Porta la issue in Done, se Linear non l'ha già fatto.

   Se un passo manca o fallisce, lo slot resta occupato: scrivi il checkpoint del punto 3 e
   annotalo in `handoff.md`.
5. Salva in `memory/` i fatti durevoli della sessione. Quando CP06 è completato, aggiungi la issue
   a `memory/storico.md`, con il link al commento CP06 e le issue nate dal lavoro, e aggiorna
   `memory/punti-aperti.md`. Controlla anche che l'ultima issue con il commento CP06 sia in
   `memory/storico.md`: se una sessione interrotta l'ha saltata, aggiungila ora.
6. Riscrivi da zero `handoff.md`, con la data di oggi e solo tre parti: lo stato controllato dello
   slot (issue di lavoro e checkpoint, oppure slot libero), il prossimo passo concreto con il
   command da proporre, le decisioni che servono all'utente. Riporta i punti di
   `memory/punti-aperti.md` che toccano la prossima issue (per M0-06, il rischio F6). Niente
   storia: la storia va in `memory/`.
7. Aggiungi per nome solo `handoff.md` e i file cambiati di `memory/`, e controlla con
   `git -C <repo-root> diff --cached --name-only` che lo stage contenga solo loro. Committa con
   messaggio `docs(ripresa): ...` ed esegui `git -C <repo-root> push origin main`.
8. Se Hindsight è disponibile, salva con `retain` le decisioni e le cause radice controllate (tag
   `claude-code`, `repo:agentmux`).
