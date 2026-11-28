# Requisiti del prodotto Ethereal — riepilogo

**Stato:** riepilogo di progettazione, parte della baseline di architettura approvata dal proprietario con ICH-588 (M0-03), sotto `governance 2.14-draft`; i criteri di accettazione tecnici devono essere elaborati nelle issue e nelle spec approvate su Linear. Le modifiche seguono `docs/DEVELOPMENT_WORKFLOW.md` §3.

## Capacità previste

- Multiplexing di terminale client/daemon su Linux, con un daemon persistente che possiede i PTY.
- Shell interattive e applicazioni di terminale a schermo intero (per esempio Vim) con emulazione VT.
- Interazione locale e remota tramite un unico contratto IPC versionato, con OpenSSH per l'accesso remoto.
- Tab e split lato client, focus dei pane e instradamento dell'input con Notcurses.
- Snapshot e diff dello schermo del terminale trasmessi dal daemon al client; riconnessione dopo la disconnessione del client o di SSH mentre il daemon resta attivo.
- Integrazione facoltativa dello stato degli agent da CLI (Claude Code, Codex, Gemini), incluso OSC 7501 quando è compatibile e verificato.
- Wizard di configurazione e profili di configurazione XDG/TOML; negoziazione indipendente del protocollo tra client e daemon.

## Non-obiettivi nelle milestone iniziali

- Reimplementare o modificare gli interni degli agent da CLI di terze parti.
- Ripristino duraturo di processi e sessioni dopo un crash del daemon, un riavvio o un guasto dell'host.
- Implementare tmux, abduco o dtach come dipendenze per il detach/attach.
- Rilasciare funzionalità di terminale o SSH durante M0.
- Presumere che lo stato OSC rifletta un successo affidabile dell'attività.

## Requisiti trasversali

Privilegio minimo; IPC sicuro; limiti per input e memoria; contratti API espliciti; build del prodotto in C17; test basati sul rischio che combinano mock, PTY reali e integrazione end-to-end; nessun segreto memorizzato nei log o nella memoria dell'AI. Consulta `docs/ARCHITECTURE.md` e `docs/security/THREAT_MODEL.md`.
