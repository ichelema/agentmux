# Ambiente e riferimenti

## Servizi

- **Linear:** team `ICH`, progetto **Ethereal**, label di progetto `lang:it` (issue in italiano) e
  `git:en` (commit e PR in inglese).
- **GitHub:** `ichelema/Ethereal`, pubblico. `main` non ha protezione né CI: arrivano con M0-06.
  Fino al 2026-11-28 il repository era `ichelema/agentmux`: GitHub reindirizza il vecchio URL.
- **Hindsight:** bank `Ethereal`, tag `repo:Ethereal` e `claude-code`. Il bank è un `clone` del
  bank `agentmux` del 2026-11-28, con i tag cambiati. Il bank `agentmux` è cancellato dal
  2026-11-28, su istruzione dell'utente; il suo export è in
  `~/.claude/tmp/ethereal-rename/agentmux-bank-export-20261128.json`. Il file
  `hindsight.config.json` nella root (mission, mental model e pagine di conoscenza del bank) è
  tracciato da Git dal 2026-11-23, così vale uguale su ogni macchina.
- **Claude Code:** `.claude/settings.json` (tracciato) contiene l'hook `SessionStart` e abilita il
  server MCP `context7` di `.mcp.json`. `.claude/settings.local.json` resta in `.gitignore` per le
  impostazioni di una sola macchina.

## Path

- Repository: `/mnt/data/Sviluppo/Progetti/Personali/20261120_ethereal`.
- Worktree delle issue e delle review: `.claude/worktrees/` nella root, ignorata da Git.
- Copie seed dei file entrati in Git, in `~/.claude/tmp/ethereal-seed-bak/`:
  - nella radice della cartella: `README.md` e `.gitignore`;
  - `m0-02/`: i 5 file tracciati da M0-02. Sotto `m0-02/.claude/` c'è anche una catena di
    quattro directory vuote con nomi sbagliati (la prima si chiama `settings.json .github`),
    creata per errore il 2026-11-20: si può rimuovere;
  - `ICH-588/`: gli 8 file tracciati da M0-03.

## Comandi

Lo script Linear legge la chiave dal file `.env` nella cartella della skill: funziona da qualsiasi
directory.

```bash
# Linear: lettura
python /home/ichelema/.claude/skills/trinity/skills/linear/scripts/linear.py query '{ issues(filter:{project:{name:{eq:"Ethereal"}}}){ nodes{ identifier title state{name} } } }'
# Linear: scrittura; crea prima il file JSON con jq -n --rawfile
python /home/ichelema/.claude/skills/trinity/skills/linear/scripts/linear.py mutation 'mutation($input: CommentCreateInput!){ commentCreate(input:$input){ success comment{ url } } }' --variables @/home/ichelema/.claude/tmp/linear-comment.json

# GitHub
gh pr list -R ichelema/Ethereal --state all

# Git: stato reale
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_ethereal worktree list
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_ethereal status --short --branch
```
