# Continuità della sessione ed esecuzione ripristinabile dell'agente

**Stato:** parte di `governance 2.14-draft`; specifica operativa esercitata nel pilota ICH-548, non ancora automatizzata. Questo protocollo è obbligatorio per avviare una nuova sessione dell'agente. Un reviewer indipendente segue invece `docs/DEVELOPMENT_WORKFLOW.md` §4: non legge i commenti di checkpoint (punto 4).

## Cosa sopravvive tra le sessioni

- **Git:** le modifiche al codice, branch/worktree, commit e diff della PR sono la fonte di verità per lo stato dell'implementazione. Possono esserci file non committati: controlla `git status` e non scartarli in silenzio.
- **Linear:** identità della issue, revisioni approvate di spec e piano, fase corrente, approvazioni, stato di dipendenza/blocco, budget di tentativi e di cicli di review, link alla PR e all'ultimo checkpoint durevole.
- **GitHub:** prove di CI/PR/review legate allo SHA esatto del commit.
- **Hindsight:** solo memoria tecnica riutilizzabile a lungo termine; può non essere disponibile; non è mai usato come registro che fa fede dello stato del task o delle approvazioni.
- **Checkpoint handoff:** istantanea operativa sintetica pubblicata sulla issue o in un altro luogo persistente autorizzato equivalente. Le sue affermazioni sono consultive finché non sono verificate sulle fonti sopra.

**Non** presumere che la memoria conversazionale di ChatGPT o dell'agente, la catena di ragionamento nascosta, il nome del worktree o un file YAML locale da soli garantiscano una ripresa affidabile.

## Algoritmo di inizio sessione

1. Determina l'ID della issue da un'istruzione esplicita dell'utente o dallo slot di esecuzione approvato su Linear. Se è ambiguo, fermati. Non scegliere in autonomia la issue successiva del backlog.
2. Leggi `CLAUDE.md` e le sezioni rilevanti del workflow e dell'architettura. Leggi da Linear la revisione della spec e la revisione del piano, approvata separatamente; conferma la versione di governance fissata all'inizio.
3. Verifica il repository e il worktree reali: posizione, branch, `HEAD`, `git status`, lavoro non tracciato o non committato, divergenza dal remoto, cherry-pick/rebase/merge in sospeso. Verifica il collegamento tra issue e branch.
4. Leggi l'ultimo checkpoint e lo stato di PR/CI, e confrontali con lo stato reale di Git e Linear. Tratta le discrepanze come potenziali blocchi di ripresa; non sovrascrivere lavoro più recente. Un reviewer indipendente legge solo lo stato di PR/CI, non il checkpoint.
5. Carica gli ADR, i contratti e il perimetro dei test rilevanti; fai query `recall` mirate su Hindsight in base alla issue e al componente. Verifica i fatti recuperati sulla fonte. Un reviewer indipendente non fa query su Hindsight (`docs/DEVELOPMENT_WORKFLOW.md` §7).
6. Determina il checkpoint esatto (CP01–CP06), i conteggi di correzioni e di review già consumati e l'azione successiva consentita. Controlla se le operazioni precedenti con effetti collaterali sono già avvenute prima di ritentarle.
7. Produci una breve valutazione di ripresa: stato verificato; AC/finding rimanenti; operazione successiva; gate umano in attesa. Esegui solo se tutte le approvazioni richieste e i controlli di sicurezza sono soddisfatti.

## Checkpoint di fine sessione / interruzione

Registra quando: si raggiunge un checkpoint, la sessione sta per finire, il contesto è saturo, un errore di un tool richiede la sostituzione dell'agente o della sessione, c'è un blocco o un passaggio alla review. Preferisci un commento sulla issue o uno stato strutturato su Linear, collegato al commit e alla PR. La prima riga di ogni commento di checkpoint dell'implementer è esattamente `**Implementer checkpoint (ethereal.handoff.v1) — reviewers do not read this comment.**`; i reviewer saltano ogni commento che inizia con questa riga marcatore. Facoltativamente usa un handoff locale effimero per comodità, mai come unica copia.

Registrazione minima:

```yaml
schema: ethereal.handoff.v1
issue: ICH-XXX
checkpoint: CP03
phase: implementation
governance_version: 2.14-draft
spec_revision: "<approved-revision-id>"
plan_revision: "<approved-revision-id>"
branch: improvments/ICH-XXX-opus
worktree: "<repo-root>/.claude/worktrees/improvments+ICH-XXX-opus"
head_commit: "<full-git-sha>"
worktree_clean: false
pr: "<url-or-null>"
acceptance_criteria:
  AC-01: passed
  AC-02: pending
verification:
  - command: "ctest --test-dir build --output-on-failure"
    result: "failed (example)"
    evidence: "<CI link or sanitized log reference>"
review:
  cycles_used: 0
  open_findings: []
recovery:
  root_cause: "<classified cause or null>"
  corrections_used_for_root_cause: 0
blocking_decisions: []
next_allowed_action: "<one concrete, authorized action>"
last_updated: "<timestamp-with-timezone>"
```

I valori sono **illustrativi**. Non dichiarare superati i controlli che non hai misurato e non inventare mai SHA, revisioni, approvazioni o timestamp.

## Sicurezza e affidabilità

- Escludi segreti, token di autenticazione, credenziali personali, contenuti sensibili del terminale e prompt dell'agente riportati alla lettera.
- Mantieni stabili tra i riavvii dell'agente gli ID dei finding di review e il contatore dei cicli; tieni i contatori di recupero legati alla causa radice.
- La verifica deve precedere l'esecuzione quando l'ultimo handoff può essere obsoleto.
- Se l'agente precedente è scomparso durante una modifica (creazione della PR, modifica su Linear), ispeziona lo stato remoto per evitare duplicati.
- Un reviewer parte da un contesto **separato** e pulito e legge in modo indipendente la spec canonica, il piano approvato, i record di approvazione, il diff, le prove dei test e la memoria su file (`memory/`) nel suo worktree di review; non usa Hindsight, non deve ricevere lo scratchpad della conversazione dell'implementer e non legge i commenti di checkpoint (riga marcatore sopra).
- Hindsight può aiutare a recuperare i gotcha ma **non deve** fare da database dei checkpoint né da meccanismo per aggirare le approvazioni della issue.

## Automazione pianificata e ciò che è disponibile oggi

Uno script di orchestrazione potrebbe in seguito implementare `resume` / `checkpoint` e validare gli stati tra Git, Linear, GitHub e Hindsight. **In questo pacchetto non esiste alcuno script né integrazione tra strumenti**. Finché non sarà costruita, i passi sono una procedura operativa esplicita per l'agente, che richiede accesso reale alle fonti indicate. Se manca un connettore, chiedi il contesto necessario o fermati al gate; non inventarlo.
