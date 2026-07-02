# STATUS — qa-regression-hardening

Tier: big   ·   Updated: 2026-07-01   ·   Owner: orchestrator

## Gates
| Gate | What | State |
|---|---|---|
| Gate 1 | Acceptance criteria approved | ✅ pass (scope + Gate-1 decisions approved 2026-07-01; AC-2 tightened to reachability post-critique) |
| Gate 2 | Final architecture approved | ✅ pass (approved 2026-07-01) |
| Gate 3 | Final acceptance / merge | 🟡 awaiting human decision (all stages ✅) |

## Stage ledger
| Stage | State | Proof / notes |
|---|---|---|
| Research | ✅ | requirements.md (root-cause diagnosis + re-verify) |
| Design | ✅ | design.md (exact directive text F1–F5), revised post-critique + tasks.md |
| Cross-critique | ✅ | 3/3 agents (function ∥ quality ∥ principle); 18 concerns, all folded in; 2 critical loopholes closed; log in design.md |
| Implement | ✅ | 9 edits across F1–F5; RED→GREEN grep + step-integrity proof |
| Verify | ✅ | independent VERIFY all PASS: qa-quality · qa-principle · qa-function (D1 fixed → retry 1 PASS) |
| Finish | 🟡 | awaiting Gate 3 decision (commit / PR / merge) |

State legend: ⬜ pending · 🟡 in-progress · ✅ pass · ❌ fail (see defect routing, CLAUDE.md §4)

## Open defects
| # | Found by | Routed to | Retry | Status |
|---|---|---|---|---|
| D1 | qa-function (VERIFY) | engineer | 1/3 | ✅ resolved — retry 1 PASS (independent); predicate-broadening now a named heuristic |

## Outcome metrics (honest signals — never LOC)
| Metric | Value |
|---|---|
| Time to first working result | pending implementation |
| Revert rate | pending (this is the ROI signal the feature aims to lower) |
| Post-merge fix rate | pending (ROI signal — escaped regressions) |
| Test count delta | n/a (governance markdown; "tests" = grep assertions + tabletop proofs) |
