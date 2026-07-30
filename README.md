# ForgeXR — Vocational Tools in WebXR

A WebXR prototype for **City of Wolverhampton College** vocational students. Pick a trade, explore the tools of that trade in 3D, tap hotspots to learn what each part does and watch a related video, then take a quick quiz to check your learning.

Built as a single self-contained page (`index.html`) using [Three.js](https://threejs.org/) and the WebXR Device API — no build step, no server-side code.

## Features

- **Choose a trade** — Plumbing (fully interactive) and Electrical Installation (mock-up preview), with room to add more.
- **3D tool viewer** — drag (or swipe) to rotate each tool; reset view at any time.
- **Always-visible parts menu** — every tool lists its learning points beside the model: what the tool is, each labelled part, how it's used in the trade, and a video. Selecting a point opens a popup and pulses the matching marker on the tool.
- **Numbered markers** — the markers on the 3D model are numbered to match the menu rows, so students can see *where* on the tool each point is without having to hit a small target.
- **Quick quiz** — a 3-question quiz per tool with instant feedback, a score screen, and retry.
- **Back navigation** — every screen is a real page (hash route), so the Back button, browser history and the phone/headset back gesture all step back through the app. Tools can be deep-linked.
- **VR headset support** — an **Enter VR** button appears on the tool viewer when a headset is available.

## Interaction design

The same content is presented through whichever input the device has, rather than forcing one interaction everywhere:

| | Browser / mobile | VR headset |
|---|---|---|
| Menu | HTML list beside (or below) the tool | 3D panel of large buttons floating beside the tool |
| Reading a point | HTML modal | world-space 3D card |
| Quiz | HTML modal | world-space 3D cards and answer buttons |
| Rotating the tool | drag / swipe | thumbstick left-right |

Selection never depends on pointing at a small marker: in VR each menu button subtends roughly 25–30° of the view, so it is comfortable to hit with a controller ray. The tool deliberately does **not** auto-rotate in VR, so nothing the student is aiming at moves on its own.

Videos play on the flat screen only — an embedded player can't be composited into a world-space panel — so in VR the video card explains to exit VR to watch, and the flat popup carries the player plus "watch on YouTube" fallback links.

## Viewing on a VR headset

WebXR requires **HTTPS**, so the easiest route is GitHub Pages:

1. In this repository go to **Settings → Pages**, set the source branch, and save.
2. Open the published `https://<user>.github.io/voctools/` link in the headset's browser (e.g. Meta Quest Browser).
3. Pick a trade and a tool, then press **Enter VR**.
4. Point a controller at the menu panel on the right and pull the trigger to open a point. Buttons grow slightly when your ray is on them. Push the thumbstick left or right to spin the tool.

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
