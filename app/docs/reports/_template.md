# <short-title>

- Date: YYYY-MM-DD
- Agent: codex | claude | <name>
- Spec: app/docs/specs/<file>.md (or "none — trivial fix")
- Commits: <hash>, <hash>
- PR: <url> (added after `Reviewed`)

## Summary

One or two sentences: what was done and why.

## Changes by file

### `src/<path>`
- Where: <function / component / section name>
- Purpose: <why this file had to change>
- Method: <approach taken; rejected alternative if it matters>

### `tests/<path>`
- Where: ...
- Purpose: ...
- Method: ...

Formatting / imports / generated only: `<file>`, `<file>`

## Verified

Commands from the spec's "Done when", with actual results
(e.g. `npm test` → 42 passed, 0 failed). If something couldn't be
verified, say so.

## Review

Written by the reviewer before any push (see `app/AGENTS.md` → "Reviewing").

- Reviewer: claude | codex | <name> (new session)
- Round 1:
  - [must-fix] `path` `symbol` — ... → resolved in <hash>
  - [suggestion] `path` `symbol` — ... → accepted / declined (why)
- Re-run: `<command>` → <result>
- Result: changes requested | Reviewed

## Follow-ups

- (link to a `app/docs/todo/` item, or "none")

---
File naming: `YYMMDD-HHMM-NN-short-title.md` — e.g.
`260219-1956-01-login-token-refresh.md`. Always include the timestamp so
reports sort chronologically and stay unique.

Example of one file entry:

### `src/auth/login.ts`
- Where: `validateToken()`, `LoginForm` submit handler
- Purpose: an expired token caused an infinite redirect on login
- Method: on expiry, try one refresh, log out if it fails. Rejected
  handling it in the global interceptor — it would affect every API call.
