# Grunge Distrutto Font — Design Spec

## Overview
Modify the "MINACCE" HTML/SVG page to make the font look more punk/rock with a grunge/destroyed aesthetic. The user approved the "Grunge Distrutto" direction.

## Changes

### 1. SVG Filter Enhancement (`#noisy-text`)
- `feTurbulence`: baseFrequency 1.8 → 4.0, numOctaves 3 → 4, animated seed (keep)
- `feDisplacementMap`: scale 10 → 25
- Add `feMorphology` operator="dilate" radius="1" for eroded edges
- Add second `feTurbulence` + `feColorMatrix` for dark noise texture overlay

### 2. JavaScript Path Distortion
- Parse SVG `path` elements at load time
- Add per-vertex jitter (random offset) that changes slowly
- Jitter stronger on right/bottom edges for a "crumbling" look
- Refresh vertex positions every 5-10 seconds

### 3. Destruction Masks
- SVG `mask` with random geometric shapes (triangles, jagged blobs) that "eat" parts of letters
- Mask updates every 3-4 seconds with new random cutouts positioned near edges
- More holes on bottom-right of each letter (crumbling effect)

### 4. Texture Overlay
- Dark noise layer on top of text with `mix-blend-mode: multiply`
- Scratch/grime lines via Hydra or canvas overlay

## Files to Modify
- `index.html` — enhance SVG filter, add JS path distortion and mask logic
- `minacce.svg` — no changes needed (it's the source, but we modify at runtime)
- `sfondo.png` — no changes

## Acceptance Criteria
- Text looks visibly distressed/grunge compared to current version
- Effects are dynamic (subtle movement, tremble)
- Readable enough to recognize "MINACCE"
- All existing effects (lightning, Hydra, glitch) preserved
