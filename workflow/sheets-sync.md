---
title: Google Sheets Sync
status: ideation
score: 0.65
source: captain
id: 010
---

Write-through sync from SQLite to Google Sheets. Every expense saved in the app also appends a row to the household Google Sheet — so Karen and her husband can view, analyze, and export the raw data in Sheets.

## User Stories

- As Karen, I want every logged expense to appear in our shared Google Sheet automatically so I can run analysis without exporting.
- As my husband, I want to see Karen's expenses in the same sheet so we have full household transparency.

## Success

- On every expense save, a row is appended to the Expenses tab in the configured Google Sheet
- Columns: date, amount, category, note, payer
- Sync uses a service account (server-to-Sheets, no user OAuth needed at runtime)
- Sync failure does not block the expense from saving to SQLite (fire-and-forget)
- Sync errors are logged server-side

### Out of Scope

Two-way sync. Editing in Sheets reflects in app. Historical backfill (that's a separate data-migration feature).
