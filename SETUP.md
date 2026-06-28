# expense-sheet-clojure — Setup Checklist

## Tools

- [x] Homebrew (`brew --version` → 6.0.4)
- [x] mise (`mise --version` → 2026.6.14)
- [x] neil (`brew install babashka/brew/neil` → installed)
- [x] Java 21 (`mise use -g java@21` → 21.0.2)

## Project Scaffold

- [x] Scaffold with neil (`neil new io.github.abogoyavlensky/clojure-stack-lite expense-sheet-clojure :auth true`)
- [x] Install Clojure toolchain (`mise trust && mise install` → clojure, babashka, java temurin-21, tailwindcss, cljfmt, clj-kondo)
- [x] Confirm server starts (`bb clj-repl` → `(reset)` → Jetty on 0.0.0.0:8000, DB migrations applied, CSS watcher running)
