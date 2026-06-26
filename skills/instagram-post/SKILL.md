---
name: instagram-post
description: Create a polished, on-brand Instagram post — square or portrait hero image with matching caption and hashtags. Use when the user wants to create an Instagram post, feed image, Reels cover, or social media visual. Trigger phrases: "instagram post," "ig post," "instagram," "feed post," "instagram creative," "make an instagram," "create a post," "social media post," "reel cover." Requires muapi.ai API access. For YouTube visuals, see youtube-thumbnail. For multi-platform ad creative sets, see ad-creative. For broader social content strategy, see social-content.
metadata:
  version: 1.0.0
  source: https://github.com/SamurAIGPT/Generative-Media-Skills
---

# Instagram Post

**Create a polished, on-brand Instagram post — square or portrait hero image with matching caption and hashtags.**

## Inputs

| Name | Required | Default | Description |
|:-----|:--------:|:--------|:------------|
| `brief` | yes | — | What the post is about (e.g. "summer coffee launch at our café, warm golden vibes"). |
| `brand_style` | no | modern, vibrant, clean typography, lifestyle photography aesthetic | Brand personality and visual style tags. |
| `format` | no | 1:1 | Post format — "1:1" for feed square, "4:5" for portrait feed, "9:16" for Reels cover. |

If `brief` is not provided, ask for it before proceeding.

## Steps

### Phase A — Generate the hero image

1. Write a detailed, atmosphere-rich image prompt based on `brief` and `brand_style`:
   - Include lighting direction, color palette, mood, subject placement, and lens feel.
   - Optimize for Instagram aesthetics: clean, punchy, single focal point.
   - Append style tags: `{brand_style}, social media photography, highly detailed`.

2. Run:

```bash
muapi image generate \
  --prompt "<your built prompt>" \
  --model nano-banana-2 \
  --aspect-ratio "<format>" \
  --view
```

3. If the user provided a product or subject image in the session, prefer `muapi image edit` to maintain visual consistency:

```bash
muapi image edit \
  --image-urls "<user_image_url>" \
  --prompt "<your built prompt>" \
  --model nano-banana-pro-edit \
  --aspect-ratio "<format>" \
  --view
```

Show the generated image before proceeding to Phase B.

### Phase B — Caption & hashtags

After the image is approved, compose and return:

- **Caption**: 2–4 lines.
  - Hook line first (punchy, curiosity-driving)
  - Brand message
  - Call to action
- **Hashtags**: 15–20 targeted hashtags in a separate block. Mix:
  - 5 niche hashtags (very specific to the topic)
  - 10 mid-tier hashtags (10K–500K posts)
  - 5 broad hashtags (1M+ posts, e.g. `#coffee`, `#lifestyle`)

## Notes

- Prioritize scroll-stopping first impressions — the image must communicate the brief within 2 seconds.
- If `format` is `9:16` (Reels cover), note that text overlays are common; include a suggestion for on-screen text placement.
- Do NOT generate multiple variants unless the user explicitly asks.
- Run `muapi auth configure` first if `MUAPI_API_KEY` is unset.

## Requirements

- `MUAPI_API_KEY` environment variable (set via `muapi auth configure`)
- muapi-cli installed: `npm install -g muapi-cli` or `pip install muapi-cli`

## Related Skills

- **generative-media**: Core muapi primitives for image, video, and audio generation
- **youtube-thumbnail**: High-CTR YouTube thumbnail generation
- **social-content**: Platform-specific content strategy, calendars, and engagement
- **ad-creative**: Ad copy variations and multi-platform creative sets
