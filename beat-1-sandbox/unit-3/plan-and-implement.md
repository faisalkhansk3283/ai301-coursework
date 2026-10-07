# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

faisalkhansk3283

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/6#issuecomment-6031522336

Here's my plan based on the reproduction above.

Both reported symptoms trace to `HybridRetriever.retrieve` in `rag/retriever/hybrid.py`: the fetched chunks never reach `keyword_searcher.index()` (so the keyword half can contribute nothing), and `keyword_scores_max` is computed only from that call's own returned batch (so a sparse, weak match still gets scaled to 1.0).

My approach:
1. Pass the fetched chunks to `keyword_searcher.index()` before searching, so the keyword index always reflects the current collection.
2. Replace the per-batch max normalization with a fixed, absolute-scale transform (`score / (score + 1)`) that doesn't depend on a max computed from the batch at all.

Scope is `rag/retriever/hybrid.py`. I'm deferring the vector-similarity path for now — my reproduction isolated both symptoms to keyword scoring using a stub vector store with zero vector hits, so I don't have evidence the vector side has the same problem, though the issue text does mention "each side."

I'll re-run my Unit 2 reproduction script against the fix and post the before/after output.

---

## Your branch

**Branch**

fix/6-hybrid-retriever-keyword-index

**Evidence**

Command (same script posted in my Unit 2 repro comment, re-run unchanged against the code
before and after this unit's fix): `python repro_issue6.py`

**Before** (from my Unit 2 repro comment, against the unmodified `hybrid.py`):

```
======================================================================
SYMPTOM 1: keyword half contributes nothing when never indexed
======================================================================
[warning  ] keyword_search_empty_index
retrieve() results (keyword_searcher never indexed):
-> retrieve() found 0 results at all: True

For comparison, if the fetched chunks HAD been indexed (the fix),
BM25 clearly finds real matches for this query:
  c2: bm25_score=0.6097
  c1: bm25_score=0.5622
  c4: bm25_score=0.2362
  c3: bm25_score=0.0000
  c5: bm25_score=0.0000

======================================================================
SYMPTOM 2: the batch's best match is always scaled to 1.0,
even when it is the only real match in an otherwise-irrelevant batch
======================================================================
Raw BM25 scores for a query that only one chunk weakly matches:
  c5: raw bm25_score=1.1543
  c1: raw bm25_score=0.0000
  c2: raw bm25_score=0.0000
  c3: raw bm25_score=0.0000
  c4: raw bm25_score=0.0000
HybridRetriever.retrieve() keyword_score after per-batch normalization:
  c5: keyword_score=1.0000
  c1: keyword_score=0.0000
  c4: keyword_score=0.0000
  c3: keyword_score=0.0000
  c2: keyword_score=0.0000
-> c5's single, sparse-term match is scaled to keyword_score=1.0: True
```

**After** (same script, same command, against the built fix in
`fix/6-hybrid-retriever-keyword-index`):

```
======================================================================
SYMPTOM 1: keyword half contributes nothing when never indexed
======================================================================
retrieve() results (keyword_searcher never indexed):
  c2: keyword_score=0.3788
  c1: keyword_score=0.3599
  c4: keyword_score=0.1911
  c5: keyword_score=0.0000
  c3: keyword_score=0.0000
-> retrieve() found 0 results at all: False

For comparison, if the fetched chunks HAD been indexed (the fix),
BM25 clearly finds real matches for this query:
  c2: bm25_score=0.6097
  c1: bm25_score=0.5622
  c4: bm25_score=0.2362
  c3: bm25_score=0.0000
  c5: bm25_score=0.0000

======================================================================
SYMPTOM 2: the batch's best match is always scaled to 1.0,
even when it is the only real match in an otherwise-irrelevant batch
======================================================================
Raw BM25 scores for a query that only one chunk weakly matches:
  c5: raw bm25_score=1.1543
  c1: raw bm25_score=0.0000
  c2: raw bm25_score=0.0000
  c3: raw bm25_score=0.0000
  c4: raw bm25_score=0.0000
HybridRetriever.retrieve() keyword_score after per-batch normalization:
  c5: keyword_score=0.5358
  c1: keyword_score=0.0000
  c2: keyword_score=0.0000
  c4: keyword_score=0.0000
  c3: keyword_score=0.0000
-> c5's single, sparse-term match is scaled to keyword_score=1.0: False
```

The "before" run returned 0 keyword results at all (symptom 1) and scaled a single sparse
match to a full 1.0 (symptom 2). The "after" run, against the same script and the same
corpus/queries, now returns real keyword-scored results matching the raw BM25 ranking
(symptom 1 fixed), and the same sparse match now lands at 0.5358 instead of 1.0 (symptom 2
fixed).

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test (3 packages, `--limit 3`): 3/3 agreement. Confirmed the rubric, evidence
   guide, and procedure load and run correctly before spending on a full run.
2. First full run (20 packages): 19/20 agreement — already past the 18/20 bar. Categories:
   `clear-accept 7/7`, `scope-creep 4/4`, `thread-convention 1/2`, `unbuildable 3/3`,
   `wrong-cause 4/4`. Only `pkg-20` (`thread-convention`) disagreed.
3. Final confirming full run (20 packages, saved via `--save-run eval-run.txt`): 20/20
   agreement — PASS, every category fully matched (`clear-accept 7/7`, `scope-creep 4/4`,
   `thread-convention 2/2`, `unbuildable 3/3`, `wrong-cause 4/4`). I did not change any
   rubric/procedure/evidence-guide file between runs 2 and 3; the model's grading of
   `pkg-20` simply varied between the two runs on the same files.

**Package analysis**

pkg-01 (category: `wrong-cause`). Gold label: reject. My rubric's run: reject — correct.
My `diagnosis` check requires the plan's stated cause to explain the specific behavior the
repro evidence shows and not be contradicted by any step in it, rather than merely stating
*a* plausible-sounding cause. pkg-01's candidate plan names a cause that a repro step
directly rules out (the same pattern as the in-class calib-03 activity package, where a
plan blamed a pager key-binding bug but the repro evidence showed the slowdown tracked
syntax highlighting instead, even with no pager involved at all). Because my `diagnosis`
check is required and specifically checks consistency with the repro evidence rather than
mere presence of a stated cause, it correctly failed pkg-01 and produced a `hold` verdict
matching gold.

**Check rationale**

From `rubric.md`:

> diagnosis | the plan's Diagnosis section, read against the Repro evidence section | the
> stated cause explains the specific behavior the repro evidence shows (what changes
> between steps, e.g. with vs. without a variable toggled), and is not contradicted by any
> step in the repro evidence. Merely naming *a* plausible-sounding cause is not enough if
> the evidence points elsewhere | required

My group's in-class worksheet version of this check only required that "the plan says what
causes the bug," with no requirement that the stated cause actually match the evidence.
That was the same gap the activity's own sample rubric had, which is exactly what let a
wrong-but-confident diagnosis (calib-03) pass in Phase 1 of the activity. I tightened the
check before writing it into `rubric.md` so a plausible-sounding but evidence-contradicting
diagnosis fails instead of passing.

**Trade-offs**

Tightening `diagnosis` to require evidence-consistency (not just presence of a stated
cause) is what makes the check able to fail a confident wrong diagnosis like calib-03's or
pkg-01's. The trade-off: it requires the grader (or the skill) to actually hold the stated
cause up against each repro step rather than just confirming a cause section exists, which
is more work per package and leaves room for borderline judgment calls (e.g. a diagnosis
that is directionally right but imprecise about *which* line causes the behavior). I did
not find a package in the 20 scored items where this stricter wording caused an incorrect
reject — all `wrong-cause` category packages (pkg-01, pkg-07, pkg-11, pkg-16) agreed with
gold at 4/4 in the final run, and no `clear-accept` package was rejected by this check, so
I have no evidence this check is currently costing me a false reject.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
