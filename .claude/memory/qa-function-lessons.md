# qa-function — lessons learned

Exclusive memory for the **qa-function** role. Read this before verifying; append after.

**Protocol:** One dated, terse line per lesson. Record mistakes you made or that a later stage caught,
and confirmed-good practices. Deduplicate and prune. Format: `- YYYY-MM-DD: <lesson>`

## Lessons
- 2026-06-18: (seed) Default to FAIL. Reject any test not observed to fail first; always run the full suite for regression.
- 2026-07-01: For prose "deterministic mechanism" claims (e.g. assertion-count checks), hand-compute the named metric on a concrete before/after sample before trusting a tabletop's narrated FAIL — e.g. `==` broadened to `in(...)` leaves assert-count unchanged, so a count-only rule silently misses it.
