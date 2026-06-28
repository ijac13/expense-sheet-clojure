---
title: Expense Entry
status: ideation
score: 1.0
source: captain
id: 004
---

Log an expense with amount, category, and optional note. The core interaction — used many times a day on mobile. Must be fast and frictionless.

## User Stories

- As Karen, I want to tap a category, enter an amount, and save in under 5 seconds so logging doesn't feel like a chore.
- As my husband, I want the same UI so we both use the same pattern.

## Success

- Calculator-style amount input works on mobile
- Category picker shows emoji + name
- Note field is optional
- Expense saves to SQLite and appears in history immediately (HTMX swap)
- Logged-in user is tagged automatically — no manual selection
- Works offline-tolerant (form submit, not SPA)

### Out of Scope

Google Sheets sync (that's a later feature). Receipt photo upload. Location tagging.
