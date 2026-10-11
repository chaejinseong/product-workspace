# Escaped bugs

Bugs found AFTER a spec was `Reviewed` — by CI, the Human check, or after
merge. One row each. Used to judge whether reviews catch real problems
and what kind they miss. Don't delete rows.

| Date | Spec | Bug | Found by | Why review missed it | Fixed in |
|---|---|---|---|---|---|
| _(example) 2026-10-10_ | m1-app-skeleton | Keychain prompt on every rebuild | Human check | ad-hoc signing doesn't exist in tests | `<hash>` |
