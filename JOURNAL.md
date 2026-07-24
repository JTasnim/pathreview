## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/152

**Issue title:** Faithfulness checker can never mark short claims as supported

**Tier:** [✅] Tier 1 [ ] Tier 2 [ ] Tier 3

**Problem summary:**
The faithfulness checker scores a generated claim as "supported" only if at least 2 non-stopword tokens overlap between the claim and its retrieved context. This threshold doesn't scale down for short claims: a claim like "Knows Python." has only one meaningful token to begin with, so it can never reach the 2-token minimum even when the context fully supports it. As a result, reviews made up of short, accurate claims are incorrectly scored as unsupported, dragging the overall faithfulness score toward 0.0. The bug lives in `_is_supported()` in `rag/evaluator/faithfulness_checker.py`, and is covered by three failing tests already in the repo: `test_partial_support_returns_middle_score`, `test_multiple_context_chunks`, and `test_multiple_claims_varying_support`.

**Selection notes:**
Worked through the "Is this right for me?" checklist before claiming this issue.

- Scope: single-function fix in `_is_supported()`, well-contained, no architectural changes needed
- Familiarity: reviewed a related bug in the same file (#153) beforehand, so I understand the surrounding code and file structure
- Claimed status: confirmed via the Development sidebar that no branches or PRs are linked to #152, unlike several other tier-1 issues I checked first (#150, #153, #154 all had competing PRs already open)
- Test coverage: three related tests already exist in the repo, giving a clear, verifiable definition of "done"

**Branch name:** fix/152-faithfulness-short-claims

**Setup confirmation:** [✅] App runs locally at localhost:5173

**Cohort ledger:** [✅] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/JTasnim/pathreview/commit/f2530bb8a574aafa728cafaeedbe5f5efab6dfa4

**Reproduction summary:**

Ran `FaithfulnessChecker().check('Knows Python. Knows SQL.', [{'text': 'python expert'}, {'text': 'sql expert'}])`
locally and observed `0.0` despite both claims being fully supported. Confirmed the three tests
named in the issue (`test_partial_support_returns_middle_score`, `test_multiple_context_chunks`,
`test_multiple_claims_varying_support`) fail on main, while 18 other tests in the same file pass.


**PLAN.md link:** https://github.com/JTasnim/pathreview/blob/fix/152-faithfulness-short-claims/PLAN.md

**Walkthrough video (recommended):** https://drive.google.com/file/d/1JWR5fG4MkvQQiAxwH1anCI2cP5upQr3Z/view?usp=sharing

**Blockers or open questions:**
Still deciding the exact scaling rule for the overlap threshold (e.g. ratio vs. fixed floor) —
will validate against the full existing test suite before finalizing in Week 9.
