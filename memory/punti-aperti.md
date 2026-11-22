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
2. **Scritture senza review nei documenti di sessione (rischio F6, rinviato a M0-06; spec-r2 di
   ICH-566, OQ-9).** Un agente può scrivere istruzioni in `handoff.md` e in `memory/` con un
   commit diretto, senza review. L'hook di avvio fa leggere `handoff.md` alla ripresa e
   `CLAUDE.md` fa consultare `memory/`. M0-06 sceglie tra protezione di `main`, CODEOWNERS o una
   regola di diff visibile all'utente, e mantiene compatibile l'eccezione `docs(ripresa)`.
3. **Prefisso del branch.** `1_create-worktree` conosce solo `bug` e `improvments`; M0-02 e M0-03
   hanno usato `improvments`. L'utente decide il prefisso delle prossime issue.
4. **Note residue della review di ICH-588** (nessuna issue aperta). Parte del testo storico di
   `docs/BOOTSTRAP_PLAN.md` («Prerequisites», «Proposed M0 issue order») è superata. Le righe di
   stato dei cinque documenti di baseline citano `governance 2.10-draft`: aggiornale al prossimo
   cambio di versione.
