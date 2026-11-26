# LinkVault

[![Latest release](https://img.shields.io/github/v/release/milleran41/MyLinks?label=download&sort=semver)](https://github.com/milleran41/MyLinks/releases/latest)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Open in browser](https://img.shields.io/badge/open%20in%20browser-GitHub%20Pages-2563eb)](https://milleran41.github.io/MyLinks/)

LinkVault is a simple app for saving links, notes and screenshots in one local table. It
runs entirely in your browser: no installation, no server, no account, no `.exe` file.
The same build works as a single HTML file, as an installable web app (PWA), and as a
Microsoft Store app.

![LinkVault screenshot](assets/screenshot.png)

## Get LinkVault

| Option | What to do |
| --- | --- |
| **Run in browser** | Open <https://milleran41.github.io/MyLinks/> |
| **Install as an app** | Open the link above in Edge or Chrome → `⋯` → *Apps* → *Install this site as an app* |
| **Single file** | Download [LinkVault.zip](https://github.com/milleran41/MyLinks/releases/latest/download/LinkVault.zip), unpack it, double-click `LinkVault.html` |
| **Microsoft Store** | Not published yet — packaging steps are in [STORE.md](STORE.md) |

There is no `.exe` file. LinkVault is a standalone HTML app, so the file you launch is
`LinkVault.html`. The installed web app is the same code and stores data in the same
place (the browser's IndexedDB for that origin).

## Features

- Save links, descriptions and screenshots in a clean table.
- Organize records into pages/categories.
- Rename and delete pages.
- Automatically highlight duplicate links.
- Store data locally in your browser with IndexedDB.
- Save and restore JSON backup files.
- Use a multilingual interface (22 languages).
- Install as an app and work fully offline.

## Pages and Categories

On first launch, LinkVault creates one default page. In English it is named `Home`; in
Russian it is `Главная`; in other interface languages it is translated as well.

- To create a new page, click the `+` button next to the page tabs.
- To switch pages, click the page tab you want.
- To rename a page, double-click the page name, type a new name, then press `Enter` or
  click somewhere else.
- To delete a page, click the `×` button on the tab.

If you manually rename the default page, it becomes a custom page name and is no longer
translated automatically.

## Adding Records

Each row has four columns:

- `№` - row number.
- `Link` - the website address.
- `Screenshot` - an optional image preview.
- `Description` - your note about the link.

To add a record:

1. Click an empty cell in the `Link` column.
2. Paste or type a link.
3. Add a note in the `Description` column if needed.
4. When the last row has a link or description, LinkVault automatically adds a new empty
   row.

If you paste a link without `https://`, LinkVault adds it automatically.

## Adding Screenshots

1. Take a screenshot on your computer.
   - On Windows, you can use `Win + Shift + S`.
2. Return to LinkVault.
3. Click the `Screenshot` cell in the needed row.
4. Press `Ctrl+V`, or right-click and choose paste.
5. The screenshot is saved in that row.

Double-click a screenshot to open it larger. Use the `✕` button on the image to remove it
from the row.

## Editing, Moving, and Deleting

- To edit a link or description, click the cell and change the text.
- To move a row, drag it to another position.
- To delete a row, right-click the row number and choose `Delete row`.
- If the same link is added more than once, duplicate rows are highlighted automatically.

## Backups

Your data is stored inside your browser. If browser data is cleared, LinkVault data can
be lost.

Use the `Save` button regularly to create a backup file named `LinkVault_backup.json`. Use
the `Load` button to restore data from a backup — it restores old backup files as well,
skipping damaged rows instead of failing.

## Privacy

LinkVault has no server and no network calls of its own: no accounts, analytics, ads or
trackers. Data stays in the local browser database, and backups are files you create
yourself. See [PRIVACY.md](PRIVACY.md).

## Project files

- `LinkVault.html` - the whole app (HTML, CSS, JavaScript, IndexedDB).
- `index.html` - entry page that opens the app (used by GitHub Pages and the PWA).
- `manifest.webmanifest`, `sw.js` - web app manifest and service worker for install/offline.
- `icon*.png`, `apple-touch-icon.png` - app icons.
- `readme.txt` - local help file opened by the `About` button.
- `assets/screenshot.png` - screenshot shown in this README.
- `store/` - Microsoft Store assets: 300×300 logo, 1366×768 screenshots, listing texts.
- `PUBLISHING.md` - GitHub repository, Pages and release setup.
- `STORE.md` - how to package and submit the Microsoft Store app.

Personal backup files such as `LinkVault_backup.json` are intentionally not published and
are not included in the archive.

## Development

The app is a single static file with no build step and no dependencies. To run it locally
with a service worker (offline install needs `http://` or `https://`):

```bash
python -m http.server 8123 --bind 127.0.0.1
```

Then open <http://127.0.0.1:8123/>. Opening `LinkVault.html` directly from disk also works,
just without install/offline support.

## License

[MIT](LICENSE)
