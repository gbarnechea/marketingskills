---
name: ugc-video-factory
description: Turn a person photo + a product photo into a vertical 9:16 UGC-style video ad with native spoken dialogue. Use when the user wants to create a UGC video ad, lifestyle product video, talking product ad, or TikTok/Reels-style ad. Trigger phrases: "ugc video," "ugc ad," "ugc video factory," "person plus product video," "talking product ad," "ugc reel," "lifestyle product video," "vertical ugc video," "make a ugc," "tiktok ad," "reels ad." Requires muapi.ai API access. For cinematic product video without a person, see product-ad-cinematic. For static ad creative sets, see ad-creative.
metadata:
  version: 1.0.0
  source: https://github.com/SamurAIGPT/Generative-Media-Skills
---

# UGC Video Factory

**Turn a person photo + product photo (+ optional script) into a vertical 9:16 UGC-style video ad with native dialogue audio.**

A three-stage pipeline:
1. Write a director-grade ultra-realistic lifestyle photography prompt.
2. Fuse the person + product into a single hero photo (9:16).
3. Animate the hero photo into a 10s vertical UGC clip with synced spoken audio.

## Inputs

| Name | Required | Default | Description |
|:-----|:--------:|:--------|:------------|
| `person` | yes | — | Photo of the person who will appear in the ad (face + upper body works best). |
| `product` | yes | — | Clear photo of the product (preferably on neutral background, logo/text legible). |
| `script` | no | "Okay… first of all, ship happens. And this hat is honestly my favorite. It also comes in navy and black, so you can pick your vibe." | The exact line the on-screen person will say (1–2 sentences fit 10s comfortably). |
| `environment` | no | study room, laptop in front of it | Scene/context where the person uses the product (e.g. "bathroom mirror, morning routine"). |

If `person` or `product` is missing, ask the user to upload them (`muapi upload file <path>`) or offer to generate placeholders before continuing.

## Steps

Run all three steps sequentially — each step's output feeds the next.

### Step 1 — Director prompt (LLM)

Use a reasoning-capable LLM with temperature 0 and max ~200 tokens to produce the hero-image prompt.

**System prompt:** `You are a helpful assistant.`

**User prompt** (substitute `person`, `product`, `environment`):

```
Uploaded images are being analyzed. Ultra-realistic lifestyle photography with {person} and {product} and {environment}.

If the product is wearable (e.g., hat, glasses, hoodie), the person wears it naturally.
If the product is handheld (e.g., cream, bottle, thermos), the person holds it naturally.

The product is clearly visible and is the main focus. Logo or text on the product must be legible.
The person has a natural and modern look with a minimalist style.
The scene is consistent with the product's use: {environment}.

Lighting: soft natural daylight.
Background: clean, aesthetic, slightly blurred (shallow depth of field).
Style: high-end commercial lifestyle photography, realistic textures, 4K quality, vertical 9:16 composition, social-media advertising style. Facial details and the product must remain unchanged.
```

Capture the LLM response as `hero_image_prompt`.

### Step 2 — Hero image (Nano-Banana Pro Edit)

```bash
muapi image edit \
  --image-urls "{person},{product}" \
  --prompt "{hero_image_prompt}" \
  --model nano-banana-pro-edit \
  --aspect-ratio 9:16 \
  --num-images 1 \
  --resolution 1K \
  --output-format jpeg \
  --view
```

Capture the resulting image URL as `hero_image`. Show it to the user for approval before proceeding to Step 3.

### Step 3 — UGC video (Seedance 2.0 VIP)

```bash
muapi video from-image \
  --image-url "{hero_image}" \
  --model seedance-2-vip-image-to-video \
  --aspect-ratio 9:16 \
  --duration 10 \
  --generate-audio true \
  --cfg-scale 0.5 \
  --negative-prompt "blur, distort, low quality" \
  --prompt "Create a 10-second vertical UGC-style video (9:16). A person interacts naturally with their setting and product. The product is used naturally. The video is a single uninterrupted shot. No cuts. No color changes. No text on screen. The person looks directly at camera with a relaxed expression. They say in a natural conversational tone: \"{script}\" Subtle hand gestures while speaking. End with a small smile or nod. Style: authentic UGC, handheld phone feel, light natural movement, soft daylight, shallow depth of field, TikTok/Reels aesthetic." \
  --view
```

Poll if using async: `muapi predict wait <request_id>`

## Notes

- Keep the script short — Seedance 2.0 compresses longer scripts and clips words. 1–2 sentences is ideal for 10s.
- Seedance VIP tolerates realistic human faces in references. Use VIP tier, not standard.
- For lower latency at same quality, swap to `seedance-2-vip-image-to-video-fast`.
- For multi-shot ads, generate several hero image variations in Step 2 and animate each independently.
- If the muapi CLI doesn't yet alias `seedance-2-vip-image-to-video`, fall back to raw API:

```bash
curl -X POST https://api.muapi.ai/api/v1/video/from-image \
  -H "x-api-key: $MUAPI_API_KEY" \
  -H "content-type: application/json" \
  -d '{"model":"seedance-2-vip-image-to-video","image_url":"...","prompt":"...","aspect_ratio":"9:16","duration":10,"generate_audio":true}'
```

## Requirements

- `MUAPI_API_KEY` environment variable (set via `muapi auth configure`)
- muapi-cli installed: `npm install -g muapi-cli` or `pip install muapi-cli`

## Related Skills

- **generative-media**: Core muapi primitives for image, video, and audio generation
- **product-ad-cinematic**: Cinematic product video without a person (product photo + brand brief)
- **instagram-post**: Static hero image + caption for Instagram
- **ad-creative**: Ad copy and multi-platform creative strategy
