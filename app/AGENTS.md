# Working Agreement — App

Entry point for any AI agent (or human) working in `app/`. The workspace
root `AGENTS.md` (roles, paths, handoff) applies too — read it first.
Keep everything in `docs/` short — these files are re-read on every task,
so bloat here costs real tokens on every run.

## Before you start

1. Read `docs/03-product-plan.md` — what this project is and its scope.
2. Read `docs/02-specs.md` — stack, exact commands, conventions, and any
   project-specific boundary overrides.
3. Read `docs/01-folder-architecture.md` — where things live and why.
4. Implementing? Read the spec you were given in `docs/specs/` in full —
   it is your task definition.
5. Check `docs/todo/00-todo-list.md` for open work related to your task.
6. Skim only the last few relevant entries in `docs/reports/` for
   context — don't read the whole folder.

## Boundaries

Default rule: reversible, local-only actions are free. Anything that
touches shared state, external systems, or is hard to undo needs a check
first. `docs/02-specs.md` can add project-specific exceptions — check
there before assuming these apply unmodified.

**Always allowed, no confirmation needed**
- Reading files and exploring the codebase
- Running local tests, builds, and linters
- Creating or editing files within the task's scope
- Installing dependencies already declared in the lockfile
- Local git commits and local branches
- Pushing your own task branch (never `main`) and opening a PR for it,
  once its spec is `Reviewed`
- Implementing a spec whose status is `Approved`

**Ask first**
- Adding, upgrading, or removing a dependency (unless the approved spec
  names it)
- Schema changes or running a migration (unless the approved spec
  names it)
- Pushing to `main` or to a branch someone else is working on
- Changing CI/CD configuration
- Deleting or bulk-moving files outside the task's scope
- Any API call that costs money or has a real external side effect
- Writing implementation code for a non-trivial change that has no
  approved spec — see "Before you implement" below

**Never, under any circumstances**
- Force-pushing or rewriting history on `main` / `master`
- Editing `.env`, secrets, or credential/auth files
- Operating directly on production data or a production database
- Deleting tests, or marking failing tests as skipped, to make a run pass
- Marking a spec `Approved` or `Verified` yourself
- Merging a PR, or committing directly to `main`

## Before you implement anything beyond a trivial fix

The person should see the plan before code gets written. In this
workspace the plan is a spec in `docs/specs/`.

Size decides the paperwork. Every size still goes through a branch, a PR
and CI.

| Size | Example | Spec | Report | Local review |
|---|---|---|---|---|
| **Trivial** | typo, one log line, a fix you can describe in one sentence | none | none — the PR's 구현 요약 is the record | none |
| **Small** | clear-cut bug fix, review-suggestion follow-up; ≤ 3 files, no new behavior | none — a `docs/todo/` item states the goal + "Done when" (it stands in for the spec) | short: Summary + Verified only | 1 round |
| **Large** | new feature, behavior change, anything unclear or > 3 files | full spec, `Approved` by the human | full | full (max 2 rounds) |

Not sure which size? Treat it as the larger one.

- **Spec is `Approved`**: that *is* the approval. Implement it without
  re-posting a plan. If you find you must deviate from it (a file it
  didn't list, a different approach), stop, add a `docs/todo/` item
  explaining why, and wait.
- **Large, no spec yet** (a new feature, a behavior change, anything
  unclear): don't write code. Draft a spec from `docs/specs/_template.md` with
  status `Draft`, add it to `docs/specs/00-index.md`, and wait for the
  human to approve it. Implement in a fresh session so the work isn't
  cluttered by the back-and-forth that produced the spec.

## Verification — don't call it done without this

- Run every command listed under the spec's "Done when" and show the
  actual output (pass/fail), not just a claim that it works.
- Confirm the project still builds / compiles cleanly.
- If you can't verify a change (no test, no build step, no way to check),
  say so explicitly instead of asserting it works.

## While you work

- Keep changes scoped to the spec. Found something unrelated? Add it to
  `docs/todo/` instead of fixing it inline.
- Follow conventions already in the codebase over inventing new ones. If
  `docs/02-specs.md` doesn't cover it, ask rather than guessing.
- Prefer small, reviewable commits over large rewrites.
- Commit messages: Conventional Commits with `app` scope (`feat(app):`,
  `fix(app):`, `refactor(app):`, `test(app):`, `docs(app):`), report path
  on the last line of the body.
- Fixing a bug that slipped past review (caught by CI, the Human check,
  or after merge)? Add a row to `docs/escaped-bugs.md`.

## When you finish

1. Verified the change? Write a report in `docs/reports/` named
   `YYMMDD-HHMM-NN-short-title.md` from `_template.md` — see "Work log"
   below.
2. Set the spec's status to `Implemented` and update
   `docs/specs/00-index.md`.
3. Work left over (follow-ups, known issues, deferred decisions)? Add or
   update `docs/todo/YYMMDD-HHMM-NN-short-title.md` (same naming) and list
   it in `00-todo-list.md`.
4. Update `01-folder-architecture.md` or `02-specs.md` ONLY if the change
   actually altered structure, stack, or constraints. Don't pad these
   files "just in case."
5. Commit everything, then stop and hand off for review — don't push yet
   (see "Reviewing").
6. End with this summary as your last message, in Korean:

   ```
   ## 구현 요약
   - 스펙: app/docs/specs/<file>.md · 브랜치: <branch>
   - 방식: 핵심 접근 2~3줄 (고른 이유, 버린 대안)
   - 바뀐 파일: `path` — 무엇을 왜 (파일당 한 줄)
   - 검증: `명령` → 결과 / 못 한 검증은 그렇다고 명시
   - 스펙과 달라진 점: 없음 | 내용
   - 남은 일 / Human check 할 것: …
   ```

## Work log

The report is what git can't record: why each file changed and how.

- One report per commit, or per small group of commits for one spec.
- For every changed file, three lines: **Where** (function / component /
  section name), **Purpose** (why this file had to change), **Method**
  (what approach, and what you rejected if it matters).
- Locate changes by symbol name, never by line number — line numbers go
  stale on the next edit.
- Don't paste code. Reference commit hashes; `git show <hash>` has the
  rest.
- Files changed only by formatting, imports, or generated output: list
  them together on one line.

## Reviewing

The reviewer is set by `Mode` in the root `AGENTS.md` (or the spec's
`Reviewer:` field). Review happens locally, before anything is pushed.

### Handoff protocol (implementer ⇄ reviewer)

1. **Implementer finishes first.** All Automated "Done when" checks pass,
   everything is committed, the report is written, the spec is
   `Implemented`. Then stop — no more edits on the branch until the
   review round is complete.
2. **Reviewer only reviews.** Read, re-run checks, write findings in the
   report's `## Review`. Never edit code, tests or the spec.
3. **Fix only after the review round is complete, and only what it asked.**
   Every `must-fix` gets fixed; each `suggestion` is accepted or declined
   with a reason. Anything else found along the way → `docs/todo/`, not
   this branch.
4. Repeat 1–3. After two rounds with `must-fix` still open, stop and hand
   it to the human — it saves usage and usually means the spec was unclear.

### How to review

1. Use a new session — never the one that wrote the code or the spec.
2. Read the spec, then the report — not the raw diff first.
3. Compare each file's stated Purpose/Method with `git diff main...HEAD`.
   A mismatch between what the report says and what the code does is the
   first thing to flag.
4. Re-run the spec's Automated "Done when" commands.
5. Write findings in the report's `## Review`, each tagged `must-fix`
   (affects correctness or the spec's requirements) or `suggestion`
   (everything else). Don't invent findings to have some.
6. End with this summary as your last message, in Korean:

   ```
   ## 리뷰 결과 (라운드 N)
   - 결론: must-fix N · suggestion N → 수정 필요 | Reviewed
   - [must-fix] `path` `symbol` — 문제와 이유 (한 줄)
   - [suggestion] `path` `symbol` — 제안 (한 줄)
   - 다시 돌린 검증: `명령` → 결과
   ```

### After `Reviewed`

1. The reviewer sets the spec to `Reviewed` and commits on the branch.
2. The implementer pushes the branch and opens a PR (`gh pr create`); the
   description follows `.github/pull_request_template.md`. Add the PR
   link to the report.
3. CI fails → the implementer fixes it on the branch, and the reviewer
   checks just that fix.
4. The human runs the Human check, sets `Verified`, and merges.

## Monorepos / multiple projects in one repo

If a subfolder needs different rules, give it its own `AGENTS.md`. The
`AGENTS.md` closest to the file being edited wins over this one.

## Context hygiene (keep this cheap to read)

- Don't paste whole files into reports, specs or todos — reference paths
  and symbol names instead.
- `00-todo-list.md` and `specs/00-index.md` are indexes only: one line
  per item. Full detail lives in the item's own file.
- Close out stale todo entries periodically so the index doesn't grow
  without bound. Reports stay — they're the history.
