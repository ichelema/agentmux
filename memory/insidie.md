# Insidie

Problemi già incontrati: sintomo, causa, correzione. Le regole che ne derivano sono in
[`CLAUDE.md`](../CLAUDE.md).

1. **Parole magiche nei commit (M0-02).** Un commit con `Resolves`, `Fixes` o `Closes` seguito
   dall'ID di un'altra issue fa collegare quella issue alla PR: Linear la porta In Progress.
   Rimuovere il link non basta, perché ogni sync della PR lo ricrea. Correzione: usa le parole
   magiche solo con l'ID della issue attiva; nel corpo della PR scrivi le altre issue in una riga
   `Related`.
2. **Bash in un worktree (M0-03).** In una sessione entrata nel worktree con `EnterWorktree`, il
   controllo di isolamento rifiuta i comandi Bash composti con `git`: cicli `for`, `cd` in
   subshell, comandi salvati in variabili. Correzione: un comando semplice per chiamata, con path
   assoluti.
3. **Scritture Linear da un subagente.** Il classificatore dei permessi può bloccare `issueUpdate`
   e i commenti scritti da un subagente o da un fork. Correzione: scrivi su Linear dalla sessione
   principale con `linear.py --variables @file.json`; crea il file JSON con `jq -n --rawfile`.
4. **File non tracciati e fast-forward di `main` (M0-02).** Le copie seed non tracciate nel
   checkout principale bloccavano il fast-forward dopo un merge che aggiungeva gli stessi file.
   Correzione: niente `git pull` e niente forzature. Con il consenso dell'utente sposta le copie in
   `~/.claude/tmp/agentmux-seed-bak/<issue>/`, poi esegui `git fetch` e `git merge --ff-only`. Dopo
   M0-03 il checkout principale non ha più copie seed.
5. **Apostrofi nell'hook di avvio.** Il comando dell'hook `SessionStart` in `.claude/settings.json`
   mette il testo tra virgolette doppie. La prima versione usava gli apici singoli, quindi il testo
   italiano non poteva contenere apostrofi (correzione nel commit `315a842`).
