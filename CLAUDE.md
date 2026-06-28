# Expense Sheet Clojure

Personal expense tracker rebuilt in Clojure. Owner is a non-engineer — see global CLAUDE.md for communication style.

## Project context

- Rebuilt from https://github.com/ijac13/expense-sheet (original Next.js version)
- Tech stack chosen by Laurence (human), not agent — follow it, don't suggest alternatives
- Workflow managed by Spacedock at `workflow/`

## Gotchas

- `[alias]` in `.mise.toml` is deprecated — use `[tool_alias]`
- Always check `git rev-parse --show-toplevel` works before assuming git is initialized
