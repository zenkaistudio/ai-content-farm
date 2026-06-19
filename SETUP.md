# Setup Status

Pipeline: **Researcher (scrape) → Copywriter (rewrite) → Designer (image-gen) → Poster (publish)**, based on `automated-ig-transcript.md`.

## 01 Researcher — scrape viral posts (Apify)
- [ ] Apify account
- [ ] Pick an Instagram post scraper actor (any niche, top 50 accounts)
- [ ] `APIFY_TOKEN` in `.env`
- **Do not run live scrapes until explicitly approved.**

## 02 Copywriter — rewrite + voice-match
- [x] No extra service needed — handled directly by Claude from the scraped rows

## 03 Designer — copy → image (ChiAI)
- [ ] ChiAI account/API key (Nano Banana 2 / Kling 2.6 access)
- [ ] `CHIAI_API_KEY` in `.env`

## 04 Poster — auto-post on schedule (Buffer)
- [x] Buffer account, Instagram Business account connected — connected to the **"collective.events"** page (a low-follower test page) intentionally, to test the pipeline before pointing it at a real account like `@studio.zenkai`
- [x] Buffer personal API key generated and verified (`BUFFER_ACCESS_TOKEN` in `.env`)
- [x] Confirmed via API: account `zenkaistudios@yahoo.com`, channel `collective.events` (Instagram), channel id `6a35014838b5579345b0bd43`
- Note: Buffer's new API is GraphQL-based (`https://api.buffer.com`, Bearer token auth), not REST

## Storage — Airtable
- [x] Airtable account + personal access token (`AIRTABLE_TOKEN` set, scoped to "Untitled Base" in "AI AUTOMATION" workspace)
- [x] Base created, `AIRTABLE_BASE_ID` filled in (`app6fCG1Dn1izXgk0`)
- [x] `Posts` table created via API (`tblXhQTqIGdFaMbba`) with the schema below

## Suggested Airtable schema (table: `Posts`)
| Field | Type | Notes |
|---|---|---|
| Source Account | Text | scraped from |
| Post URL | URL | original viral post |
| Original Caption | Long text | as scraped |
| Hook | Text | first line / hook |
| Likes | Number | at scrape time |
| Scraped At | Date | |
| Rewritten Copy | Long text | Copywriter output |
| Image Prompt | Long text | sent to ChiAI |
| Image URL/Attachment | Attachment | Designer output |
| Status | Single select | New / Rewritten / Designed / Scheduled / Posted |
| Scheduled For | Date | |
| Buffer Post ID | Text | for tracking after publish |

## Open decisions
- Exact Apify actor to use (not chosen yet)
- Whether ChiAI is accessed via direct API or an aggregator dashboard — need API docs once you share access details
