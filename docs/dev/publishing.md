# Publishing and claiming

Twenty servers build their app marketplace from npm packages that carry the `twenty-app` keyword. Each server syncs the catalog hourly, so a published package appears on Cloud and on self-hosted servers without a separate submission. There is no review gate to be listed.

## One-time setup (TWE-123)

1. Choose the package name. Provisional: `nodpack-for-twenty` (matches the repo). It must also carry the `twenty-app` keyword in `package.json`.
2. Reserve it under a Protomated-owned npm account or organisation with 2FA on. Keep the login in the team's password manager.
3. On npmjs.com, open the package > Settings > Trusted Publisher and register this repository and the `publish.yml` workflow. The first publish of a new name may need to be done once before the trusted publisher can be registered; record what was done.
4. Make sure `package.json` has the real name, `license: Apache-2.0` and the keyword, and regenerate `yarn.lock` (`publish.yml` runs `yarn install --immutable`).

## Releasing

1. Update `CHANGELOG.md` and bump `version` in `package.json`. It must be higher than any published version.
2. If an entity was removed, say so in the changelog: the sync deletes it, with its data, in every workspace that upgrades.
3. Merge to `main`, then push a tag: `git tag v<version> && git push --tags`. `publish.yml` publishes with provenance. Pushing the tag publishes to a public registry; confirm with the person who owns the release first.
4. Check the provenance badge on npmjs.com and that the app appears in a test workspace's marketplace (allow up to an hour for the catalog sync; turn "Vetted only" off).

## Claiming on a Twenty server

Claiming is what lets a workspace manage the app (variables, server routes). Requirements, from Twenty's docs:

- The package was published from a **public GitHub repository** by GitHub Actions with npm trusted publishing and provenance (a private repo can publish, but the package cannot be claimed).
- The person claiming has the Applications and MCP & APIs permissions, and is the repo's personal owner or an owner of its GitHub organisation (collaborator access is not enough).
- Ownership is per Twenty instance: claiming on Cloud does not claim on a self-hosted server.

Flow: Settings > Applications > Developer > Claim an application, look up the package, then "Claim with GitHub".

## Installing (admins)

Settings > Applications, find the app (turn off "Vetted only"; this app is not and must not be labelled Vetted), install, review the role. Members cannot install. Uninstalling deletes the app's data.

## Versions

- `engines.twenty` is a semver range checked at install time against the server version. Set it to the oldest version tested (see the F3 check in [02-spike-checks.md](../design/research/02-spike-checks.md)).
- Workspaces with auto-upgrade enabled follow new versions in the background; others see "upgrade available". Downgrades are rejected.
