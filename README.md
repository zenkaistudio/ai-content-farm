# AI Content Farm

Autonomous AI Instagram content pipeline: scrape viral posts → rewrite them → generate visuals → auto-post on a schedule.

Based on the process described in [automated-ig-transcript.md](automated-ig-transcript.md) (source: `automated IG.mov`).

## Pipeline

1. **Researcher** — Apify scrapes viral posts from top accounts in a niche
2. **Copywriter** — rewrite + voice-match (handled directly via Claude)
3. **Designer** — ChiAI (Nano Banana 2 / Kling 2.6) turns copy into images
4. **Poster** — Buffer auto-posts on a schedule

Data store: Airtable (`Posts` table).

See [SETUP.md](SETUP.md) for current setup status and the Airtable schema.

Currently posting to `collective.events` on Instagram as a test page before pointing this at a real account.
