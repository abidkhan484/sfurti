---
name: content-storytelling
description: Use when generating video production packages, image prompts, social media captions, voiceover scripts, or complete storyboard documents from a story idea or topic. Triggers when the user says "create a story", "generate content", "make a video script", "write a storyboard", "give me image prompts", or "create social captions" for any topic.
---

# Content Storytelling Pipeline

## Overview

A full content production pipeline that converts a single story idea or topic into a complete, ready-to-use multi-format content package. Output is split across separate files: **script**, **image prompts**, and **social captions** — ready to drop directly into external tools like OpenMontage, Midjourney, DALL-E, ElevenLabs, or CapCut.

**Core principle:** You provide the idea; the agent generates everything else — from scene-by-scene screenplay to per-scene image generation prompts to platform-optimised social hooks.

## When to Use

- User provides a story idea, topic, or concept and wants production-ready content
- User needs image generation prompts for a specific narrative or mood
- User needs a video script with shot directions and narration
- User needs social media captions and hooks for a story-driven post
- User wants a complete storyboard that can be handed to OpenMontage or a designer

**When NOT to use:**
- For product research or trend analysis → use `product-finding` skill
- For scoring products → use `product-scoring` skill
- For generating a single sentence or one-liner → respond directly, no pipeline needed

## Input Format

The user provides any of the following (minimal input required):

```
Topic / Story Idea: <what the story is about>
Format: <short-form video | long-form video | static post | all>
Tone: <cinematic | playful | emotional | educational | dramatic | inspirational>
Audience: <who this is for — age group, platform, context>
Language: <language for the generated text content>
Character: <optional — who the story follows>
Platform: <TikTok | Instagram Reels | YouTube Shorts | YouTube | Facebook | website>
Duration: <15s | 30s | 60s | 90s | 2min | 5min+>
Style: <Ghibli | cinematic live-action | documentary | animated explainer | Pixar | realistic>
```

If any field is missing, infer a reasonable default from context and state what you assumed.

## Output Structure

Every run produces **three separate files** in the current working directory (or a subfolder named after the story):

```
<story-name>/
  script.md           # Full production script with scenes, narration, shot directions
  image-prompts.md    # Per-scene image generation prompts (Midjourney / DALL-E / Flux / SD)
  social-captions.md  # Platform-specific hooks and captions
```

### File 1: script.md

A scene-by-scene production script. Each scene contains:

| Field | Content |
|---|---|
| Scene number | Sequential (Scene 1, Scene 2...) |
| Duration | Seconds for this scene |
| Setting | Where we are, time of day, lighting |
| Shot type | Wide, medium, close-up, aerial, POV |
| Action | What happens visually — character, motion, objects |
| Narration / Dialogue | Exact words spoken (if any) |
| Voiceover notes | Tone, pace, emotion (for ElevenLabs or TTS) |
| Sound / Music | Suggested mood, genre, instrument, SFX |
| OpenMontage note | Which pipeline fits this scene (optional) |

**Script template per scene:**
```markdown
---
## Scene [N] — [SCENE TITLE]
**Duration:** [X] seconds
**Setting:** [describe location, lighting, time of day]
**Shot:** [WIDE / MEDIUM / CLOSE-UP / AERIAL / POV / MACRO]
**Action:** [describe exactly what happens visually]

**Narration:**
> "[exact voiceover text]"

**Voiceover notes:** [tone: warm / authoritative / whispery / energetic | pace: slow / moderate / fast]

**Music / Sound:** [mood description + genre + suggested instrument]
**OpenMontage pipeline:** [animated-explainer | documentary-montage | cinematic-trailer | character-animation | etc.]
---
```

### File 2: image-prompts.md

One detailed image generation prompt per scene. Each prompt is ready to paste into:
- **Midjourney** — includes `--ar`, `--style`, `--v` flags
- **DALL-E / ChatGPT Image** — includes natural language with style instruction
- **Stable Diffusion / Flux** — includes negative prompts and CFG hints
- **Google Imagen / Gemini** — clean descriptive prompt

**Image prompt template per scene:**
```markdown
---
## Scene [N] Image — [SCENE TITLE]

**Concept:** [1-sentence description of what this image should show]

**Midjourney:**
```
[detailed prompt], [lighting], [mood], [style reference], [camera lens], [color palette] --ar 16:9 --style raw --v 7
```

**DALL-E / ChatGPT Image:**
```
[natural language description with art style, lighting, composition, color, mood, detail level]
```

**Stable Diffusion / Flux:**
```
Positive: [detailed prompt with style tokens]
Negative: [blurry, low quality, text, watermark, distorted, oversaturated, cartoon if realistic, etc.]
```

**Key visual elements:** [list the 3-5 non-negotiable elements this image must contain]
---
```

### File 3: social-captions.md

Platform-specific caption packages, each including:

| Element | Description |
|---|---|
| Hook (first line) | Stops the scroll — question, bold claim, or unexpected fact |
| Body | 2-4 lines of story or context |
| CTA (call to action) | What to do next — comment, share, click |
| Hashtags | 5-10 relevant tags for discoverability |
| Alt text | For accessibility |
| Reel/TikTok caption | ≤150 chars for the overlay caption |

**Platforms covered per run:**
- TikTok / Instagram Reels (short, punchy, 150-char overlay + full caption)
- Facebook (conversational, longer, story-first)
- YouTube (title + description + tags)
- LinkedIn (optional — professional angle)

## Pipeline Flow

```
User provides idea
       ↓
Agent infers missing fields (states assumptions)
       ↓
Generate story structure: acts, beats, emotional arc
       ↓
Write script.md → scene-by-scene with all fields
       ↓
Write image-prompts.md → one prompt set per scene
       ↓
Write social-captions.md → per-platform packages
       ↓
Output summary: file list + how to use each file
```

## Tool Integration Guide

### OpenMontage Integration

OpenMontage (github: calesthio/OpenMontage) is an agentic video production system.
To use your `script.md` with OpenMontage:

1. Install: `git clone https://github.com/calesthio/OpenMontage.git && cd OpenMontage && make setup`
2. Open the project in Claude Code, Cursor, or Copilot
3. Paste this prompt to OpenMontage:
   ```
   Here is my production script: [paste script.md content]
   Use the [animated-explainer | cinematic-trailer | documentary-montage] pipeline.
   Target duration: [X] seconds. Style: [your style].
   ```
4. OpenMontage will handle: asset generation → editing → render → captions

**Pipeline selection guide:**
| Story type | OpenMontage pipeline |
|---|---|
| Educational / how-to | `animated-explainer` |
| Emotional / brand story | `cinematic-trailer` |
| Real-world documentary | `documentary-montage` |
| Character-driven animation | `character-animation` |
| Product demo | `screen-demo` or `talking-head` |
| Social clip / Reel | `short-form` |

### Image Tools Integration

| Tool | Where to paste | Prompt file to use |
|---|---|---|
| Midjourney | Discord `/imagine` prompt | `image-prompts.md` → Midjourney section |
| DALL-E | ChatGPT image prompt field | `image-prompts.md` → DALL-E section |
| Stable Diffusion / Flux | ComfyUI or Automatic1111 | `image-prompts.md` → SD section |
| Canva AI | Canva Magic Media prompt | `image-prompts.md` → DALL-E section |
| Adobe Firefly | Firefly text-to-image | `image-prompts.md` → DALL-E section |

### Voiceover Tools Integration

Use the **Narration** text from `script.md` with:
- **ElevenLabs**: paste narration text → choose voice → apply voiceover notes (tone/pace)
- **Google TTS / Gemini TTS**: paste narration directly
- **OpenAI TTS**: use `alloy` (neutral), `nova` (warm), `onyx` (deep), `shimmer` (soft)
- **Piper TTS** (free/local): paste narration, select voice model

### Video Editing Integration

After generating images and voiceover:
- **CapCut**: import images → add voiceover → add captions from `social-captions.md` overlay
- **OpenMontage**: full pipeline — script → images → voiceover → captions → render → final video
- **DaVinci Resolve / Premiere**: import assets, use script.md as cut guide

## Format Quick Reference

| User says | Output format | Files generated |
|---|---|---|
| "make a video about X" | Full video production package | script.md + image-prompts.md + social-captions.md |
| "write a script for X" | Script only | script.md |
| "give me image prompts for X" | Image prompts only | image-prompts.md |
| "write captions for X" | Social captions only | social-captions.md |
| "create a storyboard for X" | All three files + summary table | all three |
| "make a text post about X" | Social captions only (no video) | social-captions.md |

## Tone Reference

| Tone keyword | Narration style | Music style | Visual style |
|---|---|---|---|
| `cinematic` | Slow, deliberate, gravitas | Orchestral, building | Wide shots, golden hour, contrast |
| `playful` | Light, fast, energetic | Upbeat indie or lo-fi pop | Bright colors, tight cuts, fun angles |
| `emotional` | Warm, intimate, personal | Soft piano or strings | Close-ups, candlelit, shallow depth of field |
| `educational` | Clear, paced, authoritative | Neutral ambient | Clean diagrams, moderate pacing |
| `dramatic` | Intense, urgent | Percussion, dark pads | High contrast, B&W accents, rapid cuts |
| `inspirational` | Warm, rising, hopeful | Building acoustic or synth | Sunrise, movement, human connection |

## Common Mistakes

| Mistake | Fix |
|---|---|
| Writing a narration that's too long for the scene duration | Rule: 120-150 words per minute of spoken narration. A 10s scene = 20-25 words max |
| Vague image prompts like "a beautiful scene" | Always specify: subject, setting, lighting, mood, art style, camera angle, color palette |
| Same caption for all platforms | Each platform has different character limits, tone, and user intent — write separately |
| Missing voiceover notes | Always include: tone (warm/authoritative) + pace (slow/moderate/fast) + emotion |
| Forgetting the hook in social captions | First line must stop the scroll — question, bold statement, or unexpected fact |

## Output Summary Block

At the end of every run, output this summary:

```markdown
## Production Package Summary

| File | Purpose | Paste into |
|---|---|---|
| script.md | Scene-by-scene production script | OpenMontage, CapCut, video editor |
| image-prompts.md | Per-scene image generation prompts | Midjourney, DALL-E, Flux, Firefly |
| social-captions.md | Platform captions + hooks | TikTok, Instagram, Facebook, YouTube |

**Estimated production cost (with OpenMontage):**
- Zero keys (Piper TTS + free stock): $0
- With image gen (FLUX): ~$0.15–$0.50
- With video gen (Kling/Runway): ~$1–$3

**Next step:** Paste `script.md` into OpenMontage and say:
"Here is my production script. Use the [pipeline] pipeline. Duration: [X]s."
```
