Usa il server MCP Context7 per queste tre aree, con gli ID fissi di ogni sezione:

- libghostty (`libghostty-vt`): la libreria VT scelta per `agentd` in `docs/ARCHITECTURE.md`.
  Vedi [libghostty](#libghostty).
- Notcurses (`dankamongmen/notcurses`): la libreria TUI scelta per `agentctl` in
  `docs/ARCHITECTURE.md`. Vedi [Notcurses](#notcurses).
- Interfacce di sistema Linux in C: le interfacce elencate per `agentd` in `docs/ARCHITECTURE.md`
  (API PTY, `epoll`, socket Unix domain, gestione dei processi e di wait) e l'I/O del terminale.
  Vedi [Interfacce di sistema Linux](#interfacce-di-sistema-linux).

Usa Context7 anche quando credi di sapere la risposta: i tuoi dati di addestramento possono non
contenere i cambiamenti recenti.

## libghostty

Per libghostty salta `resolve-library-id` e usa questo ID:

- `/websites/libghostty_tip_ghostty` (libghostty VT, <https://libghostty.tip.ghostty.org>):
  riferimento per la C API di `libghostty-vt` — funzioni, parametri, tipi ed esempi.

Questa fonte documenta la versione `tip` (di sviluppo) di Ghostty. La sua API può essere diversa
dalla versione che AgentMux fissa. Quando il progetto fissa una versione di `libghostty-vt`, gli
header della versione fissata fanno fede: se Context7 e gli header fissati non coincidono, vincono
gli header.

## Notcurses

Per Notcurses salta `resolve-library-id` e usa questi ID:

- `/dankamongmen/notcurses` (Notcurses su GitHub): riferimento principale per implementazione,
  funzioni, parametri ed esempi.
- `/websites/deepwiki_dankamongmen_notcurses` (Notcurses su DeepWiki): riferimento complementare
  per esplorare l'architettura, capire le relazioni tra i componenti o analizzare gli interni.

Le man page installate con Notcurses fanno fede per firme, valori di ritorno, flag, costanti e
proprietà dei puntatori. Corrispondono alla versione installata, mentre Context7 può indicizzare
una versione diversa: se Context7 e le man page installate non coincidono, vincono le man page
installate.

## Interfacce di sistema Linux

Per le interfacce di sistema Linux salta `resolve-library-id` e usa questi ID:

- `/websites/man7_linux_man-pages` (Linux man-pages, <https://man7.org/linux/man-pages/>):
  riferimento principale per firme, valori di ritorno, valori di `errno`, flag e standard, per
  esempio `posix_openpt(3)`, `grantpt(3)` e `ptsname(3)`.
- `/websites/sourceware_glibc_manual_html_node` (GNU C Library Reference Manual): riferimento
  complementare per i concetti, per esempio le coppie di pseudo-terminali (`openpty`, `forkpty`),
  i terminali di controllo e le modalità del terminale.

Context7 indicizza l'ultima versione pubblicata di entrambe le fonti. Le man page installate sul
sistema di build corrispondono al kernel e alla glibc installati: se Context7 e le man page
installate non coincidono, vincono le man page installate.

## Passi

1. Per le tre aree sopra, usa gli ID fissi della sezione corrispondente. Per ogni altra libreria,
   parti da `resolve-library-id`, con il nome della libreria e cosa cercare, salvo che l'utente
   indichi un ID esatto della libreria nel formato `/org/project`
2. Per ogni altra libreria, scegli la corrispondenza migliore (formato dell'ID: `/org/project`) in
   base a corrispondenza esatta del nome, pertinenza della descrizione, reputazione della fonte e
   punteggio di benchmark
3. Chiama `query-docs` con l'ID della libreria scelta, una query limitata a un solo concetto e
   `language: "C"` per le tre aree sopra. Fai una chiamata separata per ogni concetto
4. Rispondi usando i documenti trovati
