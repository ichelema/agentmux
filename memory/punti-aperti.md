# Punti aperti

Deviazioni, rischi rinviati e note che aspettano una decisione dell'utente. Quando un punto si
chiude, rimuovi la voce e registra l'esito in [storico.md](storico.md) o in
[decisioni.md](decisioni.md).

1. **Linear porta la issue a Done al merge (deviazione E6 di ICH-566, E5 di ICH-588).**
   L'integrazione GitHub di Linear sposta la issue a Done, non a In Review: è successo con PR #2 e
   con PR #3. Lo stato Done non prova la chiusura: la prova CP06 è il commento di chiusura. Solo
   l'utente cambia l'impostazione (OQ-2 di ICH-566, OQ-5 di ICH-588); conviene farlo prima del
   prossimo merge. Finché non cambia, dopo il merge controlla lo stato e registra la deviazione nel
   commento di chiusura.
2. **Scritture senza review lette alla ripresa (rischio F6, rinviato a M0-06).** Un agente può
   scrivere istruzioni in `handoff.md` e in `memory/` con un commit diretto, senza review. L'hook
   di avvio rimanda a «Ripresa del lavoro» di `CLAUDE.md`, che fa leggere `handoff.md` alla
   ripresa; `CLAUDE.md` fa consultare `memory/` e ogni
   sessione carica `.claude/` (hook e regole, per esempio `.claude/rules/context7.md`). La spec
   `spec-r2` di ICH-566 (OQ-9, approvata dall'utente) rinvia il finding e lo rende un input
   obbligatorio della spec di M0-06; la spec di ICH-588 lo ripete per `handoff.md` e `.claude/`.
   M0-06 sceglie tra protezione di `main`, CODEOWNERS o una regola di diff visibile all'utente:
   oggi nessuna è installata. M0-06 mantiene compatibile l'eccezione `docs(ripresa)`. Il record
   del rinvio stava in `RIPRESA.md`, poi in `handoff.md`; dal riordino del 2026-11-22 sta qui, e
   la chiusura della sessione lo riporta in `handoff.md` quando la prossima issue è M0-06.
3. **Prefisso del branch.** `1_create-worktree` conosce solo `bug` e `improvments`; M0-02 e M0-03
   hanno usato `improvments`. L'utente decide il prefisso delle prossime issue.
4. **Nota residua della review di ICH-588** (nessuna issue aperta). Parte del testo storico di
   `docs/BOOTSTRAP_PLAN.md` («Prerequisiti (di responsabilità umana)», «Ordine proposto delle
   issue M0 (label di pianificazione e ID Linear)») è superata. La traduzione in italiano del
   2026-11-23 (punto 15 di [decisioni.md](decisioni.md)) non l'ha aggiornata.
5. **Memoria scritta durante una issue (finding L3 della review Fable del 2026-11-23, Low).** Se
   il branch di una issue viene riallineato a `main` dopo un commit `docs(ripresa)`, il worktree
   di review contiene note di memoria scritte durante la issue, che il diff della PR non mostra.
   La regola «i file di memoria che la PR cambia fanno parte del changeset» non le copre. Per
   coprirle va cambiata la procedura di chiusura della sessione: decide l'utente.
6. **`handoff.md` letto dal reviewer (nota della stessa review).** La sezione «Ripresa del
   lavoro» di `CLAUDE.md` fa leggere `handoff.md` anche al reviewer indipendente, che salta solo
   il punto 5. Il problema esisteva già prima di `2.12-draft`.
7. **Termini non allineati nei documenti tradotti (finding F-08 della review Fable del
   2026-11-23, Low).** Alcune coppie traducono lo stesso concetto in modi diversi: «gotcha» e
   «tranelli tecnici» sono già allineati, restano per esempio «provisioning» contro
   «predisporre», «merge e release» contro «merge e rilascio», «M0 a fasi» contro «Bootstrap
   graduale di M0», «safety» contro «sicurezza». Anche `.claude/rules/context7.md`, tradotto dopo
   la review, non ha avuto una review. Si può correggere in una issue di documenti successiva.
