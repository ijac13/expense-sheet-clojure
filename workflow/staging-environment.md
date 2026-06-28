---
title: Staging Environment
status: ideation
score: 0.9
source: captain
id: 012
---

A deployed staging environment for verify-stage testing. The verify agent needs a live URL to verify against — not just localhost. Deployed separately from production with its own SQLite database.

## User Stories

- As the FO, I want a staging URL so verify agents can run curl checks against a live server.
- As Karen, I want staging to be visually distinct from production so I never confuse them.

## Success

- Staging deploys via a single command (e.g. `bb deploy:staging`)
- Staging URL is accessible from the internet
- Staging has a visible banner: "STAGING — not production"
- Staging uses a separate SQLite database
- Verify agents document the staging URL in every verify report

### Out of Scope

CI/CD auto-deploy. Preview environments per PR.
