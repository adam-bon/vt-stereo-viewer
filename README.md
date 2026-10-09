# Stereo Viewer (Vision Therapy)

A digital vectogram: one quoit per eye on a shared card, slid apart for base-in or base-out, with per-eye prism, rotation and blur. Built for XR display glasses in full side-by-side 3D mode (developed on the VITURE Beast), with a red/green anaglyph mode for ordinary screens.

Live: [https://adam-bon.github.io/vt-stereo-viewer/](https://adam-bon.github.io/vt-stereo-viewer/)

## What it is for

The vectogram phase that [vt-contour-target](https://github.com/adam-bon/vt-contour-target) leaves out. Each eye sees its own image of a target on the same card. Sliding the images apart adds base-out (the target floats towards you) or base-in (it sinks behind the card), as sliding the films of a printed Quoits vectogram does. Unlike a printed card, each target can be moved independently, and the two eyes' pictures can be shifted, rotated and blurred separately.

Design consequences:

- Disparity is the only depth cue. Both eyes get the same flat picture apart from the horizontal offset: no perspective, shading or motion parallax to fall back on. Quoits stay the same size at every depth, so a floated ring looks smaller (SILO) only if the depth is coming from stereo.
- Everything that should sit on the card (card, contours, centre dot, the marker for the selected quoit) is identical in both eyes, so it stays at zero disparity.
- L / R suppression boxes are on by default. Each eye sees only its own letter; a letter dropping out means that eye is suppressing.
- Prism and rotation move an eye's whole picture, like a lens, not just the target. They are meant to be set and then left alone during an exercise.
- The screen size and distance are set on the glasses, not here, so separations are reported in pixels rather than prism dioptres.

## Keyboard

| Key | Action |
|---|---|
| `←` / `→` | Base in / out for the selected quoit (`Shift` for steps of 10) |
| `0` | Align the selected quoit (no demand) |
| `1` / `2` / `3` | Select which quoit the controls move (selecting 3 shows all three) |
| `↑` / `↓` | Right eye vertical prism (`Shift` for steps of 10) |
| `[` / `]` | Previous / next gallery image |
| `D` | Toggle centre dot |
| `C` | Toggle contour background |
| `M` | Switch side by side / anaglyph |
| `X` | Swap eyes (if base-out looks sunken) |
| `H` | Hide / show the control panel |
| `F` | Toggle fullscreen |

The disparity slider along the bottom is drawn once per eye so it fuses into a single slider; it, the panel slider and the arrow keys all move the same value.

## Panel

| Section | Controls |
|---|---|
| Mode / Image | Side by side or anaglyph; gallery image; file picker for extra images (this session only) |
| Exercise | Number of quoits (1–3), which one is adjusted, base in / out (−300 to +300 % of the ring radius), ring size, edge softness, blend gamma, L / R boxes, centre dot, contour background |
| Blur | Left and right eye blur, 0–20 px (side by side only) |
| Prism | Left and right eye horizontal and vertical shift (±150 px) and rotation (±10°) |
| Display | Swap eyes (side by side) |
| Colours | Card and both rings, RGB or hex, plus red-channel subpixel shift (anaglyph only) |

**Copy values** puts the current settings on the clipboard for logging. **Reset to defaults** restores everything except the mode.

## Defaults

Side by side, one quoit at base-out 80 %, ring size 30 %, hard edges (softness 0), gamma 2.2, L / R boxes on, centre dot and contours off, no prism, rotation or blur.

Anaglyph colours, for red/green glasses with the red lens on the right eye: card `#fff0f0`, right eye ring `#00f0f0` (cyan, for the red lens), left eye ring `#ff0000` (red, for the green lens). Tune them per pair of glasses: through the red lens alone the cyan ring should be black and the red ring should vanish, and the reverse through the green lens.

## Images

Drop files into `images/` and reload. There is no list to edit.

- `name_left.png` and `name_right.png` (or `_L` / `_R`) become one gallery entry. A single file is shown to both eyes.
- A leading number sets the order and is hidden from the name: `2-Crystal_left.png` shows as "Crystal". Hyphens show as spaces.
- Cut-outs (transparent background, such as the quoit) are scaled so their width matches the ring size. Opaque pictures (photos, rendered stereo scenes) are treated as scenes and fill most of each eye's height. Ring size scales both.
- To split a side-by-side stereo image into a pair, crop both halves with exactly the same box, or the depth changes. Parallel-view pairs have the left eye's image on the left; for cross-view pairs, swap the names.
- PNG, JPEG, SVG, WebP and GIF are supported.

Shipped: Quoit (rope ring, both eyes), Crystal, Grass valley and Tube (rendered stereo pairs).

The folder is found from the host: on GitHub Pages through the GitHub contents API (the repo must be public; one request per page load, 60 per hour per IP), on a local server through its directory listing, and otherwise from `images/manifest.js`, which then has to be kept up to date.

## Session usage

1. Glasses in 3D mode, so the computer sees one wide display (3840×1200 on the Beast at 1200p). Open the site and press `F`, then `H` to hide the panel, which otherwise shows in one eye only.
2. Start aligned (`0`) and check that both L and R are visible and the ring is single. Set any prism or rotation now, with the centre dot on if it helps, then leave it.
3. Add base-out or base-in in small steps with the arrow keys, holding a single ring with both letters visible. Note SILO and whether the ring floats or sinks.
4. With the centre dot on and a floated ring fused, the dot should double horizontally only. A vertical gap between its two images is a binocular finding, not the picture. Log it with **Copy values**.
5. Two or three quoits at different settings let fixation jump between planes.

Prism settings, demands and what the readings mean are for your vision therapist to decide.

## Technical notes

- One file, `sbs.html`, inline CSS and JS. No build step, no dependencies. `index.html` only redirects to it.
- Rings and the anaglyph composite are drawn pixel by pixel in linear light, so edges and crossings stay balanced. Each side-by-side eye is drawn into its own canvas, then placed with that eye's shift, rotation and blur.
- Full side by side only: each half of the screen is one eye at full resolution. The Beast does not accept half side by side.
- Needs a current browser (Chromium or Firefox). Per-eye blur uses canvas filters.
- Anaglyph mode reads image pixels, which browsers block for files opened from disk (`file://`). Serve it over http(s); side by side works either way.
- Settings are not saved between sessions.
- Run locally with `python3 -m http.server 8000` and open `http://localhost:8000/sbs.html`.

## Deployment

Repo root contains `index.html`, `sbs.html`, `images/`, `.nojekyll` and this file. GitHub → Settings → Pages → Deploy from a branch → `main` / root. Pushes redeploy automatically within about a minute.

## Out of scope

Not a medical device. Parked ideas: an on-screen Brock string (removed as unproven), sweep and jump vergence modes, a random-dot quoit, per-eye contrast, a near-threshold depth task, URL presets and saved settings, and a VR mode.
