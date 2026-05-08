# 05 — Frame Recovery (foreground components, NOT grid crops)

The pose board (file 04) places frames on an *implied* grid — but not strictly. Strike frames swing weapons past the cell border. Recoil frames shift the body. Follow-throughs sit lower. **Naive grid slicing chops the parts that crossed boundaries.**

## The hard rule

> **Never grid-crop the pose board. Recover foreground components instead.**

## What naive grid crop produces (don't do this)

![Naive grid cells only](../references/poseboard/attack-w-naive-grid-cells-only.png)

Look at any single feature across the strip — the cutlass tip, the leading foot, the hat. They appear, disappear, jump position, get sliced. Each cell looks like a reasonable frame *until* you put them next to each other and trace one element across the sequence.

In motion the chopping is unmissable:

![Naive grid animated](../references/problems/bleeding-naive-grid-animated.gif)

## What component recovery does

For each frame:

1. **Key out the chroma background** (`#00FF00`) → binary mask.
2. **Find the largest connected foreground component** in the cell region.
3. **Bounding-box crop** the foreground component, with a small padding margin.
4. **Save the recovered frame** at its native bounding-box size (NOT scaled to a fixed cell).

Each recovered frame ends up at its own size — that's expected. File 07 (normalise) handles size unification with foot anchoring.

No AI call needed for this. Pillow + a connected-components library covers it.

## Side-by-side proof

Naive cells (left) vs recovered components (right):

![Naive grid vs recovered components](../references/poseboard/attack-w-naive-grid-vs-recovered.png)

## Native review checkpoint (do this BEFORE snap)

After recovery, lay every recovered frame onto a shared review canvas (e.g. 384×448) padded out, **bottom-aligned to a single foot baseline**, no scaling. This is where you catch bad recoveries — twisted poses, missing limbs, partial keys — before they get baked into a runtime sheet you'll throw away.

![Recovered vs native review](../references/poseboard/attack-w-recovered-vs-native-review.png)

If a frame fails review, regenerate **just that frame** with the model — don't redo the whole pose board.

## Output

A directory of native-sized PNGs, one per recovered frame. Feed these to file 06 (per-frame chroma-layout snap).
