# 02 — Pixel Snap (the step that turns mixels into real pixels)

Pixel snap is **not an AI call** — it's a deterministic image processing step. Run it on every AI-generated sprite (anchors, action frames, walk frames) before anything else touches them.

This is a port of [`Hugo-Dz/spritefusion-pixel-snapper`](https://github.com/Hugo-Dz/spritefusion-pixel-snapper) — credit and thanks to the Sprite Fusion team. The original is open source; you can use it directly via [spritefusion.com](https://spritefusion.com) or run the algorithm yourself.

## What it does

The model paints small "fake" pixels inside what should be one big pixel — that's a **mixel**. Snap recovers the underlying native grid the model was *trying* to draw, then resamples each native cell to a single solid colour.

## Algorithm (5 steps)

1. **K-means quantise** the palette to `k_colors` clusters.
2. **Compute 1D edge-gradient profiles** along x and y axes — high values where colour changes fast.
3. **Estimate cell pitch** as the median spacing between gradient peaks per axis.
4. **Walk along each axis**, placing cuts that snap to the nearest local edge peak.
5. **Resample** — one output pixel per recovered cell, picking the majority colour.

The recovered grid is *however many cells the walker placed*. There's no target — the algorithm reads the image and recovers what's there.

## Settings

| Param | Value | What it does |
|---|---|---|
| `k_colors` | `256` | Palette quantisation cluster count. Generous — preserves subtle palette while flattening sub-pixel noise. |
| `target_size` | `1024×1024` | Output canvas the recovered native is nearest-neighbour upscaled onto. Independent of recovery. |
| `chroma` | `#00FF00` | Flat green key applied to the upscaled chroma output. Optional. |

## Outputs

Three PNGs land per snap pass:

1. `*-native.png` — the recovered native grid at native size (e.g. 96×96).
2. `*-1024.png` — the native, nearest-neighbour upscaled to 1024×1024. Crisp pixels at full size.
3. `*-1024-chroma.png` — same as above but with the `#00FF00` background re-keyed for downstream identity references.

| Native (96×96) | Snapped 1024 | Snapped 1024 chroma |
|---|---|---|
| ![native](../references/prompt-discipline/good-restrictive-snapped-96x96.png) | ![1024](../references/anchors/s-snapped-1024.png) | ![1024 chroma](../references/anchors/s-snapped-1024-chroma.png) |

## Hard rule — pass the snapped chroma anchor downstream, never the raw 1024

Every subsequent generation step (directional anchors, action sprites, walk cycles) takes the snapped chroma anchor as identity input. **Never** pass the raw 1024 — it carries mixels into the next layer and the snap can't recover them cleanly downstream.

## Why the same character can show two recovered native sizes

If you snap the south candidate at 96×96 and then snap the *west* anchor (a fresh generation) at 102×101, both are correct. Each anchor (N/S/E/W) is an independent generation. Different poses recover differently — side profiles with held weapons have more directional edge structure than front-on stances, so they typically recover slightly larger.

## Zoom proof

Same character, same prompt, same seed — only the snap step differs:

| ❌ Mixel zoom (raw 1024) | ✅ Real pixels zoom (snapped 1024) |
|---|---|
| ![mixel](../references/problems/mixel-zoom-raw.png) | ![real](../references/problems/real-pixels-zoom-snapped.png) |

Side-by-side:

![Mixel vs real side-by-side](../references/problems/mixel-vs-real-side-by-side.png)
