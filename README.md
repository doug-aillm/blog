# Doug's Daily Digest

Astro-based personal blog for daily digests.

## Add a daily digest

Create a markdown file named `YYYY-MM-DD.md` in `src/content/blog/` with frontmatter:

```yaml
title: "Daily Digest: 2026-02-03"
description: "Top signals and links for the day."
pubDate: 2026-02-03
tags: ["crypto", "defi", "devtools"]
```

## Development

```sh
npm install
npm run dev
```

## Build

```sh
npm run build
```

## Deploy

This project is configured for Cloudflare Pages via Wrangler and GitHub Actions.

- Set `CF_API_TOKEN` and `CF_ACCOUNT_ID` in the GitHub repo secrets.
- Update the `site` URL in `astro.config.mjs` once the domain is known.
