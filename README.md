# U.S. Directed Energy Weapon Programs — Live Tracker

Interactive tracker of U.S. directed-energy weapon programs (Army · Navy · Air Force · **Space**) with a
dedicated **FY2027 Budget** page (linking the official budget documents), suppliers, timelines, a verification
log, sources, and a best-effort live-news ticker. Programs under each branch are arranged into chronological
**era bands** (oldest → newest), and live headlines are auto-tagged onto the program cards they mention.
The site is refreshed **on demand** (prompt in chat) — see [`UPDATE-RECIPE.md`](./UPDATE-RECIPE.md).

## Files

| File | Purpose | Edit it? |
|------|---------|----------|
| `index.html` | The layout, styling, dropdown logic, and live-news fetch. | Rarely — only for design changes. |
| `de-data.js` | **All the content** (programs, costs, timelines, suppliers, news, sources, funding). | **Yes — this is the auto-update file.** |
| `netlify.toml` | Netlify hosting config (no build step). | No. |

> **Automated updates touch `de-data.js` only.** The layout never needs rebuilding. The full routine spec lives in [`UPDATE-RECIPE.md`](./UPDATE-RECIPE.md).

## How to update the data (on-demand)

Full recipe: [`UPDATE-RECIPE.md`](./UPDATE-RECIPE.md). In short:

1. Open `de-data.js`.
2. Bump `META.lastUpdated` (YYYY-MM-DD) and `META.dataVersion`.
3. Add/adjust content:
   - **New headline news** for a program → add an item to the top of its `news:[]` array: `{d:"Jun 2026", t:"…"}` (cap ~4).
   - **Status change** (e.g. shelved → active) → edit `status:`.
   - **New figure** → edit `cost:`, `power:`, `timeline:`, or `suppliers:`.
   - **New program / opportunity announced** → add a full card to the right service. Every program needs a
     `start:YYYY` origin year (sorts it into the chronological **era bands**) and `match:[...]` keywords
     (so live headlines auto-tag onto its card).
   - **New contradiction found** → add a row to `DISCREPANCIES`.
4. Save. If hosted via Git, commit + push → Netlify redeploys automatically.

The **Live News** tab pulls an industry-wide query from GDELT (no API key) as a best-effort ticker, and matching
headlines are auto-tagged onto each program card. **On-demand refreshes** then screen this feed plus targeted
searches of every program name, the wider DE/HEL industry and Laser Wars, and promote verified developments into
the curated data (and the **FY2027 Budget** and **Space** pages).

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
