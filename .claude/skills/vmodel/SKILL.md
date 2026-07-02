---
name: vmodel
description: The full SDLC V-model pipeline contract for this project, layered on Superpowers. Load this for any SMALL or BIG coding task to get the stage flow, the three human sign-off gates, the shift-left cross-critique, defect routing, verification discipline, and proof-of-pass / traceability rules. CLAUDE.md holds the always-on summary (principles, task tiers, roles); this skill holds the procedural depth.
---

# V-Model pipeline (detailed contract)

Load this when CLAUDE.md's task-sizing puts a task in the **Small** or **Big** tier. Trivial tasks skip it.
Roles, principles, tiers, the memory protocol, and shell/path conventions live in `CLAUDE.md` — this file is the procedure.

## Flow (Big tier)

```
0. INTAKE & SIZE (orchestrator) — state the tier
1. researcher  → brainstorming + web research → requirements.md (EARS + [NEEDS CLARIFICATION])
   ══ GATE 1 (human): approve acceptance criteria ══   (Superpowers brainstorming already gates this)
2. architect   → writing-plans → design.md + tasks.md (bite-size, requirement-linked)
3. CROSS-CRITIQUE (parallel): engineer-feasibility + qa-function + qa-quality + qa-principle
   ↳ SHIFT-LEFT: each QA writes the test plan it will later run. architect logs accept/reject + rationale.
   ══ GATE 2 (human): approve final architecture ══   (added by this contract — do NOT implement before it)
4. PER TASK (subagent-driven-development, worktree-isolated):
   a. engineer implements — TDD RED→GREEN, surgical diff only
   b. auto-verify: the deterministic signal (tests green, budgets met, clean diff) gates **every** task; the
      adversarial QA agents (qa-function ∥ qa-quality ∥ qa-principle) additionally audit a **sample** of
      low-risk tasks — *"sampled" = a fraction of tasks get the deeper adversarial pass; the rest auto-gate on
      the signal alone.* **Never sampled — always the full qa-function pass (qa-function is the regression gate;
      qa-quality/qa-principle stay sampled): any task that trips the `CLAUDE.md` §1 blast-radius override.** This
      pass first **refines the feature's blast-radius list for the task's diff** (qa-function DESIGN-mode
      method) before applying the VERIFY checks.
   c. fail → defect routing (below) → re-dispatch (retry cap 3); record lesson in role memory
   d. write proof-of-pass to docs/sdlc/<feature>/proofs/; update traceability.md + STATUS.md
5. **Integration regression gate (before finish):** on the *integrated* branch (not a per-task worktree), run
   the designated regression suite + the union of the feature's declared blast-radius behaviors; qa-quality
   re-baselines its budgets against this integrated branch. Any failure blocks finish and routes per the table.
6. finishing-a-development-branch
   ══ GATE 3 (human): final acceptance / merge ══   (Superpowers already presents this)
```

**Small tier** collapses this to: mini-spec → engineer (TDD) → qa-function + qa-principle → finish.

**Balanced sign-off:** humans sign off only at Gates 1–3; everything else auto-gates on deterministic
signals with a sampled audit. A stage must pass before the next begins. Parallelize independent work within
a stage (the cross-critique, the three QA verifications, independent modules in separate worktrees).

**Test authorship:** qa-function authors acceptance criteria (EARS / Given-When-Then); the engineer writes
tests from them under TDD; qa-function + the task-reviewer audit test quality.

## Defect routing & loop-back (retry cap = 3; on 3rd failure escalate to human)

| Failure | Routes to |
|---|---|
| Wrong behavior, code ≠ spec | engineer |
| Missing / ambiguous requirement | human (re-baseline requirements.md) |
| Local inefficiency | engineer |
| Architectural perf / scaling flaw | architect |
| Touched unrelated code / not minimal | engineer (+ log qa-principle lesson) |
| Cross-module integration / regression break | architect (interfaces) |

Every rejection reason is passed into the offending role's next invocation and appended to its lessons file.

## Verification discipline (anti-rubber-stamp)

- QA agents are **adversarial**: default verdict is **FAIL** unless evidence proves PASS.
- Ground verdicts in **deterministic signals**: qa-function → test pass/fail logs; qa-quality → measured
  numbers vs budget; qa-principle → a concrete `git diff` audit. Never vibes.
- Generator ≠ verifier: the engineer never signs off its own work.
- **Big tier:** add an independent second-opinion review from a *different model/agent* than the implementer (e.g. the `code-review` skill or a reviewer subagent on another model tier) — a separate verifier catches what the generator is blind to.
- Reject test-gaming: deleted assertions, assertions **broadened/loosened** (strict `==` → range/superset/tolerance, a hard assert wrapped in try/except, a real call replaced by a stub), equality overloads, early `exit(0)`, tests never observed to fail (RED).

## Artifacts, traceability & proofs

Per feature under `docs/sdlc/<feature-slug>/` (templates in `docs/sdlc/_templates/`):
`requirements.md`, `design.md`, `tasks.md`, `traceability.md`, `STATUS.md`, `proofs/`.

**Proof naming:** `<feature>_<level>_<aspect>_<result>_<sha-or-phase>.<ext>` — e.g.
`auth_unit_function_pass_a1b2c3d.xml` (or `_function_pass_before.xml` / `_after.xml` for the regression pair), `auth_system_quality_pass_before.json` / `_after.json`.
`level` ∈ {unit, integration, system, acceptance}; `aspect` ∈ {function, quality, principle}.
Prefer machine-readable formats (JUnit XML, JSON). Every acceptance criterion must trace to a passing
test + proof before Gate 3. Proofs are committed (not git-ignored).
