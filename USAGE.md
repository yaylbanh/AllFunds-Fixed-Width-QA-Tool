# AllFunds FixedWidth QA Toolkit — usage

Single file, offline, no install: double-click `index.html` to open it in any browser
(Chrome/Edge/Firefox). Nothing is uploaded anywhere — parsing/editing/export all happen locally in the page.

## Workflow

1. **Open file** — drag a `.txt` fixed-width file onto the drop zone, or use "Open file…".
2. **Click "Verify round-trip" first, always.** It re-parses the file you just loaded and rebuilds it with
   zero edits; it must report **PASSED** (byte-for-byte identical) before you trust anything else the tool
   shows you. If it fails, it prints the exact byte offset / line number and a snippet of both versions so a
   position bug can be pinpointed.
3. Records are grouped into tabs by type:
   - **R24 / 01 — Confirmation** — the CORE-31784 target. Rows with Operation type `10` or `20` are
     highlighted (green left border + SCOPE badge). Every field is editable inline; grey fields are
     auto-computed and locked.
   - **98 — Control / trailer** — the file's trailer record. "Total records" and 4 per-type counters
     (types 24, 30, 71, 98) recompute automatically whenever you add/clone/delete rows anywhere in the file.
   - **71 — Fund catalogue** — fully decoded field-by-field for every subtype in the current (06-2025)
     Record71-Fund Catalogue spec: 01 (general), 02 (operative), 03 (commissions), 04 (indexes),
     05/55 (multiseries), 06 (UK/SG/HK/FR/CL market-specific), 10-14 (calendars), 20 (Italian retail),
     30 (CNMV A01 report — routed to one of two incompatible layouts based on the "Sequence" field),
     40 (legal document URLs). Each subtype gets its own tab with the same edit/position-header UI as
     R24/01. An unrecognized future subtype falls back to a raw, byte-exact "71 — Fund catalogue
     (unrecognized subtype)" tab instead of being mis-decoded.
   - **R24/01 — Dividend (view-only)** — Record 24/Subtype 01 rows whose Operation type is 14/50/51/68.
     AllFunds reuses the same header for dividend payouts but with different field meanings from position
     ~344 onward (see `Record24-dividends.pdf`). Shown read-only so it's never mislabeled; still exported
     byte-exact.
   - **Unparsed / Raw** — anything else (R20 prices, R30/R34 balances, etc.). Read-only, always preserved
     byte-exact, never dropped.
   - **Field map (R24/01)** — the full derived position table for reference/review.
4. **Edit** cells directly. Numeric fields reject non-digits and stop you at the max digit count; text fields
   stop you at the max length. Edited cells turn yellow.
5. **Add / Clone / Delete** rows with the buttons in the row's first column / tab toolbar.
6. **Filter to scope** — downloads a *separate* minimal file containing only R24/01 rows with Operation
   type 10 or 20 (plus a recomputed trailer), for a small targeted test file.
7. **Export .txt** — downloads the full file with your edits applied. Untouched bytes are carried over
   exactly; only edited fields are re-padded (numbers zero-padded left, text space-padded right) to their
   original width. Line endings (CRLF/LF) and any non-UTF8 bytes are preserved as-is.

## What's been validated

- Round-trip (`Verify`, no edits) passes byte-for-byte on all provided samples: `26012113822E210.txt`,
  `R24_Confirmation.txt`, `AFB sending to Distributor-fileE2.txt`, `R20/R30/R34/R71` samples, the Fund
  switch samples, and `R24_Dividend.txt`.
- The R24/01 field map was cross-checked arithmetically against `R24_Confirmation.txt`: `Confirmed shares ×
  Price` reproduces `Calculated gross amount` exactly on all 3 sample rows (op 10 and op 20).
- The Record 71 field map was transcribed from `Record Types/Record71-Fund Catalogue.pdf` via
  coordinate-aware PDF table extraction (not the flattened text layout, which scrambles multi-line cells)
  and cross-checked byte-for-byte against every row of `Samples/R71 Fund catalogue.txt`: subtypes 01, 02,
  03, 04, 10, 11, 12, 13, 14 and 30 all decode with zero invalid numeric fields, and every field tiles
  positions 1-800 with no gaps, overlaps, or duplicate field names (duplicate names would silently corrupt
  the other occurrence's bytes on export — several were found and disambiguated, e.g. the two identically
  labelled "commission segment" blocks in subtype 03 and the subscription/redemption blocks in subtype 20).
  Subtypes 05, 06, 20, 40, 55 have no sample data in this project, so they're transcribed from the spec's
  stated byte positions only (self-consistent, but not cross-checked against real bytes).

## Known limitations (by design, not bugs)

- The 98 control record's per-record-type counter table has 99 possible slots; only 4 are confirmed against
  samples (types 24, 30, 71, 98-self) and auto-recompute. The rest is kept as an untouched reserved block.
- Record 24/01 dividend-flavoured rows (op 14/50/51/68) are intentionally not editable — building that
  schema means transcribing `Record24-dividends.pdf` separately, which is outside CORE-31784.
