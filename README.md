# AI Pixel-Snapped Game Sprites — Real Pixels From AI, Game-Ready

Prompt templates, reference images, and the actual game-ready spritesheets for two characters (pirate and skeleton) — generated entirely with AI, snapped to real pixel art, and dropped straight into a game.

| Pirate (combined: walk · attack · hurt · death) |
|:---:|
| ![Pirate combined](references/runtime/pirate-w-all-actions-combined.gif) |

*Every frame above is AI-generated, snapped to a discoverable native grid, normalised to a 256×256 runtime cell with a locked foot baseline. No mixels, no bleeding between frames, no drift.*

📺 **Watch the full tutorial:** [Stop Generating Fake Pixel Art Game Sprites - Full Guide](https://www.youtube.com/watch?v=nIAIxvNUrdU)

🧩 **Excalidraw Cheatsheet:** [Pixel-Snap Pipeline Cheatsheet](https://aiod.dev/vgd-06-cheatsheet?utm_source=github&utm_medium=readme_top&utm_campaign=vgd-06)

🐦 **Follow [@chongdashu](https://x.com/chongdashu) on X** for daily AI gamedev tips, prompts, and experiments.

This repo contains everything you need to follow along:

- `prompts/` — the exact prompt templates, one per pipeline step (eight in total)
- `references/` — the reference images you'll see at each pipeline stage (problems, prompt-discipline comparisons, anchors, pose boards, runtime spritesheets, diagrams)
- `references/grids/` — the actual alternating-pixel guide canvases referenced by prompts 03 and 04 (1024×1024 anchor guide, 2048×1536 hires pose-board guide)
- `spritesheets/` — the final, game-ready output for both characters: pirate and skeleton, six animations each (idle, walk, attack, hurt, jump, death)

---

## Want more like this?

- [**Free Insiders**](https://insiders.aioriented.dev?utm_source=github&utm_medium=readme_more_like_this&utm_campaign=vgd-06) — newsletter + cheat sheets, prompts, and reference images from every video.
- [**VibeGameDev**](https://vibegamedev.com?utm_source=github&utm_medium=readme_more_like_this&utm_campaign=vgd-06) — agent skills that automate this whole pipeline (`gamedev-assets`, `animated-spritesheets`, `pixel-snapper`), full source from every project, and the step-by-step tutorial for the survival beat-em-up these sprites drop into.

---

## The 3 Problems This Pipeline Fixes

If you've tried to use AI to generate game sprites, you've probably hit these:

| Problem | What it looks like |
|---|---|
| **1. Mixels — fake pixel art that breaks when you zoom in** | ![Mixel vs real](references/problems/mixel-vs-real-side-by-side.png) |
| **2. Frame bleeding — naive grid crops chop your sprites** | ![Naive grid animated](references/problems/bleeding-naive-grid-animated.gif) |
| **3. Frame drift — sliding, bobbing, jankiness in-game** | ![Sliding before](references/problems/drift-before-sliding-attack.gif) |

This pipeline fixes all three.

---

## The Pipeline

> Image gen ≈ 20% of the work. The other 80% is the post-processing pipeline below.

```text
text prompt + style guide
  → south anchor (1024×1024 chroma green)
  → pixel snap (recover native grid, NN upscale, re-key chroma)
  → directional anchors NSEW (snapped chroma S as identity)
  → pixel snap each direction → canonical reference per direction
  → action pose boards (snapped chroma anchor + pose-board guide)
  → frame recovery (foreground components, NOT grid crops)
  → native review checkpoint (foot-aligned, no scaling)
  → per-frame chroma-layout snap
  → background clean + green-fringe despeckle
  → normalise to 1280×512 runtime sheet (5×2 cells of 256×256, foot-anchored)
  → frame aligner (manual 1-2 pixel polish)
  → game-ready spritesheet
```

Walk cycles take a parallel input path (image-to-video instead of still-image), and converge into the same downstream pipeline after frame selection.

| Stage | Output |
|---|---|
| Bad prompt (detail-heavy) ❌ | ![Bad raw](references/prompt-discipline/bad-detail-heavy-raw.png) |
| Bad prompt — recovered native (174×177) ❌ | ![Bad snapped](references/prompt-discipline/bad-detail-heavy-snapped-174x177.png) |
| Good prompt (restrictive) ✅ | ![Good raw](references/prompt-discipline/good-restrictive-raw.png) |
| Good prompt — recovered native (96×96) ✅ | ![Good snapped](references/prompt-discipline/good-restrictive-snapped-96x96.png) |
| NSEW directional anchors | ![Directional](references/anchors/direction-anchors-nsew.png) |
| Generated pose board (attack, hires preset) | ![Pose board](references/poseboard/attack-w-generated-poseboard.png) |
| Naive grid vs recovered components | ![Recovered vs grid](references/poseboard/attack-w-naive-grid-vs-recovered.png) |
| Native review checkpoint | ![Native review](references/poseboard/attack-w-recovered-vs-native-review.png) |
| Per-frame snapped (strike frame) | ![Snapped strike](references/poseboard/attack-w-pixel-snapped-strike-frame.png) |
| Final runtime spritesheet (1280×512) | ![Runtime sheet](references/runtime/attack-w-runtime-spritesheet.png) |
| Final runtime preview | ![Runtime preview](references/runtime/attack-w-preview.gif) |

---

## Prompt Templates

Step-by-step templates with `{PLACEHOLDERS}` you fill in for your own character:

1. [01 — South Anchor](prompts/01-south-anchor.md) — the most important image you'll generate
2. [02 — Pixel Snap](prompts/02-pixel-snap.md) — recover the native grid, NN upscale, re-key chroma
3. [03 — Directional Anchors](prompts/03-directional-anchors.md) — NSEW from the snapped south
4. [04 — Action Spritesheet](prompts/04-action-spritesheet.md) — pose boards for idle/attack/hurt/jump/death
5. [05 — Frame Recovery](prompts/05-frame-recovery.md) — foreground components, NOT grid crops
6. [06 — Walk Cycle (image-to-video)](prompts/06-walk-cycle-i2v.md) — the only thing that actually works
7. [07 — Per-Frame Chroma-Layout Snap](prompts/07-per-frame-snap.md) — stops mixel re-emergence at runtime
8. [08 — Runtime Normalise + Frame Aligner](prompts/08-runtime-normalize-and-align.md) — pack, foot-anchor, polish

---

## The Stack

- **Image generation:** GPT Image 2.0 (anchors, idle, attack, hurt, jump, death)
- **Walk cycles:** [fal.ai](https://fal.ai) → WAN 2.0 / SeedDance 2.0 (image-to-video)
- **Pixel snap:** Port of the open-source [Sprite Fusion Pixel Snapper](https://github.com/Hugo-Dz/spritefusion-pixel-snapper) — credit to the Sprite Fusion team
- **Background removal:** Bria via fal, or remove.bg
- **Skills (optional, automated pipeline):** `gamedev-assets` + `animated-spritesheets` + `pixel-snapper` ([VibeGameDev](https://vibegamedev.com?utm_source=github&utm_medium=readme_body_stack&utm_campaign=vgd-06))

---

## Game-Ready Spritesheets (Pirate + Skeleton)

Two characters, six animations each, all generated through the pipeline above. Drop into any 2D engine.

| | Idle | Walk | Attack | Hurt | Jump | Death |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **Pirate** | ![Pirate idle](spritesheets/pirate/idle/preview.gif) | ![Pirate walk](spritesheets/pirate/walk/preview.gif) | ![Pirate attack](spritesheets/pirate/attack/preview.gif) | ![Pirate hurt](spritesheets/pirate/hurt/preview.gif) | ![Pirate jump](spritesheets/pirate/jump/preview.gif) | ![Pirate death](spritesheets/pirate/death/preview.gif) |
| **Skeleton** | ![Skeleton idle](spritesheets/skeleton/idle/preview.gif) | ![Skeleton walk](spritesheets/skeleton/walk/preview.gif) | ![Skeleton attack](spritesheets/skeleton/attack/preview.gif) | ![Skeleton hurt](spritesheets/skeleton/hurt/preview.gif) | ![Skeleton jump](spritesheets/skeleton/jump/preview.gif) | ![Skeleton death](spritesheets/skeleton/death/preview.gif) |

Each animation directory contains:

- `spritesheet.png` — the runtime sheet (1280×512 RGBA, 5×2 cells of 256×256)
- `preview.gif` — animated preview at the manifest fps
- `manifest.json` — frame count, columns, rows, fps, anchor

The `manifest.json` anchor `(128, 255)` is foot-anchored: horizontal centre, bottom of cell. Every frame in every sheet, every animation, every character shares this baseline — that's why the figure doesn't drift in-game.

---

## What You'll Learn

- Why most AI-generated "pixel art" isn't real pixel art (mixels vs real pixels)
- The 3 problems that break AI sprites in a real game: **mixels, frame bleeding, frame drift**
- The upstream prompt discipline that determines whether pixel snap can work (96×96 vs 174×177 native grid recovery)
- Why you should pass the *snapped* anchor — not the raw 1024 — into every downstream generation
- Frame recovery: extracting characters via chroma-key bounding boxes instead of naive grid crops
- The native review checkpoint that catches bad recoveries before runtime normalisation
- Per-frame chroma-layout snap that stops mixel re-emergence in animation frames
- Runtime normalise: 1280×512 sheet, 5×2 cells, foot-baseline lock
- Manual frame alignment: when to do the 1-2 pixel polish, when to skip it
- The two-part rule: prompt discipline upstream, snap pipeline downstream

> Walk cycles need a different (image-to-video) approach. File 06 covers it.

---

## License

MIT — see [LICENSE](LICENSE). Use these prompts, references, and spritesheets freely in your own projects.

If this helped you:

- ⭐ Star this repo
- 🐦 Follow [@chongdashu](https://x.com/chongdashu) on X for more AI gamedev
- 📺 Subscribe on [YouTube @AIOriented](https://www.youtube.com/@AIOriented)
- 🎮 Check out [VibeGameDev](https://vibegamedev.com?utm_source=github&utm_medium=readme_footer&utm_campaign=vgd-06) for the full automation toolkit
