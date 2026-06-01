# Daily full-site update recipe

This is the task spec for the scheduled routine **and** for any on-demand "update the tracker" request.
The goal: refresh the **entire** curated dataset (budget, programs, suppliers, timelines, discrepancies,
news) — not just headlines — by editing **`de-data.js` only**. Never rewrite `index.html` for content.

## Steps for each run

1. **Sync**: pull the latest repo. Read `de-data.js` and this file.
2. **Research** (web). Check for changes since `META.lastUpdated`:
   - **Budget**: latest DoD/War Comptroller FY budget toplines for directed energy; Golden Dome DE; the
     counter-drone surge / JIATF-401 lines. Update the `FUNDING` cards if figures move.
   - **Each program** in `DATA.army / DATA.navy / DATA.af`: look for changes to `status`, `power`, `cost`,
     `timeline`, `suppliers`, or major `news`. Use CRS reports (R46925, R44175, IF11882, IF12421), service
     budget justification books, and reputable trade press (DefenseScoop, Breaking Defense, Defense News,
     Military Times, Naval News, TWZ, Air & Space Forces Magazine).
   - **New programs**: if a credible new DE program/contract appears, add a full card to the right service.
   - **Contradictions**: if new reporting contradicts existing data, add a row to `DISCREPANCIES`
     (`tag: t-fix | t-add | t-clar`) and update the affected card.
3. **Edit `de-data.js`** surgically:
   - Bump `META.lastUpdated` to the run date (YYYY-MM-DD) and increment `META.dataVersion`.
   - Prepend new items to a program's `news:[]` (newest first). **Cap at ~4 items per program** — drop the
     oldest beyond that to prevent unbounded growth.
   - Update changed `cost` / `power` / `timeline` / `suppliers` / `status` fields in place.
   - Add new `SOURCES` entries for any new outlet cited, and reference them by `s:` name in the card's `src:[]`.
4. **Quality bar** (do not skip):
   - Only change a fact when a credible source supports it. **Do not fabricate or guess.**
   - Keep the ⚠ `note:` flags for single-source or disputed items; add new flags where warranted.
   - Prefer primary/official sources for budget numbers; label bundled lines as bundled.
5. **Validate**: confirm `de-data.js` still parses — braces/brackets/parens balanced, ends with `};`, and
   `window.DE_DATA` is assigned. (A quick syntax check; the file must not break the page.)
6. **Ship**: commit with a dated message like `data: daily refresh YYYY-MM-DD` and **push**. With Netlify
   continuous deploy connected, the push auto-publishes.

## Guardrails
- Touch `de-data.js` only. Do not modify `index.html`, `netlify.toml`, or this file.
- If research surfaces nothing material, still bump `META.lastUpdated`, refresh any news items, and note
  "no material changes" in the commit message.
- If `de-data.js` would fail validation, **do not push** — leave the prior version live and report the error.
