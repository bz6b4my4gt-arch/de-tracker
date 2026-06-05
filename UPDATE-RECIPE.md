# Update recipe — ON-DEMAND (primary) + optional daily fallback

**Primary mode is now on-demand:** the user refreshes the site by prompting in chat. When they do, run the
standing prompt below. (A daily scheduled task may still exist as a fallback, but the on-demand run is
authoritative — see "Scheduled task" at the bottom.)

## Standing on-demand prompt (run this every time the user asks to "refresh the website")
> Search for all the programs of record listed on the website and look for news about them. Do NOT use
> Chinese or Russian websites — stick to reputable defense outlets and Laser Wars. Then search the DE/HEL
> industry more broadly. Identify notable, relevant news: changes to listed programs, new sources of funding,
> and new programs. **Wherever relevant, actually alter the website to update the information about those
> programs and/or add new ones** — the curated data itself must change, not just the news ticker.

On-demand runs MAY edit `index.html` when the change is structural (a new tab/section, e.g. the FY2027 Budget
and Space tabs). Plain data refreshes still edit **`de-data.js` only**. Goal every run: refresh the core
curated data (budget, programs, suppliers, timelines, statuses, discrepancies) **and** the news, and
**actively ADD newly announced programs/opportunities.**

## What to search EVERY run
- **Every CURRENT program** in `DATA.army / DATA.navy / DATA.af / DATA.space` — by name, code, and the aliases
  in each program's `match:[]`.
- **Every FUTURE / planned program** (e.g. E-HEL, JLWS, AMP-HEL, Songbow, BBG(X)) and anything newly announced.
- **The entire DE/HEL industry**, broadly: directed energy, high energy laser, high-power microwave, laser
  weapon, counter-drone / counter-UAS laser, Golden Dome, JIATF-401 — U.S. and allied.
- **Space-based DE:** space-based laser / HPM, the Golden Dome space layer (distinguish **kinetic** space-based
  interceptors from **DE**), counterspace DE, and MDA / SDA / Space Force / DARPA space-DE S&T.
- **Sourcing rule — NEVER use Chinese or Russian sites** (no `.cn` / `.ru`, Global Times, Xinhua, CGTN, RT,
  TASS, Sputnik). Use reputable U.S./allied defense outlets + official `.gov` / `.mil` only.
- **Laser Wars — check every run:** https://www.laserwars.net/ (and its archive). It is a primary DE
  funding/news source; fold relevant items in and cite it. It is **paywalled/metered** — the user has a
  subscription, so if a key article is gated, flag the exact title + URL for them to pull.
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
- **Data refreshes:** edit `de-data.js` ONLY (never `netlify.toml`). **Structural changes** (a new tab/section)
  may edit `index.html` — keep the layout generic and data-driven and put the content in `de-data.js`.
- Validate `de-data.js` before pushing (braces/brackets balanced; ends with `};`; `window.DE_DATA` assigned;
  every program still has `name, status, power, start, match, cost, timeline, suppliers, news, src`).
  If it would fail validation, **do not push** — leave the prior version live and report the error.

## Scheduled task
A local scheduled task `de-tracker-weekly-refresh` (9:10 AM) may exist. Since the user switched to **on-demand
prompting**, treat any scheduled run as a fallback only — the in-chat refresh is authoritative. The user can
disable the task from the Scheduled sidebar for purely manual control.
