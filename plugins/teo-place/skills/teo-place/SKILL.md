---
name: teo-place
description: Come usare da Claude i programmi desktop teo.place installati sul PC (SorterBarcode, watchHost, listBigStorage, queryF5, TraceFox, callApi) e cosa fare quando un loro server MCP non risponde. Usala quando l'utente chiede di leggere barcode da immagini, di guardare RAM/CPU/dischi dei server monitorati, di cercare file negli inventari, di interrogare un database MySQL dai profili di queryF5, di guardare il traffico HTTP/HTTPS registrato da TraceFox, di leggere le richieste delle collezioni di callApi, o nomina uno di questi programmi.
---

# I programmi teo.place da Claude

Ogni programma risponde con un suo server MCP. **Nessuno strumento cancella** (file, inventari,
server, soglie, archivio) e **nessuno passa password o token**, in nessuna direzione. Cosa puo'
cambiare, programma per programma:

- **SorterBarcode**: niente, e' in sola lettura.
- **watchHost**: chiede un controllo subito, accende o spegne il controllo di un server, cambia
  l'intervallo, crea e modifica soglie.
- **listBigStorage**: si collega a un server gia' salvato, avvia, mette in pausa, riprende e ferma
  le scansioni, esporta un inventario in CSV, SQL o MySQL (solo verso la destinazione verificata
  nella finestra, e senza sovrascrivere).
- **queryF5**: accende e spegne i monitor delle query; le query di Claude sono solo
  `SELECT`/`SHOW`/`DESCRIBE`/`EXPLAIN`.
- **callApi**: legge le collezioni di richieste aperte nel client per API (elenco, ricerca, richiesta con
  intestazioni, corpo, autenticazione, script e documentazione, ambienti) e, se la persona accende «Consenti a Claude
  di inviare richieste», INVIA richieste vere (`send_request`, `run_request`: GET, POST...). Non scrive file. Un POST
  puo' cambiare dati su un server vero: invia solo cio' che ti e' stato chiesto. Intestazioni, parametri e variabili
  che sembrano segreti escono come `[omesso]`.
- **TraceFox**: legge le transazioni registrate dal proxy (elenco, ricerca, intestazioni, tempi, corpo,
  curl, riassunto per host) e le regole; puo' accendere o fermare il proxy e mettere in pausa la
  registrazione. Non svuota la sessione e non cambia le regole. **Intestazioni sensibili e parametri
  tipo token escono come `[omesso]`, i corpi no**: se nel traffico ci sono password, le puo' leggere.

Prima di un'azione che cambia qualcosa, di' all'utente cosa stai per fare.

Il plugin avvia ogni server lanciando il programma installato con `--mcp`: un ponte su stdio che
legge da solo il token del programma (mai scritto nella configurazione di Claude) e, se il
programma e' chiuso, lo apre. Dalle versioni della colonna «si apre solo all'uso» il ponte
risponde da solo all'elenco degli strumenti e apre il programma solo quando ne usi uno: non
aprirne uno senza motivo.

| programma | server | da che versione | si apre solo all'uso | note |
|---|---|---|---|---|
| SorterBarcode | `sorterbarcode` | 1.3.0 | — | lettore senza stato: niente da accendere, niente token |
| watchHost | `watchhost` | 1.0.13 | 1.0.15 | se e' chiuso parte in background, senza aprire il browser |
| listBigStorage | `listbigstorage` | 1.0.19 | 1.0.21 | se e' chiuso si apre la finestra |
| queryF5 | `queryf5` | 1.2.14 | 1.2.16 | se e' chiuso si apre la finestra |
| TraceFox | `tracefox` | 1.0.13 | 1.0.13 | se e' chiuso si apre la finestra |
| callApi | `callapi` | 1.0.0 | 1.0.0 | se e' chiuso si apre la finestra |

## Se un server non risponde

Non insistere con lo strumento: di' all'utente cosa manca, in quest'ordine. L'errore del ponte
dice gia' quale dei casi e'.

1. **Il programma non e' installato**: il server compare come fallito. Si installa da
   https://teo.place/ (il wizard teoSetup) o dal sito del prodotto.
2. **Versione vecchia**: prima delle versioni in tabella `--mcp` non esiste, il programma si apre
   e il server non risponde. Si aggiorna dal programma stesso («Controlla aggiornamenti»).
3. **L'accesso per Claude e' spento** (watchHost, listBigStorage, queryF5, TraceFox, callApi): e' spento di default e
   si accende solo a mano, in Impostazioni del programma → «Accesso per Claude». Il ponte non lo
   accende: lo dice e si ferma.
4. **Il programma sta partendo o chiede la licenza**: il ponte aspetta 25 secondi; se non basta,
   si ricollega il server da `/mcp` quando la finestra e' pronta.
5. **Claude Desktop installato dallo Store, con una versione che apre ancora il programma da
   dentro Claude** (watchHost fino alla 1.0.14, listBigStorage fino alla 1.0.20, queryF5 fino alla
   1.2.15): il programma aperto dal ponte non parte, o parte senza server. Si apre dal menu Start,
   si ricollega il server da `/mcp`, e si aggiorna: le versioni successive lo aprono tramite
   Explorer.

Un server ricollegato vale subito («Reconnect» da `/mcp`); un plugin appena installato dalla
sessione successiva. Quando un programma si aggiorna chiude anche i suoi ponti, perche' non
blocchino i file da sostituire: di solito Claude li riapre da solo, altrimenti si ricollegano da
`/mcp`.

## Regole d'uso

- I percorsi passati a SorterBarcode devono essere **assoluti ed esistenti**.
- queryF5 esegue query solo sui profili che l'utente ha abilitato a mano; se un profilo non c'e',
  va abilitato nelle Impostazioni di queryF5, non aggirato.
- Ogni chiamata resta scritta: in listBigStorage nel registro a destra (categoria «Claude»), in
  watchHost nel suo log, in queryF5 in `mcp-log.jsonl` e nell'elenco delle Impostazioni, in TraceFox
  nel Monitor a destra (righe «Claude: ...»).
- TraceFox: con il proxy fermo o la registrazione in pausa la sessione non cresce; dillo all'utente
  invece di chiedere ancora l'elenco. Per vedere traffico nuovo usa `list_transactions` con `since_id`.
