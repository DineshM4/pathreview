## Week 7 — Issue selection

**Issue link:** (https://github.com/ascherj/pathreview/issues/153)

**Issue title:** Faithfulness checker crashes when a context chunk has text: None #153

**Tier:** Tier 1. Since it is my first time contributing I wanted to do an issue that would be easier for me to fix. Additionally I am more experienced in RAG and ingestion compared to some of the other topics on this issue lists(API, Database, etc.), and decided it would be better to work on a issue that I have more experience in. As such I chose this tier 1 issue which focuses on RAG as it covers both my criterias.

**Problem summary:** The issue revolves around the get() function when chunking. get() works but getting the required value, in this case the text or if it does not exists it doesn't crash the program by letting us send a "default" value to insert. However, what if the orignal retrieved value is NONE? In this case, the get() function will recieve the NONE and send that into a join() method which ends up giving an error as it requires the same type(Strings). A succesful fix would be adding a guardrail for this specific case with if statements or the "or" keyword to prevent this issue from occuring, letting the chunker work as it should.

**Branch name:** fix/153-rag-chunk-error

**Setup confirmation:** Yes, App runs locally at localhost:5173

**Cohort ledger:** Yes, Issue added to cohort ledger in row 108

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/DineshM4/pathreview/commit/f83a2cf75f614a926b17ca2399c9106f1fc47056

**Reproduction summary:** Ran the minimal repro (FaithfulnessChecker().check('Knows Python.', [{'text': None}])) and the failing test test_none_context_chunk_text against the project venv, both of which raised TypeError: sequence item 0: expected str instance, NoneType found at faithfulness_checker.py:34. This confirmed that chunk.get("text", "") returns None (not the "" default) when the text key is present but null, and " ".join(...) then crashes on the non-string item.

**PLAN.md link:** https://github.com/DineshM4/pathreview/blob/fix/153-rag-chunk-error/PLAN.md

**Blockers or open questions:** None

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
Implemented the core fix from PLAN.md. Changed the context concatenation in `rag/evaluator/faithfulness_checker.py` from `chunk.get("text", "")` to `chunk.get("text") or ""`. The `or ""` guardrail coerces an explicitly-null `text` value (`{"text": None}`) to an empty string before `" ".join(...)`, which is the exact case the missing-key default never handled. The previously-failing `test_none_context_chunk_text` now passes.

**Next steps:**
Add one more regression test covering a `None` chunk mixed with valid chunks (to confirm valid text is still used and not just that it avoids crashing), run the full self-review, and open the PR.

**Blockers:**
None.

---

### Check-in 2 (end of week)

**PR link:** https://github.com/ascherj/pathreview/pull/600

**Branch:** `fix/153-rag-chunk-error`

**What you built:**
A one-line guardrail fix in `FaithfulnessChecker.check()` that stops the faithfulness checker from crashing when a retrieved context chunk has `text: None`. Replacing `chunk.get("text", "")` with `chunk.get("text") or ""` coerces a null text value to an empty string so `" ".join(...)` no longer raises `TypeError: sequence item 0: expected str instance, NoneType found`.

**Tests added or updated:**
`tests/unit/test_faithfulness_checker.py` — the existing `test_none_context_chunk_text` (which reproduced the bug) now passes. `test_none_chunk_mixed_with_valid_chunks` now covers a `None` chunk mixed with valid chunks to pass.

**Self-review confirmation:** [x] make check passes (ruff + black clean on the changed file)  [x] make test-unit passes for the target test — note: the repo has ~52 pre-existing unit-test failures in unrelated modules (skill_extractor, tech_detector, structural_chunker) plus 3 pre-existing failures in the scoring-math tests of this file; all were verified to fail on the original code before my change and are outside the scope of issue #153.

**Draft PR feedback received from:** none
