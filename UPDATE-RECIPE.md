# Automated update recipe (runs DAILY)

Spec for the daily scheduled routine **and** any on-demand "update the tracker" request.
Goal: every day, refresh **both** the core curated data (budget, programs, suppliers, timelines, statuses,
discrepancies) **and** the news, and **actively ADD newly announced programs/opportunities** — by editing
**`de-data.js` only**. Never edit `index.html` for content.

## What to search EVERY run
- **Every CURRENT program** in `DATA.army / DATA.navy / DATA.af` — by name, code, and the aliases in each
  program's `match:[]`.
- **Every FUTURE / planned program** (e.g. E-HEL, JLWS, AMP-HEL, Songbow, BBG(X)) and anything newly announced.
- **The entire DE/HEL industry**, broadly: directed energy, high energy laser, high-power microwave, laser
  weapon, counter-drone / counter-UAS laser, Golden Dome, JIATF-401 — U.S. and allied.
- **Laser Wars — check every run:** https://www.laserwars.net/ (and its archive). It is a primary DE
  funding/news source; fold relevant items in and cite it (SOURCES already has a "Laser Wars" entry).
- **Primary/official:** DoD / War Comptroller budget books; CRS R46925, R44175, IF11882, IF12421.
  **Trade press:** DefenseScoop, Breaking Defense, Defense News, Military Times, Naval News, The War Zone,
  Air & Space Forces Magazine.

## Steps
1. **Sync:** `git -C "<repo>" pull`. Read `de-data.js` and this file.
2. **Research** per the list above. For each program: changes to status / power / cost / timeline / suppliers and
   any major news. For the industry: new contracts, awards, RFIs/RFPs, test events, new vendors, new programs.
3. **Promote news → curated data (the whole point):** when a development is credible and verified, update the
   matching program's fields and prepend a news item. If it is a brand-new effort, **ADD a full program card.**
   Do not leave verified developments only in the live feed — reflect them in the real cards.
4. **Edit `de-data.js` only:**
   - Bump `META.lastUpdated` to today (YYYY-MM-DD); increment `META.dataVersion`.
   - Prepend to a program's `news:[]` (newest first), cap ~4 (drop the oldest beyond that).
   - Update `cost` / `power` / `timeline` / `suppliers` / `status` in place.
   - **New program:** full card including `start:YYYY` (chronological era band) **and** `match:[...]` keywords
     (live-news routing). Use the template below.
   - Add `SOURCES` entries for new outlets; reference them by name in the card's `src:[]`.
   - Keep `META.newsQuery` broad / industry-wide (it drives the page's live feed). Add a program codename to it
     only if the codename is unambiguous (avoid e.g. "Guardian", "Phaser", "CHIMERA").
5. **Quality bar:** only change a fact with a credible source; **never fabricate.** Keep/add ⚠ `note:` flags for
   single-source or disputed items; mark brand-new single-source programs as such.
6. **Change detection:** if nothing material is new since `lastUpdated`, still bump `lastUpdated`, refresh news
   items, and note "no material changes" in the commit message.
7. **Validate `de-data.js`:** braces/brackets/parens balanced; ends with `};`; `window.DE_DATA` assigned; every
   program still has `name, status, power, start, match, cost, timeline, suppliers, news, src`.
8. **Ship (only if valid):** `git -C "<repo>" add -A`, commit `data: daily refresh YYYY-MM-DD`, then push.
   Netlify auto-deploys on push.

## New-program template
```js
{name:"NAME", start:2026, match:["name","codename","prime"], code:"Full designation", status:"new", power:"e.g. 50 kW",
 blurb:"One-sentence what-it-is.",
 cost:["Dollar figures or 'not disclosed'."],
 timeline:[{d:"Mon 2026",e:"Milestone"}],
 suppliers:"Prime / integrator / platform.",
 news:[{d:"2026",t:"Most recent development."}],
 note:"Optional ⚠ caveat (e.g. single-source).",
 src:["Source name as it appears in SOURCES"]}
```
Valid `status` keys: `active, new, shelved, concept, fielded, counter, demo, platform, retired, concluded`.
`match` = lowercase keywords/aliases the live feed uses to tag headlines onto this card (codename, acronym,
prime contractor, platform). Keep them distinctive to avoid false matches.

## Guardrails
- Edit `de-data.js` ONLY. Never modify `index.html`, `netlify.toml`, this file, or `README.md`.
- If `de-data.js` would fail validation, **do not push** — leave the prior version live and report the error.
