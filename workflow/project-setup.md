---
title: Project Setup
status: done
score: 1.0
source: captain
id: 001
verdict: PASSED
started: 2026-06-28T01:16:09Z
completed: 2026-06-28T01:16:09Z
---

Scaffold the Clojure Stack Lite project and confirm it runs locally. This is the foundation everything else builds on.

## User Stories

- As a developer, I want the project to start with `bb run` so I can begin building immediately.

## Success

- `neil new` scaffold is committed
- `mise trust && mise install` completes without error
- `bb run` starts the server and http://localhost:3000 returns 200
- Project structure matches Clojure Stack Lite conventions

### Out of Scope

No business logic. No auth. No database schema. Just a working server.
