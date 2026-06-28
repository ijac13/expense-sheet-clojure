# expense-sheet-clojure

_This application is generated with [clojure-stack-lite](https://github.com/abogoyavlensky/clojure-stack-lite)._

## Why this repo

I build my own [expense-sheet](https://github.com/ijac13/expense-sheet) — a household expense tracker — with Claude and [Spacedock](https://spacedock.dev). As a non-engineer builder, I always say yes to whatever the agent suggests on technical stacks and copy-paste the commands it gives me.

My friend Laurence has different thoughts about how agents suggest tech stacks. Since I don't have any preference, I'd rather say yes to a human than an agent. So this is the trial repo — a rebuild of my expense-sheet using the stack he suggests: [Agent-ready stack](https://replware.substack.com/p/agent-ready-stack).


## Development

Install Java, Clojure, Babashka, TailwindCSS and other tools manually or via [mise](https://mise.jdx.dev/):

```shell
mise trust && mise install
```

Check all available commands:

```shell
bb tasks
```

Run lint, formatting, tests and checking outdated dependencies:

```shell
bb check
```

Run server with built-in REPL from terminal:

> [!NOTE]
> If you're using PostgreSQL, [Docker](https://docs.docker.com/engine/install/) should be installed

 ```shell
bb clj-repl 
(reset)
````

Once server is started, it will automatically reload on code changes in the backend and TailwindCSS classes.
The server should be available at `http://localhost:8000`.

## Update assets

The idea is to vendor all js-files in the project repo eliminating build step for js part.

Once you want to update the version of AlpineJS, HTMX or add a new asset, edit version in bb.edn file at `fetch-assets` and run:

```shell
bb fetch-assets
```

Your assets will be updated in `resources/public` folder.

## Deployment

For detailed deployment instructions, refer to the documentation:

- [Kamal](https://stack.bogoyavlensky.com/docs/lite/kamal)

