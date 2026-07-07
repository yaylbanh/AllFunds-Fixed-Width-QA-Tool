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

See [`USAGE.md`](USAGE.md) for the full workflow and [`SPEC_COVERAGE.md`](SPEC_COVERAGE.md) *(local
only, not tracked in git — see `.gitignore`)* for which AllFunds record types are decoded vs. not yet.

## Status

Actively used for CCBI distributor file QA. Currently decodes Record 24/01 (confirmation), all Record 71
(fund catalogue) subtypes, and the Record 98 control/trailer. Everything else round-trips byte-exact but
is shown read-only ("raw") until a schema is added for it.
