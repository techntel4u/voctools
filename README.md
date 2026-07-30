# ForgeXR — Vocational Tools in WebXR

A WebXR prototype for **City of Wolverhampton College** vocational students. Pick a trade, explore the tools of that trade in 3D, tap hotspots to learn what each part does and watch a related video, then take a quick quiz to check your learning.

Built as a single self-contained page (`index.html`) using [Three.js](https://threejs.org/) and the WebXR Device API — no build step, no server-side code.

## Features

- **Choose a trade** — Plumbing (fully interactive) and Electrical Installation (mock-up preview), with room to add more.
- **3D tool viewer** — drag to rotate each tool; reset view at any time.
- **Hotspots** — glowing markers on each tool open popups explaining the part, how it's used in the trade, and a related video (with a "watch on YouTube" fallback link).
- **Quick quiz** — a 3-question quiz per tool with instant feedback, a score screen, and retry.
- **Back navigation** — breadcrumb and Back button return you through tools → trades → start at every step.
- **VR headset support** — an **Enter VR** button appears on the tool viewer when a headset is available. The scene is raised to standing eye height in VR, the controller gets an aiming ray for selecting hotspots, and popups/quiz render inside the headset via the WebXR `dom-overlay` feature.

## Viewing on a VR headset

WebXR requires **HTTPS**, so the easiest route is GitHub Pages:

1. In this repository go to **Settings → Pages**, set the source branch, and save.
2. Open the published `https://<user>.github.io/voctools/` link in the headset's browser (e.g. Meta Quest Browser).
3. Pick a trade and a tool, then press **Enter VR**.

Without a headset the app works as a normal 3D web page on desktop, tablet and phone.

## Running locally

Serve the folder with any static server (opening the file directly won't work because of ES module imports):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Prototype notes

- The 3D models are stylised procedural stand-ins built in code; swap in scanned/authored GLB models for production.
- The embedded YouTube video IDs are placeholders — review and replace them with college-approved content before use with students.
- Branding (purple/gold palette, logo) follows the City of Wolverhampton College website; the logo is hot-linked from wolvcoll.ac.uk and hides itself gracefully if unavailable.
- The Electrical Installation trade is a mock-up to show how additional trades slot into the same data structure (see `PROFESSIONS` in `index.html`).
