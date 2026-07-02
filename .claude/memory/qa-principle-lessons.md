# qa-principle — lessons learned

Exclusive memory for the **qa-principle** role. Read this before verifying; append after.

**Protocol:** One dated, terse line per lesson. Record mistakes you made or that a later stage caught,
and confirmed-good practices. Deduplicate and prune. Format: `- YYYY-MM-DD: <lesson>`

## Lessons
- 2026-06-18: (seed) Default to FAIL. Audit `git diff` for nice-to-have code and collateral edits to unrelated files.
- 2026-07-01: For markdown/governance-only diffs, grep the whole diff for requirements.md's "Out of scope" keywords (e.g. invariant, hook, model:) to catch smuggled deferred items fast; also diff each hunk against design.md's "exact directive text" to separate true scope creep from harmless paraphrase.
