# product-workspace

One folder for one product: planning docs and the app live side by side,
so a PRD can point at the spec that implements it and a spec can point
back at the PRD it came from. Built from two templates —
`planning-docs-template` (now `planning/`) and `docs-template` (now
`app/`) — plus the rules for splitting work between Claude Code and Codex
(or using just one of them — see "Modes").

## Layout

```
README.md                  this file — how the workspace fits together
AGENTS.md                  shared rules every agent reads first (roles, paths, handoff)
CLAUDE.md                  Claude Code entry point (imports AGENTS.md + Claude-only notes)
.github/
  workflows/ci.yml         CI: re-runs app/docs/02-specs.md commands on every PR
  pull_request_template.md PR description: 구현 요약 + 리뷰 결과 + 머지 전 체크
planning/                  product planning: one-pagers, PRDs, decisions
  AGENTS.md / CLAUDE.md
  docs/
    01-context.md          product, users, constraints
    02-guidelines.md       sourcing, writing rules, review flow
    prd/                   one-pagers + PRDs (+ Handoff to app/docs/specs/)
    todo/                  open questions
    reports/               decision records
app/                       the code + its working docs
  AGENTS.md / CLAUDE.md
  docs/
    01-folder-architecture.md
    02-specs.md            stack, exact commands, conventions
    03-product-plan.md     purpose, scope (links to source PRDs)
    specs/                 implementation specs — the spec writer → implementer handoff
    todo/                  open/deferred work
    reports/               work log: per-file "where / purpose / method"
    escaped-bugs.md        bugs found after review — does review catch real problems?
  (your source code goes here: src/, tests/, package.json, ...)
```

## Who does what

Default mode is `dual` (set in `AGENTS.md` → Roles).

| Stage | Where | Who writes (`dual`) | Who approves |
|---|---|---|---|
| One-pager → PRD | `planning/docs/prd/` | Claude | you |
| Implementation spec | `app/docs/specs/` | Claude | you (sets `Approved`) |
| Code + tests + report | branch, `app/docs/reports/` | Codex | — |
| Review (local, before push) | the report's `## Review` | Claude, new session | — |
| PR + CI | GitHub | Codex opens it | CI ✅ |
| Human check + merge | the spec's `### Human check` | you (run the branch) | you (sets `Verified`, merges) |

Why this split: implementation loops (read files, edit, run tests, retry)
burn the most usage, so they go to Codex. Planning, spec-writing and
review need judgment but few tokens, so they stay with Claude.

## Modes

| | `dual` | `codex-only` | `claude-only` |
|---|---|---|---|
| Spec writer | Claude | Codex | Claude |
| Implementer | Codex | Codex | Claude |
| Reviewer | Claude | Codex, new session (`/review`) | Claude, new session or subagent (`/code-review`) |

Change `Mode:` in `AGENTS.md`, or override one spec with its
`Implementer:` / `Reviewer:` fields. Whatever the mode, the reviewer works
in a fresh session and only writes findings — a fresh context isn't
biased toward code it just wrote.

## The flow

```
planning/docs/prd/        one-pager → PRD            (spec writer, you approve)
        │  PRD "Handoff" section
        ▼
app/docs/specs/           spec: files, approach, done criteria
                          Draft → Approved (you, in a docs PR)
        ▼
implement (local)         implementer builds on a branch, runs checks,
                          writes the report, commits → Implemented
                          → shows "구현 요약", stops
        ▼
review (local)            reviewer, new session: report vs. diff vs. spec
                          → findings in the report → shows "리뷰 결과"
                          implementer fixes only those (max 2 rounds)
                          → Reviewed
        ▼
PR + CI                   implementer pushes, opens the PR; CI re-runs checks
        ▼
verify + merge            you run the spec's Human check on the branch;
                          spec → Verified; you merge. No new spec until then.
```

## How to start a new product

1. Use this repo as a GitHub template (or clone it) — one copy per product.
2. Fill in `planning/docs/01-context.md` and `02-guidelines.md`.
3. Fill in `app/docs/02-specs.md` — especially `## Commands`; no agent can
   guess these. Add stack-specific ignores (`node_modules/`, `target/`,
   `build/`, ...) to `app/.gitignore`.
4. Fill in `.github/workflows/ci.yml` with the same commands, then in
   GitHub Settings → Branches require the `ci` check to pass before
   merging (availability depends on repo visibility / plan).
5. Set `Mode:` in `AGENTS.md`, then open each tool (shown for `dual`):
   - Planning / spec writing → Claude Code at the root, so it sees both
     `planning/` and `app/`.
   - Implementation → Codex in `app/`.
   - Review → a new Claude Code session at the root, after the
     implementer has committed and stopped.
   In a one-tool mode, the same tool does every role — with a new
   session for review.

Both tools pick up the nearest `AGENTS.md` / `CLAUDE.md` plus the ones
above it, so the root rules always apply and folder rules add to them.

## Optional: Codex review from inside Claude Code

OpenAI's Codex plugin for Claude Code (`openai/codex-plugin-cc`) adds
`/codex:review` and `/codex:adversarial-review`. Useful for a second
opinion on a PRD or before shipping. Leave its automatic "review gate"
off on a Pro plan — it can loop and drain usage quickly.

## Updating the templates

If a rule keeps getting re-added in real products, fix it here so every
future workspace gets it.
