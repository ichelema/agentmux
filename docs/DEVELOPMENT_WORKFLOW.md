# Workflow di sviluppo di Ethereal

**Stato:** `governance 2.14-draft` — tracciata nel repository dalla ICH-566 (M0-02), dopo il Controlled Pilot ICH-548; non ancora congelata. Prima release stabile prevista della governance: `1.0.0`, dopo l'approvazione esplicita di un umano. Questo documento descrive la **policy**, non è la prova che esistano già protezioni automatiche.

## 1. Autorità, stato e invarianti non negoziabili

Il proprietario umano approva specifiche, piani leggeri, modifiche di architettura, permessi e perimetro, rinvii eccezionali, revisioni della governance e merge. L'AI può implementare, testare, fare commit e preparare PR solo entro i limiti autorizzati. Il repository Git è la fonte di verità per codice, documenti di architettura, ADR e governance. Linear è la fonte di verità per specifica approvata, piano approvato, approvazioni, stato, dipendenze e decisioni umane di ogni singola issue. GitHub fornisce le prove di PR, modifiche e CI. Hindsight è consultivo, mai normativo.

- Un solo slot di esecuzione attivo (WIP=1), inclusi correzione, review e integrazione. Le issue future possono essere specificate; le eccezioni richiedono una sospensione approvata da un umano e una riassegnazione esplicita.
- Un worktree e un branch corrispondente per issue, creati dal command Trinity `1_create-worktree`: branch `<prefix>/<base-name>` e worktree `<repo-root>/.claude/worktrees/<prefix>+<base-name>`, dove `<prefix>` è `bug` o `improvments` (dal tipo o dalla label della issue) e `<base-name>` è `<issue-id>-<model>`. Non fidarti mai di un handoff al posto di verificare lo stato reale di Git.
- Niente merge autonomo, push diretto sul branch protetto, force push, modifiche distruttive non autorizzate, lettura di segreti, escalation di privilegi o bypass di policy/CI. Unica eccezione alla regola sul push diretto: i documenti di sessione, cioè il prompt di ripresa `handoff.md` e la memoria del progetto `memory/`, si aggiornano con un commit diretto su `main` che contiene solo quei file, con il messaggio `docs(ripresa): ...`. M0-06 deve mantenere questa eccezione compatibile con la protezione del branch che configura, oppure rivederla.
- Tutto il lavoro deve essere tracciabile: issue Linear → revisioni approvate di spec e piano → branch/worktree → commit/PR → review → merge umano → CI post-merge → chiusura su Linear.
- La review indipendente gira come subagente reviewer in sola lettura con contesto separato, avviato dall'orchestratore su un worktree di review detached (§4); al massimo tre cicli di review ordinari per issue, non tre per ogni riavvio dell'agente.
- I finding Critical/High bloccano il merge; i problemi residui Medium/Low richiedono un rinvio esplicito, approvato da un umano, in issue Linear collegate.

## 2. Ciclo di consegna e gate

| Checkpoint | Prove / autorità obbligatorie |
|---|---|
| CP01 Specifica | L'umano approva perimetro della issue atomica, non-obiettivi, criteri di accettazione (AC) e verifiche; revisione fissata |
| CP02 Piano | L'umano approva il piano leggero in cinque parti e la classificazione A0/A1/A2 |
| CP03 Implementazione | L'agente assegnato lavora solo nel worktree autorizzato, implementa gli AC, fornisce le prove di test e build |
| CP04 Review indipendente | Un reviewer separato valida il codice rispetto a spec, ADR applicabili, rischio, sicurezza e copertura dei test; al massimo 3 cicli |
| CP05 PR pronta | La CI richiesta e i controlli di review passano, i finding sono gestiti, l'umano vede perimetro e prove |
| CP06 Merge e chiusura | L'umano autorizza ed esegue il merge; CI di `main` verificata; worktree rimosso in sicurezza; il commento di chiusura sulla issue (Definition of Done) registra la chiusura e rilascia lo slot; poi l'agente porta la issue in Done |

Non riportare ogni comando di shell. Riporta i gate di milestone e i blocchi, i conflitti, le eccezioni di permessi, architettura e sicurezza. Dopo una sessione interrotta, riparti dall'ultimo checkpoint verificato; non dedurre mai un merge o un test riuscito da un riepilogo conversazionale.

### Definition of Ready

- Obiettivo atomico, perimetro incluso ed escluso esplicito, dipendenze, riferimenti applicabili, AC con verifica, rischi e domande aperte risolti o tracciati esplicitamente.
- Baseline di specifica approvata e, dopo, piano di implementazione approvato separatamente.
- Worktree e branch autorizzati e slot di esecuzione disponibile.
- Accesso agli ambienti e agli strumenti necessari confermato; vincoli di safety e di sicurezza individuati.

### Template della specifica (Linear)

Obiettivo; Riferimenti; Perimetro; Fuori perimetro; Precondizioni; Comportamento atteso; Gestione degli errori e casi limite; Interfacce e vincoli; AC-01...; Verifica collegata a ogni AC; Domande aperte; Approvazione e versione/revisione. I contratti tecnici condivisi stanno in `docs/specs/` quando serve; evita issue e file di spec duplicati.

### Piano di implementazione leggero (Linear)

Cinque sezioni obbligatorie: (1) Approccio, (2) file e moduli interessati, (3) piano di verifica mappato sugli AC, (4) rischi, (5) impatto architetturale A0/A1/A2 più dipendenze. Il piano si approva separatamente dopo la specifica. Non ripianificare in silenzio.

### Revisioni e record di approvazione

Una label `spec-rN` / `plan-rN` nell'intestazione di una specifica o di un piano ne identifica la revisione; ogni revisione modificata riceve la label successiva. Il proprietario umano approva nella sessione dell'agente. L'agente registra ogni approvazione come commento sulla issue, marcato "written by Claude Code on the owner's instruction", con la decisione e la data; il proprietario non è tenuto a scrivere i commenti di approvazione. Una bozza non implica alcuna approvazione.

### Definition of Done

AC approvati soddisfatti; controlli di qualità adeguati al rischio superati; review indipendente e nessun finding bloccante irrisolto; PR approvata da un umano e mergiata. Dopo il merge, verifica la CI di `main`, rimuovi il worktree, poi pubblica un commento di chiusura sulla issue. Il commento di chiusura registra lo SHA del merge, il controllo post-merge di `main`, la rimozione del worktree e il rilascio dello slot; è la prova che fa fede per CP06 e il segnale di rilascio dello slot. Il solo stato Done su Linear non prova la chiusura. Il proprietario configura l'integrazione GitHub di Linear perché porti una issue in In Review al merge; l'agente porta la issue in Done dopo il commento di chiusura. Nessun resoconto finale ridondante.

## 3. Modifiche di architettura e di decisione

- A0: le modifiche di implementazione locali e compatibili, dentro il piano approvato, possono procedere in autonomia.
- A1: le modifiche al contratto di interfaccia/API richiedono l'autorizzazione di un umano; ADR se rilevanti per l'architettura.
- A2: le modifiche ai confini, alla proprietà, alla persistenza, alla sicurezza o alle dipendenze dell'architettura richiedono un ADR approvato **prima** di scrivere il codice.
- Un blocco di perimetro ferma l'implementazione in attesa di una decisione. Difetti e miglioramenti collegati si propongono come issue Linear collegate e si rinviano (Triage & Defer). Un problema di sicurezza Critical/High che riguarda la modifica blocca l'avanzamento.
- Nessuna modifica automatica a spec o piano approvati per giustificare una deriva dell'implementazione. Le modifiche ricevono una nuova revisione e vanno riapprovate prima di riprendere.

## 4. Ruoli degli agenti e separazione

**Implementer:** modifica dei sorgenti, build e test, correzioni limitate, preparazione della PR, aggiornamenti di avanzamento della issue, entro il worktree autorizzato. Non può firmare la propria review né fare il merge.

**Reviewer indipendente:** sessione e contesto separati. I reviewer sono subagenti in sola lettura che lavorano su worktree detached allo SHA dell'head della PR; il proprietario sceglie il command Trinity. (a) `3_independent-review <issue-id> <model> <hard|soft>` avvia un subagente `trinity:reviewer` con contesto pulito, che crea il worktree `<repo-root>/.claude/worktrees/review+<base-name>`; la sessione chiamante lo rimuove dopo il report (dentro `issue-cycle review` lo rimuove il subagente stesso); la modalità sceglie solo il modello del reviewer (`hard`: Fable, `soft`: Sonnet, entrambi con effort `high`). (b) `4_review-fix-loop <issue-id> <model>` esegue due reviewer in parallelo sui worktree `review+<base-name>-gpt` e `review+<base-name>-fable`, che il loop crea e rimuove. (c) `issue-cycle review|loop` esegue senza presidio, per una issue, creazione del worktree, implementazione, semplificazione, verifica e (a) oppure (b); il proprietario lo usa solo dopo CP02. Può aprire una PR con test falliti e, in modalità `review`, non rifà la review dopo i fix, quindi CP05 si controlla a parte. Un subagente avviato così soddisfa il requisito di sessione e contesto separati; il proprietario non avvia a mano una sessione di reviewer. La sola lettura è una procedura, non un controllo tecnico: il reviewer ha accesso alla shell. Il reviewer legge la descrizione della issue (la specifica approvata), il piano approvato, i record di approvazione, gli ADR applicabili, il commit e il diff esatti, le prove di CI e i controlli che riesegue lui stesso, e la memoria su file (`memory/`) nel suo worktree di review; non usa Hindsight (§7). Non legge i commenti di checkpoint o di avanzamento dell'implementer; i commenti di checkpoint iniziano con la riga marcatore definita in `docs/specs/SESSION_CONTINUITY.md`. Non deve ricevere la conversazione dell'implementer, modificare il codice di produzione né fidarsi, senza esaminarle, delle dichiarazioni dell'implementer sui test. Il report indica ID stabile del finding, severità, posizione, problema, prove, comportamento atteso e stato. `No Findings` è valido solo dopo una review completa.

Ogni ciclo di review è una valutazione completa di un commit preciso. Le correzioni richiedono un'ulteriore review; in tutto 3 cicli ordinari. Dopo il limite, fermati ed escala. Un commit appena modificato non va mergiato senza validazione indipendente, anche a budget di review esaurito: servono un'eccezione o una decisione esplicita di un umano; l'agente non deve avviare in silenzio un quarto ciclo.

## 5. Qualità e sicurezza in base al rischio

Baseline: build, unit test, controlli statici selezionati, warning del compilatore e CI richiesta. Per PTY, memoria nativa, IPC, SSH, sessioni e input VT aggiungi test reali su PTY e processi, test end-to-end, input negativi, limiti di risorse, sanitizer e fuzzing dove giustificati. Mock e stub per logica pura, protocolli e layout del client; PTY reali per il comportamento di terminale e processi; E2E di attach/detach locale e, una volta implementato, remoto. Usa timeout limitati e fixture di test che non rivelano dati.

Confini esposti alle minacce: client/SSH ↔ IPC del daemon ↔ proprietà di PTY/processi ↔ ANSI/OSC/VT non fidati. Permessi del socket Unix, credenziali del peer, limiti di protocollo e di frame, minimo privilegio, igiene dei FD e identità SSH sicura sono requisiti, non implementazioni date per scontate. Rivedi il modello delle minacce quando cambiano i confini. Un Critical/High irrisolto blocca merge e release. I residui Medium/Low approvati diventano issue collegate.

## 6. Tentativi, recupero ed esecuzione interrotta

Classifica i fallimenti come transitori, di esecuzione, di contesto o strutturali. Strumento/rete transitorio: al massimo **3 tentativi in totale** (il primo più due ripetizioni) con un backoff semplice e breve, purché rieseguire sia sicuro e idempotente; prima di ripetere mutazioni su GitHub o Linear controlla gli effetti delle richieste precedenti. Build/test: al massimo **2 tentativi di correzione per causa radice** dentro il piano approvato, con una diagnosi significativa ogni volta. Perdita di contesto: una ricostruzione dalle fonti di verità; poi escala. Violazioni di permessi, architettura o perimetro: **nessun nuovo tentativo automatico**. I contatori restano validi anche se l'agente o la sessione cambia; rinominare un errore o un comando non li azzera.

In caso di interruzione o blocco: lascia codice e worktree in uno stato sicuro, salva un checkpoint/handoff che rimandi al commit Git reale, alla issue corrente, alle revisioni di piano e spec, alle prove, ai tentativi e al prossimo passo consentito; non salvare mai segreti. Il nuovo agente esegue `docs/specs/SESSION_CONTINUITY.md`, verifica lo stato delle fonti e riprende solo entro l'autorizzazione. Nessuna cancellazione o revert autonomo del lavoro non committato. Questo è diverso dal **Revert-first** per regressioni post-merge significative e confermate, che usa una PR di revert controllata da un umano e una issue correttiva con test, con sospensione del WIP quando serve.

## 7. Contesto e Hindsight

L0 obbligatorio: `CLAUDE.md`, sezioni applicabili del workflow, issue/spec/piano Linear approvati correnti, branch/worktree/commit reali. L1 rilevante: ADR, contratti, moduli e test interessati più un `recall` mirato di Hindsight. L2 su richiesta: codice correlato, storia di Git, issue, altra memoria mirata. Evita di scaricare interi documenti se non serve. Il bank Hindsight `Ethereal` è proposto; usa `retain` per scoperte tecniche e gotcha verificati e riutilizzabili, con riferimenti alle fonti, `recall` per recuperare contesto, `reflect` per i pattern, se disponibile. Non trattare la memoria generata come fonte di verità senza verifica e non registrare segreti né conversazioni intere. Il reviewer indipendente non usa Hindsight in alcun modo (tool MCP, skill Hindsight, chiamate dirette al server, subagenti): Hindsight non può garantire che i ricordi sulla PR corrente siano esclusi. L'orchestratore non gli passa alcun fatto di Hindsight sulla PR corrente. L'unica memoria che il reviewer legge è la memoria su file (`memory/`) nel suo worktree di review, usata come ipotesi da controllare sul codice; i file di memoria modificati dalla PR fanno parte del changeset in review, non del contesto. Il reviewer esclude le opinioni dell'implementer sulla PR corrente, compresi i commenti di checkpoint (§4). Un controllo di cattura della conoscenza nei checkpoint significativi chiede se c'è stata una scoperta riutilizzabile e verificabile; nessuna scrittura forzata.

## 8. Governance della documentazione

Documenti canonici: `docs/VISION.md` (visione), `docs/REQUIREMENTS.md` (requisiti), `docs/ARCHITECTURE.md` (architettura corrente), `docs/adr/` (scelte architetturali), `docs/specs/` (contratti tecnici condivisi), `docs/security/THREAT_MODEL.md` (modello di sicurezza), questo documento (workflow), `CLAUDE.md` (istruzioni compatte per l'agente). Linear per specifica, piano e stato di ogni issue; GitHub/Git per implementazione e CI. Un fatto, una sola fonte di verità; rimanda con un riferimento invece di copiare. Aggiorna i documenti nella stessa PR quando cambia un contratto, una policy o un requisito. I conflitti irrisolti tra fonti di verità richiedono la decisione di un umano, non un'ipotesi dell'agente.

## 9. WIP, slot e controlli tecnici

Usa un meccanismo di slot di esecuzione tecnicamente atomico quando disponibile; controllare solo i worktree non basta. Lo slot è tenuto durante implementazione, review, merge e verifica post-merge, ed è rilasciato solo dal commento di chiusura CP06 (§2) o da una sospensione autorizzata, mai dal solo cambio di stato su Linear. Una sospensione autorizzata può rilasciarlo o riassegnarlo; assicurati che ci sia al massimo un flusso di lavoro attivo. Il registro di stato, se implementato, è solo operativo; riconcilialo con Linear e Git. Verifica l'identità di issue e branch e che il commit sottoposto a review coincida con l'HEAD della PR. I branch protetti e i ruleset di GitHub dovrebbero richiedere PR e CI, impedire all'agente push diretto, merge e force push, e proteggere la configurazione della CI; le decisioni umane non si possono sostituire con un'euristica della CI. Gli script di controllo tecnico (enforcement) e i controlli di CI sono *pianificati*, non dichiarati installati. L'avvio di questi controlli richiede una configurazione amministrativa autorizzata da un umano.

## 10. CI, dipendenze e release

Prodotto in C17; CMake/Ninja; Clang fissato come principale e GCC come secondario; clang-tidy, CTest, ASan/UBSan secondo necessità; GitHub Actions pianificate `ci.yml`, `integration.yml`, `release.yml`. Fissa le versioni di librerie e toolchain di terze parti e gli SHA delle Actions in un manifest controllato. Valida la compatibilità di Zig/libghostty-vt e le funzionalità opzionali di Notcurses. Separa la CI ordinaria delle PR dagli E2E/integrazione su PTY e dalla pubblicazione di release approvata da un umano. Release alle milestone approvate: versione di prodotto condivisa per `agentd`/`agentctl`, handshake e capacità dell'IPC con un major/minor proprio, separato dalla versione di prodotto. Tag tracciabili, artefatti prodotti dalla CI e SHA256. Modifiche controllate alle dipendenze: inventario di versione, licenza e provenienza; revisione mensile, triage tempestivo dei problemi di sicurezza; issue Linear proposta, approvazioni, test di rischio, review indipendente, nessun aggiornamento automatico.

## 11. Bootstrap e pilota

M0 a fasi: provisioning amministrativo di repository e Linear, governance dello sviluppo, baseline di architettura, scheletro C17, infrastruttura di test, CI. Nessuna implementazione di PTY, libghostty-vt, Notcurses o SSH in M0. Vedi `docs/BOOTSTRAP_PLAN.md`. Il pilota usa una vera issue atomica minima, spec e piano approvati da un umano, worktree, review indipendente, PR, merge umano e verifica post-merge. Consolida e correggi le contraddizioni di policy prima del congelamento della governance. Il pilota si è svolto nella ICH-548 (M0-01); la ICH-566 (M0-02) ha risolto i conflitti di governance che ha trovato (ICH-557, ICH-558, ICH-559). Il congelamento `1.0.0` richiede ancora l'approvazione esplicita del proprietario.

## 12. Versioni e modifiche della governance

Gli identificatori di bozza come `2.10-draft` sono identificatori di pianificazione, **non** SemVer di una governance congelata; ogni identificatore di bozza indica un solo testo, perché le issue lo fissano. Dopo il pilota e l'approvazione di un umano, congela la prima governance `1.0.0`; questa versione è indipendente dalla versione di prodotto di Ethereal e dalla versione di IPC. Governance stabile: PATCH per chiarimenti editoriali senza obblighi modificati; MINOR per nuovi controlli compatibili; MAJOR per modifiche fondamentali di gate, permessi o workflow.

Tutte le modifiche normative: issue/proposta `ICH-XXX` separata, analisi d'impatto, perimetro e piano approvati, worktree e PR autorizzati, review indipendente, approvazione e merge umani, validazione proporzionata (coerenza editoriale, test mirato del controllo, oppure pilota mirato o completo per una modifica strutturale). `main` è normativo dopo il merge. Le issue attive esistenti restano fissate alla versione di governance approvata, salvo migrazione esplicita di un umano; una protezione di sicurezza urgente richiede una decisione esplicita. Nessuna modifica automatica ai controlli tecnici che proteggono la stessa PR.

## 13. Risoluzioni pratiche dei conflitti

- Il WIP attivo unico è compatibile con il lavoro di emergenza solo tramite sospensione autorizzata da un umano e trasferimento dello slot.
- Il tetto ai cicli di review non autorizza il merge di codice nuovo senza review; l'eccezione va escalata.
- La DoD minima non omette la CI di `main` post-merge e il commento di chiusura CP06 richiesti prima del rilascio dello slot.
- Hindsight aiuta il recupero tecnico ma non può asserire la correttezza della PR corrente; il reviewer indipendente non lo usa (§7).
- Il provisioning del bootstrap è autorizzato da un umano prima che esistano gli strumenti; un gate pianificato non è un gate applicabile oggi.
- Una nuova sessione eredita lo stato verificato della issue e i contatori di tentativi e review già consumati; non li azzera mai in silenzio.

## 14. Controlli tecnici non implementati e approvazioni aperte

Questo file non crea ruleset GitHub, stati Linear, installazione o bank di Hindsight, script, meccanismi di controllo della review né accessi a SSH o CI. La loro disponibilità reale va controllata prima del congelamento della governance. Architettura, issue di bootstrap e congelamento della governance richiedono l'accettazione esplicita di un umano. In assenza di uno stato di approvazione o di gate verificabile in modo oggettivo, **fermati e chiedi** invece di presumere il successo.
