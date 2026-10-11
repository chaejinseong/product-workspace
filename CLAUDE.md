@AGENTS.md

## Claude-only notes

- Your role comes from `Mode` in `AGENTS.md` (and the spec's
  `Implementer:` / `Reviewer:` fields). In `dual` you write specs and
  review; you don't implement unless a spec says `Implementer: claude`.
- Writing a spec: make it complete enough that the implementer never has
  to guess — exact files, approach, done criteria with real commands from
  `app/docs/02-specs.md`.
- Reviewing: use a new session (not the one that wrote the spec) and
  follow `app/AGENTS.md` → "Reviewing". Start from the report, not the
  raw diff, to save usage.
