# Bootstrap graduale di M0 e pilota controllato

**Stato:** in corso. GitHub (`ichelema/agentmux`) e Linear (team ICH, progetto Agentmux) sono predisposti; la protezione dei branch e la CI non esistono ancora (M0-06).

## Prerequisiti (di responsabilità umana)

Approvare i documenti seed e l'architettura del progetto; predisporre e autorizzare GitHub e Linear; stabilire le credenziali degli agenti e i confini dei permessi; stabilire l'ambiente del reviewer indipendente; confermare quali controlli e quale automazione si possono davvero imporre con un controllo tecnico (enforcement). Il repository seed non può dichiarare un gate di review né la protezione dei branch finché non sono configurati e provati.

## Ordine proposto delle issue M0 (label di pianificazione e ID Linear)

| Ordine | Issue Linear | Titolo della issue | Deliverable verificabile | Esclusione importante |
|---|---|---|---|---|
| M0-01 | ICH-548 | Repository Foundation / Controlled Pilot | Repository minimo valido, README e `.gitignore`, PR/merge con review | Niente CMake, PTY o funzionalità dell'app |
| M0-02 | ICH-566 | Development Governance | Workflow consolidato, istruzioni per gli agenti e template tracciati nel repository | Nessuna nuova policy senza approvazione |
| M0-03 | ICH-588 | Architecture Baseline | Visione, requisiti, architettura e ADR sottoposti a review | Nessun codice di integrazione |
| M0-04 | — | CMake Foundation | Gli eseguibili C17 minimi `agentd` e `agentctl` compilano | Nessuna sessione/PTYS reale |
| M0-05 | — | Test Infrastructure | CTest, test rappresentativo, integrazione dei sanitizer | Nessun E2E via SSH |
| M0-06 | — | GitHub Actions CI | Build, test e controlli sulle PR, main protetto configurato e validato | Nessuna pubblicazione di release |

Le label di pianificazione `M0-xx` devono essere sostituite dagli ID `ICH-XXX` emessi da Linear. Dividi ogni issue troppo grande per essere atomica. Nota: il seed iniziale del repository e i documenti di governance potrebbero dover essere introdotti in via amministrativa prima della PR pilota. L'umano deve approvare come evitare di trattare i file preesistenti come un deliverable completato del pilota. **Un pilota che consiste solo nell'aggiungere README/.gitignore ha senso solo se quei file non sono già stati integrati nella baseline.** Un'alternativa pulita è predisporre prima un repository Git vuoto, poi eseguire il pilota, poi aggiungere la documentazione consolidata in M0-02/M0-03. Il proprietario ha scelto questa alternativa pulita: il pilota ICH-548 è stato eseguito su un repository vuoto, e ICH-566 e ICH-588 hanno aggiunto i documenti seed.

## Sequenza del pilota

1. Approvare la specifica atomica della issue, con AC e fuori perimetro esplicito.
2. Approvare il piano leggero indipendente.
3. Acquisire lo slot di esecuzione esclusivo; creare il worktree e il branch corrispondenti.
4. Eseguire l'Implementer con il contesto approvato; raccogliere prove di test limitate.
5. Eseguire il Reviewer indipendente in sola lettura (`docs/DEVELOPMENT_WORKFLOW.md` §4) sul commit esatto e sugli AC; correggere entro tre cicli.
6. Richiedere la review e l'approvazione umane e il merge; verificare la CI di main; pubblicare sulla issue il commento di chiusura CP06, che rilascia lo slot (`docs/DEVELOPMENT_WORKFLOW.md` §2).
7. Controllare l'esclusività del WIP, i permessi, l'isolamento della review, la tracciabilità, l'efficacia dei gate e la continuità della sessione, fermando e riprendendo di proposito una sessione non distruttiva durante il pilota.
8. Registrare i conflitti di governance scoperti e le correzioni approvate da un umano; congelare `1.0.0` solo dopo un'approvazione esplicita.

## Criteri di accettazione del pilota di continuità della sessione

- Il checkpoint conserva la issue, gli ID delle revisioni approvate, lo SHA del commit, le prove di test, i contatori del budget e la prossima azione.
- Una nuova sessione indipendente dell'agente ricostruisce lo stato senza bisogno della cronologia originale della chat.
- La nuova sessione si accorge di uno stato Git alterato e rifiuta di sovrascrivere modifiche in conflitto o non committate.
- Il recall di Hindsight è verificato e non prevale su un'approvazione di ADR, spec o Linear.
- Il reviewer riceve un proprio contesto; nessuno scratchpad dell'implementer viene trasferito.

## Note sul controllo tecnico (enforcement)

Il lock WIP atomico, i ruleset di GitHub, la CI protetta, il controllo del reviewer, i lock delle dipendenze e l'accesso automatico alla memoria di Hindsight sono attività di implementazione e configurazione di M0. Finché non esistono, usa la supervisione umana, controlli espliciti e permessi ristretti; non presentare mai una protezione procedurale come imposta da un controllo tecnico.
