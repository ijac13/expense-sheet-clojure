---
commissioned-by: spacedock@0.22.0
entity-type: feature
entity-label: feature
entity-label-plural: features
id-style: sequential
stages:
  defaults:
    worktree: false
    concurrency: 2
  states:
    - name: ideation
      initial: true
      gate: true
    - name: spec
      gate: true
      feedback-to: ideation
    - name: build
      worktree: true
    - name: agent-verify
      fresh: true
      feedback-to: build
    - name: captain-review
      gate: true
      feedback-to: build
    - name: staging-deploy
      fresh: true
    - name: done
      terminal: true
---

# Expense Sheet — Clojure Edition

A household expense tracker rebuilt on Clojure Stack Lite — the same product as the original, with a better stack. Two users, shared Google Sheet as the data view, built with Clojure + HTMX + HoneySQL + SQLite.

## What's different from the original

The first expense-sheet (Next.js + Firebase + Google Sheets) worked but had structural costs: 3 separate moving parts (Firebase Auth, Cloud Functions, Hosting), async error paths that were hard for agents to trace, and React client state that made verification slow.

This rebuild uses Clojure Stack Lite:
- **SQLite** as the primary data store (HoneySQL queries, no ORM)
- **Google Sheets sync** as a write-through feature (added mid-workflow, not day 1)
- **HTMX** for the frontend — server-driven, no client state, verifiable with `curl`
- **Single Clojure server** — no split between frontend build and backend functions
- **Blocking IO** — errors propagate to one place, agents debug one location

## Feature build sequence

Build in this order to avoid rework:

1. Project setup + database schema
2. Auth (Google Sign-in, 2 emails)
3. Expense entry (core loop: log → persist → display)
4. Category system
5. Expense history (list, edit, delete)
6. Subscription tracking
7. Reports
8. Spending insights (Claude API)
9. Google Sheets sync
10. Bilingual support
11. Staging environment

Don't build UI polish before core data persistence is solid. Don't build reports before history works.

## Stage Flow

```
ideation (gate)
  └─ spec (gate, feedback-to: ideation)
       └─ build (worktree)
            └─ agent-verify (fresh, curl/localhost, auto-advance or back to build)
                 └─ captain-review (gate, mobile on LAN, approve or back to build)
                      └─ staging-deploy (fresh, auto — staging IS the live app)
                           └─ done (merged to main)
```

## Environments

There is one environment: **staging**. This is the live app Karen and her husband use daily. There is no separate production.

When the original expense-sheet data needs to be migrated, that is a separate feature (`data-migration`) filed and worked through the workflow when ready — not a workflow stage.

## File Naming

Each feature is a markdown file named `{slug}.md` — lowercase, hyphens, no spaces.

## Schema

Every feature file has YAML frontmatter.

### Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier, zero-padded sequential (e.g., `001`) |
| `title` | string | Human-readable feature name |
| `status` | enum | One of: `ideation`, `spec`, `build`, `agent-verify`, `captain-review`, `staging-deploy`, `done` |
| `source` | string | Where this feature came from |
| `started` | ISO 8601 | When active work began |
| `completed` | ISO 8601 | When the feature reached terminal status |
| `verdict` | enum | PASSED or REJECTED — set at final stage |
| `score` | number | Priority score, 0.0–1.0 |
| `worktree` | string | Worktree path while a build agent is active, empty otherwise |
| `pr` | string | GitHub PR reference (e.g., `#12`) |

## Stages

### `ideation`

Captain captures an observation, a pain point, or a goal. No agent work here — this is raw thinking. The captain gates this stage: only features worth speccing move forward.

- **Inputs:** Captain's observations, pain points, or goals
- **Outputs:** Clear statement of what problem this feature solves, why it matters, what success looks like
- **Good:** Grounded in real use. Concrete enough to spec.
- **Bad:** Vague, no real use case, or technical without a user perspective.

### `spec`

Agent takes the ideation content and writes a structured spec. Captain reviews before any code is written.

For data features, the spec must include:
- SQLite table schema (as a table: column, type, constraints, notes)
- Key HoneySQL queries (at least the main read and write)
- HTMX fragment description (trigger → route → what's returned)
- Flow diagram for non-trivial interactions

Use tables for AC (criterion + how to verify), user stories, schema, edge cases, and HTMX triggers. Use a flow diagram for any interaction with more than two steps. Prose is for context only — facts go in structure.

- **Inputs:** Approved ideation body
- **Outputs:** Completed spec — goal, user stories, AC table, flow, schema table, queries, HTMX table, edge cases table, out of scope
- **Good:** AC are binary and have a named curl command. Schema has every column. Edge cases cover real household messiness.
- **Bad:** AC that require judgment. Missing schema. Prose where a table would be clearer. Scope that bleeds into other features.

### `build`

Agent reads the approved spec, plans the implementation, writes the code, and self-checks against every AC before marking complete. Runs in an isolated branch.

Build sequence within a feature:
1. Database: migration + schema
2. Data functions: HoneySQL queries
3. Routes: Clojure handlers
4. HTMX views: server-rendered HTML fragments
5. AC self-check: run `curl` against the local server to verify each AC

- **Inputs:** Approved spec
- **Outputs:** Working implementation on a dedicated branch, every AC checked with evidence
- **Good:** Each AC explicitly checked off with `curl` output or test output. No regressions.
- **Bad:** AC left unchecked. Code that interprets rather than implements the spec.

### `agent-verify`

A fresh agent (no context from build) starts the server and verifies every AC with live `curl` calls. No gate — if all ACs pass it auto-advances to `captain-review`. If any AC fails it routes back to `build`; the captain is not involved.

**Every AC must be verified with a live HTTP call.** This is a Clojure + HTMX project — server-rendered responses mean `curl` is sufficient. No browser simulation needed.

```bash
# Typical verify pattern
curl -s -X POST http://localhost:8000/expenses \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "amount=150&category=food&note=lunch"
# Response is an HTML fragment — inspect it directly
```

#### Live Evidence Requirement (mandatory)

A report is invalid — and the FO must reject it and re-dispatch — if it contains no live evidence.

Every report must include, for each AC:
- The exact `curl` command run
- The HTTP status code returned
- The response body (HTML fragment or JSON)

"I read the code and it looks correct" is not evidence. Code inspection belongs in `build`. If the agent cannot run Bash commands, it must set `verdict: REJECTED` immediately.

#### Server startup check

Before verifying any AC, confirm the server is running:
```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:8000/health
# Must return 200
```

If the server won't start, set `verdict: REJECTED` with the startup error — routes back to build.

#### Mandatory Secrets Check

Before marking complete, confirm:
- No `.env` files with real values committed
- No API keys, tokens, or passwords in any committed file
- No personal data in test fixtures or comments

### `captain-review`

Captain tests the feature on their mobile phone while the agent watches the server log. Because this is an HTMX app, every mobile action is an HTTP request — the server log is a complete record of what the captain does on mobile. The agent tails it in real-time so when the captain reports a problem on desktop, the agent already has the context.

#### Agent setup

1. Get the LAN IP: `ipconfig getifaddr en0`
2. Start the server with output tee'd to a log file. This project uses the Clojure REPL — start it and redirect output:
   ```bash
   bb clj-repl 2>&1 | tee /tmp/expense-review.log &
   # then send (reset) to start the system
   ```
   Or run the server directly via clj:
   ```bash
   clj -A:dev -e "(require 'user)(user/reset)" 2>&1 | tee /tmp/expense-review.log &
   ```
3. Confirm reachable: `curl -s -o /dev/null -w "%{http_code}" http://0.0.0.0:8000/health`
4. Tell the captain: `http://[mac-ip]:8000` — open this on your phone
5. Watch the log: `tail -f /tmp/expense-review.log`

#### During review

The agent stays live, watching the log. Every tap the captain makes on mobile appears as a log line — method, path, status, response time. When the captain reports something on desktop, the agent correlates it with the log:

| Captain says | Agent checks |
|---|---|
| "the save button didn't work" | Was there a `POST /expenses`? What status did it return? |
| "the page loaded blank" | Was there a `GET /expenses 200` or a 500? |
| "it felt slow" | What was the response time on that last request? |
| "I see an error" | What error body did the server return? |

The agent reports what it saw in the log for each issue the captain raises. This replaces guessing with evidence.

#### Gate outcome

- **Approve** → routes to `staging-deploy`
- **Reject** → captain states what to fix; agent records the feedback; routes back to `build`

The FO gate summary shows: LAN URL + one-line curl result per AC from agent-verify. Captain does not re-run curl checks — just use it on mobile and report.

### `staging-deploy`

Auto-deploys to the public staging environment after captain approval. No gate — if the deploy succeeds, it advances to `done`. If it fails, the FO surfaces the error to the captain.

- **Agent's job:** Run the deploy command (e.g. `fly deploy --config fly.staging.toml`), confirm the public staging URL returns 200, report the live URL
- **On success →** advances to `done`
- **On failure →** FO surfaces the deploy error to captain; captain decides whether to fix-and-retry or investigate

### `done`

Feature is merged to main. Staging is the live app — there is no separate production environment. When a feature reaches `done`, it is live and in use.

## Spec Template

Use tables, lists, and diagrams wherever they make things clearer. Prose is for context; structure is for facts.

```markdown
## Spec

### Goal
One sentence: what this feature does and why it exists.

### User Stories

| As | I want | So that |
|----|--------|---------|
| Karen | to tap a category and enter an amount | logging takes under 5 seconds |
| my husband | the same UI | we use the same pattern |

### Acceptance Criteria

| # | Criterion | How to verify |
|---|-----------|---------------|
| AC-1 | Expense saves to DB | `curl POST /expenses` returns 200 + HTML fragment |
| AC-2 | Logged user is tagged | row in DB has correct `user_id` |
| AC-3 | ... | ... |

### Flow

```
User taps category → picks amount → submits form
  └─ POST /expenses
       ├─ insert into SQLite
       ├─ return HTMX fragment (new row in history)
       └─ [later] fire-and-forget Sheets sync
```

### Schema

| Column | Type | Constraints | Notes |
|--------|------|-------------|-------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | |
| user_id | INTEGER | NOT NULL, FK → users.id | |
| amount | INTEGER | NOT NULL | stored in cents |
| category_id | INTEGER | NOT NULL, FK → categories.id | |
| note | TEXT | | optional |
| date | DATE | NOT NULL | user-selected date |
| created_at | DATETIME | DEFAULT CURRENT_TIMESTAMP | |

### Key Queries (HoneySQL)

```clojure
; Insert expense
{:insert-into :expenses
 :values [{:user_id user-id :amount amount :category_id cat-id :note note :date date}]}

; Recent expenses for history
{:select [:e.id :e.amount :e.note :e.date :c.name :c.emoji :u.display_name]
 :from [[:expenses :e]]
 :join [[:categories :c] [:= :e.category_id :c.id]
        [:users :u]      [:= :e.user_id :u.id]]
 :order-by [[:e.created_at :desc]]
 :limit 50}
```

### HTMX Fragment

| Trigger | Route | Returns |
|---------|-------|---------|
| `hx-post="/expenses"` on form submit | `POST /expenses` | `<tr>` row to prepend into history list |
| `hx-get="/expenses"` on page load | `GET /expenses` | full history `<tbody>` |

### Edge Cases

| Scenario | Expected behaviour |
|----------|--------------------|
| Amount is zero or negative | Return 400, show inline error |
| Category not selected | Form validation blocks submit |
| DB write fails | Return 500, show error toast, no Sheets sync attempt |
| Two users submit simultaneously | Each row gets correct `user_id`, no collision |

### Out of Scope

- Google Sheets sync (separate feature)
- Receipt photo upload
- Location tagging
```

## Feature Template

```yaml
---
id:
title: Feature name here
status: ideation
source:
started:
completed:
verdict:
score:
worktree:
pr:
---

One sentence: why this feature exists and what problem it solves.

## User Stories

- As [user], I want [action] so that [outcome].

## Success

What done looks like — specific, scoped to this feature only.

- Criterion 1
- Criterion 2

### Out of Scope

What this feature explicitly does not cover.

## Plan

Architecture decisions, constraints, open questions.
```

## Commit Discipline

- Commit status changes at dispatch and merge boundaries
- Commit feature body updates when substantive
- Never commit `.env` files or secrets
