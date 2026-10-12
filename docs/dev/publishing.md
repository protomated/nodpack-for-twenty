# Publishing and claiming

Twenty servers build their app marketplace from npm packages that carry the `twenty-app` keyword. Each server syncs the catalog hourly, so a published package appears on Cloud and on self-hosted servers without a separate submission. There is no review gate to be listed.

## One-time setup (TWE-123)

Package name: **`nodpack-for-twenty`**, unscoped, the same as the repo (TWE-122 asks for matching names). It was free on npm on 12 Oct 2026. Twenty does not require a scope; the marketplace shows the app's `displayName`, not the npm name. The `twenty-app` keyword is set in `package.json`. `publishConfig.access` is `public`, which also keeps a later move to a scope safe.

The Trusted Publisher setting only exists on a package that is already published, so the first release is manual:

1. Use Node 24.5+ (`nvm install && nvm use`, from `.nvmrc`), and make sure the npm account has 2FA on.
2. `npm login`, then from the repo root: `TWENTY_APP_PUBLISH_DISABLE_PROVENANCE=true yarn twenty app:publish`. This builds and runs `npm publish --access public`. Provenance only works from GitHub Actions, so `0.1.0` has none and cannot be claimed.
3. On npmjs.com open the package > Settings > Trusted Publisher and register GitHub Actions, user `protomated`, repository `nodpack-for-twenty`, workflow `publish.yml`, no environment. No npm token or GitHub secret is needed after this.
4. Bump the version (a published version can never be reused) and release from CI as below.
5. Record the name on TWE-97 and close TWE-123. Keep the npm login in the team's password manager.

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
