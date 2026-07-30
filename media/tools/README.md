# Tool photographs

The viewer shows a **photograph of the real tool** with numbered hotspots pinned to points on the image. A photo is far truer to the tool than a stylised 3D stand-in, which is why it is the preferred option.

## Current state

| Tool | Photo | Notes |
|---|---|---|
| Pipe Wrench | ✅ `pipe-wrench.jpg` | Real photo with hotspots placed on the image |
| Adjustable Spanner | ❌ | Falls back to the 3D stand-in |
| Plunger | ❌ | Falls back to the 3D stand-in |
| Wire Strippers, Multimeter, Insulated Screwdriver | ❌ | Falls back to the 3D stand-in |

Tools with no photo keep working — they simply render the procedural 3D model as before. Adding a photo switches that tool to photo mode automatically.

## Adding a photo

1. Put the image in this folder, e.g. `media/tools/adjustable-spanner.jpg`.
2. In `index.html`, add a `photos` array to that tool and `x`/`y` to each hotspot:

```js
{
  id: "adjustable-spanner",
  name: "Adjustable Spanner",
  photos: [
    { src: "media/tools/adjustable-spanner.jpg", w: 1200, h: 900, label: "Side view" }
  ],
  hotspots: [
    // x and y are PERCENTAGES of the image: x from the left, y from the top.
    { key: "jaw", label: "Jaws", x: 30, y: 22, body: "..." },
    ...
  ]
}
```

`w`/`h` are the image's pixel dimensions. They are only used to get the aspect right on the first frame — the real dimensions are read from the file once it loads and the layout corrects itself, so an approximate value is harmless.

## Finding the x/y percentages

Open the image in any editor that shows cursor position, hover over the part, and convert to a percentage:

```
x = (pixels from left  ÷ image width)  × 100
y = (pixels from top   ÷ image height) × 100
```

Percentages (rather than pixels) mean the hotspots stay correct at every screen size and in the headset.

## Choosing images

- **Plain, uncluttered background.** The photo is shown against a dark stage; a white or light background works well.
- **Whole tool in frame** with a little margin, so markers near the edges are not cropped.
- **Portrait or landscape both fine** — the viewer follows the image's aspect ratio.
- **Roughly 800–1600 px on the long edge.** Larger files slow the first load with no visible benefit at this size.
- **Rights matter.** Use College-owned photographs, or images you have a licence for. Manufacturer product shots usually need permission.

## Multiple views

The `photos` array can hold several views of the same tool (front, side, close-up). Only the first is shown today; the array exists so a view switcher can be added without changing the data format. Hotspot `x`/`y` refer to the first photo.

## Why not 3D models?

The original prototype used procedural 3D models so the tool could be rotated. They were recognisable but not accurate representations of real tools. Photographs are accurate, which matters more for teaching tool identification. If you later want true 3D, the right route is scanned or authored **GLB models** of the actual tools rather than code-drawn approximations — the viewer's 3D path is still in place and could load those.
