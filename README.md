# ai-crawler-access

Trova cosa impedisce a ChatGPT, Claude e Perplexity di leggere un sito, da robots.txt ai blocchi di Vercel e Cloudflare, e lo corregge.

Fa parte della [Prompt Library di CH Labs](https://www.cryptohelvetia.com/ch-labs/prompt-library), dove la pagina di riferimento è [Find and fix AI crawler blocks](https://www.cryptohelvetia.com/ch-labs/prompt-library/skills/ai-crawler-access). Il repo contiene una copia della skill, rigenerata a ogni aggiornamento della libreria.

## Installare

In Claude Code aggiungi una volta il marketplace e installa il plugin.

```
/plugin marketplace add MicheDada21/ch-labs-skills
/plugin install ai-crawler-access@ch-labs-skills
```

Da shell i comandi sono `claude plugin marketplace add MicheDada21/ch-labs-skills` e `claude plugin install ai-crawler-access@ch-labs-skills`. Nell'app desktop premi + accanto al campo del prompt, apri Plugins, aggiungi il marketplace `MicheDada21/ch-labs-skills` con Add marketplace e poi il plugin con Add plugin.

Per avere tutte le skill della libreria installa `tutte-le-skill@ch-labs-skills`. Con altri agenti copia la cartella `skills/ai-crawler-access` tra le skill dell'agente, per esempio in `~/.codex/skills/ai-crawler-access`.

## Licenza

MIT, testo completo in [LICENSE](LICENSE).
