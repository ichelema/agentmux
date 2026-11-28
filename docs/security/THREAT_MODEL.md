# Modello delle minacce di Ethereal — baseline

**Stato:** parte della baseline di architettura approvata dal proprietario con ICH-588 (M0-03), sotto `governance 2.14-draft`. Le modifiche seguono `docs/DEVELOPMENT_WORKFLOW.md` §3.

Modellazione basata sul rischio. Riesamina i confini quando cambiano IPC/SSH, credenziali, proprietà del PTY, parsing del terminale, privilegi, dipendenze o superfici sensibili.

| Confine | Asset / minacce | Controlli minimi |
|---|---|---|
| TB-01 client ↔ trasporto | Impersonificazione remota, intercettazione | Verifica della host key di OpenSSH, autenticazione standard, nessuna credenziale in chiaro |
| TB-02 trasporto ↔ IPC del daemon | Client non autorizzato, frame malformati | Permessi UDS, credenziali del peer Linux (`SO_PEERCRED`), autorizzazione della sessione, validazione di versione e dimensione |
| TB-03 daemon ↔ processi figli | Escalation di privilegi, leak di FD, esaurimento delle risorse | Esecuzione senza privilegi, chiusura e proprietà degli FD, pulizia del ciclo di vita, sessioni e buffer limitati |
| TB-04 output del PTY figlio ↔ parser VT/UI | ANSI/OSC malformati, abuso delle escape del terminale, abuso di memoria, stato falso | Tratta l'output come ostile, test del parser e target di fuzz dove giustificato, limiti, sanifica risposte e azioni, OSC non è una prova di sicurezza |

## Gate di sicurezza

- Valuta le modifiche ai confini di fiducia e la validazione degli input nel piano e nella review della issue.
- Usa test negativi e di integrazione mirati per le interfacce a rischio; ASan/UBSan e controlli CI secondo la policy.
- I finding Critical/High bloccano il merge e il rilascio; quelli Medium/Low si possono rinviare solo tramite issue collegate approvate esplicitamente.
- Evita i segreti nei log, in Hindsight, nei prompt degli agenti, negli handoff e negli artefatti non fidati delle PR.
- Limita chi può modificare i CI/workflow protetti o i controlli di governance.

## Rischi da valutare nelle issue future

Controllo degli accessi alla sessione, accesso ai socket tra utenti diversi, comportamento del bridge remoto, effetti delle escape del terminale, limitazione delle risorse, gestione dei crash, ridimensionamento simultaneo da più client, esposizione dell'ambiente dei processi figli, provenienza delle dipendenze. Non si afferma che questi controlli siano già implementati.
