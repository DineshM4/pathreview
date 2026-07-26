## Week 7 — Issue selection

**Issue link:** (https://github.com/ascherj/pathreview/issues/153)

**Issue title:** Faithfulness checker crashes when a context chunk has text: None #153

**Tier:** Tier 1. Since it is my first time contributing I wanted to do an issue that would be easier for me to fix. Additionally I am more experienced in RAG and ingestion compared to some of the other topics on this issue lists(API, Database, etc.), and decided it would be better to work on a issue that I have more experience in. As such I chose this tier 1 issue which focuses on RAG as it covers both my criterias.

**Problem summary:** The issue revolves around the get() function when chunking. get() works but getting the required value, in this case the text or if it does not exists it doesn't crash the program by letting us send a "default" value to insert. However, what if the orignal retrieved value is NONE? In this case, the get() function will recieve the NONE and send that into a join() method which ends up giving an error as it requires the same type(Strings). A succesful fix would be adding a guardrail for this specific case with if statements or the "or" keyword to prevent this issue from occuring, letting the chunker work as it should.

**Branch name:** fix/153-rag-chunk-error

**Setup confirmation:** Yes, App runs locally at localhost:5173

**Cohort ledger:** Yes, Issue added to cohort ledger in row 108

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** [link to commit documenting the reproduced issue]

**Reproduction summary:** Ran the minimal repro (FaithfulnessChecker().check('Knows Python.', [{'text': None}])) and the failing test test_none_context_chunk_text against the project venv, both of which raised TypeError: sequence item 0: expected str instance, NoneType found at faithfulness_checker.py:34. This confirmed that chunk.get("text", "") returns None (not the "" default) when the text key is present but null, and " ".join(...) then crashes on the non-string item.

**PLAN.md link:** [link to PLAN.md in your fork]

**Blockers or open questions:** None