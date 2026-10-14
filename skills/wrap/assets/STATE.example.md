# STATE

Read this first. It is the current state of the work, not its history.
Updated: 2026-03-14

## Current focus

Moving the invoice export from CSV to Parquet without breaking the nightly report.

## Last completed

- 2026-03-14: `export/parquet_writer.py` writes the same 41 columns as the CSV path; 212 tests pass.
- 2026-03-13: Agreed the report keeps reading CSV until Parquet has run clean for 7 nights.

## Next action

Run `make nightly-dry` against yesterday's data and compare row counts with the CSV output.

## Open decisions

- Keep the CSV export after the switch? · default if nobody answers: keep it for 30 days.

## Do not re-derive

- The `amount` column is stored in cents, not currency units. The old docs are wrong.
- `pyarrow` 17 changed the default timestamp unit; we pin `coerce_timestamps="ms"`.

## Waiting

- Uncommitted: `export/parquet_writer.py`, `tests/test_parquet_writer.py` (from the repo root)
- For the user: approve the extra storage bucket in the cloud console.
