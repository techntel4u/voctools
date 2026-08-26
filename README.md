# ForgeXR — Vocational Tools in WebXR

A WebXR prototype for **City of Wolverhampton College** vocational students. Pick a trade, explore the tools of that trade in 3D, tap hotspots to learn what each part does and watch a related video, then take a quick quiz to check your learning.

Built as static pages using [Three.js](https://threejs.org/) and the WebXR Device API — no build step, no server-side code.

| File | What it is |
|---|---|
| `index.html` | The student app |
| `admin.html` | The admin portal for managing content — see [ADMIN.md](ADMIN.md) |
| `content.json` | All trades, tools, parts, videos and quizzes. Edited by the portal, versioned by git |
| `media/` | Tool photographs and video clips |

## Features

- **Choose a trade** — Plumbing (fully interactive) and Electrical Installation (mock-up preview), with room to add more.
- **Photo of the real tool** — the viewer shows a photograph with numbered hotspots pinned to points on the image, which is truer to the tool than a stylised 3D stand-in. Tools without a photo fall back to the procedural 3D model, which can be rotated by dragging. See [media/tools/README.md](media/tools/README.md) for adding photos.
- **Always-visible parts menu** — every tool lists its learning points beside the image: what the tool is, each labelled part, how it's used in the trade, and a video. Selecting a point opens a popup and pulses the matching marker.
- **Numbered markers** — markers are numbered to match the menu rows, so students can see *where* on the tool each point is without having to hit a small target. They can also be tapped directly.
- **Quick quiz** — a 3-question quiz per tool with instant feedback, a score screen, and retry.
- **Back navigation** — every screen is a real page (hash route), so the Back button, browser history and the phone/headset back gesture all step back through the app. Tools can be deep-linked.
- **VR headset support** — an **Enter VR** button appears on the tool viewer when a headset is available.
- **Admin portal** — add and edit trades, tools, parts, photos, videos and quizzes at `admin.html`, then publish. Every publish is a git commit, so nothing is lost and any change can be rolled back. Full guide: [ADMIN.md](ADMIN.md).

## Interaction design

The same content is presented through whichever input the device has, rather than forcing one interaction everywhere:

| | Browser / mobile | VR headset |
|---|---|---|
| Menu | HTML list beside (or below) the tool | 3D panel of large buttons floating to the right of the tool |
| Reading a point | HTML modal | world-space 3D card, **directly over the tool** so no head-turning is needed |
| Quiz | HTML modal | world-space 3D cards and answer buttons |
| Video | YouTube embed in the modal | video file painted onto an in-world screen, with play/pause and restart |
| Changing tool or trade | Back button / breadcrumb | **Change tool or trade** button at the top of the VR menu |
| Rotating the tool (3D fallback only) | drag / swipe | thumbstick left-right |

### Comfort in VR

Everything is placed in degrees of the field of view rather than arbitrary units, and the constants are grouped together in `index.html` so they are easy to retune after trying the app in a headset:

| Constant | Value | Effect |
|---|---|---|
| `VR_DISTANCE` | 2.0 m | How far away the tool stands |
| `VR_EYE_HEIGHT` | 1.5 m | Height of the stage above the floor |
| `VR_WORLD_SCALE` | 0.7 | Tool ends up ~30° tall — present but not looming |
| `VR_UI_SCALE` | 1.6 | Panels enlarged so text stays legible at that distance |
| `VR_MENU_STEP`, `VR_MENU_BTN_H` | 0.108, 0.098 | Menu row pitch — keeps a long list within a comfortable vertical sweep |

An earlier version put the stage 0.85 m from the viewer, which was uncomfortably close: objects that near force the eyes to converge hard and the tool filled almost the whole view.

Selection never depends on pointing at a small marker: in VR each menu button subtends roughly 25–30° of the view, so it is comfortable to hit with a controller ray. The tool deliberately does **not** auto-rotate in VR, so nothing the student is aiming at moves on its own.

A student in the headset never has to take it off: the VR menu's **Change tool or trade** button opens an in-world list of the trade's tools, and from there a list of trades, so tools can be switched without leaving the session. Videos play on an in-world screen too — see [media/README.md](media/README.md) for why the headset uses a video file while flat screens use the YouTube embed, and how to swap in College footage.

## Viewing on a VR headset

WebXR requires **HTTPS**, so the easiest route is GitHub Pages:

1. In this repository go to **Settings → Pages**, set the source branch, and save.
2. Open the published `https://<user>.github.io/voctools/` link in the headset's browser (e.g. Meta Quest Browser).
3. Pick a trade and a tool, then press **Enter VR**.
4. Point a controller at the menu panel on the right and pull the trigger to open a point — the card appears over the tool, straight ahead. Buttons grow slightly when your ray is on them. Push the thumbstick left or right to spin the tool.
5. **Change tool or trade** at the top of the menu switches tools without leaving VR. **Watch it in action** plays the clip on a screen in front of you.

Without a headset the app works as a normal 3D web page on desktop, tablet and phone.

## Running locally

Serve the folder with any static server (opening the file directly won't work because of ES module imports):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Prototype notes

- Only the pipe wrench has a real photograph so far. The other five tools still render the stylised procedural 3D stand-ins — add photos per [media/tools/README.md](media/tools/README.md) to switch them over.
- The embedded YouTube video IDs are placeholders — review and replace them with college-approved content before use with students. The same applies to `media/sample-tool-clip.*`, the generated placeholder the headset plays.
- Branding (purple/gold palette, logo) follows the City of Wolverhampton College website; the logo is hot-linked from wolvcoll.ac.uk and hides itself gracefully if unavailable.
- The Electrical Installation trade is a mock-up to show how additional trades slot into the same data structure (see `PROFESSIONS` in `index.html`).
