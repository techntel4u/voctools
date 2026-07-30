# Video clips

## Why there are two video sources

| Where | Source | Why |
|---|---|---|
| Browser / mobile popup | YouTube embed (`video:` — a YouTube ID) | Full player, captions, no hosting needed |
| Inside a VR headset | video **file** in this folder (`videoFile:`) | A YouTube iframe cannot be drawn onto a surface in a 3D scene, so the clip must be a real file the app can paint onto an in-world screen |

This is why the headset plays a file rather than the YouTube clip — it is a WebXR limitation, not a preference.

## Replacing the placeholder

`sample-tool-clip.mp4` is a generated placeholder so the in-VR video screen works out of the box. To use College footage:

1. Drop your clip in this folder, e.g. `media/pipe-wrench.mp4`.
2. In `index.html`, add a `videoFile` to that tool in the `PROFESSIONS` data:

```js
{
  id: "pipe-wrench",
  name: "Pipe Wrench",
  video: "IwqlSQ3iu1o",              // YouTube ID, used on flat screens
  videoFile: "media/pipe-wrench.mp4", // file, used inside the headset
  ...
}
```

Tools with no `videoFile` fall back to `sample-tool-clip.mp4` (see `VR_CLIP_FALLBACK` in `index.html`).

To use the same file everywhere, remove the `video:` field and the flat popup will need updating to use `videoFile` too.

## Encoding guidance

Meta Quest Browser plays these reliably:

- **Container** MP4
- **Video** H.264 (High profile is fine; Baseline is the safest), `yuv420p`
- **Audio** AAC
- **Resolution** 1280×720 is plenty for a panel this size; 1920×1080 if the clip is detailed
- **Faststart** put the metadata at the front so playback begins before the whole file downloads

```bash
ffmpeg -i source.mov \
  -c:v libx264 -pix_fmt yuv420p -crf 23 \
  -vf "scale=1280:-2" \
  -c:a aac -b:a 128k \
  -movflags +faststart \
  media/pipe-wrench.mp4
```

Keep clips short (30–90 seconds) and the file size modest — the whole file is served from GitHub Pages, which is not a video CDN. For anything longer, host the clips elsewhere and put the full HTTPS URL in `videoFile`; the host must send permissive CORS headers (`Access-Control-Allow-Origin`) or the headset will refuse to use it as a texture.

## Audio

Audio plays through the headset. The controller trigger press that opens the video counts as user activation, so sound is normally allowed straight away; if the browser blocks it, the clip starts muted and the panel says so — pressing **Play / Pause** then enables sound.
