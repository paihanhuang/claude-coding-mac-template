# Design — qa-regression-hardening

Date: 2026-07-01   ·   Owner: architect   ·   References: requirements.md, docs/research/harness-engineering-cross-source-synthesis.md
*Revised after the DESIGN-mode cross-critique (qa-function ∥ qa-quality ∥ qa-principle).*

## Approach
Surgically edit the existing QA harness so regression defense rests on **deterministic signals**, not agent
diligence. No new subsystems (AC-7). Each edit is a directive added to an existing prompt/skill file.

Target files are **markdown**, so TDD is adapted: each directive's "failing test" is a **grep assertion**
(absent today = RED, present after edit = GREEN) plus a **tabletop dry-run** of a planted regression proving
the strengthened gate returns FAIL where today's gate returns a false PASS. Gates stay **language-agnostic**.

**Two loopholes the cross-critique caught are now closed in the directive text:** (1) AC-2 is anchored to
**call/import-graph reachability**, not file-edits — otherwise a changed shared function silently breaking an
*unedited* untested caller (the paradigm regression) would still PASS; (2) the blast-radius list is
**cross-checked against a project-wide symbol search**, not agent reasoning alone.

*Alternatives rejected:* new `invariants.md` subsystem + Stop-hooks (violates minimalism/AC-7, couples a
portable template to one environment — deferred); verifier model-tier bump (secondary; gaps here are
structural — deferred).

## Tech stack
Markdown only. Verification uses `grep` (directive presence + changed-symbol reference-search) and `git`
(before/after refs, `diff --stat` for AC-7). No new dependencies, no code.

## Architecture & interfaces — exact directive text (Gate 2 review surface; revised post-critique)

### F1 · `.claude/agents/qa-function.md`
**AC-1 (DESIGN mode) — add:**
> Produce a **blast-radius list** — per feature at cross-critique, refined per task before that task's VERIFY.
> Enumerate existing behaviors the change can reach: **direct importers/callers (1 hop)** of the changed
> symbols, plus shared state / config / schema it reads or writes; note "+N transitive (not itemized)" rather
> than exhausting the graph. Name each behavior's guarding test(s), or mark it **UNTESTED**. Then
> **cross-check against reality**: reference-search the changed symbol names project-wide; any hit not already
> on the list is added as **UNTESTED until proven otherwise**. (`qa-principle` audits the diff against this list.)

**AC-2 (VERIFY mode) — add:**
> For every blast-radius behavior marked **UNTESTED that the change's call/import graph can reach — whether or
> not that behavior's own file is edited** — require a **characterization test** that pins current behavior and
> is observed to pass on the pre-change code, before acceptance. A reachable-untested behavior with no
> characterization test ⇒ **FAIL** (the suite is blind exactly where regressions hide). *Legacy/low-coverage
> escape hatch:* pin **directly-reached** behaviors first; log deeper untested reach as tracked follow-ups
> rather than blocking task 1 indefinitely.

**AC-3 (VERIFY mode) — replace "Run the **full** suite to prove **no regression**…" with:**
> Establish a **regression baseline** at the pre-change ref (**immediate parent commit** for per-task checks;
> **feature-branch base** for the AC-5 integration gate): the designated regression suite's known-green state
> and its test inventory, **captured once per task and reused across retries** (the base ref doesn't change;
> only the post-change run repeats). Re-run post-change. **FAIL** if any test green at baseline now fails, or
> if a baseline test was **deleted, skipped, or weakened** — detect weakening via a **source diff of touched
> test files** (assertion count per test ID must not decrease **and no assertion is broadened** — strict `==`
> → range/superset/tolerance, hard assert wrapped in try/except, or a real call replaced by a stub; apply the
> test-gaming heuristics already in vmodel's Verification-discipline section), since a loosened-but-passing assertion is invisible to a
> pass/fail diff. A green-but-shrunk suite is a regression.

**AC-6 — add a standalone `##` section after Output (an escaped regression surfaces after a VERIFY cycle, not within one):**
> ## Escaped-regression protocol (regression found after acceptance, including by the human)
> 1. Reproduce it; capture the failing observation.
> 2. Write a test that **pins the lost behavior** and fails on the current broken code (RED).
> 3. Route the fix to the engineer; the test goes GREEN.
> 4. **Keep that test permanently** as a regression guard.
> 5. Append one dated line to `.claude/memory/qa-function-lessons.md` naming the class of change that slipped.

### F2 · `.claude/skills/vmodel/SKILL.md`
**AC-4 — replace step 4b's parenthetical so "sampled" is defined:**
> the deterministic signal (tests green, budgets met, clean diff) gates **every** task; the adversarial QA
> agents (qa-function ∥ qa-quality ∥ qa-principle) additionally audit a **sample** of low-risk tasks —
> *"sampled" = a fraction of tasks get the deeper adversarial pass; the rest auto-gate on the signal alone.*
> **Never sampled — always the full `qa-function` pass: any task that trips the `CLAUDE.md` §1 blast-radius override.**

**AC-5 — insert new step 5, renumber existing "5. finishing…" to "6." (keep the ══ GATE 3 ══ marker on step 6):**
> 5. **Integration regression gate (before finish):** on the *integrated* branch (not a per-task worktree),
>    run the designated regression suite + the union of the feature's declared blast-radius behaviors;
>    `qa-quality` re-baselines its budgets against this integrated branch. Any failure blocks finish and
>    routes per the defect table.

**Proof-naming — one-line symmetry addition (function proofs now pair pre/post, like quality):**
> e.g. `auth_unit_function_pass_before.xml` / `_after.xml`.

### F3 · `CLAUDE.md` §1 — append after the tier table (AC-4; sole source of the threshold)
> **Blast-radius override:** if a change touches shared / high-fan-in code — a symbol **imported by ≥2
> modules**, shared state / config / schema, or a **package's public entrypoint** — size **up one tier**.
> This override is the exception, not the rule; if it fires on most of a feature's tasks, the threshold is
> miscalibrated — raise it.

### F4 · `.claude/agents/qa-quality.md` — VERIFY mode, add one line (AC-5)
> For the final integration gate, baseline against the **integrated** branch, not a per-task worktree, so
> cross-task performance regressions are visible.

### F5 · `.claude/agents/qa-principle.md` — VERIFY mode, **fold into the existing collateral-edits bullet** (no new bullet) (AC-1)
> Flag changes to files or lines unrelated to the task (scope creep / collateral edits) — where `qa-function`
> supplied a blast-radius list, anything touched outside it is unplanned by definition.

## Stage decomposition
| Task | Deliverable | File | Satisfies | Depends on |
|---|---|---|---|---|
| T1 | Blast-radius list (per-feature, refined per-task) + completeness cross-check + qa-principle audit fold-in | F1, F5 | AC-1 | — |
| T2 | Characterization test for **reachable** (not merely edited) untested behavior + legacy escape hatch | F1 | AC-2 | T1 |
| T3 | Regression baseline (once/task, reused across retries) + deleted/**weakened** detection + ref definition | F1 | AC-3 | — |
| T4 | Escaped-regression protocol | F1 | AC-6 | — |
| T5 | Define "sampled" + never-sampled exclusion (vmodel 4b) + sizing override w/ calibration (CLAUDE.md §1) | F2, F3 | AC-4 | — |
| T6 | Integration gate (new step 5, renumber old→6) + qa-quality integrated baseline + proof-naming symmetry | F2, F4 | AC-5 | T1 |
| T7 | Whole-diff minimalism audit | — | AC-7 | all |

## Cross-critique log (Gate 2 input) — 18 concerns, all folded in; 2 critical loopholes closed
| # | Raised by | Concern | Decision | Rationale / how folded |
|---|---|---|---|---|
| P1 | qa-principle | AC-6 "add to blast-radius baseline" reintroduces deferred cumulative-memory | accept | dropped; AC-6 ends at "keep test permanently" |
| P2 | qa-principle | AC-4 threshold defined verbatim in both F2 & F3 (DRY) | accept | threshold defined **once** in CLAUDE.md §1; F2 references "the §1 override" |
| P3 | qa-principle | F3 "non-sampled audit" clause belongs in vmodel, not the CLAUDE.md summary | accept | dropped from F3; F2 owns the never-sampled consequence |
| P4 | qa-principle | F5 new bullet duplicates existing collateral-edits bullet | accept | folded into the existing bullet; checklist stays 4 bullets |
| Q1 | qa-quality | AC-3 cost unconditional but Risk claimed it scoped — contradiction | accept | baseline = known-green captured once (post-run is the existing 1×); Risk text corrected |
| Q2 | qa-quality | retry loop multiplies AC-3 cost, no caching guard | accept | "captured once per task, reused across retries" added |
| Q3 | qa-quality | AC-4 "≥2 modules" too low, erodes sampling savings | accept | calibration note added to §1 (exception, not rule; raise if it fires on most tasks) |
| Q4 | qa-quality | prompt growth concentrated in qa-function.md (+64% words) | accept (risk) | noted in Risks; soft ceiling ~90 lines/agent for future edits |
| Q5 | qa-quality | AC-2 char-test cost scales with test debt (legacy stall) | accept | escape hatch: pin direct reach first, log deeper as tracked follow-ups |
| Q6 | qa-quality | AC-1 enumeration uncapped | accept | capped to 1-hop + "+N transitive (not itemized)" |
| Q7 | qa-quality | step-renumber risk in vmodel | accept | explicit renumber note (= F8) |
| Q8 | qa-quality | "full suite" should be "designated regression suite" (portable) | accept | wording applied in AC-3/AC-5 |
| Q9 | qa-quality | wire ROI into STATUS.md metrics | accept (noted) | existing Revert / Post-merge-fix rows are the ROI signal; no new artifact |
| **F1** | **qa-function** | **AC-2 "diff actually touches" = file-edit → false PASS on the paradigm regression** | **accept (critical)** | **reworded to call/import-graph reachability, whether or not the file is edited** |
| **F2** | **qa-function** | **no independent completeness check of the blast-radius list** | **accept (critical)** | **added project-wide symbol reference-search cross-check; unlisted hits ⇒ UNTESTED** |
| F3 | qa-function | "sampled audit" never mechanically defined → AC-4 may be a no-op | accept | defined "sampled" in F2 step 4b before the exclusion clause |
| F4 | qa-function | AC-3 "weakened" had a verdict rule but no mechanism | accept | assertion-count-per-test-ID via test-source diff; references vmodel test-gaming heuristics |
| F5 | qa-function | AC-5 gate uses union of per-task lists; misses emergent cross-task interactions | accept (as risk) | documented residual risk (defense-in-depth, not primary mechanism) |
| F6 | qa-function | AC-3 "pre-change ref" undefined | accept | immediate parent per-task; feature-branch base for the AC-5 gate |
| F7 | qa-function | AC-4 "public interface" vague/gameable | accept | tightened to "package's public entrypoint" |
| F8 | qa-function | duplicate step-5 numbering | accept | renumber existing step to 6 (= Q7) |
| F9 | qa-function | proof-naming lacks a function before/after example | accept | one-line symmetry addition |
| F10 | qa-function | AC-1 lifecycle (per-feature vs per-task) unstated | accept | per-feature at cross-critique, refined per task; F5 audits the per-task slice |

## Risks
- **Before/after cost:** the post-change run is the existing 1×; the baseline is the known-green state captured **once per task and reused across retries** — not a mandatory second full run per verify. Shared-code tasks additionally get the non-sampled full qa-function pass (AC-4). *(Q1/Q2)*
- **"Shared / high-fan-in" is a judgment call** → pinned to concrete signals (≥2 importers / shared state / public entrypoint) + a calibration note; still tunable per project. *(Q3/F7)*
- **Emergent cross-task interactions:** AC-5's gate re-runs the *union* of per-task blast-radius lists, not a freshly-derived integration-level one; an interaction where neither task calls the other is caught only if a pre-existing test exercises the combined path — accepted residual risk, defense-in-depth. *(F5)*
- **Prompt growth:** qa-function.md grows most (AC-1/2/3/6 all land there); soft ceiling ~90 lines/agent file for future harness edits. *(Q4)*
- **Directive drift** (agents ignore prose) → only partially mitigated here; hard-hook enforcement is the deferred D3 option if prose proves insufficient.

> Gate 2: human approves this final architecture before implementation begins.
