---
title: Database Schema
status: ideation
score: 1.0
source: captain
id: 002
---

Define the SQLite schema for the app — expenses, subscriptions, and users. This is the data contract everything else depends on. Get it right before building any features.

## User Stories

- As a developer, I want a well-defined schema so I can write HoneySQL queries without guessing column names.

## Success

- Migrations run cleanly on a fresh database
- Tables: expenses, subscriptions, users
- Expenses: id, user_id, amount, category_id, note, date, created_at
- Subscriptions: id, user_id, name, amount, category_id, frequency, next_date
- Categories: id, name, emoji, parent_id (for gov mapping later)
- Users: id, email, display_name
- All tables accessible via HoneySQL queries in a REPL test

### Out of Scope

No Google Sheets sync. No migration UI. Schema only.
