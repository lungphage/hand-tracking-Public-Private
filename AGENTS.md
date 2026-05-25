# AGENTS.md

## What this is

Static HTML + MediaPipe Hands project. No build step, no package manager, no tests. All logic lives in inline `<script>` blocks.

## Running

Must use a local server — `file://` breaks camera access and CDN loading:

```bash
python -m http.server 8080
# open http://localhost:8080/index.html
```

## File variants

| File | Mode | Model source |
|------|------|--------------|
| `index.html` | basic draw | local `mediapipe/` |
| `full-version.html` | full (draw/select/erase/undo) | local `mediapipe/` |
| `index-cdn.html` | basic draw | jsdelivr CDN |
| `full-version-cdn.html` | full | jsdelivr CDN |

CDN variants require internet. Local variants require the `mediapipe/` directory with all 10 files (JS, WASM, tflite, binarypb, data).

## Canvas architecture (critical)

Two stacked canvases — this is the core design:

- **overlay** — hand skeleton + cursor. Cleared every frame via `overlayCtx.clearRect()`.
- **draw** — persistent drawings. Must NOT be cleared each frame.

Setting `canvas.width` clears it. The code saves/restores draw canvas content with `getImageData`/`putImageData` around any resize. If you clear draw canvas accidentally, all drawings vanish.

Both canvases need `transform: scaleX(-1)` in CSS to match the mirrored video (`<video>` also has `scaleX(-1)`). Text drawn on mirrored canvases appears backwards — use `ctx.save(); ctx.scale(-1, 1); ... ctx.restore()` to un-mirror text.

## MediaPipe loading

Scripts loaded via `<script src>` tags (not ES modules). The `Hands` constructor `locateFile` callback must return the correct path:

- Local: `'mediapipe/' + f`
- CDN: `'https://cdn.jsdelivr.net/npm/@mediapipe/hands@0.4.1675469240/' + f`

The CDN version of `hands.js` tries to load model files from the same origin as the script. The local version loads from `mediapipe/` which must contain: `hands.binarypb`, `hands_solution_packed_assets.data`, `hands_solution_simd_wasm_bin.wasm`, `hand_landmark_full.tflite`, etc.

## Hand detection

Landmark indices: `8`=index_tip, `6`=index_pip, `12`=middle_tip, `10`=middle_pip, `16`=ring_tip, `14`=ring_pip, `20`=pinky_tip, `18`=pinky_pip.

Gesture detection compares tip.y < pip.y (lower y = finger extended in screen coords). Canvas Y is inverted from MediaPipe normalized coords.

`maxNumHands: 2` is required for the two-hand selection box feature. The selection box uses index fingertips (landmark 8) from both detected hands as opposite corners.

## Button handlers

The working version uses inline `onclick="fn()"` or `.onclick = function(){}`. Functions must be in global scope (or attached to `window`). The IIFE-wrapped version in `full-version.html` assigns handlers via `.onclick` inside the closure.

## Common pitfalls

- Camera permission loop: usually means loading via `file://` instead of localhost
- Hand not detected: check lighting, distance (30-50cm), hand facing camera
- Canvas not drawing: verify you're not clearing the draw canvas each frame
- Mirrored text: use save/scale(-1,1)/restore when drawing text on mirrored canvas
- Buttons not working: check for JS errors that prevent function definitions; verify onclick handlers are wired up
