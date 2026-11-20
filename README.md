# claude-plugins — i programmi teo.place in Claude Code

Marketplace di plugin per [Claude Code](https://code.claude.com/). Contiene un plugin, `teo-place`,
che collega a Claude i programmi desktop teo.place installati sul PC e porta una skill che spiega a
Claude come usarli e cosa fare quando un programma non risponde.

## Installare

Una volta sola, da un terminale:

```
claude plugin marketplace add TeoPlace/claude-plugins
claude plugin install teo-place@teo-place --scope user
```

Oppure dall'app Claude, scheda Code: **+ → Add plugins**, marketplace `TeoPlace/claude-plugins`.
Vale dalla sessione successiva. Gli aggiornamenti arrivano con `claude plugin marketplace update`.

## Cosa collega

Ogni programma si avvia con `--mcp`: un ponte su stdio che legge da solo il token del programma
(nessun token sta in questo repository ne' nella configurazione di Claude) e, se il programma e'
chiuso, lo apre.

| programma | da che versione |
|---|---|
| SorterBarcode | 1.3.0 |
| watchHost | 1.0.13 |
| listBigStorage | 1.0.19 |
| queryF5 | 1.2.14 |
| TraceFox | 1.0.13 |
| callApi | 1.0.0 |

watchHost, listBigStorage, queryF5, TraceFox e callApi rispondono solo con **«Accesso per Claude» acceso** nelle loro
Impostazioni: e' spento di default, e il plugin non lo accende. I server MCP locali funzionano in
Claude Code (terminale e scheda Code dell'app), non nella scheda Chat.
