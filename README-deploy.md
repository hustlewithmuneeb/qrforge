# QRForge — Deploy Guide

QRForge is a **single-file static website** (`index.html`). There is no backend, no database, no build step and no signup system. The QR library is embedded in the file, so it works on any static hosting — and even offline after the first load.

## Files in this ZIP
- `index.html` — the whole website (must sit at the site root)
- `logo.svg` — QRForge logo (dark version)
- `logo-white.svg` — logo for dark backgrounds
- `favicon.svg` — favicon source (the site also has an inline favicon, so this is optional)
- `README-deploy.md` — this guide

## Option A — Netlify Drop (fastest, ~1 minute)
1. Go to https://app.netlify.com/drop
2. Unzip `qrforge-website.zip` on your computer.
3. Drag the unzipped **folder** onto the Netlify Drop page.
4. You get a live URL instantly. Later you can rename the site or attach your own domain in Site settings.

## Option B — Vercel
1. Go to https://vercel.com/new
2. Drag the unzipped folder into the import area (or push it to a GitHub repo and import the repo).
3. Framework preset: **Other** / no build command, output directory = root.
4. Deploy — you get a live `*.vercel.app` URL.

## Option C — GitHub Pages
1. Create a new public GitHub repository, e.g. `qrforge`.
2. Upload `index.html` (and the SVG files) to the repository root and commit.
3. Settings → Pages → Source: **Deploy from a branch** → `main` / `(root)` → Save.
4. Your site goes live at `https://<your-username>.github.io/qrforge/` within a few minutes.

## Important: static vs dynamic QR codes (honest note)
QRForge creates **static** QR codes. The content (URL, Wi-Fi details, contact, etc.) is encoded directly into the QR pattern, so:

- ✅ They **never expire** and need **no server** — they keep working forever, even if this website goes offline.
- ❌ They **cannot be edited after printing** and scans **cannot be tracked**, because there is no redirect server in between.

If you need an editable destination later, point the QR at a redirect URL on a domain **you** control (e.g. `yourdomain.com/menu`) and change where that URL forwards on your own hosting. True "dynamic QR" products charge a subscription precisely because that redirect server must run forever — if that company shuts down, every printed dynamic QR breaks. Static codes have no such risk.

## Notes
- Everything (generation, logo colour sampling, image scanning, bulk CSV, saved "My Codes") runs in the visitor's browser. No data is uploaded anywhere.
- The only external loads are the Google Font (with a system-font fallback) and, **only if the built-in scanner API is unavailable**, the jsQR library from a CDN for the image scanner fallback. QR generation itself needs no network at all.
- Brand name "QRForge" is a placeholder — to rename, find-and-replace `QRForge` in `index.html` and swap the logo SVGs.

Founded by Muneeb Ullah · hustlewithmuneeb@gmail.com
