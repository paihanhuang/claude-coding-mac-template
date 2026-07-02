# Requirements — qa-regression-hardening

Tier: big   ·   Date: 2026-07-01   ·   Owner: researcher + qa-function

## Problem / intent
Regressions in existing features escape the QA gates when new features are added, and `qa-function`
does not catch them. Confirmed root causes: (1) `qa-function`'s regression defense is prose-only
("run the full suite"), which is blind to existing behavior that has **no test**; (2) the adversarial QA
is a **sampled** audit (`vmodel/SKILL.md` steps 4b and the balanced-sign-off note), so the regressing
change may never be scrutinized; (3) each task is verified **worktree-isolated** with no full-suite run on
the **integrated** branch before Gate 3; (4) when the human catches a regression, nothing pins it with a
permanent test, so it can recur. This feature strengthens the three QA roles **surgically** — honoring the
project's minimalism principle — so regressions are caught by deterministic signals, not diligence. Scope
is the approved core **C1–C3** only.

## Assumptions
- This is a governance change to prompt/skill **markdown**, not application code. Each AC's "test" is
  (a) a **static grep assertion** that the required directive exists in the harness file, plus
  (b) a **tabletop dry-run** of a *planted regression scenario* showing the strengthened gate returns
  FAIL where the old gate returned a false PASS. Proofs saved under
  `docs/sdlc/qa-regression-hardening/proofs/`.
- Gates are written **language-agnostically** — this is a portable template applied to arbitrary projects;
  each consuming project instantiates its own concrete test/coverage command. *(Decided at Gate 1: language-agnostic.)*
- Implementation edits only existing files: `.claude/agents/qa-function.md`,
  `.claude/skills/vmodel/SKILL.md`, and (one line each) `.claude/agents/qa-quality.md`,
  `.claude/agents/qa-principle.md`, and `CLAUDE.md §1` (task-sizing rule — decided in scope at Gate 1).

## Out of scope (deferred — do not build without separate approval)
- New cumulative `invariants.md` subsystem — instead fold "must-not-break" into existing `traceability.md`.
- Verifier model-tier bump / cross-model second-opinion (Big-tier option, secondary).
- Stop-hook / `/regression` hard enforcement (env-specific, brittle for a portable template).
- Any stack-specific coverage tooling wired into the template itself.

## Acceptance criteria (EARS / Given-When-Then)
Each criterion is measurable, falsifiable, and maps 1:1 to a test.

| ID | Criterion |
|---|---|
| AC-1 | WHEN `qa-function` runs in DESIGN mode THE SYSTEM SHALL require a **blast-radius list** enumerating existing behaviors reachable from the change (imports / callers / shared state), and `qa-principle` SHALL audit that the delivered diff stays within it. |
| AC-2 | GIVEN a change whose call/import graph **reaches** an existing behavior that has **no test** (whether or not that behavior's own file is edited) WHEN `qa-function` verifies THEN THE SYSTEM SHALL require a **characterization test** that pins current behavior and is observed to pass on the pre-change code, before the change is accepted. |
| AC-3 | WHEN `qa-function` verifies THE SYSTEM SHALL run the full suite on the **pre-change ref and post-change**, and SHALL return FAIL if any test that passed before now fails, is **deleted, or is weakened**. |
| AC-4 | WHEN a change touches **shared / high-fan-in** code THE SYSTEM SHALL size it up one tier at intake (`CLAUDE.md §1`), exclude it from the sampled audit, and always run full `qa-function` verification. |
| AC-5 | BEFORE Gate 3 THE SYSTEM SHALL run the full suite plus the declared blast-radius behaviors on the **integrated branch** (not a per-task worktree), with `qa-quality` baselining perf against that same integrated branch, and SHALL block finish on any failure. |
| AC-6 | WHEN a regression is discovered after acceptance (including by the human) THE SYSTEM SHALL require: reproduce → write a **failing test** pinning the lost behavior → fix → **retain that test permanently** → append one dated `qa-function` lesson. |
| AC-7 | THE SYSTEM SHALL deliver AC-1..AC-6 by editing only existing harness files, adding **no** new files (except this feature's `docs/sdlc/` artifacts), hooks, or model-tier changes — verifiable via `git diff --stat` and a `qa-principle` audit. |

## Decisions (resolved at Gate 1)
- Gates stay **language-agnostic / portable** — no stack-specific commands wired into the template.
- `CLAUDE.md §1` task-sizing **gains** the rule "touches shared / high-fan-in code ⇒ size up one tier" (folded into AC-4).

> Gate 1: human approves these acceptance criteria before architecture begins.
