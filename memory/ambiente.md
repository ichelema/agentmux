# Ambiente e riferimenti

## Servizi

- **Linear:** team `ICH`, progetto **Agentmux**, label di progetto `lang:en`.
- **GitHub:** `ichelema/agentmux`, pubblico. `main` non ha protezione né CI: arrivano con M0-06.
- **Hindsight:** bank `agentmux`, tag `repo:agentmux` e `claude-code`. Il file locale
  `hindsight.config.json` nella root è ignorato da Git.

## Path

- Repository: `/mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux`.
- Worktree delle issue e delle review: `.claude/worktrees/` nella root, ignorata da Git.
- Copie seed dei file entrati in Git, in `~/.claude/tmp/agentmux-seed-bak/`:
  - nella radice della cartella: `README.md` e `.gitignore`;
  - `m0-02/`: i 5 file tracciati da M0-02. Contiene anche una directory vuota con un nome
    sbagliato, creata per errore il 2026-11-20: si può rimuovere;
  - `ICH-588/`: gli 8 file tracciati da M0-03.

## Comandi

Lo script Linear legge la chiave dal file `.env` nella cartella dello skill: funziona da qualsiasi
directory.

```bash
# Linear
python /home/ichelema/.claude/skills/trinity/skills/linear/scripts/linear.py query '{ issues(filter:{project:{name:{eq:"Agentmux"}}}){ nodes{ identifier title state{name} } } }'
python /home/ichelema/.claude/skills/trinity/skills/linear/scripts/linear.py mutation 'mutation($input: CommentCreateInput!){ commentCreate(input:$input){ success comment{ url } } }' --variables @<file.json>

# GitHub
gh pr list -R ichelema/agentmux --state all

# Git: stato reale
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux worktree list
git -C /mnt/data/Sviluppo/Progetti/Personali/20261120_agentmux status --short --branch
```
