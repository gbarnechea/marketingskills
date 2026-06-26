---
name: product-ad-cinematic
description: Generate a 5–10 second cinematic product video ad from a product photo and brand direction, with matching background music. Use when the user wants to create a product video, cinematic ad, short product commercial, or animated product showcase. Trigger phrases: "product ad," "cinematic ad," "product video," "product commercial," "animate my product," "product showcase video," "short video ad," "cinematic product." Requires muapi.ai API access. For UGC-style video with a person, see ugc-video-factory. For static ad creatives, see ad-creative.
metadata:
  version: 1.0.0
  source: https://github.com/SamurAIGPT/Generative-Media-Skills
---

# Product Ad — Cinematic

**Generate a 5–10 second cinematic product video ad from a product photo and brand direction.**

## Inputs

| Name | Required | Default | Description |
|:-----|:--------:|:--------|:------------|
| `product_image` | yes | — | URL of the product photo (upload via `muapi upload file <path>` if local). |
| `brand_brief` | yes | — | Mood/style direction (e.g. "luxury minimal," "playful and bold," "clean tech"). |
| `duration_sec` | no | 6 | Video length in seconds (5–10). |

If `product_image` or `brand_brief` is missing, ask before proceeding.

## Steps

**Never bundle Phase A and Phase B in a single plan submission.** Always get user approval after Phase A before running Phase B.

### Phase A — Hero frame exploration

Generate 4 hero frame variants with different lighting, palettes, and framing while preserving the product geometry. Apply palette guidance from `brand_brief`:

| Mood keyword | Palette |
|:------------|:--------|
| luxury | gold, black, deep shadow |
| playful | bright, saturated, warm |
| minimal | white, grey, negative space |
| tech | blue-white, clean lines, gradient |
| organic / natural | earth tones, warm daylight |

Build 4 distinct prompts, each varying one key variable (lighting angle, background color, hero framing). Run all 4 in parallel:

```bash
for i in 1 2 3 4; do
  muapi image edit \
    --image-urls "{product_image}" \
    --prompt "<variant $i prompt>" \
    --model nano-banana-2 \
    --aspect-ratio 16:9 \
    --view &
done
wait
```

Present all 4 variants to the user. Ask which one to proceed with.

### Phase B — Video production

After user selects a hero frame (`selected_frame`), run these three steps:

#### B1 — Upscale the hero frame

```bash
muapi image upscale \
  --image-url "{selected_frame}" \
  --view
```

Capture as `upscaled_frame`.

#### B2 — Animate to video

```bash
muapi video from-image \
  --image-url "{upscaled_frame}" \
  --model kling-v3.0-standard \
  --prompt "slow cinematic push-in, soft volumetric light, subtle product micro-rotation, {brand_brief} aesthetic" \
  --duration {duration_sec} \
  --aspect-ratio 16:9 \
  --view
```

#### B3 — Generate background music

Derive music style from `brand_brief` keywords:

| Brand brief | Music style |
|:-----------|:-----------|
| luxury | ambient cinematic, warm strings, minimal |
| playful | upbeat electronic, bright, energetic |
| tech | modern electronic, clean beats, forward motion |
| organic | acoustic, gentle, nature-inspired |

```bash
muapi music generate \
  --style "<derived music style>" \
  --prompt "background score for a {duration_sec}-second product ad, {brand_brief} mood, no vocals" \
  --duration {duration_sec}
```

Return the upscaled image, the final video, and the audio track.

## Notes

- Phase B costs approximately 80 credits on muapi.ai. Always confirm with the user before running.
- If video generation fails, gracefully degrade to a still-frame slideshow with the music track.
- Run `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For local product images, upload first: `muapi upload file ./product.jpg --output-json --jq '.url'`

## Requirements

- `MUAPI_API_KEY` environment variable (set via `muapi auth configure`)
- muapi-cli installed: `npm install -g muapi-cli` or `pip install muapi-cli`

## Related Skills

- **generative-media**: Core muapi primitives for image, video, and audio generation
- **ugc-video-factory**: Person + product → UGC-style vertical video ad
- **youtube-thumbnail**: High-CTR YouTube thumbnail from title and style
- **ad-creative**: Ad copy and multi-platform creative strategy
