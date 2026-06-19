# Automated AI Instagram Content Machine

**Source:** Instagram Reel (screen recording — no audio captured)
**Creator:** jerry.ai_tutor ("Jerry the AI") — IG bio: "Hi, I'm Jerry! Your friendly AI tutor on Instagram. Daily carousels teaching you AI basics, tutorials, and insights. Making complex tech simple! DM for topics." (58 posts, 15.4K followers, 231 following at time of recording)
**Duration:** ~29.5s (ReplayKit screen recording, captured 2026-06-14)

> Note: This file (`automated IG.mov`) has no audio track — it's a silent screen recording. The "transcript" below was reconstructed by extracting frames and reading the burned-in on-screen captions/diagrams in chronological order, since there's no spoken dialogue to transcribe.

## Transcript (on-screen captions, in order)

1. **[Opening diagram: "THE MACHINE — The whole screen"]** Four nodes — 01 SCRAPE: "copies what already went viral", 02 REWORD, 03 DESIGN: "spins up the visuals", 04 POST: "auto-posts on a schedule" — center label "0 HUMANS — nobody is home"
2. "all [accounts you] follow on instagram are fully autonomous AI"
3. "Scrape, reword, design, post. Runs while you sleep."
4. "Not only is this shit sketchy as fuck,"
5. "I'll explain what I mean by that"
6. "But it is a lot easier to set this up than you think"
7. **[Cut to an Instagram profile: "jerry.ai_tutor" / "Jerry the AI"]**
8. "Create your AI Instagram account — Part 1"
9. **[SKILL 01 / THE RESEARCHER — "It goes shopping."]** Diagram: INPUT "TOP 50 ACCOUNTS (any niche)" → Apify "SCRAPE — grabs every post that already blew up" → Airtable "POST / LIKES / HOOK — saved to Airtable"
10. "Give that agent a skill like Apify"
11. "Does not guess what works. Copies what already did"
12. "scrapes and saves viral Instagram posts"
13. **[SKILL 02 / THE COPYWRITER — "It launders the idea."]** Diagram: NODE 1 INPUT "READS THE WINNERS" → NODE 2 ENGINE "old idea, more words, sounds human / fresh idea, new words, sounds human — REWRITE + VOICE-MATCH: same idea, new words, sounds human" → NODE 3 OUTPUT
14. "Then a skill that takes the top posts and rewrites them"
15. "Same idea. New words. Just different enough." → "carousel scripts"
16. **[SKILL 03 / THE DESIGNER — "It makes the visuals."]** Diagram: INPUT "READS THE COPY TABLE" → ENGINE "NANO BANANA — turns each row of copy into an image" → OUTPUT "A skill that takes that copy and then generates a series of images"
17. "No designer. No camera. No face."
18. **[SKILL 04 / THE POSTER — "It posts itself."]** Diagram: INPUT "TAKES THE FINISHED POSTS" → ENGINE "AUTO-POST — pick your tool, runs on a schedule" (logos shown: Metricool, Unipile) → OUTPUT
19. "And then finally a skill that then posts these"
20. "POSTS EVERY DAY, FOREVER"
21. "Nobody approves it. Nobody is home."
22. **[Back to "THE MACHINE — The whole screen" diagram]**
23. "And then let those skills run free."
24. "Scheduling them to just kind of do that"
25. **[Final face-cam]** "congrats on contributing to the dead internet"

## Key Components / Tools Mentioned

- **Apify** — scrapes viral posts from a list of top accounts in a given niche
- **Airtable** — stores scraped post data (post, likes, hook) as the working dataset
- **AI rewrite/voice-match engine** — rewrites winning post copy with new wording, same idea ("laundering" the idea)
- **"Nano Banana"** — image-generation step that turns each row of rewritten copy into a visual (likely referring to Google's Gemini 2.5 Flash Image model, nicknamed "Nano Banana")
- **Metricool / Unipile** — scheduling/auto-posting tools for publishing on a recurring schedule
- **Pipeline shape:** Scrape → Reword → Design → Post, run as 4 chained "skills"/agents with no human approval step

## Notes for Our Setup

This is the source material behind the `ai-content-farm/` folder scaffolded the same day (2026-06-14) but never built out. (`ai-content-farm/` is a subfolder of this `A.I. Assets` git repo, not its own nested repo.) It maps to a 4-stage pipeline: Researcher (scrape) → Copywriter (rewrite) → Designer (image-gen) → Poster (schedule) — structurally closer to the flat-script pattern in [tradelocker-bot](../tradelocker-bot/) than the markdown-SOP pattern in [Prompts Repo](../Prompts%20Repo/), since this is a recurring automated pipeline rather than one-off agent tasks.

Tool decisions made so far (see `SETUP.md` for full status):
- Researcher: Apify (account/scraper TBD, **not running live scrapes yet**)
- Copywriter: handled directly by Claude, no extra API
- Designer: ChiAI (kie.ai-style aggregator) for Nano Banana 2 / Kling 2.6 image generation
- Storage: Airtable (token already set, base/schema TBD)
- Poster: Buffer (official IG Business API, reuses the Buffer MCP connector noted in the Jarvis transcript)
