# Traceability — qa-regression-hardening

Date: 2026-07-01   ·   Owner: orchestrator

Living matrix linking each requirement to its test(s) and proof(s). Complete for Gate 3.
Proofs under `docs/sdlc/qa-regression-hardening/proofs/`.

| Req (AC) | Task | Test(s) | Proof | Aspect | Status |
|---|---|---|---|---|---|
| AC-1 | T1 | grep "Blast-radius list" + "cross-check" (F1); folded bullet (F5); tabletop AC-1 | green + tabletop | function/principle | ✅ pass (independent) |
| AC-2 | T2 | grep reachability "whether or not that behavior's own file is edited"; tabletop AC-2 (paradigm) | green + tabletop | function | ✅ pass (independent) |
| AC-3 | T3 | grep "regression baseline" + "no assertion is broadened"; old line count 0; tabletop AC-3 A/B | green + green_retry1 + tabletop | function | ✅ pass (independent; D1 fixed) |
| AC-4 | T5 | grep "Never sampled" + `"sampled" =` + "Blast-radius override" + "public entrypoint"; tabletop AC-4 | green + tabletop | function | ✅ pass (independent) |
| AC-5 | T6 | grep "Integration regression gate" + step-6 renumber + single GATE 3 + "integrated branch" + proof pair; tabletop AC-5 | green + tabletop | function/quality | ✅ pass (independent) |
| AC-6 | T4 | grep "Escaped-regression protocol" + "Keep that test permanently"; tabletop AC-6 | green + tabletop | function | ✅ pass (independent) |
| AC-7 | T7 | `git diff --stat` (5 feature files + docs/ + process-artifact lesson appends); no hooks / no `model:` lines | principle_pass | principle | ✅ pass (independent) |

All 7 ACs trace to a passing grep + tabletop proof, independently verified (generator ≠ verifier).
Defect D1 (AC-3 count-only weakened-detection) caught by qa-function VERIFY, fixed, re-verified PASS (retry 1).
