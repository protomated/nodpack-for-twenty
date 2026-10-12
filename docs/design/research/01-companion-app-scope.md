# 01 - Companion app scope

Status: draft, Oct 2026. Source: the Twenty app platform docs and source as read on 1 Oct 2026 (Twenty v2.44.0), not yet confirmed by running this app. Open checks are in [02-spike-checks.md](02-spike-checks.md).

## Answer

"Nodpack for Twenty" is planned as a declarative, schema-only Twenty app (the working assumption from TWE-122 and TWE-99; not decided until TWE-100). It would create what the Nodpack mobile app needs to store in a workspace and nothing that needs server-side code. If the spike passes, it is the way a workspace gets set up; if not, the phone creates the same objects through the Metadata API and this repo is retired.

## In scope

| Entity | Declared with | Notes |
|---|---|---|
| Message template object | `defineObject` | Reusable text for email and text follow-ups. |
| Logged call object | `defineObject` | Name it `phoneCall` (prefixed per the naming rule); Twenty already has a standard `callRecording` object. |
| Logged text object | `defineObject` | Same prefix rule. |
| Fields on Person and Company | `defineField` with the standard object's identifier | For example "where we met". The only way to add fields to objects the app does not own. |
| Relations | `defineField` with `FieldType.RELATION` | From the app's objects to Person and Company. |
| Views | `defineView` | Additional to the server-owned default view. |
| Navigation items | `defineNavigationMenuItem` | One for every view that should appear in the sidebar. |
| Role | `defineApplicationRole`, optionally `defineRole` | What the app itself may touch; an assignable role for reps if the spike needs it. |

## Out of scope for now

| Idea | Why not now |
|---|---|
| Logic functions (webhook receivers, scheduled send, reply detection) | Logic functions are disabled by default on production self-hosted servers; one function breaks the "installs everywhere" promise. Revisit as an opt-in later, behind its own decision. |
| Front components, record-page widgets | Documented by Twenty as under active development; a second UI codebase for little gain. |
| Sending email from the app | Twenty has no rule yet for which connected mailboxes an app may use and is tightening this. |
| Billing | Not needed for a schema pack. |

## Constraints to design around

| Constraint | Consequence |
|---|---|
| Uninstalling an app deletes its data (objects, rows, app-owned field values). | Say so in the listing and in the phone's settings; offer an export first. |
| A sync deletes every entity the new manifest no longer declares. | Removing an entity is a data-loss release; note it in the changelog. |
| Only a Twenty admin (the `APPLICATIONS` permission) can install; a Member cannot. | The phone shows "Ask an admin to install" with a link. |
| Claiming needs a public repo, GitHub Actions and npm provenance. | This repo is public; the licence is Apache-2.0. |
| `package.json` `version` must strictly increase; downgrades are rejected. | Never reuse a version number. |
| The scaffold ships a health-check function, a front component and a page layout. | Delete them before testing on a functions-disabled server. |
