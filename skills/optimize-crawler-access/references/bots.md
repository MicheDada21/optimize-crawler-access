# AI bots

Last verified: 2026-10-28.

The JavaScript column says whether the bot runs scripts before reading a page. A bot marked No reads only the HTML the server sends, so content that the browser builds with JavaScript stays invisible to it. Server-side or static rendering makes the content readable to every bot below. Unmeasured means no published crawl study covers that bot yet; treat it as No.

## Search and citation, allowed

They index pages so an assistant can cite them in answers.

| Token | Owner | JavaScript | Notes |
|---|---|---|---|
| `OAI-SearchBot` | OpenAI | No | ChatGPT search. Respects robots.txt. |
| `Claude-SearchBot` | Anthropic | No | Claude search results. Respects robots.txt. |
| `PerplexityBot` | Perplexity | No | Perplexity search; not used for training. |
| `Googlebot` | Google | Yes, in a delayed second pass | Google Search, AI Overviews and AI Mode. Gemini reads pages through the same renderer. Keep allowed in every case. |
| `Bingbot` | Microsoft | Yes, with Edge | Bing and Copilot. Keep allowed in every case. |

## User-triggered, allowed

They fetch one page when a person asks the assistant about it.

| Token | Owner | JavaScript | Notes |
|---|---|---|---|
| `ChatGPT-User` | OpenAI | No | May ignore robots.txt. |
| `Claude-User` | Anthropic | Unmeasured | Respects robots.txt. |
| `Perplexity-User` | Perplexity | Unmeasured | Generally ignores robots.txt. |

## Training, the user's choice

Blocking them keeps future content out of model training and does not affect search or citations.

| Token | Owner | JavaScript | Notes |
|---|---|---|---|
| `GPTBot` | OpenAI | No | Respects robots.txt. |
| `ClaudeBot` | Anthropic | No | Respects robots.txt. |
| `CCBot` | Common Crawl | No | Public dataset used by many labs. |
| `Google-Extended` | Google | Token only | robots.txt token only, no separate crawler. Blocking it keeps Gemini training out and leaves Google Search alone. |
| `Applebot-Extended` | Apple | Token only | robots.txt token only. Blocking it leaves Applebot search alone. Applebot itself renders JavaScript. |

## User agent strings for curl tests

Use the token inside a realistic string, as in these two examples.

```txt
Mozilla/5.0 AppleWebKit/537.36 (KHTML, like Gecko); compatible; OAI-SearchBot/1.3; +https://openai.com/searchbot
Mozilla/5.0 AppleWebKit/537.36 (KHTML, like Gecko; compatible; ClaudeBot/1.0; +claudebot@anthropic.com)
```

Replace the token for each bot. Edge rules that verify bots by IP will not match these requests, so read the edge configuration as well.

## Sources

- [OpenAI bots](https://developers.openai.com/api/docs/bots), with IP ranges in `https://openai.com/searchbot.json`, `gptbot.json` and `chatgpt-user.json`
- [Anthropic crawlers](https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler)
- [Perplexity bots](https://docs.perplexity.ai/guides/bots)
- [Google common crawlers](https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers)
- [About Applebot](https://support.apple.com/en-us/119829)
- [Vercel and MERJ, The rise of the AI crawler](https://vercel.com/blog/the-rise-of-the-ai-crawler), crawl logs on JavaScript execution by AI bots
- [The new evergreen Bingbot](https://blogs.bing.com/webmaster/october-2019/The-new-evergreen-Bingbot-simplifying-SEO-by-leveraging-Microsoft-Edge)
- [Vercel managed rulesets](https://vercel.com/docs/vercel-waf/managed-rulesets)
- [Cloudflare AI bot policies](https://developers.cloudflare.com/bots/additional-configurations/block-ai-bots/)
- [Next.js robots](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots)
