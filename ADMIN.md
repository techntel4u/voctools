# Admin portal

The portal at **`admin.html`** manages everything students see: trades, tools, the parts of each tool, videos and quizzes.

- Live URL: `https://<user>.github.io/voctools/admin.html`
- Default password: **`CoWC-ForgeXR-2026`** — change it (see [Changing the password](#changing-the-password))

## How the content is stored, and why

All content lives in a single file, **`content.json`**, committed to this repository. The student app reads it at startup; the portal edits it and commits it back.

This was chosen over a database or a SharePoint list for three reasons:

1. **Nothing can be lost.** Every publish is a git commit. You can see who changed what and when, and roll back to any earlier version — the whole history is kept forever, for free.
2. **No server to run, pay for or maintain.** The student app is a static page that must be openable from a VR headset with no login. A database would need hosting and credentials; SharePoint would need students to sign in inside the Quest browser, which is painful in a classroom.
3. **Nothing to migrate later.** If the project outgrows a file, `content.json` is a plain, documented structure that imports straight into a database or a SharePoint list.

**Layers of protection against losing work:**

| When | What protects it |
|---|---|
| While you are typing | Saved to the browser continuously; a refresh offers to restore your draft |
| Photos and videos you've added but not published | Held in the browser's local database, so they survive a refresh too |
| Leaving the page with unsaved changes | The browser warns you first |
| Every publish | A backup file downloads automatically **before** anything is sent |
| After publishing | A permanent git commit; older versions stay in the repository history |
| At any time | The **Backup** button downloads the current content as a file |

## First-time setup

You need a GitHub token so the portal can save changes.

1. Go to GitHub → **Settings → Developer settings → Personal access tokens → Fine-grained tokens** → **Generate new token**.
2. **Repository access:** Only select repositories → this repository.
3. **Permissions:** Repository permissions → **Contents: Read and write**. Nothing else is needed.
4. Copy the token (it is shown once).
5. In the portal, press **Settings** and fill in:
   - **Owner** — your GitHub username or organisation
   - **Repository** — `voctools`
   - **Branch** — `main` (press *List branches* to confirm). This must be the same branch GitHub Pages publishes from. There is no default: a branch that does not exist is the most common cause of a failed publish.
   - **Token** — paste it

Press **Test connection** before saving. It checks each thing separately and tells you exactly what is wrong — a bad token, a repository the token cannot see, or a branch that does not exist — rather than leaving you with a bare error at publish time.

The token is stored **only in your browser** and is never written into the site or the repository. If you use a different computer, you'll enter it again there.

> **Keep the token private.** Anyone with it can change the site. If it leaks, delete it on GitHub and generate a new one — that instantly stops the old one working.

## Everyday use

1. Open the portal and unlock it.
2. Pick a trade or tool on the left, or add one with **+ Trade** / the **+** on a trade.
3. Fill in the details. The dot next to the ForgeXR logo shows there are unpublished changes.
4. Press **Preview** to open the student app showing *your draft* (marked with a PREVIEW bar) without affecting students.
5. Press **Publish**. You'll see any problems first, then it commits. The live site updates in a minute or two.

### What each field does

| Field | Where students see it |
|---|---|
| Tool name, icon, short description | The tool card in the list of tools |
| What this tool is | Menu row "What this tool is" |
| How it's used in the trade | Menu row "How it's used in the trade" |
| Photo | The main image, with numbered markers on it |
| Parts 1–3 | Numbered markers on the photo and rows in the menu |
| YouTube link | The video popup on phone, tablet and computer |
| Video file | The video screen **inside the VR headset** |
| Quiz questions 1–3 | The quiz, reached from the button under the menu |

### Placing the part markers

Upload a photo first, then for each part press **Place on photo** and click the spot on the image. The photo scrolls into view automatically. You can drag a marker afterwards to fine-tune it.

Parts you never place still appear in the student menu — they just get no marker on the photo.

### About video

There are two video fields because of a real limitation, not a preference:

- A **YouTube link** plays on phones, tablets and computers.
- A **YouTube player cannot be shown inside VR** — the headset needs an actual video file to paint onto a screen in the 3D scene.

Fill in both and every device gets a video. Fill in only YouTube and headset users get none. Keep uploads to short clips (under about 90 seconds, roughly 25 MB); the portal warns you above 30 MB. See [media/README.md](media/README.md) for encoding settings.

### Archive vs delete

- **Archive** hides a trade or tool from students but keeps everything, ready to bring back.
- **Delete** removes it from the next publish. Even then it is not gone — it stays in the repository history and can be recovered from an earlier commit or from a downloaded backup.

## Checks before publishing

The publish screen lists two kinds of message:

- **Errors** (red) block publishing — for example a question with no correct answer marked, or two tools sharing an id.
- **Warnings** (amber) do not block — for example a missing photo or an empty description. They tell you what students will not get.

## Changing the password

The portal is a static page, so the password is a **gate against casual access, not a security control** — anyone can read the page source. That is acceptable because the content is public by design. What actually matters is *write* access, and that is protected by your GitHub token.

To change it, generate a SHA-256 hash of the new password:

```bash
printf '%s' 'your new password' | shasum -a 256
```

Paste the result into `PASSWORD_SHA256` near the top of the script in `admin.html`, and commit.

## Restoring an earlier version

**From a backup file:** any downloaded `forgexr-content-*.json` is a complete copy. Rename it to `content.json` and upload it to the repository, replacing the current one.

**From git history:** open `content.json` on GitHub, press **History**, choose a version, and copy its contents back. Every publish is a separate commit with your description on it.

## Structure of `content.json`

```jsonc
{
  "schemaVersion": 1,
  "updated": "2026-08-26T21:00:00.000Z",
  "trades": [
    {
      "id": "plumbing",          // used in links; keep stable once published
      "name": "Plumbing",
      "icon": "🔧",
      "tagline": "Keeping water, waste and gas systems flowing safely.",
      "chipBg": "#e7eefc",
      "mockup": false,            // shows a "Mock-up" badge
      "archived": false,          // hidden from students
      "tools": [
        {
          "id": "pipe-wrench",
          "name": "Pipe Wrench",
          "icon": "🔧",
          "short": "Grips and turns round pipe without crushing it.",
          "what": "…",
          "usage": "…",
          "model": "pipeWrench",  // 3D stand-in used only when there is no photo
          "photo": { "src": "media/tools/pipe-wrench.jpg", "w": 464, "h": 695 },
          "video": { "youtube": "IwqlSQ3iu1o", "file": "media/videos/pipe-wrench.mp4" },
          "parts": [
            { "label": "Hook jaw", "body": "…", "x": 12, "y": 26 }  // x/y are % of the photo
          ],
          "quiz": [
            { "q": "…", "options": ["A", "B", "C", "D"], "correct": 1 }  // correct is 0-based
          ],
          "archived": false
        }
      ]
    }
  ]
}
```

The file can also be edited by hand and committed directly — the portal is a convenience, not a lock-in.

## If something goes wrong

| Symptom | Cause and fix |
|---|---|
| "Content unavailable" in the student app | `content.json` is missing or malformed on the published branch. Restore the previous version from git history. |
| Publish fails with **GitHub 401/403** | The token is wrong, expired, or lacks *Contents: Read and write* on this repository. Generate a new one. |
| Publish fails naming a **branch that does not exist** | The branch in Settings is wrong. The message lists the branches that do exist — pick one, or press *List branches*. |
| Publish says it **cannot see the repository** | The owner/repository is wrong, **or** the token was not granted access to that repository. GitHub answers "not found" rather than "forbidden" when a token cannot see a repository, so a permissions problem looks exactly like a typo. Check the token covers this repo. |
| Publish fails with **409** | Someone else (or another commit) changed the branch since the portal loaded it. Reload the portal and publish again. |
| Changes published but the site looks unchanged | GitHub Pages takes a minute or two, and browsers cache. Wait, then hard-refresh. |
| A photo or video does not appear | It is still staged. The header shows "N files to upload" until you publish. |

Nothing is lost in any of these cases — a failed publish changes nothing on the site, your work stays in the browser, and a backup was downloaded before the attempt.
