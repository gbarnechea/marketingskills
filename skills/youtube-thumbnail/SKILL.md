---
name: youtube-thumbnail
description: Design a high-CTR YouTube thumbnail — striking imagery, bold text placement guidance, and emotional face or subject if needed. Use when the user wants to create a YouTube thumbnail, increase video click-through rate, or design a cover image for a YouTube video. Trigger phrases: "youtube thumbnail," "yt thumbnail," "thumbnail," "video thumbnail," "youtube cover," "make a thumbnail," "thumbnail for my video," "CTR thumbnail." Requires muapi.ai API access. For Instagram visuals, see instagram-post. For full ad creative sets, see ad-creative.
metadata:
  version: 1.0.0
  source: https://github.com/SamurAIGPT/Generative-Media-Skills
---

# YouTube Thumbnail

**Design a high-CTR YouTube thumbnail — striking imagery, bold text placement, and emotional face/subject if needed.**

## Inputs

| Name | Required | Default | Description |
|:-----|:--------:|:--------|:------------|
| `title` | yes | — | The video title or topic (e.g. "I tried 7 AI tools in 24 hours"). |
| `channel_style` | no | bold, high contrast, bright colors, clean design, YouTube tech aesthetic | Channel brand style (e.g. "dark moody gaming," "bright educational," "minimal corporate"). |
| `subject_description` | no | — | Optional description of person or subject to feature (e.g. "a surprised young man in a hoodie"). |

If `title` is not provided, ask for it before proceeding.

## Steps

Thumbnails are the #1 factor in YouTube CTR. Generate a single, maximum-impact 16:9 image.

### Phase A — Plan the composition

Before generating, briefly reason about the best thumbnail formula for this topic:

- **Emotion-first**: shocked/curious face if relevant + bold text = high CTR
- **Text overlay**: 3–5 words max, high-contrast (white/yellow on dark, or vice-versa)
- **Contrast & saturation**: thumbnails compete in a grid — they must pop

State your composition plan in 2–3 sentences before generating.

### Phase B — Generate the thumbnail

Build the image generation prompt:

1. **Subject**: use `subject_description` if provided; otherwise design an object or scene that dramatizes the topic.
2. **Mood**: derives from `channel_style`.
3. **Composition**: rule-of-thirds, subject on left or right with empty space for text overlay.
4. **Style tags**: `{channel_style}, youtube thumbnail composition, ultra detailed, vibrant, high contrast, 16:9`.

Run:

```bash
muapi image generate \
  --prompt "<your built prompt>" \
  --model gpt-image-2-text-to-image \
  --aspect-ratio 16:9 \
  --view
```

If the user already has a face photo or channel image, use `muapi image edit` with that reference instead.

### Phase C — Text overlay guidance

After generation, return:

- **Suggested overlay text**: 3–5 bold words that complement `title`.
- **Text placement**: where on the canvas to position text (e.g. "bold yellow text, top-right third").
- **Font recommendation**: style suggestion (e.g. "Impact-style all-caps with black outline").

Do NOT embed text directly in the image prompt — text rendering in image models is unreliable. Guide the user to add text in post-production (Canva, Photoshop, etc.).

## Notes

- Suggest A/B variants only if the user explicitly asks.
- If video generation fails or the model is unavailable, fall back to `flux-dev` or `nano-banana-2`.
- Run `muapi auth configure` first if `MUAPI_API_KEY` is unset.

## Requirements

- `MUAPI_API_KEY` environment variable (set via `muapi auth configure`)
- muapi-cli installed: `npm install -g muapi-cli` or `pip install muapi-cli`

## Related Skills

- **generative-media**: Core muapi primitives for image, video, and audio generation
- **instagram-post**: Instagram hero image + caption workflow
- **social-content**: Platform-specific content strategy (including YouTube)
- **ad-creative**: Ad creative sets with platform-optimized crops
