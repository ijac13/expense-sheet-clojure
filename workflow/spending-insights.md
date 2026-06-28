---
title: Spending Insights — Claude API
status: ideation
score: 0.7
source: captain
id: 009
---

AI-generated spending analysis using the Anthropic Claude API. Surfaces patterns and observations the user might not notice on their own.

## User Stories

- As Karen, I want a plain-language summary of my spending patterns so I don't have to interpret the charts myself.

## Success

- "Generate Insights" button sends current month's data to Claude API
- Response is a 3–5 bullet summary: top category, unusual spend, comparison to last month
- Result cached — don't re-call API on every page load
- Cache shows timestamp ("last generated: X minutes ago")

### Out of Scope

Budget recommendations. Multi-month trend analysis (v1).
