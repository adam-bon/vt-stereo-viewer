# Vectogram viewer

A browser-based stereo viewer for binocular vision exercises, built for XR display glasses in full side-by-side 3D mode (developed on the VITURE Beast) with a red/green anaglyph fallback for ordinary screens.

Each eye sees its own image of a target on the same card. Sliding the two images apart adds base-out (the target floats towards you) or base-in (it sinks behind the card), like sliding the films of a printed vectogram. Both eyes get the same flat picture apart from that horizontal offset, so the only depth cue is binocular disparity: no perspective, shading or motion parallax to fall back on.

> This is a personal training tool, not a medical device. Use it alongside, not instead of, a vision therapist, and run any programme, prism settings and progress readings past them.

## Files

| File | What it is |
| --- | --- |
| `sbs.html` | The viewer. Side-by-side and anaglyph modes, gallery, all controls. |
| `index.html` | Redirects the site's address to `sbs.html`. |
| `images/` | Gallery pictures, picked up automatically on load. |
| `images/manifest.js` | Fallback list of the images, only used where the folder can't be listed. |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are. |

## Using it

**On XR glasses (side by side):** put the glasses in 3D / SBS mode so the computer sees one wide display (3840×1200 on the Beast at 1200p), open the viewer, press `F` for fullscreen and `H` to hide the control panel. Screen size and distance are set on the glasses; the viewer reports separations in pixels.

**On a normal screen (anaglyph):** switch mode with `M` and wear red/green glasses, red lens on the right eye. The colours for the card and both eyes are adjustable in the panel; tune them per pair of glasses using the tuning notes in the panel.

### Controls

| Key | Action |
| --- | --- |
| `←` `→` | Base in / out for the selected quoit (`Shift` for bigger steps) |
| `0` | Align (no demand) |
| `1` `2` `3` | Select which quoit the controls move (pressing `3` shows all three) |
| `↑` `↓` | Right eye vertical prism |
| `[` `]` | Previous / next gallery image |
| `D` | Centre dot on the card plane |
| `C` | Contour background |
| `M` | Switch side by side / anaglyph |
| `X` | Swap eyes (if base-out looks sunken) |
| `H` | Hide / show the panel |
| `F` | Fullscreen |

The disparity slider along the bottom is drawn once per eye so it fuses into a single slider; it, the panel slider and the arrow keys all move the same value.

### Panel features

- **Up to three quoits**, stacked, each with its own base in / out, so planes can be held, compared and fixation can jump between them.
- **Prism per eye:** horizontal and vertical shift and rotation (torsion) of that eye's whole picture, like prism in the lens. Set once, then leave alone during exercises.
- **Blur per eye** (side by side): softens one eye's whole picture, like a blur foil.
- **L / R suppression boxes:** each eye sees only its own letter; a letter dropping out means that eye is suppressing.
- **Centre dot:** identical in both eyes, so it stays on the card plane. With a floated ring fused, the dot doubles horizontally as expected; any vertical gap between its two images is a binocular finding rather than the picture, which makes it a useful readout to log over time.
- **Contour background:** faint irregular contour lines on the card, identical in both eyes, to give the card plane texture to fuse. Irregular on purpose: a regular grid can be fused at the wrong repeat.
- **Ring size, edge softness, blend gamma**, and in anaglyph mode the colours and a subpixel shift.
- **Reset to defaults** restores everything except the mode.

## Adding images

Drop files into `images/` and reload; there is no list to edit.

- A pair named `name_left.png` and `name_right.png` (or `_L` / `_R`) becomes one gallery entry. A single file is shown to both eyes.
- A leading number sets the order and is hidden from the displayed name: `2-Crystal_left.png` shows as "Crystal". Hyphens show as spaces.
- Pictures with a transparent background (cut-outs such as the quoit) are scaled so their width matches the ring size. Pictures with no transparency (photos, rendered stereo scenes) are treated as scenes and fill most of each eye's height. The ring-size slider scales both.
- Splitting a side-by-side stereo image into a pair: crop both halves with exactly the same box, or you change the depth. Parallel-view pairs have the left eye's image on the left; for cross-view pairs, swap the names.
- Supported formats: PNG, JPEG, SVG, WebP, GIF.

The file picker in the panel adds images for the current session only.

### How the folder is found

A browser can't list a folder by itself, so the viewer asks the host:

1. On GitHub Pages (`*.github.io`), the GitHub contents API for the repo's `images/` folder. The repo must be public. This makes one API call per page load; GitHub allows 60 per hour per IP.
2. On a local server that lists directories (for example `python3 -m http.server`), the folder's listing.
3. Otherwise `images/manifest.js`. Keep it up to date if you host somewhere else, such as a custom domain.

Opening `sbs.html` straight from disk (`file://`) works for side by side, but anaglyph mode can't read image pixels there; serve it over http(s).

## Deploying to GitHub Pages

1. Create a **public** repository on GitHub, for example `vectograms`.
2. From this folder:
   ```
   git init -b main
   git add .
   git commit -m "Vectogram viewer"
   git remote add origin https://github.com/YOUR-USERNAME/vectograms.git
   git push -u origin main
   ```
3. In the repo: **Settings → Pages → Build and deployment**, Source "Deploy from a branch", branch `main`, folder `/ (root)`, Save.
4. When the "pages build and deployment" run in the **Actions** tab goes green, the viewer is at
   `https://YOUR-USERNAME.github.io/vectograms/` (which opens `sbs.html`)

To add images later: copy them into `images/`, then `git add images`, commit, push, and reload once the Pages run has finished.


## Running locally

```
python3 -m http.server 8000
```
then open `http://localhost:8000/sbs.html`. The images folder is found from the server's directory listing.

## Notes and limits

- XR glasses focus at a fixed distance wherever the target appears in depth, so large demands carry a vergence–accommodation mismatch that real cards and strings don't have.
- Optics and fit matter: if the glasses sit off-centre, the two eyes can see slightly different distortion. Check a finding with a second person on the same glasses before reading it as your own.
- Parked ideas: an on-screen Brock string (removed for now as unproven), sweep and jump vergence modes, a random-dot quoit, per-eye contrast, a near-threshold depth task, and a log button for prism and dot readings.
