<div align="center">

# InkSpace

**Procreate, but the canvas has depth.**

Draw a line. Spin the canvas. Snap to a glowing corner. Draw again — the two lines meet in real 3D.

![single file](https://img.shields.io/badge/single-file-7c3aed?style=flat-square)
![three.js r160](https://img.shields.io/badge/three.js-r160-ff3d8e?style=flat-square)
![no build step](https://img.shields.io/badge/build-none-39d0ff?style=flat-square)
![WebGL2](https://img.shields.io/badge/renderer-WebGL2-c8f04a?style=flat-square)
![license](https://img.shields.io/badge/license-MIT-ffb02e?style=flat-square)

</div>

---

InkSpace is a sketching app that behaves like an ordinary drawing tool — brushes, layers, a colour wheel, undo — except every stroke lives in three-dimensional space. There are no meshes to model, no gizmos to learn, no axes to type numbers into. You draw on a clean sheet of white paper, turn the sheet, and keep drawing.

It is the drawing-first half of 3D creation: the part where you capture a shape by sketching it, not by operating software.

## Contents

- [The one idea](#the-one-idea)
- [Quick start](#quick-start)
- [Drawing a cube](#drawing-a-cube)
- [Features](#features)
- [Controls](#controls)
- [Brushes](#brushes)
- [Depth & snapping](#depth--snapping)
- [Layers & history](#layers--history)
- [Paper & colour](#paper--colour)
- [Saving & export](#saving--export)
- [Recording video](#recording-video)
- [Sound](#sound)
- [Architecture](#architecture)
- [Tech stack](#tech-stack)
- [Browser support](#browser-support)
- [Performance](#performance)
- [Tuning it](#tuning-it)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Credits & license](#credits--license)

---

## The one idea

A point on your screen is not a point in space — it is a ray. Every 3D drawing tool has to decide where along that ray the ink lands. Most answers make you operate the software: pick a plane, type a depth, drag a gizmo.

InkSpace answers with two rules you never have to think about:

1. **Ink lands on a sheet facing you.** Draw, spin, draw again, and your lines intersect in space.
2. **Existing ink is magnetic.** Hover a line and its corners glow. Start drawing there and the sheet snaps to that exact 3D point.

Rule 2 is what makes solid forms possible. Without it, the second face of a cube floats at the wrong depth. With it, faces meet.

## Quick start

No install, no bundler, no dependencies to fetch by hand.

```bash
git clone https://github.com/your-name/inkspace.git
cd inkspace
python -m http.server 8000
```

Open <http://localhost:8000>.

Opening `index.html` directly also works in Chrome, Edge and Firefox. A local server is recommended because some browsers restrict IndexedDB on `file://`, which disables autosave. three.js loads from jsDelivr, so the first load needs a network connection.

The canvas opens with a seeded sketch that draws itself in. Start drawing over it, or hit **New canvas** in the ⋯ menu.

## Drawing a cube

This is the workflow the whole app is built around.

```
1. Draw a square            → one closed stroke on the sheet facing you
2. Press →                  → camera spins exactly 90°
3. Hover a corner           → it glows magenta, labelled "corner"
4. Start drawing there      → the sheet now passes through that 3D point
5. Hold Shift and drag      → a straight edge locked to X, Y or Z
6. Repeat 2–5               → six faces, all joined
```

**Cube practice** in the dock runs step 1 for you and drops you at step 2.

Two details worth knowing:

- Corners are detected by curvature, so a hand-drawn square works — you do not need four separate strokes.
- Snapping also happens *mid-stroke*, so you can close a face onto a far corner and the ink will jump exactly onto it.

## Features

### Drawing
| | |
|---|---|
| Five brushes | Ink, Pencil, Marker, Airbrush, Neon — each with its own edge, grain and blending behaviour |
| Pressure | Pen pressure drives width, opacity, or both; independent toggles |
| Stabilisation | Speed-adaptive 1€ filter — kills jitter at low speed, adds no lag at high speed |
| Taper & grain | Per-stroke taper ramps and procedural paper grain, live |
| Vertical sliders | Procreate-style Size and Opacity rails pinned to the left edge |
| Eraser | Partial mode splits strokes at the centreline; whole-stroke mode removes them outright |
| Colour | HSV field, hue rail, hex entry, twelve swatches, recent colours, palette shuffle |

### Working in 3D
| | |
|---|---|
| Corner snapping | Corners and endpoints glow; drawing starts exactly there |
| Pinned sheet | Pin the drawing sheet to any point; `D` releases it back to the view centre |
| Axis-locked lines | `Shift`-drag pulls a straight edge along the nearest world axis, with live 3D axis guides |
| Depth push/pull | `Shift` + wheel moves the sheet toward or away from you |
| Spin deck | 90° spin left/right, 90° tilt up/down, eight snap views, animated transitions |
| Auto-spin | Continuous turntable with a speed dial; pauses while you draw or drag |
| Ghost sheet | The sheet fades in while you turn, so you always know where ink will land |
| Face helpers | **Insert face** drops a perfect square on the current sheet |

### Organising
| | |
|---|---|
| Layers | Add, duplicate, rename (double-click), reorder by drag or arrows, hide, per-layer opacity |
| History | One command stack across strokes, erasing and every layer operation — 120 steps |
| Undo after destructive actions | Clearing the canvas or deleting a layer is fully reversible |

### Output
| | |
|---|---|
| Autosave | Debounced writes to IndexedDB, plus named projects and a project picker |
| Project files | JSON export/import carrying strokes, layers, camera, pinned anchor and settings |
| PNG | Snapshot composited with the paper, colour washes and vignette |
| Video | Free-spin, 360° turntable or timelapse; 720p–4K, 30/60 fps, 4–40 Mbps, pause and stop |

### Feel
- Seeded artwork animates in stroke by stroke on first open.
- Brush scratch sound follows stroke velocity, mutable from the top bar.
- Pen, mouse and touch each get their own routing: pen draws, one finger spins, two fingers pan and zoom.
- Responsive down to phone width — the dock becomes a bottom sheet, the spin pad stays reachable.
- Scrollbars, toasts, popovers and dialogs all drawn in the app's own palette.

## Controls

### Pointer

| Input | Action |
|---|---|
| Left drag | Current tool |
| Pen | Always draws, regardless of tool |
| Right drag | Spin |
| Middle drag | Pan |
| Wheel | Zoom |
| `Shift` + wheel | Push/pull the sheet |
| `Shift` + drag | Straight axis-locked line |
| `Alt` + click | Pin the sheet to the hovered point |
| `Space` + drag | Temporary spin |
| One finger | Spin |
| Two fingers | Pan + zoom |
| *Finger draws* enabled | One finger draws instead |

### Keyboard

| Key | Action |
|---|---|
| `B` `E` `V` | Brush · Eraser · Spin tool |
| `E` twice | Toggle whole-stroke erasing |
| `1`–`5` | Ink · Pencil · Marker · Airbrush · Neon |
| `←` `→` | Spin 90° (`Shift` = 15°) |
| `↑` `↓` | Tilt 90° (`Shift` = 15°) |
| `[` `]` | Brush size |
| `D` | Release the pinned sheet |
| `F` | Frame the drawing |
| `P` | Auto-spin |
| `L` `R` `H` | Layers · Record · Help |
| `Ctrl/⌘ Z` | Undo |
| `Ctrl/⌘ ⇧ Z` / `Ctrl Y` | Redo |
| `Ctrl/⌘ S` | Save named project |

## Brushes

| Brush | Edge | Character |
|---|---|---|
| **Ink** | Crisp | Clean, confident line. The default. |
| **Pencil** | Soft, broken | Grain eats into the stroke; thinner and rougher. |
| **Marker** | Hard, blunt | Wide, semi-transparent, edges darken like real marker. Minimal taper. |
| **Airbrush** | Very soft | Broad falloff, low alpha, builds up in passes. |
| **Neon** | Glowing | Additive and blooming on Night paper; saturated and solid on White. |

Every brush shares the same pipeline: pressure → width and alpha curves → taper ramp → screen-space ribbon extrusion → soft-edge fragment shader.

## Depth & snapping

**The sheet.** A camera-facing plane through an anchor point. Unpinned, the anchor is the orbit target, so the sheet follows your view. Pinned, it is a fixed world point, so the sheet stays put while you turn.

**Snapping.** Existing strokes are searched in screen space, culled by their projected bounding sphere. Candidates are ranked: corners and endpoints first, then sampled interior points; ties break toward the camera. The winner is shown as a ring — magenta for a corner, cyan for a point.

**On snap**, three things happen at once: the stroke's first point becomes the exact 3D candidate, the sheet is re-anchored through it, and the depth offset resets to zero. The stabilisation filter is re-seeded to that point so smoothing does not drag the stroke off the corner.

**Corners** are found by comparing tangent directions across a window proportional to stroke length, keeping local maxima above ~30° with a minimum separation. Endpoints always qualify.

**Axis lock.** With `Shift` held, the vector from the stroke's start to the pointer is projected onto whichever world axis it aligns with most. Three guide lines appear through the start point; the active one lights up cyan.

## Layers & history

Layers are organisational and visual: visibility, opacity, ordering. Strokes are batched per *(layer, brush)* pair, so a layer's opacity is a single uniform change rather than a geometry rebuild.

History is a command stack, not snapshots. Commands are `add`, `remove`, `split`, `layerAdd`, `layerDel`, `layerMove`, `layerVis`, `layerOp`, `layerRename` and `compound`. An eraser gesture collapses into one compound command, so a single undo restores the whole pass rather than one point at a time. Commands hold references to immutable stroke records, which keeps memory flat.

## Paper & colour

Three papers: **White** (default), **Cream**, **Night**. Each carries procedural fibre grain, drifting colour washes and an edge vignette. Switching to Night flips the Neon brush to additive blending and adjusts the shader's highlight behaviour so strokes stay visible instead of washing out.

The default ink is a deep near-black, chosen for white paper. The palette is ink-first — graphite, crimson, magenta, amber, olive, teal, blue, violet, burnt orange — and **Shuffle** regenerates it harmonically from a random base hue, tuned for the current paper.

## Saving & export

Autosave writes 1.2 s after your last change, and again when the tab is hidden. Named projects live alongside the autosave and appear in the ⋯ menu picker.

Project files are plain JSON:

```jsonc
{
  "version": 4,
  "name": "Untitled",
  "strokes": [
    {
      "layer": "l3…", "brush": 0, "color": "#1b1626",
      "size": 18, "opacity": 100,
      "pts": [/* xyz… */], "w": [/* half-widths */], "a": [/* alphas */],
      "order": 12
    }
  ],
  "layers": [{ "id": "l3…", "name": "Cube", "visible": true, "opacity": 1 }],
  "activeLayerId": "l3…",
  "orderCounter": 42,
  "camera": { "p": [0, 14, 150], "t": [0, 0, 0] },
  "anchor": { "locked": true, "p": [20, -20, 0] },
  "settings": { /* brush, snap, paper, spin, toggles… */ }
}
```

Widths and alphas are stored baked, so a reloaded drawing is byte-identical to the one you saved — no re-derivation from settings.

**PNG** renders one frame, then composites the paper colour, washes and vignette onto a 2D canvas before drawing the WebGL buffer over it. What you download matches what you see.

## Recording video

Recording resizes the renderer to the target resolution *before* opening the capture stream, sets an opaque paper-coloured clear, then drives `captureStream(0)` with an explicit `requestFrame()` after every render — so every captured frame is a complete frame. Browsers without `requestFrame` fall back to a fixed-rate capture.

Container and codec are negotiated, never hardcoded: MP4/H.264 where available, then VP9, VP8, plain WebM.

| Mode | Behaviour |
|---|---|
| **Free spin** | You navigate; drawing stays live |
| **360° turntable** | One full orbit around the current centre, then auto-stops |
| **Timelapse** | Strokes reveal in creation order via a shader progress uniform; you can still navigate |

Pause, resume and stop are on the recording overlay. Resolution, frame rate, bitrate and duration are all adjustable before you start.

## Sound

A single WebAudio voice: looping noise through a band-pass filter, with gain driven by stroke velocity. It reads as a nib on paper, and it only sounds while ink is actually moving. On by default; the speaker button in the top bar mutes everything in one action and shows the current state. The context is created on your first stroke, so nothing plays before you interact.

## Architecture

One file, clearly sectioned. No framework, no state library, no build.

```
index.html
├── <style>            palette, paper, chrome, dock, popovers, responsive
├── <body>             top bar · paper · canvas · HUD · rails · dock · spin pad · dialogs
└── <script module>
    ├── utils          colour conversion, toast, download, seeded RNG
    ├── state          single mutable settings object
    ├── three          renderer, camera, OrbitControls, shared uniforms
    ├── shaders        ribbon vertex/fragment, ghost sheet
    ├── geometry       ribbon builder, corner detection, chunk batching
    ├── history        command apply / do / undo / redo
    ├── filters        1€ filter, Ramer–Douglas–Peucker
    ├── depth          sheet anchor, ray sampling, snap search, axis lock
    ├── strokes        width/alpha curves, live preview, commit pipeline
    ├── eraser         screen-space centreline hit, split / remove
    ├── camera         spherical tweens, 90° steps, snap views, auto-spin
    ├── input          capture-phase pointer router, keyboard
    ├── record         MediaRecorder lifecycle, turntable, timelapse
    ├── persistence    IndexedDB, serialize / deserialize, PNG
    ├── ui             layers, popovers, brushes, colour, rails
    ├── audio          velocity-driven scratch voice
    ├── seed           opening artwork
    └── loop           one requestAnimationFrame driving everything
```

**Stroke record.** The polyline is the source of truth; geometry is derived and regenerable. That is what makes partial erasing and re-styling possible.

**Ribbon rendering.** Two vertices per point. The vertex shader projects the centre and a tangent, then offsets perpendicular in screen space by a world-space half-width converted to pixels — so strokes attenuate with distance but always face the camera. The fragment shader handles soft edges, grain, marker edge-darkening, airbrush falloff, neon bloom, and a per-stroke reveal used by both the entrance animation and timelapse.

**Batching.** Strokes merge into one mesh per *(layer, brush)*. Materials are cached against the same key and share global uniform objects, so draw calls stay near *layers × brushes used* rather than one per stroke. Depth testing is off and render order follows layer order, which suits ink and avoids transparent sorting artefacts.

**One loop.** Controls, camera tweens, auto-spin, recording animations, ghost fade, overlay projection, render and capture all run from a single frame callback. Nothing spawns a second loop.

## Tech stack

| Piece | Why |
|---|---|
| **three.js r160** | Mature WebGL2 renderer, curves, raycasting, controls. Loaded via import map from jsDelivr. |
| **OrbitControls** | Damped orbit/pan/zoom with per-button routing, so drawing and navigation coexist. |
| **Custom ribbon shader** | Per-point width, taper, grain and reveal — none of which `Line2` or `TubeGeometry` give together. |
| **1€ filter** | Casiez–Roussel–Vogel adaptive low-pass; the right answer for interactive pointer noise. |
| **Centripetal Catmull-Rom** | Resampling without cusps or self-intersections at sharp turns. |
| **Pointer Events** | One path for mouse, pen and touch, with pressure and coalesced samples. |
| **IndexedDB** | Project-sized data with typed arrays, no localStorage ceiling. |
| **MediaRecorder + captureStream** | Baseline-wide viewport capture with explicit frame pacing. |
| **WebAudio** | One filtered noise voice; no assets to load. |

No React, no bundler, no dependency tree. The whole app is readable in one sitting.

## Browser support

| Browser | Drawing | Pressure | Autosave | Recording |
|---|---|---|---|---|
| Chrome / Edge | ✅ | ✅ | ✅ | WebM, MP4 on recent versions |
| Firefox | ✅ | ✅ | ✅ | WebM |
| Safari 18.2+ | ✅ | ✅ | ✅ | MP4 / H.264 |
| Safari 14.1–18.1 | ✅ | ✅ | ✅ | MP4; coarser strokes without coalesced events |
| iPadOS + Apple Pencil | ✅ | ✅ | ✅ | MP4 |
| Android Chrome | ✅ | device-dependent | ✅ | WebM |

Requires WebGL2. `getCoalescedEvents()` is used when present and silently skipped when not.

## Performance

- Pixel ratio capped at 2; no resolution or effect is dropped while you drag.
- Geometry is rebuilt only for the affected batch, and only on commit or erase — never per frame.
- The live stroke is a single small mesh, disposed and replaced as it grows.
- Snap search culls by projected bounding sphere, strides long polylines, and falls back to corners-only past 350 strokes.
- Eraser hit-testing is throttled to ~30 Hz and uses the same sphere cull.
- Materials and shared uniforms are reused; chunks and GPU buffers are disposed when emptied.
- A few thousand strokes stay comfortably at 60 fps on a GTX 1060-class GPU.

## Tuning it

Everything interesting is a named constant near the top of the module.

| What | Where |
|---|---|
| Brush character | `BRUSHES[]` — `soft`, `floor`, `mul`, `add` |
| Size → world width | `halfWidth()` (`size * 0.06`) |
| Depth range | `planeOffset()` (`depth * 0.6`) |
| Snap feel | `state.snapRadius`, `detectCorners()` angle threshold |
| Stabilisation curve | `makeFilter()` cutoff and beta ranges |
| Camera | field of view `45`, `minDistance 30`, `maxDistance 700` |
| Ghost sheet size | `PlaneGeometry(150, 110)` |
| Default palette | `palette[]` |
| Opening artwork | `seed()` |
| History depth | `undoStack` cap (`120`) |
| Autosave delay | `scheduleSave()` (`1200 ms`) |

## Troubleshooting

**Blank canvas.** three.js loads from a CDN — check the network tab. The console will show a failed import if jsDelivr is unreachable.

**Autosave does nothing.** Some browsers block IndexedDB on `file://`. Serve the folder over HTTP. JSON export always works.

**Video comes out `.webm`, not `.mp4`.** Expected on Chrome and Firefox. The container is negotiated per browser; Safari records MP4.

**Eraser seems not to erase.** Partial mode removes the stroke's centreline, not its silhouette. Sweep across the middle of the line, or press `E` twice for whole-stroke mode.

**Strokes vanished after switching to Night.** Your ink is near-black. Pick a light swatch — the app warns you when this happens.

**No pressure response.** Confirm *Pen pressure → size* is on and that your device reports pressure. Mouse input is treated as constant pressure by design.

**New strokes land at the wrong depth.** The sheet is probably pinned somewhere behind your subject. Press `D` to release it, or hover a corner and start there.

## Roadmap

- [ ] Feather-style guide bending — warp a sheet from a second viewpoint
- [ ] Stroke selection and transform (move, rotate, scale in view space)
- [ ] BVH-accelerated erasing for very large drawings
- [ ] Frame-exact MP4 export via WebCodecs
- [ ] Orthographic camera mode for clean technical sketching
- [ ] Mirror / symmetry plane
- [ ] Editable pressure and taper curves
- [ ] Visual history panel
- [ ] Offline PWA with a bundled three.js

## Contributing

Issues and pull requests are welcome. The bar is the drawing feel: if a change makes the app more like a 3D editor and less like a sketchbook, it is probably the wrong change.

Keep it a single file. Keep the hot path out of the DOM. Add no dependency without arguing for it in the PR. Test on a pen device if you touch input, and on Safari if you touch recording.

## Credits & license

Built on [three.js](https://threejs.org) (MIT).

Stabilisation uses the **1€ filter** — Casiez, Roussel & Vogel, *CHI 2012*.

The snap-to-existing-ink approach is informed by the public research behind **Feather** (Kim, Hong & Yang, *SIGGRAPH 2023 Appy Hour*), which introduced extruded 3D guides for multi-view sketching. InkSpace is an independent implementation: no affiliation with Sketchsoft, and no assets, code or branding taken from Feather, Procreate or Autodesk Sketchbook. Those apps informed the interaction language, nothing more.

Released under the **MIT License**. Add a `LICENSE` file alongside this README.

<div align="center">

---

*Draw. Spin. Snap. Draw again.*

</div>
