---
title: Auth — Google Sign-in
status: ideation
score: 0.95
source: captain
id: 003
---

Restrict app access to two authorized household emails using Google Sign-in. Anyone else gets an access-denied page.

## User Stories

- As Karen, I want to sign in with my Google account so my expenses are tagged to me.
- As my husband, I want to sign in with my Google account so I can log expenses too.
- As anyone else, I want to see a clear "access denied" message if I try to log in.

## Success

- Google OAuth flow works end-to-end
- Only the two authorized emails can access the app
- Unauthorized users see an access-denied page
- Session persists across page refreshes
- Sign-out works

### Out of Scope

Password auth. Email/password reset. Admin UI for managing users.
