---
title: Category System
status: ideation
score: 0.9
source: captain
id: 005
---

Categories with emoji icons. Pre-seeded with the same set as the original app. Captain can add custom categories.

## User Stories

- As a user, I want to pick a category with an emoji icon so logging feels quick and visual.
- As Karen, I want to add a new category if I need one so the system fits my actual spending.

## Success

- Default categories are seeded in the DB on first run
- Each category has: name, emoji, sort order
- Category picker renders as a grid of emoji buttons
- Adding a new category persists to DB and appears immediately (HTMX swap)
- Categories are shared between both users

### Out of Scope

Category deletion (data integrity). Government category mapping (separate feature).
