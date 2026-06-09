# MINACCE

## Stack
- Single `index.html` — zero dependencies (except Hydra via CDN)
- SVG text paths, Canvas (Hydra + noise overlay), CSS

## Key Files
- `index.html` — everything: SVG text paths, Hydra canvas, lightning, grunge filter, noise overlay, glitch/rotation animation, portrait letter sequence
- `minacce.svg` — source SVG with clean font paths
- `sfondo.png` — background image

## Architecture

### SVG Text ("MINACCE")
- 7 `<path>` elements inside `<g id="logo-paths">`, one per letter (M,I,N,A,C,C,E)
- Grunge effect via SVG filter `#noisy-text`: feTurbulence + feDisplacementMap (no feMorphology or feColorMatrix)
- Noise seed updates every 80ms

### Layers (z-index)
- z-index 2: Hydra canvas (`mix-blend-mode: screen`, opacity 0.3, `pointer-events: none`)
- z-index 3: Logo SVG (fullscreen, `opacity: 0.7`, `filter: drop-shadow`)
- z-index 4: Noise overlay canvas (`mix-blend-mode: multiply`, opacity 0.1, `pointer-events: none`)
- z-index 5: Lightning SVG (`mix-blend-mode: difference`, `pointer-events: none`)

### Animations
- **Breathing**: scale, rotate, translate oscillate via sin/cos in `animateLogo` (requestAnimationFrame)
- **Glitch on hover**: triggered via `triggerGlitch()` — applies invert, offset, scale bump, rotate bump for 0.1-0.3s. Activated by `mousemove` on `document` (2s idle timeout), suppressed during portrait sequence
- **Lightning**: 2-5 bolts every 1.2-2s, white lines with flicker, `mix-blend-mode: difference` creates black cuts on white text
- **Portrait letter sequence**: 7 letters appear one by one (1s each), glitch transitions, then full text for 6s, loop. Active when `innerHeight > innerWidth`

### Event Handling
- Hover: `document.addEventListener('mousemove', ...)` with 2s idle timeout (`hoverTimer`)
- Touch: `touchstart`/`touchend` on document
- Orientation: `resize` event via `checkOrientation()`, tracks `wasPortrait` to detect real changes

### CSS Conventions
- `position: fixed` with `top: 0; left: 0; width: 100vw; height: 100vh` for fullscreen layers
- Logo centered: `top: 50%; left: 50%; transform: translate(-50%, -50%)` (transform overridden by JS)
- SVG viewBox `0 0 1366 768`, `preserveAspectRatio="xMidYMid meet"`

## Portrait Mode
- Detected via `isPortrait()` (`innerHeight > innerWidth`)
- Hides `#logo-paths`, shows cloned paths in `#letter-sequence` scaled individually
- Scale: `Math.min((1366 * 0.55) / bbox.width, 7)` — caps narrow letters (I) from becoming too large
- After full text appears, 6s delay then loop restarts
- Rotation to landscape resets sequence

## Notes
- Do NOT add path jitter scripts — breaks bezier curves (C letters get holes)
- Do NOT use SVG masks for destruction — creates persistent black holes on curved shapes
- Lightning uses mix-blend-mode difference on white text to create visual "cuts"
- Noise overlay is a separate canvas with setTimeout loop (200ms interval), not SVG filter