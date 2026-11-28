# Architettura di Ethereal — baseline di progetto concordata

**Stato:** deriva dalle decisioni di pianificazione; fa parte della baseline di architettura approvata dal proprietario con ICH-588 (M0-03), sotto `governance 2.15-draft`. L'evoluzione dell'architettura segue §3 di `docs/DEVELOPMENT_WORKFLOW.md` e gli ADR approvati.

## Obiettivo e modello operativo

Multiplexer/orchestratore di terminale nativo per Linux, per gli agent da CLI esistenti (Claude Code, Codex, Gemini), per le shell normali e per i programmi TUI interattivi (Vim compreso). È uno strumento a livello di applicazione, non una patch agli agent da CLI esistenti. I sorgenti del prodotto sono in **C17**, con CMake/Ninja; alcune librerie di terze parti con versione fissata possono essere scritte in altri linguaggi (in particolare Zig dietro la C API di `libghostty-vt`).

## Eseguibili

### `agentd` — daemon persistente lato host

- Gira sul target Linux locale o remoto, indipendente dai client connessi; la supervisione spetta a un servizio utente esterno, per esempio `systemd --user` con il lingering dove serve.
- Possiede i master PTY, il ciclo di vita di processi e sessioni, lo stato del terminale virtuale di ogni sessione e il server IPC.
- Primitive Linux: API PTY, `epoll`, socket Unix domain, gestione dei processi e di wait.
- Usa **libghostty-vt** nel daemon per interpretare l'output ANSI/VT, gestire lo stato del terminale e produrre la rappresentazione snapshot/diff; la C API esatta, la licenza e la compatibilità di dipendenze e toolchain vanno controllate in una milestone.
- Tratta come non fidati i byte di terminale e gli eventi OSC in ingresso. OSC 7501 può portare indicatori di stato dell'agent, ma non è una prova fidata che un task sia finito con successo; il supporto al protocollo tastiera di Kitty richiederà test delle capacità.
- Lo stato della sessione sopravvive al detach del client e alla disconnessione SSH finché il daemon e i processi dell'host restano in esecuzione; **non promette di sopravvivere a un riavvio del daemon, a un riavvio dell'host o a un crash di processo**.

### `agentctl` — client locale interattivo

- CLI/TUI con **Notcurses**, che usa i plane per pane, split e tab e per il focus e l'instradamento dell'input da tastiera.
- Non possiede il PTY che fa fede e non usa `libghostty-vt` per riprodurre lo stato del terminale. Riceve da `agentd` gli snapshot dello schermo e gli aggiornamenti incrementali da disegnare.
- Supporta la configurazione al primo avvio dei profili locali e remoti; configurazione XDG/TOML. Evita di salvare segreti nella configurazione.
- Target locale: socket Unix domain. Target remoto: trasporto **OpenSSH**, che porta lo stesso protocollo applicativo a un bridge verso il socket del target/daemon (implementazione con bridge stdio o tunnel, da scegliere e specificare prima di scrivere codice). Usa `~/.ssh/config`, l'autenticazione di OpenSSH e il controllo delle host key.

## Mappa della fiducia e delle interfacce

```text
Local workstation                         Target Linux host (local or remote)
+-----------------------------+            +------------------------------------+
| agentctl                    |            | agentd                             |
| Notcurses UI / layout       |            | IPC endpoint and authorization     |
| config profiles / SSH       |            | session + process manager          |
+--------------+--------------+            | PTY masters + epoll                |
               | Unix socket / SSH bridge  | libghostty-vt instances            |
               +-------------------------->| virtual terminal snapshots/diffs   |
                                            +----------------+-------------------+
                                                             | PTY child I/O
                                            +----------------v-------------------+
                                            | shell / Vim / agent CLI processes   |
                                            +------------------------------------+
```

## IPC e compatibilità

- Il protocollo a livello applicativo funziona sul socket Unix locale e sugli stream portati da SSH; ha una versione separata da quella del rilascio del prodotto.
- L'handshake tra client e server negozia versione e capacità del protocollo. I due eseguibili condividono una versione di prodotto, mentre la versione dell'IPC è indipendente.
- Sicurezza e limiti per il framing e la convalida dei messaggi sono obbligatori. Il formato concreto sul filo, gli schemi dei payload, la semantica esatta dei diff e la strategia di bridge **non sono ancora approvati** e vanno specificati in issue atomiche successive.

## Proprietà e comportamento a runtime

- In ogni momento un solo daemon possiede un dato PTY/sessione gestito; attach e detach del client non devono mai chiudere il PTY che fa fede solo perché la UI si disconnette.
- `agentd` mantiene lo stato VT mentre nessun client è collegato. I client che si ricollegano devono ottenere uno snapshot coerente, poi seguire diff ed eventi con l'ordinamento e la risincronizzazione adatti.
- L'utente può usare shell e Vim normali, indipendenti dalle integrazioni con gli agent; le funzioni di stato degli agent sono estensioni di milestone successive.
- All'inizio il layout delle sessioni appartiene ad `agentctl`; la sincronizzazione del layout tra più client non fa parte della baseline approvata.

## Milestone incrementali

| Milestone | Scopo |
|---|---|
| M0 | repository, governance, baseline di architettura, scheletro di build C17, framework di test, CI |
| M1 | possesso e ciclo di vita dei PTY nel daemon |
| M2 | integrazione di libghostty-vt; snapshot/diff del terminale |
| M3 | client Notcurses, tab/split, input/focus |
| M4 | attach/detach tra daemon e client e persistenza dopo la disconnessione del client |
| M5 | profili di target locali e remoti e trasporto SSH |
| M6 | integrazione degli agent da CLI e stato OSC 7501 |

L'ordine delle milestone è un piano e può cambiare con una issue o un ADR approvati; assicurati che ogni consegna incrementale si possa testare.

## Vincoli noti e domande di progetto aperte

1. Controlla la C API esatta di `libghostty-vt`, l'integrazione della build Zig, le funzioni di stato e Kitty e la licenza prima di fissare la versione della dipendenza.
2. Definisci il framing dell'IPC sul filo, la gestione di errori e versioni, snapshot/diff, il controllo di flusso e i limiti di risorse.
3. Scegli una modalità di bridge SSH e autentica e autorizza in modo corretto il socket del daemon locale sull'host remoto.
4. Definisci l'identità della sessione, la concorrenza tra client e l'arbitraggio del resize prima dell'implementazione.
5. Decidi le mitigazioni delle minacce per OSC, risposte alle sequenze di escape del terminale, effetti simili agli appunti e pressione sulle risorse.
6. Definisci le distribuzioni Linux supportate e le versioni della toolchain; fissa le versioni solo dopo aver controllato le build.

Queste sono **decisioni tecniche aperte**, non un permesso di improvvisare cambi di architettura durante una issue atomica.
