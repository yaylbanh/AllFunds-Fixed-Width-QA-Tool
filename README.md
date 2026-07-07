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
dividends, control files, …). This tool only decodes field-by-field the ones marked **✅ Supported**
below — anything else is **still safe to load and export** (bytes are preserved exactly, nothing is
ever corrupted), it's just shown as a read-only raw line instead of an editable table, because it
hasn't been mapped yet.

| Status | Meaning |
|---|---|
| ✅ **Supported** | Decoded field-by-field, editable, exports byte-exact |
| ⚠️ **Read-only** | Recognized, but fields can't be edited yet |
| ❌ **Not yet** | Not decoded — loads/exports fine as a raw, unreadable line |

| Record type | Status |
|---|---|
| Record 24 / Subtype 01 — Confirmation | ✅ Supported |
| Record 71 — Fund catalogue, all subtypes (01–06, 10–14, 20, 30, 40, 55) | ✅ Supported |
| Record 98 — Control / trailer record | ✅ Supported |
| Record 24 / Subtype 01 — Dividend variant | ⚠️ Read-only |
| Record 04 — Product | ❌ Not yet |
| Record 05 — Contract | ❌ Not yet |
| Record 06 — CCC/CCV | ❌ Not yet |
| Record 08 — Commitment | ❌ Not yet |
| Record 10 — Orders (in/out) | ❌ Not yet |
| Record 11 — ETF orders (in/out) | ❌ Not yet |
| Record 15 — Pledge | ❌ Not yet |
| Record 20 — Price | ❌ Not yet |
| Record 21 — MOC price | ❌ Not yet |
| Record 23 — Partial confirmation | ❌ Not yet |
| Record 25 — Initial load | ❌ Not yet |
| Record 26 — Mergers | ❌ Not yet |
| Record 27 — Splits | ❌ Not yet |
| Record 28 — Currency redenomination | ❌ Not yet |
| Record 30 — Balance | ❌ Not yet |
| Record 34 — Balance (distributor-fund) | ❌ Not yet |
| Record 35 — Subdistributor | ❌ Not yet |
| Record 36 — Balance (end of month) | ❌ Not yet |
| Record 50 — Balance live items | ❌ Not yet |
| Record 60 — Settlement notice (incl. dividends variant) | ❌ Not yet |
| Record 71 — ETF catalogue *(different spec from Fund catalogue above, despite same number)* | ❌ Not yet |
| Record 72 — Hedge fund collapse | ❌ Not yet |
| Record 73 — EMT | ❌ Not yet |
| Record 74 — PRIIPS-EPT | ❌ Not yet |
| Record 75 — MIFID ex post | ❌ Not yet |
| Record 77 — EET | ❌ Not yet |
| Record 86 — Official standards | ❌ Not yet |
| Record 90/91/93 — Rebates | ❌ Not yet |
| Record 92 — Rebates full catalogue | ❌ Not yet |
| Record 94/95/96 — Rebates consolidated (quarterly) | ❌ Not yet |
| Record 98/99 — file-level ACK/NACK *(separate small response files, not the 800-byte batch format above — needs a different parser, not just a new schema)* | ❌ Not yet |

Want to help extend one of these? Open an issue/PR — each "Not yet" record just needs its byte-position
field map added to the `SCHEMAS` object in `index.html`, the same way Record 71 was done.
