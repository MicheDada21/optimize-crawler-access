---
name: ai-crawler-access
description: "Audit and fix AI crawler access to a site, from robots.txt and Vercel or Cloudflare edge blocks to content that only appears with JavaScript. Use when ChatGPT, Claude or Perplexity should cite a site (AEO, GEO), or when the user wants AI training bots blocked."
---

# AI crawler access

The goal is that search and user-triggered AI bots reach every public page while training bots follow the user's choice. A block can sit in the robots rules, in page headers and meta tags, in the Vercel Firewall or in Cloudflare in front of Vercel. Check all four layers, because a bot blocked at the edge never reads robots.txt. Then check that the page content is in the HTML the server sends, because most AI bots run no JavaScript.

Read [references/bots.md](references/bots.md) for the bot classes and their user-agent tokens. When its date is more than three months old, re-verify it against the sources it lists and update the file.

## Steps

1. Target. Work from the project folder, or from a domain the user names. Find the production domain with `vercel project inspect <project> --scope <team>` and `vercel domains ls`. Audit production only, because preview deployments carry `X-Robots-Tag: noindex` and Deployment Protection by design. Complete when you have one production URL, plus the repo when there is one.

2. Source. Find every place that shapes crawler access.
   - `app/robots.ts|js|txt` or `src/app/...`, and `public/robots.txt`. More than one of them is a conflict to fix in step 8.
   - `robots` in `metadata` or `generateMetadata` in layouts and pages (`index: false`, `noai`, `noimageai`).
   - `headers()` in `next.config.*` and `headers` in `vercel.json` that set `X-Robots-Tag`.
   - `proxy.ts` or `middleware.ts` code that rewrites, blocks or rate-limits by user agent, and `mitigate` rules in `vercel.json`.
   - Launch gates such as `NEXT_PUBLIC_LAUNCH` that disallow everything before launch. They are intentional, so report their state and keep them.

   Complete when each place is listed with what it does, or marked absent.

3. Live robots.txt. Run `curl -sS https://<domain>/robots.txt` and compare it with the source. Lines missing from the source come from the edge, for example the block that Cloudflare Managed robots.txt prepends. Evaluate each bot by the standard rules. A bot that matches a named group ignores the `*` group, and inside a group the longest matching path wins, with `Allow` winning ties. Complete when every bot has a verdict for `/` and for one content page (allowed, blocked or partially blocked).

4. Live responses. Request the home page and the same content page with each bot's user agent, and record status, `X-Robots-Tag`, `<meta name="robots">` and whether the main content is in the served HTML.

   ```bash
   curl -sS -D - -A "<user agent>" https://<domain>/<path> -o page.html
   grep -c "<sentence from the page>" page.html
   ```

   Pick a sentence from the main text as a browser shows it. Curl runs no JavaScript, like the bots marked No in [references/bots.md](references/bots.md), so a count of 0 means those bots receive the page without its content. A 403, a 429 or a challenge page means an edge block. Curl only reproduces rules that match the user agent; rules for verified bots match by IP and need step 5. Complete when the table has a row per bot and path.

5. Edge rules.
   - In the linked Vercel project, run `vercel firewall status` and `vercel firewall bot-management`. Look for the AI Bots managed ruleset in Deny mode, Bot Protection, custom rules that match user agents or AI bot categories, and attack mode. Complete when you can state which rules touch AI bots, or that none do.
   - A `server: cloudflare` or `cf-ray` response header means Cloudflare sits in front. Then read the zone's AI bot policies (Search, Agent and Training since July 2026, each set to Block, Block on pages with ads or Allow), Managed robots.txt, Bot Fight Mode and WAF custom rules. Use the Cloudflare API when a token with zone read access exists, otherwise the dashboard in a browser already signed in. Complete when each setting has its current value, or the user knows which one you could not read.

6. Diagnose. Show one table with bot, class, robots.txt, headers and meta, edge, live status, content in HTML and verdict. Mark as a problem every block on a search or user-triggered bot, every noindex on a public page and every public page whose main content is missing from the served HTML, where the layer to change is rendering. Report a training block as the user's choice. Complete when each row has a verdict and each problem names the layer to change.

7. Decide. When fixes are needed or the training policy is unknown, ask in one question widget.
   - For training bots, offer to keep the current setting (Recommended when the project already states a choice), allow all or block all. robots.txt is a request that some crawlers skip, so a block that must hold also needs the edge setting.
   - Ask one question per Vercel or Cloudflare setting to change, since each applies to the whole project or zone.

   Complete when the user has answered.

8. Fix.
   - Write `app/robots.ts` from [references/robots-template.md](references/robots-template.md), repeating the project's private paths in every named group (step 3 explains why). Keep the sitemap line and any launch gate.
   - Limit `noindex` and `noai` headers and meta tags to pages that are meant to stay private.
   - When the main content is missing from the served HTML, render it on the server or at build time, for example with Server Components or static generation, and keep client JavaScript for interaction.
   - On Vercel, change the ruleset or rule with `vercel firewall`, check `vercel firewall diff`, then run `vercel firewall publish`.
   - On Cloudflare, set Search and Agent to Allow and Training to the user's choice, and narrow WAF rules that hit search bots. Turn Managed robots.txt off when it contradicts the repo's rules.

   Complete when each approved change is made and every other setting is as you found it.

9. Ship and verify. Deploy under the project's delivery rules, then repeat steps 3 and 4 on production. Complete when the new table shows only the blocks the user chose, or the user knows which one remains and why.
