# Setup Status

See [content-strategy-reference.md](content-strategy-reference.md) for the master reference on everything learned from scraping/analyzing viral content — engagement methodology, hook patterns, and carousel formats. Check it before writing any rewrite or designing any carousel.

Pipeline: **Researcher (scrape) → Copywriter (rewrite) → Designer (image-gen) → Poster (publish)**, based on `automated-ig-transcript.md`.

## 01 Researcher — scrape viral posts (Apify)
- [x] Niche decided (2026-06-19): **AI tools / tech tips** — same lane as the source video itself
- [x] Apify account (reused existing token, already used in other projects: `WebProspects/`, `z-tracker/`)
- [x] Actor: `apify/instagram-post-scraper` (input field is singular `username`, an array — not `usernames`; `resultsLimit` caps posts/profile; $2.70/1000 results on Free plan, ~2000 free results/month)
- [x] Starter "top accounts" list built (12 confirmed handles + 4 names to confirm) — see [target-accounts.md](target-accounts.md). Not the full ~50 yet; expand via Apify's discovery once scraping is live.
- [x] Cross-niche hook/format reference built — see [viral-hooks-and-formats.md](viral-hooks-and-formats.md).
- [x] `APIFY_TOKEN` in `.env`
- [x] **First surface-level test scrape run 2026-06-19**: 3 accounts (@digitalsamaritan, @ninjaaitools, @tech_shuffle), 5 posts each = 15 results, ~$0.04, inserted into Airtable `Posts` table. Pipeline confirmed working end to end for this stage.
- **Important methodology fix:** raw like count is misleading across accounts of different sizes. Added an `Engagement Rate %` field (likes ÷ followers via `apify/instagram-profile-scraper`) to the `Posts` table — always compute this before treating a post as a "winner" to rewrite. Bigger account ≠ better content (see [target-accounts.md](target-accounts.md) "Confirmed engagement findings" for the real numbers — ninjaaitools at 417K followers had the worst engagement of any account tested, app_source_hub at 117K matched digitalsamaritan's 622K).
- [x] Scraped 2 more rounds: app_source_hub + thesocialabhii (5 posts each). `@wordofmachine` confirmed defunct (account deleted), removed from active targets.
- [x] **Cleaned Airtable 2026-06-19**: deleted 13 low-quality/unverifiable records (all 5 ninjaaitools, all 5 thesocialabhii [hidden likes], 1 practicalyai [unknown followers], 2 weak tech_shuffle posts under 0.15% engagement). **Airtable now holds only 11 confirmed-viral records** (0.215%–1.286% engagement), 7 already rewritten. No noise.
- [x] **Image Prompts written 2026-06-19** for the 3 rewritten posts that have real substance (24/7 AI agents [6 slides], Android Auto tip [4 slides, using verified real facts not fabricated steps], streaming app tips [4 slides]). All follow a consistent visual brand system (charcoal + electric blue, bold condensed sans, consistent logomark) defined once and referenced in each prompt. The other 3 rewrites (YouTube/Google, AI playbooks, Siri) are hook-only with no real facts behind them (came from videos we never transcribed) — intentionally left alone rather than fabricating claims about what Google/Apple actually did.
- [x] **Credits topped up 2026-06-19, full Designer stage now verified end-to-end.** Confirmed `recordInfo` polling endpoint (documented in nano-banana-2-api.md). First test image: 8 credits ($0.04), 62s.
- [x] **Android Auto carousel (4 slides) fully generated and attached to its Airtable record, Status=Designed.** First attempt had a garbled-text bug + unwanted Instagram UI chrome + a soon-to-be-wrong account handle baked in — fixed all three (see "Prompt-writing lessons learned" in nano-banana-2-api.md) and regenerated clean. All 3 stored Image Prompts (Android Auto, 24/7 AI agents, streaming apps) updated to the corrected lean approach — no handle/logo until branding is finalized, no redundant headlines, explicit no-IG-chrome instruction.
- [x] **First post LIVE 2026-06-19**: published for real to `collective.events` — https://www.instagram.com/p/DZyM6EJD0F6/ (Buffer post id `6a35c1698cced62613ca64e8`, status confirmed `sent` via API). Caption rewritten to a fuller, more complete version before publishing. Airtable record Status=Posted.
- **Full pipeline now proven end-to-end on a real account: Researcher → Copywriter → Designer → Poster, all real, all working.**
- Next: generate the other 2 carousels (24/7 AI agents [6 slides], streaming apps [4 slides]) using the now-corrected prompts, and decide a posting cadence going forward.

## 02 Copywriter — rewrite + voice-match
- [x] No extra service needed — handled directly by Claude from the scraped rows

## 03 Designer — copy → image (KIE AI / kie.ai)
- [x] KIE AI API key generated, added to `.env` as `KIEAI_API_KEY`, and verified valid (auth succeeds)
- [ ] **Account needs a credit top-up** — test `createTask` call returned `402 Credits insufficient`. Can't confirm a real image generation until credits are added.
- Full API reference notes: see [nano-banana-2-api.md](nano-banana-2-api.md)

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
| Image Prompt | Long text | sent to KIE AI |
| Image URL/Attachment | Attachment | Designer output |
| Status | Single select | New / Rewritten / Designed / Scheduled / Posted |
| Scheduled For | Date | |
| Buffer Post ID | Text | for tracking after publish |

## Open decisions
- Exact Apify actor to use (not chosen yet)
- Which KIE AI model to default to per image (`google/nano-banana` vs `nano-banana-2` vs Kling for video) — TBD once we're generating real images
