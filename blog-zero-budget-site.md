# Running a Product Site on $0/Month: Static HTML, Dual-Region Caddy, and One Sync Script

Most indie makers assume a product site needs a framework, a CDN plan, and a hosting bill. Ours ships on plain HTML with two Caddy servers in different regions, automatic HTTPS, and a single sync script that keeps both ends byte-identical. Total monthly cost: zero.

## The stack that shouldn't work (but does)

- **Plain static HTML** — 50+ hand-built pages, no build step, no framework. Dark mode is a CSS class toggle. The whole site loads in under 100KB per page.
- **Two Caddy instances** — one primary in Shanghai, one mirror in Guangzhou. Caddy handles TLS certificates automatically, which removes the entire cert-renewal class of problems.
- **A sync script on a cron** — every 20 minutes it diffs a manifest, copies changed files, rewrites absolute paths, and pushes a public mirror to GitHub Pages as a third fallback.
- **IndexNow for search** — both the primary domain and the mirror have keys. Every new or updated page gets submitted automatically. Search engines discover changes in minutes, not weeks.

## The details that actually matter

**Byte-identical dual deploy.** Every deploy goes to the primary first, then the sync script pushes to the second region. We verify with per-file MD5 comparison. "Deployed" means both ends match — anything else is a lie you tell yourself until a region goes down.

**Honest stats.** Early on our daily visitor number was inflated 12x because raw logs included our own IPs and crawlers. We now serve a *clean* number — filtered for self-traffic and bots, labeled as such on the page. Showing a smaller real number beat showing a flattering fake one. Trust compounds; vanity doesn't.

**Error pages are brand pages too.** The 404 page is a hand-crafted "dead end" page in our own visual style, served by Caddy's `handle_errors` on the primary and natively on the mirror. Users who hit a broken link still land in-world.

## What this buys you

When your entire site is files, everything becomes a file problem — and file problems are the easiest kind. Backup is `tar`. Migration is `scp`. DR is "copy it back". A regional outage means flipping DNS to one IP. No databases to corrupt, no CMS to patch, no surprise bills at 3am.

You can see the result at [zhishendiguo.com](https://zhishendiguo.com/) — Chinese primary, [English landing page](https://zhishendiguo.com/index-en.html) for international visitors. It loads fast because there's almost nothing to load.

*Part of the [ai-ops-notes](https://github.com/xushilianzhuren/ai-ops-notes) series — field notes from operating a small AI product line with no budget and no staff.*
