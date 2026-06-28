---
title: Expense History
status: ideation
score: 0.85
source: captain
id: 006
---

View, filter, search, edit, and delete past expenses. The audit trail and correction mechanism.

## User Stories

- As Karen, I want to see all expenses in reverse chronological order so I can review what was logged.
- As Karen, I want to filter by date range and category so I can find specific expenses.
- As a user, I want to edit a saved expense so I can fix mistakes.
- As a user, I want to delete an expense so I can remove duplicates.

## Success

- History list loads with amount, category emoji, note, user, date
- Filter by: date range, category, payer
- Search by note text
- Inline edit via HTMX — no full page reload
- Delete with confirmation
- Pagination or infinite scroll for large histories

### Out of Scope

Bulk delete. Export to CSV (that comes with Sheets sync).
