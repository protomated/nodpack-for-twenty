# Nodpack for Twenty

A companion [Twenty](https://twenty.com) app for [Nodpack](https://nodpack.com), the mobile CRM app for Twenty. It is a **schema-only pack**: it sets up the objects, fields, views and role that Nodpack needs in a Twenty workspace (message templates, logged calls and texts), so every workspace gets the same defaults. It ships no logic functions, so it installs on a stock self-hosted Twenty as well as on Twenty Cloud.

Nodpack is an independent product of Protomated. It is not affiliated with or endorsed by Twenty.

Status (Oct 2026): scaffolded, not yet modelled. The spike that decides whether this pack is the setup path for Nodpack (TWE-99, TWE-100) is in progress. Work is tracked in Nifty, project **TWE**.

## What is here

| Path | What |
|---|---|
| `src/` | The app: application config, default role, constants; objects, fields, views and navigation items go here as they are added. |
| `src/__tests__/` | Unit tests and integration tests (the latter need a Twenty server). |
| `public/`, `locales/` | App logo (Nodpack's own) and translations. |
| `docs/design/research/` | Numbered notes: what the app is for, what it may contain, and what the spike must prove. |
| `docs/dev/` | Developer guides: [publishing and claiming](docs/dev/publishing.md). |
| `docs/gtm/` | [Naming rules](docs/gtm/01-naming-rules.md) for the app's public name. |
| `.github/workflows/` | CI, a deploy-and-install check, and npm publishing with provenance. |

Documentation index: [docs/README.md](docs/README.md). Coding agents: see [AGENTS.md](AGENTS.md).

## Getting started

Setup steps are in [SETUP.md](SETUP.md). In short:

```sh
yarn install
yarn twenty docker:start   # local Twenty at http://localhost:2020
yarn twenty dev            # sync the app and watch for changes
```

Check your work with `yarn lint`, `yarn typecheck`, `yarn test:unit` and (with the server running) `yarn test`.

## Publishing

The `Publish` workflow publishes to npm with provenance using [npm trusted publishing](https://docs.npmjs.com/trusted-publishers). Publishing from this public repo is also what lets a Twenty admin claim the app. Steps, owners and gotchas: [docs/dev/publishing.md](docs/dev/publishing.md).

## Working on it

- Branches: `twe-<n>/<short-name>` for the Nifty task, merged into `main` with a merge commit.
- Commit messages: `TWE-<n>: <what changed, in plain words>`, with a body that says why.
- This repo is public. Do not commit secrets, real customer data or internal planning material.

## Changelog

Notable changes are documented in [CHANGELOG.md](CHANGELOG.md).

## Licence

Apache-2.0. See [LICENSE](LICENSE).

## Learn more

- [Twenty Apps documentation](https://docs.twenty.com/developers/extend/apps/getting-started/quick-start)
- [twenty-sdk CLI reference](https://www.npmjs.com/package/twenty-sdk)
- [Twenty Discord](https://discord.gg/cx5n4Jzs57)
