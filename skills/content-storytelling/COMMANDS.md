# Content Storytelling — Command Reference

A slash-command style reference for triggering the `content-storytelling` skill.
Use this as your cheat sheet for getting the exact output you need.

---

## The Master Command

```
/story [IDEA] --format [FORMAT] --tone [TONE] --platform [PLATFORM] --duration [DURATION] --style [STYLE] --language [LANGUAGE] --for [AUDIENCE]
```

All flags are optional. Only `[IDEA]` is required.

---

## Quick Commands

### Full Video Production Package
```
/story "a mother who watches her child solve a wooden puzzle for the first time"
```
**Output:** `script.md` + `image-prompts.md` + `social-captions.md`

---

### Short-Form Social Video (TikTok / Reels)
```
/story "the moment a child's patience pays off" --format short-video --platform tiktok --duration 30s --tone emotional
```

---

### Cinematic Brand Video
```
/story "why hands-on play matters in a world of screens" --format long-video --duration 90s --tone cinematic --style live-action
```

---

### Animated Explainer
```
/story "how a puzzle teaches a child to think step by step" --format short-video --style animated-explainer --duration 60s --tone educational
```

---

### Image Prompts Only
```
/story "a child solving a maze in golden afternoon light" --output image-prompts --style ghibli
```
**Output:** `image-prompts.md` only

---

### Social Captions Only
```
/story "screen-free Sunday with wooden toys" --output captions --platform instagram,tiktok,facebook
```
**Output:** `social-captions.md` only

---

### Script Only
```
/story "a rainy day where a child builds a wooden tower, fails, and tries again" --output script --duration 60s --tone inspirational
```
**Output:** `script.md` only

---

### Bengali Language Content
```
/story "একটি শিশু প্রথমবার ধাঁধা সমাধান করে" --language bengali --platform facebook --tone emotional
```

---

### Reference-Style (OpenMontage format)
```
/story "I love this video: [paste YouTube URL or describe it]. Make me something like this, but about [your topic]."
```
Generates: differentiated concept + full production package

---

## Flag Reference Table

| Flag | Options | Default | Description |
|---|---|---|---|
| `--format` | `short-video`, `long-video`, `static-post`, `all` | `all` | Content format type |
| `--tone` | `cinematic`, `playful`, `emotional`, `educational`, `dramatic`, `inspirational` | `emotional` | Narrative and visual tone |
| `--platform` | `tiktok`, `instagram`, `facebook`, `youtube`, `youtube-shorts`, `website` | `all` | Target social platform |
| `--duration` | `15s`, `30s`, `60s`, `90s`, `2min`, `5min+` | `60s` | Target video duration |
| `--style` | `ghibli`, `cinematic`, `animated-explainer`, `documentary`, `pixar`, `realistic`, `anime` | `cinematic` | Visual style |
| `--language` | Any natural language | English | Language for generated text |
| `--for` | Age group, role, or context description | General audience | Target audience |
| `--output` | `script`, `image-prompts`, `captions`, `all` | `all` | Which files to generate |
| `--character` | Any character description | Inferred from idea | Story's main character |
| `--pipeline` | Any OpenMontage pipeline name | Auto-selected | Force a specific OpenMontage pipeline |

---

## Output Files Guide

| File | What it is | Paste it into |
|---|---|---|
| `script.md` | Scene-by-scene production script with narration, shot directions, sound notes | OpenMontage, CapCut, video editor, director |
| `image-prompts.md` | Per-scene prompts for Midjourney, DALL-E, Flux, Imagen, Firefly | Any image generation tool |
| `social-captions.md` | Hook + body + CTA + hashtags, per platform | TikTok, Instagram, Facebook, YouTube |

---

## OpenMontage Integration Workflow

After running `/story`, take `script.md` and do this:

```bash
# 1. Set up OpenMontage (one-time)
git clone https://github.com/calesthio/OpenMontage.git
cd OpenMontage
make setup

# 2. Open OpenMontage in Claude Code / Cursor / Copilot
# 3. Paste this into chat:
```

```
Here is my production script:
[paste contents of script.md]

Please use the [animated-explainer / cinematic-trailer / documentary-montage] pipeline.
Target duration: [X] seconds. Visual style: [ghibli / realistic / animated].
Generate assets, compose, and render.
```

**OpenMontage selects the pipeline automatically** if you don't specify one.
Use `--pipeline` flag in your `/story` command to pre-select.

---

## Pipeline Match Guide

| Story Type | OpenMontage Pipeline | Best For |
|---|---|---|
| Educational "how it works" | `animated-explainer` | Explainers, step-by-step tutorials |
| Emotional brand moment | `cinematic-trailer` | Powerful feeling-first stories |
| Real-world documentary feel | `documentary-montage` | Authentic, grounded stories |
| Cartoon character story | `character-animation` | Animated characters (SVG/GSAP) |
| Ghibli / Pixar style | `character-animation` + Kling images | Anime/animated aesthetics |
| Product demo | `screen-demo` | Product walkthroughs |
| Social clip / short reel | `short-form` | TikTok, Reels, Shorts |
| Talking head + b-roll | `talking-head` | Personal brand videos |

---

## Image Tool Integration

| Tool | Prompt to paste | Prompt file |
|---|---|---|
| **Midjourney** | `/imagine [prompt] --ar 16:9 --v 7` | `image-prompts.md` → Midjourney section |
| **DALL-E / ChatGPT** | Paste DALL-E prompt directly | `image-prompts.md` → DALL-E section |
| **Flux / Stable Diffusion** | Paste Positive prompt into ComfyUI | `image-prompts.md` → SD/Flux section |
| **Canva AI** | Use as Magic Media prompt | `image-prompts.md` → DALL-E section |
| **Adobe Firefly** | Paste into text-to-image field | `image-prompts.md` → DALL-E section |
| **Kling / Runway** | Use as text-to-video prompt (scene action) | `script.md` → Action field per scene |

---

## Voiceover Tool Integration

Take the **Narration** text from each scene in `script.md`:

| Tool | How to use | Voice note |
|---|---|---|
| **ElevenLabs** | Paste narration → select voice → apply emotion/pace | Use voiceover notes from script |
| **OpenAI TTS** | `alloy` (neutral), `nova` (warm), `onyx` (deep), `shimmer` (soft) | Match to tone in script |
| **Google TTS / Gemini** | Paste narration, select language + voice | Check language match |
| **Piper TTS** (free) | Install via pip, paste narration | Best for zero-cost runs |
| **OpenMontage built-in** | Script handled automatically | OpenMontage manages TTS |

---

## Examples by Use Case

### Use Case 1: A 30-second TikTok Reel
```
/story "the first time silence replaced a screen in our house" --format short-video --platform tiktok --duration 30s --tone emotional --language english
```

### Use Case 2: A Ghibli-style animated video
```
/story "a small wooden robot who learns to solve puzzles in a magical forest" --style ghibli --duration 60s --tone playful --platform youtube-shorts
```

### Use Case 3: Facebook post (image + caption)
```
/story "patience is the skill no app can teach" --output captions --platform facebook --tone inspirational
```

### Use Case 4: Educational YouTube video
```
/story "why children who build things with their hands become better problem solvers" --format long-video --duration 3min --platform youtube --tone educational --style animated-explainer
```

### Use Case 5: Bengali emotional campaign
```
/story "মায়ের চোখে ধৈর্যের গল্প" --language bengali --tone emotional --platform facebook,instagram --duration 60s
```

---

## Cost Estimates (via OpenMontage)

| Configuration | Estimated Cost |
|---|---|
| Zero keys (Piper TTS + no image gen) | $0.00 |
| + FLUX image generation (~12 images) | ~$0.15 |
| + Kling or Runway video clips | ~$1.00–$3.00 |
| + ElevenLabs premium voice | ~$0.10–$0.30 |
| Full premium run (all paid providers) | ~$1.50–$5.00 |
