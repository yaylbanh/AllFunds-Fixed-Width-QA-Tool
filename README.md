# AllFunds FixedWidth QA Toolkit

Single-file, offline, no-install browser tool to inspect, edit, and byte-exact export AllFunds
fixed-width fund-record files (Record 24 confirmations, Record 71 fund catalogue, Record 98
control/trailer). Built for QA / reconciliation work on distributor file exchanges.

**Author:** Hoa Vo - Product TTLVN

## Why

AllFunds fixed-width files are plain-text, positional (byte-offset) record formats — no delimiters,
no schema in the file itself. Reading or editing one by hand means counting bytes. This tool decodes
each line into named, typed fields you can read and edit in a table, then re-encodes only what you
touched back into the original byte layout — so untouched bytes, line endings, and encoding are
preserved exactly.

## Features

- **Drag & drop** a `.txt`/`.dat` file straight into the browser — nothing is uploaded, everything
  runs client-side.
- **Tabbed, field-by-field view** grouped by record type/subtype, with byte position shown per column.
- **Inline editing** with type-aware input masking (numeric fields zero-pad/right-align, text fields
  space-pad/left-align to their original width) and visual markers for edited / invalid cells.
- **Verify round-trip** — re-parses and rebuilds the loaded file with zero edits and confirms it's
  byte-for-byte identical to the source, before you trust anything else the tool shows you.
- **Search / highlight by ISIN or fund code** — type into the search box to highlight matching rows
  live across the current tab.
- **Export by ISIN** — export only the records belonging to one fund code, plus a recomputed trailer.
- **Filter to scope** — export a minimal file containing only in-scope confirmation rows.
- **Add / Clone / Delete rows**, with the control-record trailer counters recomputed automatically.

## Quick start

**Try it online:** https://yaylbanh.github.io/AllFunds-Fixed-Width-QA-Tool/ — runs entirely in your
browser, nothing is uploaded anywhere.

Or run it locally:

1. Download `index.html` from this repo.
2. Open it in any modern browser (Chrome/Edge/Firefox) — no server, no build step.
3. Drop a fixed-width file onto it, click **Verify round-trip**, then explore the tabs.

See [`USAGE.md`](USAGE.md) for the full workflow.

## Record type coverage

AllFunds' fixed-width interface has dozens of record types (orders, prices, balances, rebates,
dividends, control files, …). This tool only decodes field-by-field the ones checked below — anything
else is **still safe to load and export** (bytes are preserved exactly, nothing is ever corrupted), it's
just shown as a read-only raw line instead of an editable table, because it hasn't been mapped yet.

- [x] **Record 24 / Subtype 01** — Confirmation (main use case: edit + export)
- [x] **Record 71** — Fund catalogue, all subtypes (01–06, 10–14, 20, 30, 40, 55)
- [x] **Record 98** — Control / trailer record (counters auto-recompute on edit)
- [ ] **Record 24 / Subtype 01 (dividend variant)** — recognized and shown read-only, not editable yet
- [ ] **Record 04** — Product
- [ ] **Record 05** — Contract
- [ ] **Record 06** — CCC/CCV
- [ ] **Record 08** — Commitment
- [ ] **Record 10** — Orders (in/out)
- [ ] **Record 11** — ETF orders (in/out)
- [ ] **Record 15** — Pledge
- [ ] **Record 20** — Price
- [ ] **Record 21** — MOC price
- [ ] **Record 23** — Partial confirmation
- [ ] **Record 25** — Initial load
- [ ] **Record 26** — Mergers
- [ ] **Record 27** — Splits
- [ ] **Record 28** — Currency redenomination
- [ ] **Record 30** — Balance
- [ ] **Record 34** — Balance (distributor-fund)
- [ ] **Record 35** — Subdistributor
- [ ] **Record 36** — Balance (end of month)
- [ ] **Record 50** — Balance live items
- [ ] **Record 60** — Settlement notice (incl. dividends variant)
- [ ] **Record 71** — ETF catalogue (different spec from the Fund catalogue above, despite same number)
- [ ] **Record 72** — Hedge fund collapse
- [ ] **Record 73** — EMT
- [ ] **Record 74** — PRIIPS-EPT
- [ ] **Record 75** — MIFID ex post
- [ ] **Record 77** — EET
- [ ] **Record 86** — Official standards
- [ ] **Record 90/91/93** — Rebates
- [ ] **Record 92** — Rebates full catalogue
- [ ] **Record 94/95/96** — Rebates consolidated (quarterly)
- [ ] **Record 98/99 — file-level ACK/NACK** — separate small response files, not the 800-byte batch
      format above; would need a different parser, not just a new schema entry

Want to help extend one of these? Open an issue/PR — each unchecked record just needs its byte-position
field map added to the `SCHEMAS` object in `index.html`, the same way Record 71 was done.
