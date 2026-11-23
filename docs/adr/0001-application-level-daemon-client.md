# ADR-0001 — Daemon PTY a livello di applicazione e client Notcurses

**Stato:** Accettato — approvazione del proprietario con ICH-588 (M0-03), specifica approvata il 2026-11-22, in vigore dal merge del proprietario.

## Contesto

AgentMux ha bisogno di processi figli di terminale indipendenti, di collegamento locale e remoto e della conservazione dello stato del PTY dopo la disconnessione della UI o di SSH, senza dipendere da tmux/abduco/dtach.

## Decisione

Usa due eseguibili Linux in C17: `agentd`, persistente e proprietario dei PTY, e `agentctl`, basato su Notcurses. `agentd` esegue il parsing ANSI/VT con `libghostty-vt`, a versione fissata, tramite la sua interfaccia C, ed espone un protocollo IPC con versione su socket Unix o su stream portato da SSH. `agentctl` gestisce layout e disegno della UI.

## Conseguenze

- Responsabilità e confini di fiducia chiari; il daemon ha una supervisione indipendente.
- Vanno costruiti snapshot/diff via IPC, riconnessione/risincronizzazione, gestione delle risorse e autenticazione affidabili.
- La persistenza dura quanto il daemon, non quanto l'interfaccia utente; il ripristino dopo un riavvio del daemon è escluso all'inizio.
- L'integrazione delle librerie di terze parti e le API esatte vanno controllate prima di implementare le milestone interessate.

## Alternative considerate

Emulatore di terminale incorporato nel client; passthrough del terminale verso l'emulatore di terminale; dipendenza da tmux/abduco/dtach. Scartate per l'architettura scelta.
