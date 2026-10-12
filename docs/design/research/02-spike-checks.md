# 02 - Spike checks (TWE-99)

Status: **no results recorded yet.** Fill in the Result column as each check is run and link the evidence; correct this note rather than adding a competing one.

Environment: a stock self-hosted Twenty in Docker (default settings, logic functions left disabled) and a Twenty Cloud workspace (TWE-97).

| # | Check | Pass when | Result |
|---|---|---|---|
| F1 | Strip the scaffold to schema-only, declare the entities in [01](01-companion-app-scope.md), `yarn twenty dev` syncs, `dev:build --tarball` works | Every entity syncs and renders (kanban groups, filters, file field) | not run |
| F2 | On stock self-hosted with functions disabled: install, upgrade twice (add a field, then remove it), uninstall | No step needs `LOGIC_FUNCTION_TYPE` | not run |
| F3 | Minimum server version, starting at the SDK's own floor (2.40.0) | A floor is found; set `engines.twenty` to it | not run |
| F4 | Publish from this repo with provenance, wait for catalog sync, claim on Cloud, install from a second workspace as admin | An admin installs from a link in a few minutes | not run |
| F5 | Uninstall with data present, on both servers | Behaviour matches [01](01-companion-app-scope.md); warning wording captured | not run |
| F6 | Name collision with an existing object or field | Clean failure with a clear message, or clean coexistence | not run |
| F7 | A Member's token creates and reads the app's objects and writes its Person fields | All succeed | not run |
| F8 | A Member tries to install | A clear permission error | not run |

Optional, v1.5 only: F9 (an unauthenticated HTTP-route function verifying an HMAC, on Cloud) and F10 (signing in with the packaged app's own OAuth client).

## Decision these feed (TWE-100)

| Result | Decision |
|---|---|
| F2 passes | The pack is the setup path on Cloud and self-hosted; remove schema creation from the phone. |
| F2 fails | The pack is Cloud-only at best; the phone creates objects through the Metadata API, under names the pack will never claim. |
| F1 partial | Ship what works; the phone creates the rest. Acceptable only if the split is small. |
| F6 clean failure | Prefix all API names with `nodpack`. |
| F5 deletes data | State it in the listing and in the phone's settings; offer export first. |
