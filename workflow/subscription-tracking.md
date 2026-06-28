---
title: Subscription Tracking
status: ideation
score: 0.8
source: captain
id: 007
---

Log recurring subscriptions (Netflix, gym, etc.) so they appear automatically in history without manual entry each month.

## User Stories

- As Karen, I want to register a recurring subscription so it logs itself each billing cycle.
- As a user, I want to see upcoming subscriptions so I know what's due this month.

## Success

- Add subscription: name, amount, category, frequency (monthly/yearly), next date
- Subscription auto-creates an expense record on due date (or on server start if overdue)
- Edit and delete subscriptions
- Due-this-month list visible on dashboard

### Out of Scope

Bank import. Credit card statement parsing.
