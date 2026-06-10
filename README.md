# Private Notes — your personal Notesnook-style app

A single-user, local-first notes app. No accounts, no auth, no server storing your notes.
All data lives in the browser's IndexedDB **on each device** — nothing is ever uploaded.

## What's in this folder

| File | Purpose |
|---|---|
| `index.html` | The entire app — UI, storage, editor, importers. Edit this to make your changes. |
| `manifest.webmanifest` | Makes it installable (home screen icon, standalone window). |
| `sw.js` | Service worker — makes it work fully offline once installed. |
| `icon-192.png` / `icon-512.png` | App icons. |

## Run it on your PC

Option 1 — quickest: just double-click `index.html`. It opens in your browser and works
(notes are saved per-browser). The only thing that won't activate from a `file://` page is
the offline service worker, which you don't need on a PC anyway.

Option 2 — proper local server (recommended):

```bash
cd notesnook-private
python -m http.server 8000
# then open http://localhost:8000
```

## Install it on your iPhone

Safari needs the app served over **HTTPS** to install it as a home-screen app with offline
support. The easiest way is free static hosting — and this is safe even on a public URL,
because the hosted files are just the empty app shell. Your notes never leave your phone;
they're stored only in the phone's local storage.

1. Host the folder on any free static host. Three options that take ~2 minutes:
   - **GitHub Pages**: create a repo, upload these 5 files, enable Pages in repo settings.
   - **Cloudflare Pages** or **Netlify**: drag-and-drop the folder onto their dashboard.
2. Open the URL in **Safari** on your iPhone.
3. Tap the **Share** button → **Add to Home Screen**.
4. Launch it from the home screen — it now runs fullscreen like a native app and works offline.

If you'd rather not host it anywhere, you can also open the URL of a server running on your
PC over your home Wi-Fi (e.g. `http://192.168.1.x:8000`) — the app works, you just won't get
the offline install (Safari requires HTTPS for that).

## Import your Notesnook notes

Best path (keeps notebooks, tags, dates):

1. In the official Notesnook app: **Settings → Export all notes → Markdown** → you get a `.zip`.
2. In this app: **Settings → Import from Notesnook** → pick that `.zip`.

Also supported:
- **`.nnbackup` / `.nnbackupz`** files — works if the backup is **unencrypted**
  (in Notesnook: Settings → Backup & restore → turn off backup encryption, then back up again).
  Encrypted backups can't be read without Notesnook's key derivation; the importer will tell
  you if it hits one and what to do instead.
- Plain `.md`, `.txt`, or `.html` files (select multiple at once).

## Moving notes between iPhone and PC

There is **no sync** — that's the trade-off of having no server. Each device keeps its own copy.
To move notes: **Settings → Back up this app's data** on device A → send the JSON file to
device B (AirDrop, email, USB) → **Settings → Restore a backup** on device B. Restore *merges*
(it never deletes existing notes).

## Making your changes

Everything is in `index.html`, organized in clearly labeled sections:

1. `DB` — IndexedDB wrapper (stores: `notes`, `notebooks`, `meta`)
2. `State` — in-memory state, note model, helpers
3. `Render` — nav / list / editor rendering
4. `Editor` — autosave (500 ms debounce), toolbar, preview
5. `Markdown` — the renderer (escapes HTML, so it's XSS-safe)
6. `Import` — ZIP parser, frontmatter parser, HTML→Markdown, nnbackup reader
7. `Backup` — this app's own export/restore format

Theme colors are CSS variables at the top of the `<style>` block (`--accent`, `--bg`, …).
After changing `index.html`, bump the `CACHE` version string in `sw.js` (e.g. `v1` → `v2`)
so installed devices pick up the new version.

## Keyboard shortcuts (PC)

- `Ctrl/Cmd + N` — new note
- `Ctrl/Cmd + K` — focus search
- `Ctrl/Cmd + E` — toggle markdown preview
