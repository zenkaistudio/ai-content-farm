# Nano Banana 2 on KIE AI — Reference Notes

Working notes on how the Designer stage actually calls KIE AI's Nano Banana 2 model. Written for myself (Claude) to reference when constructing image-generation prompts/requests for this pipeline — keep this updated as we confirm more by actually calling the API.

## Auth & Endpoint

- Base: `https://api.kie.ai`
- Create task: `POST https://api.kie.ai/api/v1/jobs/createTask`
- Header: `Authorization: Bearer <KIEAI_API_KEY>`
- Header: `Content-Type: application/json`

This is an **async job API** — `createTask` returns a `taskId` immediately; the actual image isn't in that response. Two ways to get the result:
1. Provide a `callBackUrl` (webhook) — recommended for production
2. Poll **`GET https://api.kie.ai/api/v1/jobs/recordInfo?taskId=<id>`** (confirmed working 2026-06-19). Response: `data.state` is `waiting`/`queuing`/`generating`/`success`/`fail`; on success, `data.resultJson` is a JSON-encoded string containing `{"resultUrls": ["..."]}` — parse it twice (it's JSON inside JSON). First real test: 1K image, 8 credits ($0.04), completed in 62 seconds.

## Request shape (confirmed from kie.ai's own docs page for this model)

```json
{
  "model": "nano-banana-2",
  "callBackUrl": "https://your-domain.com/webhook",
  "input": {
    "prompt": "...",
    "image_input": [],
    "aspect_ratio": "auto",
    "resolution": "1K",
    "output_format": "png"
  }
}
```

Response (immediate ack, not the image):
```json
{ "code": 200, "msg": "success", "data": { "taskId": "string" } }
```

## Important: model name string varies by version

- Nano Banana (original): `"google/nano-banana"` (has the `google/` prefix)
- Nano Banana 2: `"nano-banana-2"` (**no** `google/` prefix)
- Always double-check the exact model string on that model's specific kie.ai docs page before using it — don't assume the prefix convention carries across versions.

## Known parameters

- `prompt` — text description, up to 5000 characters (limit confirmed on the original Nano Banana page; assume same ballpark for v2 unless proven otherwise)
- `image_input` — array, for image-to-image/editing workflows (reference images). Empty array `[]` for pure text-to-image.
- `aspect_ratio` — `"auto"`, plus explicit ratios like `"1:1"`, `"16:9"`, `"9:16"` (confirmed on original model docs; v2 likely supports same set)
- `resolution` — `"1K"`, `"2K"`, `"4K"` confirmed via pricing tiers below; a `"0.5K"` tier is also mentioned in third-party comparisons, unconfirmed on kie.ai directly
- `output_format` — `"png"` or `"jpeg"`
- `nsfw_checker` — boolean, default false (confirmed on original model docs, not yet re-confirmed for v2)

## Pricing (via this kie.ai reseller account, ~40% below "official" price)

| Resolution | Credits | Cost |
|---|---|---|
| 1K | 8 | $0.04 |
| 2K | 12 | $0.06 |
| 4K | 18 | $0.09 |

With high-tier top-up bonus (+10%), effective cost drops to ~$0.036 (1K) / ~$0.054 (2K) / ~$0.081 (4K).

## Model choice for this pipeline

Default to **Nano Banana 2** for all routine carousel-slide generation (best cost/speed for high-volume daily posting). Reserve **Nano Banana Pro** only for slides where on-image text legibility is critical — Pro has meaningfully better text rendering but costs roughly 1.6–2x more per image and ~8x more on token-based pricing.

## Prompt-writing notes for this project

- Content is IG carousel-style slides with text overlays (per the source video — "reads the copy table" → one image per row of copy)
- Nano Banana 2 has strong subject/character consistency — worth exploiting for a recurring visual "host" or consistent brand style across slides in the same carousel, by reusing consistent prompt language/character descriptions across the rows of a single post
- Since `image_input` supports references, we can feed a previous slide's image back in as a style/character reference for the next slide in the same carousel, if consistency across a multi-slide post becomes an issue

## Prompt-writing lessons learned (from first real carousel test, 2026-06-19)

**Mistakes that wasted credits — avoid these:**
- **Never state the same headline/text in two places on one slide.** Putting a big title AND a smaller header bar with the same words caused the big title to render as garbled hallucinated text (e.g. "HOW ACTUAL IRCE UPOROJA" instead of "HOW IT ACTUALLY WORKS") while the smaller instance rendered fine. State each piece of text exactly once.
- **The model adds unprompted Instagram UI chrome** (like/comment/share/save icon bar) when the prompt says "Instagram carousel slide" without restriction. Always explicitly instruct: "no Instagram app UI chrome of any kind (no like/comment/share/save icons, no caption bar)."
- **Don't hardcode account handle/logo/branding into prompts before branding is finalized.** We had to regenerate a full 4-slide batch after baking in a handle that was about to change. Leave branding out entirely until it's locked, add it back in once decided.
- **Keep prompts lean and tied only to confirmed format elements** (from real scraped examples), not invented brand-identity flourishes (custom logomarks, forced color palettes not actually observed). Simpler prompts = fewer failure points = fewer wasted regenerations.

**What worked well — lean into these:**
- Solid flat color background + bold centered pull-quote text (Complex-style) renders reliably clean every time — low-risk slide type. **Exception:** longer quotes (multi-line) occasionally still glitch a word (e.g. "Even after you cose close laptop" instead of "close your laptop"). Adding "double-check spelling carefully before rendering each word" plus stating the quote text after "exactly this and nothing more:" to the prompt fixed it on regeneration — add this to the standard negative-instructions block for any slide with more than ~10 words of quote text.
- The model fills in relevant supporting icons/illustrations on its own when given a list of bullet points, without needing every visual detail over-specified (e.g. it added a phone+app icon, an Android Auto dash icon, and a no-parking-style icon matching each bullet's content unprompted) — don't over-specify, let it infer reasonable supporting visuals.
- Headline-card format (black gradient bar + pill tag + bold condensed headline over a background illustration) renders cleanly when the headline text is stated once.

**Cost/timing per image:** 8 credits ($0.04) at 1K resolution, ~60 seconds generation time.

## Buffer posting (confirmed 2026-06-19)

Full pipeline now verified end-to-end. To post a multi-image carousel via Buffer's GraphQL API:

```graphql
mutation CreatePost($input: CreatePostInput!) {
  createPost(input: $input) {
    ... on PostActionSuccess { post { id dueAt } }
    ... on MutationError { message }
  }
}
```
With `input`: `text`, `channelId`, `schedulingType: automatic`, `mode: addToQueue` (or `customScheduled` + `dueAt`), `assets: [{image: {url: ...}}, ...]` (ordered array = carousel order), and **required for Instagram**: `metadata: { instagram: { type: post, shouldShareToFeed: true } }` — without this, Buffer rejects with "Instagram posts require a type." Image URLs must be publicly fetchable (we used Airtable's hosted attachment URLs, not the original KIE AI tempfile links which expire).

First real post: 4-slide Android Auto carousel → `collective.events`, added to queue, then published immediately for a live end-to-end test (see below) — live at [instagram.com/p/DZyM6EJD0F6](https://www.instagram.com/p/DZyM6EJD0F6/).

**To publish immediately instead of waiting for the queue slot**, use the `editPost` mutation (not documented on the public docs site — found via GraphQL introspection on `__schema { mutationType }`) with `mode: shareNow` (other `ShareMode` enum values: `addToQueue`, `shareNext`, `customScheduled`). Same `id`/`assets`/`metadata` shape as `createPost`, just pass the existing post's `id`.

**To check a post's real status** (did it actually publish, any errors): `query { post(input: {id: "..."}) { status sentAt dueAt externalLink error { message } } }`. `status: "sent"` + a populated `externalLink` confirms it's actually live, not just queued.

**Critical gotcha confirmed 2026-06-23: Airtable's hosted attachment URLs expire.** A post scheduled hours ahead (`customScheduled` with a future `dueAt`) failed with `status: "error"`, message: "Please update the media URL to be publicly accessible before retrying the post." Airtable's `v5.airtableusercontent.com` attachment URLs are signed and time-limited — fine for `shareNow` (Buffer fetches them immediately), but they expire before Buffer tries to fetch them at a future scheduled time. **Don't use Airtable attachment URLs for anything scheduled more than ~1 hour out.** Options going forward: (a) always use `shareNow` and handle timing ourselves, (b) host images somewhere with truly permanent URLs (e.g. a public S3/Cloudflare bucket) before scheduling ahead, or (c) re-fetch fresh Airtable URLs and re-submit right before the scheduled time via a cron-like process. Not yet decided which.

## Open items still to confirm

- Whether `resolution` accepts `"0.5K"` on this model
- Actual max prompt length for v2 specifically (assumed same 5000 char limit as v1, unverified)
- Typical latency at scale (first test was 62s for one 1K image — fine for polling at this volume, may need webhook if generating many images per run)
