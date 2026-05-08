# 03 — Directional Anchors (NSEW from the snapped south)

Generate west and north anchors from the snapped south. East is a horizontal flip of west — don't generate it separately.

## Inputs (two images, not one)

1. **Snapped chroma south anchor** (`s-snapped-1024-chroma.png`) — identity reference. **Always** the snapped chroma version, never the raw 1024.
2. **Alternating-pixel guide** — a uniform 1024×1024 black/white checker. One square = one native pixel. This is what tells the model "draw at this native pixel resolution." The exact file used in this pipeline is shipped here: [`references/grids/alternating-1024x1024.png`](../references/grids/alternating-1024x1024.png).

The guide is uniform — no implied grid, no cell layout. That's the difference from the pose-board guide (file 04), which uses the same alternating-pixel pattern but at a larger canvas with an implied multi-frame layout.

## Why two images

The identity reference fixes the character. The pixel-style guide reinforces the chunky pixel treatment so the model doesn't drift back into mixels just because it's drawing a new pose.

## Output

- One 1024×1024 PNG per direction (W, N), south-facing identity preserved, on `#00FF00` chroma green.
- Snap each output (file 02). The snapped chroma version becomes the canonical reference for every action in that direction.

## Prompt template

```text
Intended use: a reusable single-frame directional anchor sprite for a top-down 2D action game.

Image 1 role: identity anchor. Preserve the exact approved character identity, silhouette, outfit, proportions, and pixel-art readability from this reference image.
Image 2 role: pixel-style anchor. Use this only to reinforce the crisp pixelated treatment, chunky pixel texture, and sprite readability.

Primary request: generate a single-frame {DIRECTION}-facing anchor sprite.

Subject:
- Same character as image 1.
- Direction: {DIRECTION_DESCRIPTION}.
- Keep this as the same character, not a redesign.
- Preserve the character's main handheld weapon as one complete object.
- The weapon must remain visibly gripped in the character's hand.
- The weapon handle must be one continuous unbroken shaft, not split into two angles or disconnected segments.
- For back-facing views, keep the weapon visibly attached to the hand or strapped clearly against the body; do not let it float separately.
- For profile views, keep the weapon silhouette simple and readable with one continuous handle line.

Look and rendering:
- High-resolution pixelated sprite art.
- Chunky crisp sprite edges.
- Preserve the visual family of image 1.
- No painterly shading, no blur, no soft gradients.

Background and composition:
- 1024×1024 square canvas.
- Exact #00FF00 chroma green background.
- Single full-body character, centered, generous margin.
- Neutral upright pose, no held effects.
```

## Placeholders

- `{DIRECTION}` — `west` / `north`.
- `{DIRECTION_DESCRIPTION}` — e.g. *"profile view, facing screen-left, 90 degrees from the south-facing reference"*.

## Result

![Directional anchors NSEW](../references/anchors/direction-anchors-nsew.png)

The W canonical reference used by every action in the west direction:

![W canonical reference](../references/anchors/w-snapped-1024-chroma-canonical.png)
