# Insidie

Problemi già incontrati: sintomo, causa, correzione. Le regole che ne derivano sono in
[`CLAUDE.md`](../CLAUDE.md).

1. **Parole magiche di Linear (M0-02).** Una parola magica di Linear seguita dall'ID di un'altra
   issue collega quella issue alla PR e la porta In Progress. Vale per le parole di chiusura
   (`Resolves`, `Fixes`, `Closes`…) e per quelle di collegamento (`Refs`, `Related to`…): in PR #2
   ICH-557, ICH-558 e ICH-559 le ha spostate `Refs` nel corpo della PR. Rimuovere il link non
   basta, perché ogni sync della PR lo ricrea. Correzione: usa una parola magica solo con l'ID
   della issue attiva; scrivi le altre issue in una riga `Related: ICH-…`. Attenzione:
   `2_work-issue` consiglia `Refs` per le issue correlate.
2. **Bash in un worktree (M0-03).** In una sessione entrata nel worktree con `EnterWorktree`, il
   controllo di isolamento rifiuta i comandi Bash composti con `git`: cicli `for`, `cd` in
   subshell, comandi salvati in variabili. Correzione: un comando semplice per chiamata, con path
   assoluti.
3. **Scritture Linear da un subagente.** Il classificatore dei permessi può bloccare `issueUpdate`
   e i commenti scritti da un subagente o da un fork. Correzione: scrivi su Linear dalla sessione
   principale con `linear.py --variables @file.json`; crea il file JSON con `jq -n --rawfile`.
4. **File non tracciati e fast-forward di `main` (M0-02).** Le copie seed non tracciate nel
   checkout principale bloccavano il fast-forward dopo un merge che aggiungeva gli stessi file.
   Correzione: niente `git pull` e niente forzature. Sposta le copie in
   `~/.claude/tmp/agentmux-seed-bak/<issue>/` solo con il consenso dell'utente, che vale per una
   volta (per M0-02 l'ha dato il 2026-11-20); poi esegui `git fetch` e `git merge --ff-only`. Dopo
   M0-03 il checkout principale non ha più copie seed.
5. **Apostrofi nell'hook di avvio.** Il comando dell'hook `SessionStart` in `.claude/settings.json`
   mette il testo tra virgolette doppie. La prima versione usava gli apici singoli, quindi il testo
   italiano non poteva contenere apostrofi (correzione nel commit `315a842`).
