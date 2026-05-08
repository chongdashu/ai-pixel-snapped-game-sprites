# 06 — Walk Cycle (image-to-video, not still-image generation)

Walks come from **image-to-video** — not still-image generation. Asking GPT Image 2.0 (or any still-image model) for a multi-frame walk cycle reliably fails: the model can't keep the character grounded across frames.

i2v models (WAN 2.0 / SeedDance 2.0 via [fal.ai](https://fal.ai)) hold the character in place across the full clip, then you extract 8–10 frames from the result.

## The two big differences vs image-gen actions

| | Image-gen actions (file 04) | i2v walks (this file) |
|---|---|---|
| Output | Pose board, multi-frame, drift between cells | Continuous video, character roughly anchored throughout |
| Drift | Heavy per-cell — needs frame alignment later | Minimal — character stays put in the source video |
| Selection | Recover all generated frames | Pick 8–10 from 80–90 video frames |

## Inputs

1. **Snapped chroma anchor for the direction** (e.g. `w-snapped-1024-chroma-canonical.png`) — same identity reference as image-gen.
2. The video prompt below.

## Output

- A 4–5 second clip at 16–30 fps (80–90 frames).
- Most are filler. You select N frames spaced through one full cycle (left foot extreme → right foot extreme → left foot extreme).

## i2v video prompt

```text
Generate a {DURATION}-second image-to-video clip from the input anchor.

Subject: same character as input image, {DIRECTION}-facing.
Motion: in-place walk cycle. Alternating left/right leg poses, counter-swinging arms. Explicitly forbid synchronized arm/leg sway.
Constraint: character must remain centered, no horizontal drift, no rotation, no scale change. Character must not jump out of frame.
Style: preserve input pixel-art style. Hard edges. No painterly motion blur.
Background: keep input chroma background unchanged across all frames.
```

## Frame selection

After the video is generated, scrub through and pick:

- **Cycle start** — extreme left leg forward (or whichever pose is your zero).
- **Cycle end** — return to the same pose.
- Distribute N frames evenly between start and end (typically 8).

Anything outside [start, end] is filler — drop it. Anything between [start, end] beyond the chosen N is also dropped.

## After selection — both branches converge

Selected walk frames go through the **same downstream pipeline** as image-gen actions:

1. Per-frame chroma-layout snap (file 07)
2. Background clean
3. Normalise into 1280×512 runtime sheet (file 08)
4. Optional manual alignment (file 08)

The only difference is you don't need heavy per-frame anchoring on walks — the source video already aligned them.

## Result

8 evenly-distributed frames packed into a runtime sheet, sharing the same native grid as the idle and attack:

| Walk runtime spritesheet | Walk preview |
|---|---|
| ![Walk sheet](../references/runtime/walk-w-runtime-spritesheet.png) | ![Walk preview](../references/runtime/walk-w-preview.gif) |
