# 07 — Per-Frame Chroma-Layout Snap

After frame recovery (file 05) or walk-cycle selection (file 06), every recovered frame is still a mixel — the AI rendered it that way. Snap each frame **individually** before runtime normalisation.

Without this step, the recovered frames re-emerge as mixels at runtime no matter what you do downstream.

## Same algorithm as the anchor snap

The 5-step pixel snap from file 02 is reused here, with one change: the snap source mode is `chroma-layout` instead of plain image input.

## Why `chroma-layout` mode

The pose board (file 04) uses chroma green as a cell-layout cue — every pose sits inside a green-bounded region. When snapping a single recovered frame, the snapper sees the green-keyed background and uses it as additional structure. This produces cleaner edge recovery than running plain pixel snap on the keyed PNG.

For walk frames (no chroma layout, since they came from i2v video frames), use the same mode — the snapper handles the absence of layout cues gracefully.

## Settings (same as file 02)

| Param | Value |
|---|---|
| `k_colors` | `256` |
| `target_size` | matches the recovered frame's bounding box, upscaled to fit |
| `chroma` | `#00FF00` |

## Result (single frame example — strike frame from attack)

![Pixel-snapped strike frame](../references/poseboard/attack-w-pixel-snapped-strike-frame.png)

Hard pixel edges, locked palette, no blur. Same native grid as the anchor.

## Output

A directory of snapped per-frame PNGs, native-grid-aligned with the anchor. Feed these to file 08 (background clean + runtime normalise).
