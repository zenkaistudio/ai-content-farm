# Content Strategy Reference

Master reference for everything learned from scraping/analyzing viral content for this pipeline. Refer back to this before writing any rewrite or designing any carousel. Supporting detail lives in [target-accounts.md](target-accounts.md) (account list) and [viral-hooks-and-formats.md](viral-hooks-and-formats.md) (hook taxonomy) — this file is the synthesized summary of both, plus the format study.

## 1. Engagement methodology — never rank by raw likes

Raw like count is misleading across accounts of different sizes. Always compute **Engagement Rate % = likes ÷ followers** (we added this as a real field in Airtable's `Posts` table, backed by `apify/instagram-profile-scraper` follower data) before treating any post as a "winner."

Confirmed findings (2026-06-19 test scrapes, 5 AI/tech-niche accounts):
- **@digitalsamaritan** (622K followers): best post in our sample at 1.286% engagement
- **@app_source_hub** (117K followers, ~1/5 digitalsamaritan's size): 0.5–0.82% — matched digitalsamaritan's rate almost exactly despite being far smaller. Proof that follower count ≠ content quality.
- **@tech_shuffle** (168K followers): 0.36–0.67% — consistently solid, beat bigger accounts on a rate basis
- **@ninjaaitools** (417K followers, 2nd-biggest account tested): only 0.007–0.031% — the WORST engagement of any account tested despite being the 2nd largest. Its "comment X for the link" lead-magnet format does not actually work for its audience.
- **@thesocialabhii**: likes hidden by creator (Apify returns `-1`) — can't compute from likes, would need comments/saves as a proxy
- **@wordofmachine**: account no longer exists, removed from targets

**Rule going forward:** never pick a rewrite candidate by raw likes. Always check the rate.

## 2. What actually drives the hook (caption-level findings)

From analyzing our top 3 by engagement rate:

- **#1 (1.286%):** newsjacking + understatement. "Ummm… casually changed" downplays language to imply something huge — a curiosity gap. Tags a recognizable brand, rides a live event hashtag, has almost zero caption body — the hook's only job is curiosity, not explanation.
- **#2 (0.824%):** oversized, hyper-specific value-dump. "80" (not a round "10") signals exhaustive value — a save/bookmark post. Niche-specific targeting ("for Designers") beats generic claims. CTA is secondary, added after the value promise, not the headline.
- **#3 (0.82%):** exposé framing ("Exposing… insane…") implies insider access. "(part 1)" gives a reason to follow for the next part. Cross-tagging a collaborator doubles audience reach.

**Cross-cutting lesson:** the worst-performing posts in our sample (all 5 ninjaaitools posts) lead with "comment X for the link" as the *primary* hook. The winners lead with genuine curiosity, oversized value, or insider framing, and only use a CTA — if at all — as a secondary element after the hook lands. Don't make the engagement-bait CTA the headline.

Full hook taxonomy (question/shock-stat/promise/mistake-myth/curiosity templates, adapted for AI/tech topics) is in [viral-hooks-and-formats.md](viral-hooks-and-formats.md).

## 3. Visual/carousel format findings

Checked actual media (not just captions) for our top posts and for two format-reference accounts.

**Format we can actually produce (image-only Designer stage via KIE AI / Nano Banana 2):**
- **@app_source_hub "80 Best Websites for Designers"** (11-slide image carousel) — bold sans-serif headline + flat icon illustration on slide 1; each following slide is a category header (e.g. "FOR PNGS") + plain bulleted list + consistent logo/handle bar + small swipe-hand icon. Pure typography + simple shapes, no photography/realism needed. **This is our primary template.**

**Format we CANNOT produce (out of scope — video, not carousel):**
- **@digitalsamaritan's top 2 posts** were Reels, not carousels — talking-head selfie footage intercut with either conference B-roll or stop-motion miniature dioramas. Great content, wrong medium for our image-only pipeline. Would need video generation (Kling, available via KIE AI) to ever attempt this format.

**Format references studied purely for structure (not their topics — sports betting and celebrity news aren't our content, just their layout patterns):**
- **Complex** — magazine-editorial format: bold masthead/wordmark branding, full-bleed cover-style photo slide with headline+byline overlay, followed by solid-color-background slides with large bold serif/slab pull-quote text centered, brand wordmark anchoring the bottom of every slide.
- **Polymarket** — headline-card format: small logo top-left, full-bleed photo background, black gradient bar at the bottom of the slide with a small pill-shaped tag (e.g. "TRENDING") + bold condensed white headline overlaid on top of the bar. Explainer slides use a plain white background with a short text intro followed by an embedded "screenshot" graphic as visual proof/evidence.

## 4. Our synthesized carousel template

Combining app_source_hub's clean producibility + Polymarket's punchy headline-card hook + Complex's editorial pull-quote rhythm. Standing 6-slide structure to use as the default carousel shape:

1. **Cover/Hook** (Polymarket headline-card format) — full-bleed background visual, black gradient bar + small pill tag (e.g. "AI NEWS"), bold condensed white headline, small brand logo top-left
2. **The Claim** (Complex pull-quote format) — solid color background, small header line (title + date), large bold pull-quote stating the core idea, brand wordmark anchoring the bottom
3. **What Actually Changed / The List** (app_source_hub list format) — header bar, 3-4 bulleted facts, consistent branding bar + swipe icon
4. **Why It Matters** (Complex pull-quote format, different accent color than slide 2, for rhythm) — the "so what" takeaway, bold quote style
5. **Proof** (Polymarket embedded-screenshot format) — white background, short intro text, embedded screenshot-style graphic as evidence
6. **Close/CTA** — bold closing line + brand wordmark + follow prompt

First applied example: the carousel concept built for our #1 post ("Did Google just quietly change how we use YouTube forever?") — see chat history 2026-06-19, can be rebuilt here once we're generating real images.

## 5. Image-gen prompt lessons (from first real test, 2026-06-19)

Full detail in [nano-banana-2-api.md](nano-banana-2-api.md#prompt-writing-lessons-learned-from-first-real-carousel-test-2026-06-19). Summary — these cost real credits to learn, don't repeat them:
- Never state the same headline in two places on a slide — causes garbled hallucinated text on the larger instance.
- Always explicitly ban Instagram UI chrome in the prompt (like/comment/share/save icons) — the model adds it unprompted otherwise.
- Don't hardcode account handle/logo into prompts until branding is actually finalized — leave it off, add it back once decided.
- Keep prompts lean and tied only to confirmed format elements from real scraped examples — don't invent extra brand-identity flourishes. Simpler prompt = fewer failure points = less wasted spend.
- Solid-color pull-quote slides render reliably; the model fills in sensible supporting icons for bullet lists on its own without needing every visual over-specified.

## 6. Open data-quality notes
- Apify's `instagram-post-scraper` occasionally misattributes a result's `ownerUsername` to a different account than the one queried (seen twice: "practicalyai" and "cooldeep.ai" showed up unprompted) — likely a collab-post quirk. Filter results to only the usernames you actually queried before trusting `ownerUsername`.
- Actor input field is the singular `username` (as an array), not `usernames` — easy mistake, the actor's own docs/examples are misleading here.
