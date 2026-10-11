# Workspace Agreement

Read first, by every agent, in every folder. Folder-level `AGENTS.md`
files (`planning/`, `app/`) add to these rules; when they conflict, the
one closest to the file being edited wins. Keep this file short — it is
loaded on every task.

## Map

- `planning/` — what to build and why (one-pagers, PRDs, decisions).
  Rules: `planning/AGENTS.md`.
- `app/` — the code and how it was built (specs, work log).
  Rules: `app/AGENTS.md`.

## Roles

Mode: `dual` — this workspace's default. A spec's `Implementer:` /
`Reviewer:` fields override it for that spec.

| Role | `dual` | `codex-only` | `claude-only` |
|---|---|---|---|
| Spec writer | Claude | Codex | Claude |
| Implementer | Codex | Codex | Claude |
| Reviewer | Claude | Codex, new session (`/review`) | Claude, new session or subagent (`/code-review`) |

- The reviewer never reviews in the session that wrote the code or the spec.
- The reviewer writes findings only; the implementer makes every code change.
- **The human**: approves one-pagers, PRDs and specs; runs the Human
  check; merges.

## Paths

- Every path written inside a doc is relative to this workspace root
  (`planning/docs/prd/login.md`, `app/docs/specs/...`), never `../`-style,
  so links read the same from any folder.

## Handoff points

1. PRD `Approved` → the spec writer drafts a spec in `app/docs/specs/` and fills
   the PRD's `## Handoff` section with its path.
2. Spec `Approved` (set by the human only) → the implementer builds it
   without asking for plan approval again.
3. Spec `Implemented` → the reviewer reviews the branch locally (before
   any push), following `app/AGENTS.md` → "Reviewing". No `must-fix`
   open → spec `Reviewed` → the implementer pushes and opens the PR; CI runs.
4. Spec `Reviewed` → the human runs the Human check on the PR branch,
   sets `Verified`, and merges the PR.
   Don't start a new spec while an older one is `Reviewed` but not `Verified`.

## Shared never-list

- Never mark a PRD or spec `Approved` or `Verified` — only the human does.
- Never edit `.env`, secrets, or credential files.
- Never delete a past report or decision — mark it superseded.

## Branches & commits

- One git repo for the whole workspace. Every change reaches `main`
  through a PR — nobody commits to `main` directly. Only the human merges.
- Docs PR: a one-pager, PRD or spec draft on `docs/<short-title>`. The
  human sets `Approved` in that PR and merges it.
- Code PR: one approved spec = one branch `<type>/<spec-short-title>`
  (e.g. `feat/login-token-refresh`) = one PR, opened only after the spec
  is `Reviewed`. The report and the spec's status changes go in the same PR.
- Conventional Commits with the folder as scope: `feat(app): ...`,
  `fix(app): ...`, `docs(planning): ...`, `docs(app): ...`. Put the
  report path on the last line of the body.
