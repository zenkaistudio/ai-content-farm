# Target Accounts — AI Tools / Tech Tips Niche

Starter list for the Researcher stage's "top accounts" input (the source video calls for ~50 accounts in the niche). This is a seed list from web research — not yet scraped, no Apify calls made. Expand this via Apify's own "similar accounts" discovery once scraping is approved.

## Source / direct inspiration
- **@jerry.ai_tutor** — "Jerry the AI," the creator behind the original `automated-ig-transcript.md` source video. Daily carousels teaching AI basics/tutorials.

## Core niche — AI tools/tips carousels
| Handle | Followers | Style |
|---|---|---|
| @digitalsamaritan | 534K | Mix of reels, carousels, visual AI formats, posts ~5-6x/week |
| @ninjaaitools | 320K | Walkthroughs of practical AI tools w/ use-case demos |
| @app_source_hub | 120K | Carousel breakdowns of AI tools/hacks |
| @tech_shuffle | 158K | Explainers w/ voiceover + tool comparisons |
| @thesocialabhii | 47.2K | Simple carousels explaining AI terms for beginners. **Likes hidden by creator — can't compute engagement rate from likes; would need comments/saves as a proxy instead.** |
| ~~@wordofmachine~~ | ~~30.4K~~ | **Account no longer exists (deleted/renamed as of 2026-06-19) — remove from active scrape targets.** |

## Adjacent — AI + creator business/automation
| Handle | Followers | Style |
|---|---|---|
| @theclarkgary | 160K | ManyChat automation, DM funnel tutorials |
| @gannon.meyer | 161K | Instagram-to-email automation pipelines |
| @marketing.with.m | 140K | AI x social media marketing |
| @tenny_hainsworth | 93K | AI prompts for content creation |
| @itstylergermain | — | AI automation for creator businesses (DM responses, content gen) |
| @imanoubou | 60K | AI tools for content marketing (runs Vocable.ai) |

## Names mentioned but handle not yet confirmed
- "Tecotech" — AI/tech news + tutorials, posts daily
- "Yaswanth" — "1 tool = 1 idea" format, tech/AI/career content
- "AI Daily Art" — daily AI-generated art (more art-focused, maybe not a fit for tips/tutorial format)
- "Official AI Tips" — daily productivity hacks, prompt systems

## Notes
- Core niche list is the closest match to jerry.ai_tutor's actual format and should be the priority scrape targets.
- Adjacent list leans more "creator business/automation" than pure AI-tool-tips — keep as secondary/optional sources, watch whether their content actually performs once we see scraped engagement numbers.
- This list is a starting point, not the full 50 — once Apify scraping is live, use its related-accounts/hashtag discovery to round it out.

## Confirmed engagement findings (2026-06-19 test scrapes)
Verified with real follower counts + likes via Apify, not raw like count alone:
- **@digitalsamaritan** (622K followers): top post 1.29% engagement — best in our sample
- **@app_source_hub** (117K followers): 0.5–0.82% engagement — **a 1/5th-the-size account matching digitalsamaritan's engagement rate.** Real example of "smaller page, same-caliber impressions."
- **@tech_shuffle** (168K followers): 0.36–0.67% — also consistently outperforms larger accounts on a rate basis
- **@ninjaaitools** (417K followers): only 0.007–0.031% engagement — despite being the 2nd-biggest account in our sample, it has by far the *worst* engagement. Bigger ≠ better. Don't use its posts as "viral" examples to model.
- **@thesocialabhii**: likes hidden (`-1` from Apify) — engagement rate not computable from likes; would need comments/saves as an alternative signal
