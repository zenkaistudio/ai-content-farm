# Nano Banana 2 on KIE AI — Reference Notes

Working notes on how the Designer stage actually calls KIE AI's Nano Banana 2 model. Written for myself (Claude) to reference when constructing image-generation prompts/requests for this pipeline — keep this updated as we confirm more by actually calling the API.

## Auth & Endpoint

- Base: `https://api.kie.ai`
- Create task: `POST https://api.kie.ai/api/v1/jobs/createTask`
- Header: `Authorization: Bearer <KIEAI_API_KEY>`
- Header: `Content-Type: application/json`

This is an **async job API** — `createTask` returns a `taskId` immediately; the actual image isn't in that response. Two ways to get the result:
1. Provide a `callBackUrl` (webhook) — recommended for production
2. Poll a "Get Task Details" endpoint with the `taskId` (exact path not yet confirmed — check the docs "Examples"/full API reference when we do the first live call, don't guess the path)

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

## Open items to confirm on first real API call

- Exact "Get Task Details" endpoint path + polling interval
- Whether `resolution` accepts `"0.5K"` on this model
- Actual max prompt length for v2 specifically (assumed same 5000 char limit as v1, unverified)
- Typical generation latency (for deciding poll vs webhook in the eventual script)
