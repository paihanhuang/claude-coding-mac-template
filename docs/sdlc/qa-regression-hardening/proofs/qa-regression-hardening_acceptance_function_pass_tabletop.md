# Tabletop proof — qa-regression-hardening

Date: 2026-07-01 · Aspect: function · Result: PASS (self-verification by implementer; independent VERIFY-mode review is the next step)

Each scenario plants a regression, states the **OLD** gate's verdict (false PASS = the bug we're fixing) and
the **NEW** gate's verdict, and names the directive that forces the change. Grep evidence: `..._function_pass_green.txt`.

## AC-1 — blast-radius list + reality cross-check
- **Scenario:** task changes shared `hashPassword()`. Author's list names caller `login.js` but forgets `admin/reset.js`; the diff also collaterally edits unrelated `billing/exporter.js`.
- **OLD:** no blast-radius concept → both the missed caller and the collateral edit pass unremarked. **False PASS.**
- **NEW:** reference-search for `hashPassword` surfaces `admin/reset.js` → added as UNTESTED-until-proven; `qa-principle` flags `billing/exporter.js` as outside the list. **FAIL.** ✔

## AC-2 — characterization test for *reachable* untested behavior (the paradigm regression)
- **Scenario:** diff changes rounding in `pricing/discount.js` only. Untested caller `invoicing/lineitem.js` is **never edited** but calls the changed function and silently shifts invoice totals.
- **OLD:** "run the full suite" is green (no test covers `lineitem.js`); AC-2's earlier "diff touches" wording never fires because the file isn't edited. **False PASS — the exact escape the feature exists to stop.**
- **NEW:** `lineitem.js` is on the blast-radius list (reachable), marked UNTESTED; "whether or not that behavior's own file is edited" forces a characterization test that pins the pre-change total → it fails post-change. **FAIL.** ✔

## AC-3 — before/after baseline; deleted / weakened detection
- **Scenario A (weakened):** an existing assertion is loosened from `assert status == 401` to `assert status in (200, 401)`; the test still passes pre and post.
  - **OLD:** pure pass/fail suite is green. **False PASS.**
  - **NEW:** the touched test-file source diff shows a **broadened assertion** (strict `==` → membership/range) — a named test-gaming form (vmodel Verification-discipline), independent of assertion count. **FAIL.** ✔
- **Scenario B (deleted):** the diff removes `test_login_rejects_expired_token`.
  - **OLD:** fewer tests, all green. **False PASS.**
  - **NEW:** test present in baseline inventory, absent post-change → shrunk suite. **FAIL.** ✔

## AC-4 — shared-code changes are sized up and never sampled
- **Scenario:** a change to a config schema imported by 4 modules lands on a task that the sampled audit would have skipped.
- **OLD:** "sampled audit" (undefined) lets the deterministic green signal auto-gate it with no adversarial pass. **False PASS risk.**
- **NEW:** blast-radius override sizes it up (CLAUDE.md §1) and vmodel 4b marks it "never sampled — always full qa-function." **Audited every time.** ✔

## AC-5 — integration regression gate (cross-task isolation)
- **Scenario:** Task A (session storage) and Task B (migration reorder) each pass in their own worktree; merged, a boot-order interaction crashes startup.
- **OLD:** neither worktree ever runs the merged code before Gate 3. **False PASS.**
- **NEW:** step 5 runs the suite + declared blast-radius behaviors on the *integrated* branch before finish. **FAIL** (given a test exercising the boot path; emergent-interaction residual risk documented). ✔

## AC-6 — escaped-regression becomes permanent protection
- **Scenario:** human reports `formatCurrency('JPY', 1000)` regressed (no-decimal locale broken) after acceptance.
- **OLD:** no protocol → fixed ad hoc, nothing pins it, can recur. 
- **NEW:** reproduce → RED test pinning JPY behavior → fix → GREEN → test kept permanently → dated lesson. Recurrence now trips AC-3 (deleted-baseline-test ⇒ FAIL). ✔

## AC-7 — minimalism / surgical
- `git diff --stat`: 5 feature files (F1–F5) + 1 expected cross-critique protocol write (`qa-quality-lessons.md`); no new files/hooks, no `model:` changes; all new content in `docs/sdlc/qa-regression-hardening/`. Evidence: `..._principle_pass.txt`. ✔
