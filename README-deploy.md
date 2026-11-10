# QRForge — Deploy Guide (v5.2: real pages + blog)

QRForge v5 is a **multi-page static website** — seven real app pages plus a **blog** (index + 5 guides) with clean URLs, built for Google indexing. There is no build step. The QR library is embedded in every app page file, so it works on any static hosting — and even offline after the first load. Accounts, the guest 10-code gate, saved codes and the tracked-code panel all run in the browser; an **optional free cloud backend** (Supabase, in the `backend/` folder) switches on Google sign-in and real scan tracking — see **Accounts & tracking** below.

**Pages / URLs** (with `vercel.json` cleanUrls on):

| File | URL | Indexed? |
|---|---|---|
| `index.html` | `/` — Create (QR studio + home) | ✅ yes |
| `scan.html` | `/scan` — QR code scanner | ✅ yes |
| `bulk.html` | `/bulk` — Bulk QR codes from CSV | ✅ yes |
| `signin.html` | `/signin` | 🚫 noindex |
| `signup.html` | `/signup` | 🚫 noindex |
| `my-codes.html` | `/my-codes` | 🚫 noindex |
| `my-panel.html` | `/my-panel` | 🚫 noindex |
| `blog.html` | `/blog` — Blog index | ✅ yes |
| `blog/restaurant-menu-qr-code.html` | `/blog/restaurant-menu-qr-code` | ✅ yes |
| `blog/wifi-qr-code-for-guests.html` | `/blog/wifi-qr-code-for-guests` | ✅ yes |
| `blog/static-vs-dynamic-qr-codes.html` | `/blog/static-vs-dynamic-qr-codes` | ✅ yes |
| `blog/google-reviews-qr-code.html` | `/blog/google-reviews-qr-code` | ✅ yes |
| `blog/bulk-qr-codes-labels-packaging.html` | `/blog/bulk-qr-codes-labels-packaging` | ✅ yes |

Old v4 hash links (`#/panel`, `#/scan`, …) still work: the home page forwards them to the matching real page automatically.

## Files in this ZIP (upload ALL of these to the repo root)
- `index.html`, `scan.html`, `bulk.html`, `signin.html`, `signup.html`, `my-codes.html`, `my-panel.html` — the 7 app pages
- `blog.html` + the `blog/` folder (5 article files) — the blog; upload the folder as-is so articles land at `/blog/<slug>`
- `vercel.json` — turns on cleanUrls so `/scan` serves `scan.html`, `/my-panel` serves `my-panel.html`, `/blog/<slug>` serves `blog/<slug>.html`, etc. **Do not skip this file.**
- `robots.txt` — allows everything except the 4 private pages; points to the sitemap
- `sitemap.xml` — the 9 public URLs (`/`, `/scan`, `/bulk`, `/blog` + the 5 articles)
- `logo.svg` — QRForge logo (dark version)
- `logo-white.svg` — logo for dark backgrounds
- `favicon.svg` — favicon source (the site also has an inline favicon, so this is optional)
- `README-deploy.md` — this guide
- `backend/supabase/schema.sql` — database tables for tracked codes + scans
- `backend/supabase/functions/r/index.ts` — redirect + scan-logging Edge Function
- `backend/supabase/functions/api/index.ts` — My Panel stats + code registration Edge Function
- `backend/README-backend.md` — the step-by-step free cloud setup
- `schema.sql`, `function-r.ts`, `function-api.ts` — the same backend pieces, loose at the root for convenience

## Recommended — GitHub → Vercel (the live route)
1. Open your GitHub repo `qrforge`.
2. **Add file → Upload files**: upload **all** the site files listed above (7 app HTML + `blog.html` + the `blog/` folder + `vercel.json` + `robots.txt` + `sitemap.xml` + the 3 SVGs). They replace the old single `index.html` setup; commit.
3. Vercel redeploys automatically (~1 minute). Check: `https://qrforge.vercel.app/`, `/scan`, `/bulk`, `/signin`, `/blog` and one article all open as separate pages.

## Option B — Netlify Drop
1. Go to https://app.netlify.com/drop
2. Unzip `qrforge-website.zip` on your computer.
3. Drag the unzipped **folder** onto the Netlify Drop page.
4. You get a live URL instantly. (Netlify serves the extension-less page URLs too; the `vercel.json` file is simply ignored there.)

## Option C — GitHub Pages
1. Create a new public GitHub repository, e.g. `qrforge`.
2. Upload all the site files to the repository root and commit.
3. Settings → Pages → Source: **Deploy from a branch** → `main` / `(root)` → Save.
4. Your site goes live at `https://<your-username>.github.io/qrforge/` within a few minutes.

## Google Search Console (get indexed)
1. Go to https://search.google.com/search-console and add a **URL-prefix property**: `https://qrforge.vercel.app/`
2. Verify ownership (the HTML-tag method: paste the meta tag into `index.html`'s `<head>` and redeploy — or use the Google Analytics / DNS method if you add a custom domain later).
3. **Sitemaps** → submit: `https://qrforge.vercel.app/sitemap.xml`
4. **URL Inspection** → request indexing for `/`, `/scan`, `/bulk`, `/blog` and the 5 articles (the 9 URLs in the sitemap — Sign In / Sign Up / My Codes / My Panel are `noindex` on purpose).
5. Backlinks can point at any public page — the blog articles are the natural targets (e.g. a post about menu QR codes can link to `/blog/restaurant-menu-qr-code`), and each article links onward to the matching tool page.

## Important: static vs dynamic QR codes (honest note)
QRForge creates **static** QR codes by default. The content (URL, Wi-Fi details, contact, etc.) is encoded directly into the QR pattern, so:

- ✅ They **never expire** and need **no server** — they keep working forever, even if this website goes offline.
- ❌ A purely static code **cannot be edited after printing** and its scans **cannot be tracked**, because there is no redirect server in between.

QRForge also has a **Tracked (dynamic) mode** for URL, PDF/Menu and Google Review codes: the QR then encodes a short redirect link, the destination stays editable in **My Panel**, and scans are counted there. That mode needs the free cloud connection below — until the owner switches it on, the site says so plainly (tracked codes are registered in the panel with 0 scans, and their placeholder links do not open). For anything you print before the cloud setup, use static codes.

## Accounts & scan tracking (optional free cloud)
- **Accounts work out of the box, locally:** Sign Up / Sign In in the header, passwords stored only as SHA-256 hashes in the visitor's own browser, codes kept per account on that device. Guests get **10 free code creations** (a download or a save counts as one; bulk CSV is not counted), then they are asked to create a free account — accounts are unlimited.
- **Google sign-in, working tracked links and scan counting** need the one-time free Supabase setup in **`backend/README-backend.md`** (free plan, no card): run `schema.sql`, deploy the two Edge Functions, and paste your Project URL + anon key into `QRFORGE_CONFIG` — that config line appears **once in each of the 7 HTML files**, so if you ever change keys, update all 7 (in v5 the owner's live Supabase URL + publishable key are already filled in). The site labels all of this honestly in the UI — nothing pretends to be connected when it is not.

## What's inside
- **12 QR types** — URL, Text, Wi-Fi, vCard, Email, SMS, Phone, WhatsApp, Location, Event, PDF/Menu, Google Review.
- **Frames gallery** — 10 frames (none, classic, double-line, rounded, corner brackets, black banner top/bottom, bordered label, speech bubble, divider lines) with editable frame text and one-click presets (SCAN ME, SCAN FOR MENU, FOLLOW US, SCAN FOR WI-FI, LEAVE A REVIEW, SCAN TO PAY, SCAN TO ORDER). Frames are drawn into the live preview and every export — PNG, JPG, true-vector SVG (frame text is a real SVG text element) and PDF. Saved designs keep their frame and frame text. Bulk CSV codes stay frameless by design.
- **Test scan** — a button beside the preview that decodes your current design (styles + logo, without the frame) in the browser and checks it reads back exactly. Use it after adding a logo. Verified locally: square and rounded styles decode exactly, including a logo version at error correction H. The **dots** style could not be decoded by the jsQR test decoder — phone scanner apps are usually more tolerant, but test-scan any dotted design before printing.
- **Upload & scan** — a studio card (plus the full Scanner section) that reads any uploaded QR image on-device (built-in BarcodeDetector first, jsQR fallback), with Copy / Open buttons and **Recreate this QR**: links load into the URL type, anything else into Text, ready to re-style with your logo and frame.
- Logo upload with brand-colour sampling, live contrast checker, templates, and "My Codes" saved in the browser.
- **Accounts + guest gate** — Sign Up / Sign In (local, SHA-256 hashed passwords, per-user code lists, guest codes merge into a new account), a usage chip in the studio (above the download buttons) showing "Guest · X of 10 free codes used — Sign up free for unlimited" or "Signed in — unlimited codes", and a blocking sign-up prompt at 10. The header itself carries no counter — just logo, nav (incl. Blog) and the account buttons.
- **Blog** — `/blog` plus 5 original guides (restaurant menus, Wi-Fi codes, static vs dynamic, Google reviews, bulk labels & packaging), each with its own title/meta/canonical, Article structured data, internal links to the tools, and a call-to-action block. The blog pages are static content pages sharing the app's header, footer and styling.
- **Tracked QR mode + My Panel** — per-code stat cards, platform split (iOS / Android / Desktop / Other), top location, scan log, and inline destination editing. Scan numbers are always real: 0 until the free cloud connection is on and scans actually happen.

## Notes
- Everything (generation, logo colour sampling, image scanning, bulk CSV, saved "My Codes") runs in the visitor's browser. No data is uploaded anywhere.
- The only external loads are the Google Font (with a system-font fallback) and the jsQR library from a CDN — used **only** by the Test Scan check and, if the built-in scanner API is unavailable, by the image scanner fallback. Its first load needs internet; QR generation itself needs no network at all.
- Brand name "QRForge" is a placeholder — to rename, find-and-replace `QRForge` in all 7 app HTML files, `blog.html` and the 5 files in `blog/`, and swap the logo SVGs.

Founded by Muneeb Ullah · hustlewithmuneeb@gmail.com
