# Ethereal

**Stato:** `governance 2.14-draft`, non congelata. Questo repository **non** è un progetto implementato.

Ethereal è un multiplexer e orchestratore di sessioni di terminale per Linux, a livello di applicazione, per gli agent AI da CLI esistenti, con un daemon PTY persistente e un client Notcurses in C17.

Non esistono ancora codice, CI, test né protezione dei branch.

## Documenti

- [`CLAUDE.md`](CLAUDE.md): istruzioni operative per un agente che parte in questo repository.
- [`docs/VISION.md`](docs/VISION.md): visione del prodotto, principi e non-obiettivi.
- [`docs/REQUIREMENTS.md`](docs/REQUIREMENTS.md): riepilogo dei requisiti del prodotto.
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md): baseline di architettura di `agentd` e `agentctl`.
- [`docs/adr/0001-application-level-daemon-client.md`](docs/adr/0001-application-level-daemon-client.md): ADR-0001, daemon PTY a livello di applicazione e client Notcurses.
- [`docs/security/THREAT_MODEL.md`](docs/security/THREAT_MODEL.md): confini di fiducia e controlli minimi.
- [`docs/BOOTSTRAP_PLAN.md`](docs/BOOTSTRAP_PLAN.md): issue di bootstrap di M0 e pilota controllato.
- [`docs/DEVELOPMENT_WORKFLOW.md`](docs/DEVELOPMENT_WORKFLOW.md): governance dello sviluppo e salvaguardie.
- [`docs/specs/SESSION_CONTINUITY.md`](docs/specs/SESSION_CONTINUITY.md): ripresa tra sessioni, formato dell'handoff, recupero.
- [`.github/pull_request_template.md`](.github/pull_request_template.md): struttura del corpo della PR (in inglese, come vuole la label `git:en` del progetto Linear).
- [`handoff.md`](handoff.md): prossimi passi per la prossima sessione dell'agente, nient'altro.
- [`memory/memory.md`](memory/memory.md): indice della memoria persistente del progetto.
- [`.mcp.json`](.mcp.json) e [`.claude/rules/context7.md`](.claude/rules/context7.md): server di documentazione Context7 e la regola che dice agli agenti quando usarlo.
