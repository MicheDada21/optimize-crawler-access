# robots.ts template

Adapt `PRIVATE`, the sitemap URL and `blockTraining` to the project. Keep any launch gate the project already has in front of the return.

```ts
import type { MetadataRoute } from "next"

const siteUrl = "https://example.com"

// Paths no crawler should read. Named groups ignore the "*" group, so every group repeats them.
const PRIVATE = ["/api/", "/admin"]

const SEARCH_AND_USER = [
  "OAI-SearchBot",
  "ChatGPT-User",
  "Claude-SearchBot",
  "Claude-User",
  "PerplexityBot",
  "Perplexity-User",
]

const TRAINING = ["GPTBot", "ClaudeBot", "CCBot", "Google-Extended", "Applebot-Extended"]

// The user's choice from step 7.
const blockTraining = false

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      { userAgent: "*", allow: "/", disallow: PRIVATE },
      { userAgent: SEARCH_AND_USER, allow: "/", disallow: PRIVATE },
      blockTraining
        ? { userAgent: TRAINING, disallow: "/" }
        : { userAgent: TRAINING, allow: "/", disallow: PRIVATE },
    ],
    sitemap: `${siteUrl}/sitemap.xml`,
  }
}
```

When the project also has `app/robots.txt` or `public/robots.txt`, move its rules into `app/robots.ts` and delete the static file, so one source remains.
