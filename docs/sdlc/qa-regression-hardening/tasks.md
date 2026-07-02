# Tasks — qa-regression-hardening

Date: 2026-07-01   ·   Owner: architect   ·   Executed via Superpowers subagent-driven-development
*Test plans sharpened by the DESIGN-mode cross-critique (see design.md cross-critique log).*

Bite-size tasks (each small, testable, surgical). Each links to the acceptance criteria it satisfies.

| ID | Task | Satisfies | Test plan (qa-function) | Status |
|---|---|---|---|---|
| T1 | Blast-radius list in `qa-function` DESIGN (per-feature, refined per-task; 1-hop + "+N transitive") **with project-wide symbol reference-search cross-check**; fold audit clause into `qa-principle`'s existing collateral-edits bullet | AC-1 | grep F1 for "blast-radius list" + "reference-search" + "until proven otherwise"; grep F5 folded bullet (checklist stays 4 bullets); tabletop: change to shared `hashPassword` lists caller `login.js`; a collateral edit to `billing/exporter.js` outside the list ⇒ FAIL | done |
| T2 | Characterization-test requirement anchored to **call/import-graph reachability, not file-edit** (`qa-function` VERIFY); legacy escape hatch | AC-2 | grep F1 for "characterization test" + "whether or not that behavior's own file is edited"; **tabletop B (paradigm case): `discount.js` rounding changed, untested *unedited* caller `lineitem.js` reachable ⇒ FAIL** until a characterization test exists | done |
| T3 | Regression baseline (captured **once/task, reused across retries**; ref defined) + deleted/**weakened** detection via test-source diff (`qa-function` VERIFY) | AC-3 | grep F1 for "regression baseline" + "deleted, skipped, or weakened" + "assertion count"; confirm old "full suite… no regression" line is **replaced not duplicated**; tabletop 1 (baseline-green now red) FAIL · 2 (test deleted) FAIL · 3 (assertion loosened but still passing) FAIL | done |
| T4 | Escaped-regression protocol, ending at permanent guard (`qa-function` VERIFY) | AC-6 | grep F1 for "Escaped-regression protocol" + "Keep that test permanently"; confirm no "add to blast-radius baseline" clause; tabletop: reported escape yields a permanent RED→GREEN test + one lesson line | done |
| T5 | Define "sampled" + never-sampled exclusion (vmodel step 4b) + sizing override with calibration note (`CLAUDE.md` §1, sole threshold source) | AC-4 | grep F2 for the "sampled" definition + "Never sampled"; grep F3 for "Blast-radius override" + calibration ("exception, not the rule"); confirm threshold not duplicated in F2; tabletop: shared-code task is sized up **and** gets full (non-sampled) qa-function | done |
| T6 | Integration regression gate (vmodel **new step 5; renumber old→6, GATE 3 marker stays on `finishing-a-development-branch`**) + `qa-quality` integrated baseline + proof-naming before/after symmetry | AC-5 | grep F2 for "Integration regression gate" + exactly one "GATE 3" attached to the finishing step; grep F4 for "integrated branch"; grep proof-naming for a `_function_..._before/_after` example; tabletop: T-A ∥ T-B green in worktrees, merged boot-order interaction ⇒ integrated gate FAILs | done |
| T7 | Whole-diff minimalism audit | AC-7 | `git diff --stat` shows only F1–F5 + `docs/sdlc/qa-regression-hardening/`; `.claude/hooks/**` empty; no `±model:` lines in agent files; no new scripts | done |

Status ∈ {todo, in-progress, in-review, blocked, done}. Retry cap per task = 3 (see CLAUDE.md §4).
