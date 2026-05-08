# 04 — Action Spritesheet (idle, attack, hurt, jump, death)

Generate a multi-frame pose board for a single action in a single direction. The output is a 2048×1536 PNG with multiple frames laid out on an implied grid against a chroma background.

The pose board is **not** a runtime spritesheet yet — it's an intermediate the rest of the pipeline (frame recovery → snap → bg clean → normalise → align) processes into a clean 1280×512 runtime sheet.

## Inputs (three)

1. **Snapped chroma anchor for the chosen direction** (e.g. `w-snapped-1024-chroma-canonical.png`) — identity reference. Always snapped, never raw.
2. **Pose-board alternating-pixel guide** at the exact target size (e.g. 2048×1536 for hires preset). Same checker pattern as the anchor guide (file 03), just larger. The hires preset guide used in this pipeline is shipped here: [`references/grids/alternating-2048x1536-4x3-pose-board.png`](../references/grids/alternating-2048x1536-4x3-pose-board.png).
3. The frame-by-frame prompt below.

## Two presets

| Preset | Canvas | Grid | Cell |
|---|---|---|---|
| `standard` | 1536×1152 | 4×3 | 384×384 |
| `hires` | 2048×1536 | 4×3 | 512×512 |

Use `hires` when the snap step needs more pixels to work with (default for most characters).

## Suggested frame counts

| Action | Frames |
|---|---|
| Idle | 10 |
| Attack | 8 |
| Hurt | 6 |
| Jump | 6 |
| Death | 10 |

## Prompt template

```text
Intended use: a reusable {ACTION} animation spritesheet for a top-down 2D game.

Image 1 role: identity anchor. Preserve the exact approved anchor sprite identity.
Image 2 role: alternating-pixel pose-board guide at the exact target size. Use it to preserve the output aspect ratio, full-board composition, pixel texture, and implied {COLS} column × {ROWS} row pose-board layout. Do not copy it as visible grid lines.

Subject:
- Same already-approved sprite character.
- Direction: {DIRECTION_DESCRIPTION}.
- Keep this as the same character, not a redesign.

Primary request: create a {N}-frame {ACTION} sequence on a {CANVAS_W}×{CANVAS_H} pose board. Place the animation frames in the first {N} cells of an implied {COLS} column × {ROWS} row grid, reading left to right, top to bottom.

{FRAME_BY_FRAME_DESCRIPTION}

Look and rendering:
- High-resolution pixelated sprite art.
- Crisp chunky sprite edges.
- Preserve visible pixel structure.
- No painterly rendering, no airbrushing, no soft gradients.
- Keep the sprite large and centered in each frame area.

Composition and background constraints:
- Background must be exact #00FF00 chroma green throughout the entire pose board.
- Each frame should be visually centered inside its implied cell.
- No drift between frames.
- No turn between frames.
- {ACTION_SPECIFIC_CONSTRAINTS}
```

## Two ways to write the frame-by-frame block

**Explicit (best for death and other narrative actions):**

```text
Frame 1: ready stance, neutral.
Frame 2: anticipation, wind back.
Frame 3: wind-up, weapon raised.
Frame 4: strike — weapon at maximum extension.
Frame 5: recoil, weapon pulling back past neutral.
Frame 6: follow-through, momentum carrying through.
Frame 7: recovery, returning toward neutral.
Frame 8: return to ready stance.
```

**Loose (often works for attack and idle):**

```text
Create eight readable {ACTION} poses that feel like a coherent, short game animation. Frame 1 is the ready stance and frame 8 returns to it.
```

Try explicit first. If the model produces inconsistent frames, switch to loose.

## Hard rule — never grid-crop the pose board

The model places each pose somewhat freely inside its cell. Strike frames swing weapons past the cell border, recoil frames shift the body, follow-throughs sit lower. **Naive grid slicing chops the parts that crossed boundaries.** File 05 (frame recovery) covers the right way.

## Result

The pose board comes back like this — visibly laid out on the implied grid, with chroma background:

![Generated pose board](../references/poseboard/attack-w-generated-poseboard.png)

Don't ship this. Process it through files 05 → 08.
