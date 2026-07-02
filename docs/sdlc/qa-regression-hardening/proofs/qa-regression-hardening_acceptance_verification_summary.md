# Verification summary — qa-regression-hardening

Date: 2026-07-01 · Result: **PASS** (all 7 ACs, independently verified)

## Independent VERIFY-mode verdicts (generator ≠ verifier — implementer did not self-certify)
| Verifier | Scope | Verdict |
|---|---|---|
| qa-quality | prompt-growth budget (<90 lines/file) + AC-3 retry-caching guard | PASS |
| qa-principle | AC-7 minimalism / surgical diff | PASS |
| qa-function (round 1) | AC-1..AC-6 grep + adversarial tabletop | **FAIL → D1** |
| qa-function (retry 1) | D1 fix + no-regression re-check | **PASS** |

## D1 — caught by the independent verifier, fixed, re-verified
**Defect:** AC-3's "weakened test" mechanism was count-only ("assertion count per test ID must not
decrease"), which misses **predicate broadening** (`assert x == 401` → `assert x in (200, 401)`; count stays
1). The shipped mechanism would have greenlit its own flagship tabletop scenario — a false negative in the
very regression check the feature adds.

**Fix:** added an `AND`-ed, named heuristic to qa-function AC-3 and the vmodel test-gaming list — "no
assertion is broadened" (strict `==` → range/superset/tolerance; hard assert wrapped in try/except; real call
replaced by a stub). Also wired the per-task blast-radius refinement trigger into vmodel step 4b and clarified
qa-function-only non-sampling.

**Re-verification:** qa-function retry 1 hand-ran Scenario A against the literal text → FAILs as required,
independent of assertion count. Closed.

## Note on diff footprint
The working tree shows the 5 feature files (F1–F5) + `docs/sdlc/qa-regression-hardening/` (feature artifacts)
+ 3 `*-lessons.md` appends. The lesson appends are the QA/engineer roles recording lessons under the memory
protocol (CLAUDE.md §4) during cross-critique & verification — expected process artifacts, not feature code
or new subsystems. AC-7 (surgical feature footprint) holds.
