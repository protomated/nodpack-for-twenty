# AGENTS.md

Instructions for coding agents (and a quick orientation for developers) working in this repo. Read `README.md` first for what this app is and where things live.

## Commands

```sh
yarn install                 # the committed yarn.lock pins exact versions
yarn twenty docker:start     # local Twenty on http://localhost:2020 (login tim@apple.dev / tim@apple.dev)
yarn twenty dev              # sync the app to the local server and watch for changes
yarn twenty plan             # show what a sync would create, change or delete
yarn lint                    # oxlint
yarn typecheck               # tsgo --noEmit -p tsconfig.spec.json
yarn test:unit               # unit tests (no server needed)
yarn test                    # integration tests (need the local Twenty server)
```

Run `yarn lint`, `yarn typecheck` and `yarn test:unit` before saying a change is done. Run `yarn test` too when the schema changed, and say so if the server was not available.

Node and Yarn versions: `.nvmrc` and `package.json` (`engines`, `packageManager`). `npm` is blocked on purpose; use Yarn.

## What this repo is

"Nodpack for Twenty" is a **schema-only** companion Twenty app: it declares the objects, fields, views and role that the Nodpack mobile app needs in a user's Twenty workspace (message templates, logged calls and texts). It has **no logic functions**, so it installs on a stock self-hosted server where logic functions are disabled by default. Background: [docs/design/research/01-companion-app-scope.md](docs/design/research/01-companion-app-scope.md).

This repo is **public**, because Twenty only lets a workspace claim an app that was published from a public GitHub repo with npm trusted publishing and provenance. The licence is Apache-2.0 (`LICENSE`). The Nodpack mobile app lives in a separate private repo, protomated/app-nodpack.

## Layout and boundaries

- `src/application-config.ts`, `src/default-role.ts`: the application and its default role. `src/constants/universal-identifiers.ts` holds every universal identifier; add new IDs there.
- `src/objects/`, `src/fields/`, `src/views/`, `src/navigation-menu-items/`, `src/roles/`: the schema pack. Create each with `yarn twenty dev:add <type>` (table below), not by hand.
- `src/__tests__/`: unit tests (`*.test.ts`) and integration tests (`*.integration-test.ts`).
- `public/`: public assets; the logo is Nodpack's own.
- `locales/`: translations.
- `docs/design/research/NN-*.md`: numbered notes. Add a new number rather than rewriting an old note; update an old note only to correct it, and say so.
- `docs/dev/`: developer guides (publishing). `docs/gtm/`: naming rules for the app's public name.
- `.github/workflows/`: `ci.yml` (spawns an ephemeral Twenty and runs the tests on every push and PR), `cd.yml` (deploy-and-install check against an ephemeral Twenty on push to `main`, or on a PR labelled `deploy`), `publish.yml` (npm with provenance, on a `v*` tag or a manual run).
- Never hand-edit `generated/` or `*.d.ts` files; they are git-ignored.

The scaffold still contains `src/logic-functions/health-check.ts`, `src/front-components/main-page.tsx`, `src/page-layouts/` and `src/navigation-menu-items/main-page.*`. A schema-only pack must **remove all four** before install tests on a functions-disabled server, or the test measures the wrong thing.

## Twenty app rules

- Names: prefix app-owned API names with `nodpack` (for example `nodpackTemplate`) so they never collide with an object or field an admin already made. Do not name an object `callRecording`; Twenty has a standard object by that name (use `phoneCall`).
- Uninstalling an app **deletes its data** (destroyed objects drop their rows and the values of app-owned fields). Anything user-facing must say so; do not add a feature that stores irreplaceable data here without an export path.
- `package.json` `version` must strictly increase per release. A sync deletes every entity the app owns that the new manifest no longer declares, so removing an entity is a data-loss change: call it out in the changelog.
- Set `engines.twenty` to the oldest server version that has been tested, not the newest.
- Do not add logic functions, front components or install hooks without a decision recorded in `docs/design/research/` (they break the "works on stock self-hosted" promise).
- Never claim the app is "Vetted" or "official". Use Nodpack's own logo, never Twenty's. See [docs/gtm/01-naming-rules.md](docs/gtm/01-naming-rules.md).
- All generated UUIDs must be valid UUID v4.

### Common pitfalls

- Creating a view without a navigation menu item for it, so it never shows in the left sidebar.
- Building a front component that scrolls instead of fitting its widget's fixed width and height (unless it is meant for a canvas tab).

### Creating entities

Use `yarn twenty dev:add`; it generates the required IDs.

| Entity type          | Command                                  | Generated file                        |
| -------------------- | ---------------------------------------- | ------------------------------------- |
| Object               | `yarn twenty dev:add object`             | `src/objects/<name>.ts`               |
| Field                | `yarn twenty dev:add field`              | `src/fields/<name>.ts`                |
| Logic function       | `yarn twenty dev:add logicFunction`      | `src/logic-functions/<name>.ts`       |
| Front component      | `yarn twenty dev:add frontComponent`     | `src/front-components/<name>.tsx`     |
| Role                 | `yarn twenty dev:add role`               | `src/roles/<name>.ts`                 |
| Skill                | `yarn twenty dev:add skill`              | `src/skills/<name>.ts`                |
| Agent                | `yarn twenty dev:add agent`              | `src/agents/<name>.ts`                |
| View                 | `yarn twenty dev:add view`               | `src/views/<name>.ts`                 |
| Navigation menu item | `yarn twenty dev:add navigationMenuItem` | `src/navigation-menu-items/<name>.ts` |
| Page layout          | `yarn twenty dev:add pageLayout`         | `src/page-layouts/<name>.ts`          |
| Page layout tab      | `yarn twenty dev:add pageLayoutTab`      | `src/page-layout-tabs/<name>.ts`      |
| Command menu item    | `yarn twenty dev:add commandMenuItem`    | `src/command-menu-items/<name>.ts`    |
| View field           | `yarn twenty dev:add viewField`          | `src/view-fields/<name>.ts`           |
| Connection provider  | `yarn twenty dev:add connectionProvider` | `src/connection-providers/<name>.ts`  |

For a schema-only pack, only Object, Field, Role, View, View field and Navigation menu item apply.

## Twenty documentation

- Getting started: [quick start](https://docs.twenty.com/developers/extend/apps/getting-started/quick-start.md), [concepts](https://docs.twenty.com/developers/extend/apps/getting-started/concepts.md), [project structure](https://docs.twenty.com/developers/extend/apps/getting-started/project-structure.md), [local server](https://docs.twenty.com/developers/extend/apps/getting-started/local-server.md), [scaffolding](https://docs.twenty.com/developers/extend/apps/getting-started/scaffolding.md), [troubleshooting](https://docs.twenty.com/developers/extend/apps/getting-started/troubleshooting.md)
- Config: [overview](https://docs.twenty.com/developers/extend/apps/config/overview.md), [application](https://docs.twenty.com/developers/extend/apps/config/application.md), [roles](https://docs.twenty.com/developers/extend/apps/config/roles.md), [install hooks](https://docs.twenty.com/developers/extend/apps/config/install-hooks.md), [public assets](https://docs.twenty.com/developers/extend/apps/config/public-assets.md)
- Data: [overview](https://docs.twenty.com/developers/extend/apps/data/overview.md), [objects](https://docs.twenty.com/developers/extend/apps/data/objects.md), [extending objects](https://docs.twenty.com/developers/extend/apps/data/extending-objects.md), [relations](https://docs.twenty.com/developers/extend/apps/data/relations.md)
- Logic: [overview](https://docs.twenty.com/developers/extend/apps/logic/overview.md), [logic functions](https://docs.twenty.com/developers/extend/apps/logic/logic-functions.md), [skills and agents](https://docs.twenty.com/developers/extend/apps/logic/skills-and-agents.md), [connections](https://docs.twenty.com/developers/extend/apps/logic/connections.md)
- Layout: [overview](https://docs.twenty.com/developers/extend/apps/layout/overview.md), [views](https://docs.twenty.com/developers/extend/apps/layout/views.md), [navigation menu items](https://docs.twenty.com/developers/extend/apps/layout/navigation-menu-items.md), [page layouts](https://docs.twenty.com/developers/extend/apps/layout/page-layouts.md), [front components](https://docs.twenty.com/developers/extend/apps/layout/front-components.md), [command menu items](https://docs.twenty.com/developers/extend/apps/layout/command-menu-items.md)
- Operations: [overview](https://docs.twenty.com/developers/extend/apps/operations/overview.md), [CLI](https://docs.twenty.com/developers/extend/apps/operations/cli.md), [testing](https://docs.twenty.com/developers/extend/apps/operations/testing.md), [publishing](https://docs.twenty.com/developers/extend/apps/operations/publishing.md)
- Rich example: [postcard](https://github.com/twentyhq/twenty/tree/main/packages/twenty-apps/examples/postcard)

## Public repo: secrets, privacy and evidence

Everything committed here is world-readable.

- Secrets live only in git-ignored files (`.env*`, `.mcp.json`). Read them by key name; never print, log, commit or paste their values anywhere, Nifty included.
- No real customer or prospect names, emails, phone numbers, message text or signed URLs in code, tests, fixtures, docs or screenshots. Use sample data.
- Do not copy internal material from the private app repo (market research, pricing, competitor notes, launch plans, legal reasoning). Link to a Nifty task by number if a reader needs the history.
- Anything against Twenty Cloud or someone else's workspace is read-only unless the person running the task approves the specific write. The local server (`yarn twenty docker:start`) is the only default write target.

## Ask before acting outside the repo

Confirm with the person you are working for before: pushing or opening pull requests, writing to Nifty (task edits, comments, status changes), publishing to npm, pushing a version tag (it triggers `publish.yml`), and registering or removing a trusted publisher, OAuth client or remote on a shared service.

## What "done" looks like

`yarn lint`, `yarn typecheck` and `yarn test:unit` pass (and `yarn test` when the schema changed); the affected docs and research notes are updated in the same change; the report says what was verified and what was not. A person reviews the change before it merges; an agent does not approve its own work.

## Commits and branches

- Branch per task: `twe-<n>/<short-name>`; merge into `main` with a merge commit.
- Message: `TWE-<n>: <what changed, in plain words>`, then a body explaining why and what was verified.
- **No AI attribution lines** in commit messages or pull requests (no "Co-Authored-By" or "Generated with" footers).

## Nifty (task tracking)

Project **TWE** ("Nodpack (for Twenty CRM · works with Quo)"). Use the Nifty MCP tools. Before writing code, fetch the whole task: description, comments (scope changes often live there), subtasks and checklists, linked tasks. Treat **Done when** as the definition of done; before saying you're finished, state which items are met, which are deliberately out of scope and which are unmet or unclear, and ask rather than guess. If the task can't be fetched, say so instead of working from the title.

Task descriptions follow **Context / What to do / Dependencies / Done when**, then dated **Update, <date>** paragraphs, then the **Ref** line. Add findings as a new dated Update before the Ref line; do not rewrite the original sections. A task closes only when its "Done when" is met; tasks blocked on someone outside the team go to the **Waiting for** status with a comment saying what is awaited.

Tasks that touch this repo: TWE-97 (spike environment), TWE-122 (this repo), TWE-123 (npm package name and trusted publishing), TWE-99 (the schema-pack spike), TWE-100 (decision review), TWE-102 (workspace setup build).

## Writing

Docs, notes and task text: plain, specific and short. Lead with the answer, then the evidence. Prefer tables for comparisons and numbers with units. Say what was verified and what was not.
