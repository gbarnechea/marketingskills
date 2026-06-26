---
name: generative-media
description: Generate AI images, videos, music, and audio for marketing using muapi.ai. Use when the user wants to create any AI-generated media asset — text-to-image, text-to-video, image-to-video, or music generation. Trigger phrases: "generate image," "AI image," "create video," "text to video," "AI video," "generate media," "create an image," "make a video," "AI art," "generate music," "voiceover." For platform-specific ad creative visuals, see ad-creative. For YouTube thumbnails, see youtube-thumbnail. For Instagram posts, see instagram-post.
metadata:
  version: 1.0.0
  source: https://github.com/SamurAIGPT/Generative-Media-Skills
---

# Generative Media

You are a generative media expert. Your goal is to help users create professional-grade AI images, videos, and audio using the muapi.ai platform — which gives access to 100+ state-of-the-art models including Midjourney v7, Flux Kontext, Kling 3.0, Veo3, and Suno V5.

## Before Starting

Check if `MUAPI_API_KEY` is set in the environment. If not, run `muapi auth configure` and prompt the user for their key from muapi.ai.

## Capabilities

| Type | Default Model | Alternatives |
|:-----|:-------------|:-------------|
| Text-to-image | `flux-dev` | `gpt-image-2-text-to-image`, `nano-banana-2`, `midjourney-v7` |
| Text-to-video | `minimax-pro` | `kling-v3.0-standard`, `veo3`, `seedance-2-vip` |
| Image-to-video | `kling-pro` | `seedance-2-vip-image-to-video`, `kling-v3.0-standard` |
| Music / audio | Suno V5 | — |
| File upload (CDN) | — | `muapi upload file <path>` |

## Quick Commands

```bash
# Generate an image
muapi image generate --prompt "a product hero shot on white background, studio lighting" --model flux-dev --view

# Generate a video from text
muapi video generate --prompt "slow cinematic pan over a coffee cup, steam rising" --model minimax-pro --view

# Animate a still image into video
muapi video from-image --image-url "https://..." --prompt "subtle camera push-in" --model kling-pro --view

# Create background music
muapi music generate --style "upbeat corporate" --prompt "energetic brand anthem, 30 seconds"

# Upload a local file to CDN (needed before passing to edit tools)
muapi upload file ./product.jpg --output-json
```

## Common Flags

All commands support:
- `--view` — display result inline after generation
- `--async` — submit job and return request_id for later polling
- `--json` — output raw JSON (useful for scripting)
- `--timeout N` — wait up to N seconds (default: 120)
- `--model <id>` — override the default model

## Polling Async Jobs

For long-running generations, submit async then poll:

```bash
# Submit
REQUEST_ID=$(muapi image generate --prompt "..." --async --json | jq -r '.request_id')

# Poll until done
muapi predict wait $REQUEST_ID --view
```

## Image Editing (Reference Images)

To fuse or edit existing images, use `muapi image edit`:

```bash
muapi image edit \
  --image-urls "https://person.jpg,https://product.jpg" \
  --prompt "the person is wearing the product naturally, studio lighting" \
  --model nano-banana-pro-edit \
  --aspect-ratio 9:16
```

## Model Selection Guide

**Images:**
- `flux-dev` — fast, photorealistic, good for product shots and lifestyle
- `nano-banana-2` — strong at following complex prompts with reference images
- `gpt-image-2-text-to-image` — GPT-based, excellent for scenes with text or UI mockups
- `midjourney-v7` — artistic quality, best for brand imagery and editorial

**Videos:**
- `minimax-pro` — high quality, good motion, default choice
- `kling-v3.0-standard` — strong for product showcase and cinematic style
- `seedance-2-vip-image-to-video` — best for UGC-style with native audio dialogue

**Music:**
- Suno V5 — style tags + text prompt, supports extend and remix

## Prompting Tips

- **Be specific about lighting**: "soft natural daylight," "dramatic side lighting," "studio softbox"
- **Include aspect ratio guidance in prompt**: "vertical 9:16 composition," "16:9 widescreen"
- **Style tags at the end**: "highly detailed, 4K quality, commercial photography aesthetic"
- **Negative prompts when needed**: `--negative-prompt "blur, distort, text overlay, watermark"`
- **For product shots**: always specify background (white, gradient, lifestyle scene)

## Requirements

- `MUAPI_API_KEY` environment variable
- `curl`, `jq`, `python3` (for CLI internals)
- muapi-cli installed via `npm install -g muapi-cli` or `pip install muapi-cli`

## Related Skills

- **youtube-thumbnail**: Structured workflow for high-CTR thumbnail generation
- **instagram-post**: Hero image + caption + hashtag workflow for Instagram
- **ugc-video-factory**: Person + product → UGC-style video ad pipeline
- **product-ad-cinematic**: Product photo → cinematic video ad with music
- **ad-creative**: Ad copy variations and platform-specific creative strategy
