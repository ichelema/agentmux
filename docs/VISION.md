# AgentMux — Visione del prodotto

**Stato:** visione del prodotto che fa fede, parte della baseline di architettura approvata dal proprietario con ICH-588 (M0-03), sotto `governance 2.13-draft`. Le modifiche seguono `docs/DEVELOPMENT_WORKFLOW.md` §3.

## Scopo

AgentMux è un gestore leggero di sessioni di terminale, pensato per Linux. Serve a far funzionare strumenti CLI interattivi esistenti e, più avanti, a orchestrare agent AI di programmazione esistenti. Separa i processi di terminale di lunga durata dalla loro interfaccia utente, così le sessioni locali o remote possono continuare quando il client si disconnette.

## Principi fondamentali

1. **Il daemon possiede l'esecuzione:** `agentd` possiede i master PTY, il ciclo di vita dei processi figli, le identità delle sessioni e lo stato del terminale virtuale che fa fede.
2. **Il client possiede la presentazione:** `agentctl` fornisce una TUI interattiva, la configurazione iniziale e i profili di connessione, le tab, gli split, il focus e l'instradamento dell'input.
3. **Un unico modello mentale per locale e remoto:** `agentctl` si connette a un endpoint `agentd` tramite un socket Unix locale o un trasporto via SSH; il protocollo applicativo resta coerente.
4. **Riconnettersi senza perdere la sessione:** un client disconnesso può riconnettersi finché il daemon e l'host restano in esecuzione; il ripristino dopo un guasto del daemon o un riavvio dell'host **non** è promesso nel perimetro iniziale.
5. **Usare le librerie secondo la responsabilità:** `libghostty-vt` in `agentd` per l'emulazione di terminale VT/ANSI; Notcurses in `agentctl` per il rendering della TUI. Le funzionalità Linux per PTY ed eventi sostengono il runtime.
6. **L'integrazione degli agent è facoltativa e incrementale:** shell e Vim devono funzionare in modo indipendente prima di aggiungere il supporto a strumenti esistenti come Claude Code, Codex e Gemini CLI. OSC 7501 è un possibile segnale di stato, non la prova che un'attività sia completata.
7. **Incrementi piccoli e facili da sottoporre a review:** consegna guidata dalle specifiche tramite issue Linear atomiche (`ICH-XXX`), review indipendente e merge controllato da un umano.

## Capacità previste

- Avviare e gestire processi interattivi indipendenti, basati su PTY, su Linux.
- Mostrare più sessioni di terminale tramite tab e pane divisi, con focus dell'input e ridimensionamento corretti.
- Mantenere l'output della sessione e lo stato dello schermo virtuale mentre nessun client è collegato.
- Usare lo stesso client per endpoint locali e remoti via SSH; supportare la riconnessione e i profili di configurazione.
- Esporre un protocollo client/daemon versionato, con compatibilità e negoziazione delle capacità esplicite.
- In seguito, osservare e controllare gli agent AI da CLI esistenti senza modificarne gli interni.

## Non-obiettivi espliciti per le prime versioni

- Reimplementare tutte le funzionalità di tmux o dipendere da tmux, abduco o dtach per la persistenza delle sessioni.
- Garantire il recupero dei processi attivi dopo un crash del daemon o un riavvio dell'host Linux.
- Implementare un LLM, sostituire gli interni delle applicazioni CLI di AI esistenti, o affidarsi solo al testo del terminale o allo stato OSC per la correttezza semantica.
- Offrire la condivisione remota del terminale tra più utenti, l'alta disponibilità distribuita o l'orchestrazione di cluster di livello produttivo nelle prime milestone.
- Aggiungere molte dipendenze da framework o UI complesse prima che il prototipo del runtime di terminale sia validato.

## Criteri di successo

1. Due sessioni PTY Linux indipendenti possono eseguire Vim e una shell, con input e rendering funzionanti.
2. Un client può mostrare entrambe le sessioni in uno split verticale, cambiare il focus e ridimensionare senza corrompere il loro stato di terminale.
3. Dopo che `agentctl` si chiude o SSH si disconnette, le sessioni continuano mentre `agentd` resta attivo e possono essere ricollegate.
4. Le connessioni locali e remote usano lo stesso protocollo applicativo versionato, con un comportamento esplicito in caso di errore.
5. Il progetto segue il workflow di sviluppo approvato: approvazione della specifica e del piano su Linear, isolamento con worktree, prove della CI, review indipendente e merge approvato da un umano.

## Confini del perimetro e riferimenti che fanno fede

Questo documento definisce **perché** AgentMux esiste e che cosa vuole ottenere. `docs/REQUIREMENTS.md` definisce i requisiti; `docs/ARCHITECTURE.md` definisce l'architettura; gli ADR approvati documentano le decisioni architetturali; `docs/DEVELOPMENT_WORKFLOW.md` definisce la governance dello sviluppo; Linear contiene i criteri di accettazione specifici di ogni issue e i piani approvati. I conflitti richiedono una risoluzione umana, non ipotesi unilaterali degli agenti.

## Direzione delle milestone

M0: fondamenta e pilota controllato; M1: runtime PTY; M2: emulazione di terminale; M3: TUI Notcurses e layout; M4: detach/reattach; M5: configurazione locale/remota e SSH; M6: integrazione degli agent da CLI esistenti.

**Approvazione:** approvata dal proprietario tramite ICH-588 (M0-03); il record di approvazione è su quella issue di Linear.
