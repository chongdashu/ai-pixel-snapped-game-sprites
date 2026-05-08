# 01 — South Anchor (the most important image you'll generate)

The single south-facing 1024×1024 candidate every other anchor and every animation frame depends on. Generated from a text prompt (or a higher-fidelity image redrawn into a simpler sprite).

The constraints below are the **prompt-discipline lever** — they're what makes pixel snap actually work later. Detail-heavy prompts produce native grids the snap can't reduce; restrictive prompts produce small native grids and big pixel clusters that survive every downstream stage.

## Inputs

- (Optional) one high-fidelity reference image to redraw as a simpler sprite. Identity reference only — the model converts the rendering style.
- The text prompt below.

## Output

- One 1024×1024 PNG, south-facing, on a flat `#00FF00` chroma green background.

## Why these constraints matter

Apples-to-apples comparison at this exact stage, identical settings:

| Prompt style | Recovered native grid |
|---|---|
| Detail-heavy, ornate trim | **170×168** |
| Simple, chunky, 8–12 colours | **96×96** |

Smaller native = bigger pixel clusters = real pixel art at runtime. The snap step (file 02) recovers what's there — it can't reduce noise the prompt asked for.

## Prompt template

```text
Intended use: create a lower-fidelity production sprite anchor for a top-down 2D game.

Input image role: high-fidelity identity reference only. Preserve broad character identity, silhouette, outfit, proportions, and readable personality. Do NOT preserve the high-detail rendering style — convert the reference into a simpler game sprite.

Create one full-body south-facing / front-facing character anchor on a 1024×1024 square canvas.

Style target:
- polished 16-bit / early 32-bit JRPG character sprite style
- lower fidelity than the reference
- simpler shapes and fewer fine details
- crisp chunky pixel art
- readable silhouette
- dark outline clusters
- designed to fit cleanly inside future 256×256 animation cells

Composition:
- exactly one full-body character
- facing screen/front/south
- centered
- full body visible
- generous margin on all sides
- neutral upright pose
- no held weapons (effects live in attack sheets only)

Background:
- exact #00FF00 chroma green, flat opaque
- no transparency
- no shadow under character

Character identity:
- {CORE_IDENTITY}
- {COSTUME_AND_PALETTE}
- {SILHOUETTE_NOTES}

Restrictive constraints (the prompt-discipline lever):
- deliberately simple 16-bit era pixel art
- low fidelity
- chunky readable silhouette
- compact body proportions
- large simple head
- limited 8-12 color palette
- big pixel clusters
- no ornate trim
- no tiny accessories
- no jewelry
```

## Settings

- Image size: **1024×1024** (square). Required.
- Background: **flat `#00FF00`**. Don't accept transparent or shaded backgrounds — pixel snap (file 02) keys off this.
- Quality: high.
- Reference images (if used): one. Identity only.

## Comparison

| ❌ Detail-heavy prompt | ❌ Detail-heavy snapped (174×177) |
|---|---|
| ![bad raw](../references/prompt-discipline/bad-detail-heavy-raw.png) | ![bad snapped](../references/prompt-discipline/bad-detail-heavy-snapped-174x177.png) |

| ✅ Restrictive prompt | ✅ Restrictive snapped (96×96) |
|---|---|
| ![good raw](../references/prompt-discipline/good-restrictive-raw.png) | ![good snapped](../references/prompt-discipline/good-restrictive-snapped-96x96.png) |
