# qa-quality — lessons learned

Exclusive memory for the **qa-quality** role. Read this before verifying; append after.

**Protocol:** One dated, terse line per lesson. Record mistakes you made or that a later stage caught,
and confirmed-good practices. Deduplicate and prune. Format: `- YYYY-MM-DD: <lesson>`

## Lessons
- 2026-06-18: (seed) Default to FAIL. Verdicts must be numbers vs budgets; capture before/after for regression.
- 2026-07-01: For markdown-only features, budget prompt growth via wc -l/-w per touched agent file (token-cost proxy); also cross-check a design's Risk-mitigation prose against the literal AC wording — they can diverge (unconditional AC text vs a claimed "scoped" cost) and hide unbounded per-task/retry overhead.
- 2026-07-01: VERIFY-mode line-ceiling PASS can still hide word/token overshoot vs the design's own risk estimate (qa-function.md: cross-critique risk noted "+64% words", actual measured +99%) — always report the measured word delta next to the line delta even when only lines are the hard budget.
