# Weekly full-site update recipe

This is the task spec for the **weekly scheduled routine** and for any on-demand "update the tracker" request.
The goal: refresh the **entire** curated dataset (budget, programs, suppliers, timelines, discrepancies, news)
**and actively add newly announced programs/opportunities** — by editing **`de-data.js` only**. Never rewrite
`index.html` for content.

## Steps for each run

1. **Sync**: `git -C <repo> pull`. Read `de-data.js` and this file.
2. **Research** (web). Cover changes since `META.lastUpdated`:
   - **Budget**: latest DoD/War Comptroller FY budget toplines for directed energy; Golden Dome DE; the
     counter-drone surge / JIATF-401 lines. Update the `FUNDING` cards if figures move.
   - **Each existing program** in `DATA.army / DATA.navy / DATA.af`: look for changes to `status`, `power`,
     `cost`, `timeline`, `suppliers`, or major `news`. Use CRS reports (R46925, R44175, IF11882, IF12421),
     service budget justification books, and reputable trade press (DefenseScoop, Breaking Defense,
     Defense News, Military Times, Naval News, TWZ, Air & Space Forces Magazine).
   - **NEW opportunities (do this every run)**: actively search the week's news for **newly announced DE
     programs, contracts/awards, RFIs/RFPs, prototype deliveries, test events, new vendors, or budget lines**
     that are not already in the dataset. Good queries: `"directed energy" contract award`, `high energy laser
     Army/Navy/Air Force new program`, `high-power microwave counter-drone award`, `Golden Dome laser`,
     `JIATF-401 directed energy`, `laser weapon RFI OR solicitation`. If credible, **add a full new program
     card** to the correct service (see field template below).
   - **Contradictions**: if new reporting contradicts existing data, add a row to `DISCREPANCIES`
     (`tag: t-fix | t-add | t-clar`) and update the affected card.
3. **Edit `de-data.js`** surgically:
   - Bump `META.lastUpdated` to the run date (YYYY-MM-DD) and increment `META.dataVersion`.
   - Prepend new items to a program's `news:[]` (newest first). **Cap at ~4 items per program** — drop the
     oldest beyond that to prevent unbounded growth.
   - Update changed `cost` / `power` / `timeline` / `suppliers` / `status` fields in place.
   - **Add new programs** using the template below. **Every program MUST have a `start:YYYY` (origin year)** —
     it controls which chronological **era band** the program falls into on the site:
     `pre-2018 = Foundations & legacy`, `2018–2021 = First-generation`, `2022–2024 = Current generation`,
     `2025+ = Newest & next-generation`. Pick the year the effort began (first contract/RFI/demo).
   - Add new `SOURCES` entries for any new outlet cited, and reference them by `s:` name in the card's `src:[]`.
4. **Quality bar** (do not skip):
   - Only change a fact when a credible source supports it. **Do not fabricate or guess.**
   - Keep the ⚠ `note:` flags for single-source or disputed items; add new flags where warranted.
   - Prefer primary/official sources for budget numbers; label bundled lines as bundled.
   - When adding a brand-new program from a single source, mark it with a `note:` saying so.
5. **Validate**: confirm `de-data.js` still parses — braces/brackets/parens balanced, ends with `};`, and
   `window.DE_DATA` is assigned. Every program object should still have `name`, `status`, `power`, `start`,
   `cost`, `timeline`, `suppliers`, `news`, `src`.
6. **Ship**: `git -C <repo> add -A` then commit `data: weekly refresh YYYY-MM-DD` and **push**. With Netlify
   continuous deploy connected, the push auto-publishes.

## New-program field template

```js
{name:"NAME", start:2026, code:"Full name / designation", status:"new", power:"e.g. 50 kW",
 blurb:"One-sentence what-it-is.",
 cost:["Dollar figures or 'not disclosed'."],
 timeline:[{d:"Mon 2026",e:"Milestone"}],
 suppliers:"Prime / integrator / platform.",
 news:[{d:"2026",t:"Most recent development."}],
 note:"Optional ⚠ caveat (e.g. single-source).",
 src:["Source name as it appears in SOURCES"]}
```

Valid `status` keys: `active, new, shelved, concept, fielded, counter, demo, platform, retired, concluded`
(see `STATUS` in `de-data.js`). If a needed status is missing, reuse the closest existing one.

## Guardrails
- Touch `de-data.js` only. Do not modify `index.html`, `netlify.toml`, or this file.
- If research surfaces nothing material, still bump `META.lastUpdated`, refresh news items, and note
  "no material changes" in the commit message.
- If `de-data.js` would fail validation, **do not push** — leave the prior version live and report the error.
