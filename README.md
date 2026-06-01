# U.S. Directed Energy Weapon Programs — Live Tracker

Interactive tracker of U.S. directed-energy weapon programs (Army · Navy · Air Force) with FY2027
budget figures, suppliers, timelines, a verification log, sources, and an auto-refreshing live-news feed.

## Files

| File | Purpose | Edit it? |
|------|---------|----------|
| `index.html` | The layout, styling, dropdown logic, and live-news fetch. | Rarely — only for design changes. |
| `de-data.js` | **All the content** (programs, costs, timelines, suppliers, news, sources, funding). | **Yes — this is the daily-update file.** |
| `netlify.toml` | Netlify hosting config (no build step). | No. |

> **Daily updates touch `de-data.js` only.** The layout never needs rebuilding.

## How to update the data (daily / periodic)

1. Open `de-data.js`.
2. Bump `META.lastUpdated` (YYYY-MM-DD) and `META.dataVersion`.
3. Add/adjust content:
   - **New headline news** for a program → add an item to the top of its `news:[]` array: `{d:"Jun 2026", t:"…"}`.
   - **Status change** (e.g. shelved → active) → edit `status:`.
   - **New figure** → edit `cost:`, `power:`, `timeline:`, or `suppliers:`.
   - **New contradiction found** → add a row to `DISCREPANCIES`.
4. Save. If hosted via Git, commit + push → Netlify redeploys automatically.

The **Live News** tab refreshes on every page load from GDELT (no API key); it covers day-to-day
headlines automatically. The curated, verified data is the part a human/Claude edits.

## Deploy to Netlify

**Option A — Drag & drop (fastest, no account wiring):**
1. Go to https://app.netlify.com/drop
2. Drag this `de-tracker-site` folder (or the provided `.zip`) onto the page.
3. Done — you get a live URL. To update later, drag the folder again onto the site's *Deploys* tab.

**Option B — Git + continuous deploy (best for daily auto-updates):**
1. Push this folder to a GitHub repo.
2. In Netlify: *Add new site → Import from Git →* pick the repo. Publish directory = repo root.
3. Every `git push` auto-deploys. A scheduled job that edits `de-data.js` and pushes will keep the site current hands-free.

## Local preview

Open `index.html` in a browser. Note: opening via `file://` may block the live-news fetch (browser
cross-origin rule) and, in some browsers, the `de-data.js` load. Hosting on Netlify resolves both.
