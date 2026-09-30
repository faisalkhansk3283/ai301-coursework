# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

faisalkhansk3283

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/6#issuecomment-5902551061

I'd like to take this one on. I'll reproduce the keyword-indexing gap and the per-batch BM25 normalization in `HybridRetriever.retrieve` and report back with what I find.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/6#issuecomment-5902571397

### Reproduction Report: HybridRetriever skips keyword indexing and normalizes BM25 scores per batch (#6)

**Environment.** Repo at commit `2f4e82f` (2026-09-16, my fork). Python 3.12.4, isolated venv with only `structlog==26.1.0` and `rank-bm25==0.2.2` installed (Windows 11). I stubbed `VectorStore` (no real ChromaDB/embeddings) since both reported symptoms live entirely in `HybridRetriever`'s keyword-scoring path and don't depend on vector search at all.

**Preparation.** Wrote a standalone script (`repro_issue6.py`, saved at the repo root so `from rag.retriever...` resolves — running it from elsewhere, or without the repo root on `PYTHONPATH`, will fail that import) that imports the real `rag.retriever.hybrid.HybridRetriever` and `rag.retriever.keyword_search.KeywordSearcher` (real BM25 via `rank_bm25`), with a fake `VectorStore` returning no vector hits so only the keyword half is exercised. Full script, exactly as saved:

```python
"""Reproduction script for issue #6: 'Hybrid retriever skips keyword
indexing and normalizes BM25 scores per batch'.

Two symptoms, reproduced separately below with the repo's real
HybridRetriever and KeywordSearcher (real BM25 via rank_bm25). Only the
VectorStore is stubbed, since neither symptom needs real embeddings.

1. HybridRetriever.retrieve() fetches the collection's chunks via
   _get_all_chunks() but never passes them to keyword_searcher.index()
   (hybrid.py line 65, `# noqa: F841`), so if the searcher was never
   indexed elsewhere, the keyword half contributes nothing.
2. Even when the searcher IS indexed, each retrieve() call normalizes
   keyword scores by that call's own returned batch's top score, so the
   best match in a batch of otherwise-irrelevant chunks is still scaled
   to 1.0.
"""

from rag.retriever.hybrid import HybridRetriever
from rag.retriever.keyword_search import KeywordSearcher


class FakeCollection:
    def __init__(self, chunks):
        self._chunks = chunks

    def get(self, include=None):
        return {
            "ids": [c["id"] for c in self._chunks],
            "documents": [c["text"] for c in self._chunks],
            "metadatas": [{} for _ in self._chunks],
        }


class FakeVectorStore:
    """Stub vector store: no vector hits, so only the keyword-scoring
    path in HybridRetriever is exercised."""

    def __init__(self, chunks):
        self._chunks = chunks

    def query(self, query_embedding, collection_name, n_results):
        return []

    def get_collection(self, collection_name):
        return FakeCollection(self._chunks)


CHUNKS = [
    {"id": "c1", "text": "python list comprehension tutorial for beginners"},
    {"id": "c2", "text": "python dictionary comprehension advanced guide"},
    {"id": "c3", "text": "javascript array methods map filter reduce"},
    {"id": "c4", "text": "python generator expressions and lazy evaluation"},
    {"id": "c5", "text": "rust ownership and borrowing explained"},
]

print("=" * 70)
print("SYMPTOM 1: keyword half contributes nothing when never indexed")
print("=" * 70)

query_1 = "python comprehension"

# retrieve() calls _get_all_chunks(collection_name) internally, fetching
# CHUNKS -- but hybrid.py never passes that result to
# keyword_searcher.index(). A fresh KeywordSearcher (as HybridRetriever
# receives at construction, before any indexing elsewhere) stays empty.
fresh_searcher = KeywordSearcher()
retriever_1 = HybridRetriever(FakeVectorStore(CHUNKS), fresh_searcher)

results_1 = retriever_1.retrieve(
    query=query_1, profile_id="p1", query_embedding=[0.0], max_chunks=5, min_score=0.0
)
print("retrieve() results (keyword_searcher never indexed):")
for r in results_1:
    print(f"  {r['id']}: keyword_score={r['keyword_score']:.4f}")
print(f"-> retrieve() found 0 results at all: {len(results_1) == 0}")

print()
print("For comparison, if the fetched chunks HAD been indexed (the fix),")
print("BM25 clearly finds real matches for this query:")
control_searcher = KeywordSearcher()
control_searcher.index(CHUNKS)
control_hits = control_searcher.search(query_1, top_k=5)
for h in control_hits:
    print(f"  {h['id']}: bm25_score={h['bm25_score']:.4f}")

print()
print("=" * 70)
print("SYMPTOM 2: the batch's best match is always scaled to 1.0,")
print("even when it is the only real match in an otherwise-irrelevant batch")
print("=" * 70)

query_2 = "borrowing"  # only c5 shares this term; everything else is unrelated

searcher_2 = KeywordSearcher()
searcher_2.index(CHUNKS)
raw_hits = searcher_2.search(query_2, top_k=5)
print("Raw BM25 scores for a query that only one chunk weakly matches:")
for h in raw_hits:
    print(f"  {h['id']}: raw bm25_score={h['bm25_score']:.4f}")

retriever_2 = HybridRetriever(FakeVectorStore(CHUNKS), searcher_2)
results_2 = retriever_2.retrieve(
    query=query_2, profile_id="p1", query_embedding=[0.0], max_chunks=5, min_score=0.0
)
print("HybridRetriever.retrieve() keyword_score after per-batch normalization:")
for r in results_2:
    print(f"  {r['id']}: keyword_score={r['keyword_score']:.4f}")
c5_score = next((r["keyword_score"] for r in results_2 if r["id"] == "c5"), None)
print(f"-> c5's single, sparse-term match is scaled to keyword_score=1.0: "
      f"{c5_score == 1.0}")
print("   (blended_score = 0.3 * 1.0 for the keyword half -- full weight -- ")
print("    even though c5 only matched on one term against an otherwise ")
print("    unrelated batch of documents.)")
```

Run from the repo root with `python repro_issue6.py` (a venv with only `structlog` and `rank-bm25` installed is enough; no Docker or ChromaDB needed for this script).

**Execution and output.** Full, unedited console output from `python repro_issue6.py`:

```
======================================================================
SYMPTOM 1: keyword half contributes nothing when never indexed
======================================================================
2026-09-29 18:48:55 [warning  ] keyword_search_empty_index
2026-09-29 18:48:55 [info     ] hybrid_retrieval_complete      blended_count=0 filtered_count=0 final_count=0 keyword_results=0 query_len=20 vector_results=0
retrieve() results (keyword_searcher never indexed):
-> retrieve() found 0 results at all: True

For comparison, if the fetched chunks HAD been indexed (the fix),
BM25 clearly finds real matches for this query:
2026-09-29 18:48:55 [info     ] keyword_index_built            chunk_count=5
2026-09-29 18:48:55 [info     ] keyword_search_complete        query_len=2 results_count=5
  c2: bm25_score=0.6097
  c1: bm25_score=0.5622
  c4: bm25_score=0.2362
  c3: bm25_score=0.0000
  c5: bm25_score=0.0000

======================================================================
SYMPTOM 2: the batch's best match is always scaled to 1.0,
even when it is the only real match in an otherwise-irrelevant batch
======================================================================
2026-09-29 18:48:55 [info     ] keyword_index_built            chunk_count=5
2026-09-29 18:48:55 [info     ] keyword_search_complete        query_len=1 results_count=5
Raw BM25 scores for a query that only one chunk weakly matches:
  c5: raw bm25_score=1.1543
  c1: raw bm25_score=0.0000
  c2: raw bm25_score=0.0000
  c3: raw bm25_score=0.0000
  c4: raw bm25_score=0.0000
2026-09-29 18:48:55 [info     ] keyword_search_complete        query_len=1 results_count=5
2026-09-29 18:48:55 [info     ] hybrid_retrieval_complete      blended_count=5 filtered_count=5 final_count=5 keyword_results=5 query_len=9 vector_results=0
HybridRetriever.retrieve() keyword_score after per-batch normalization:
  c5: keyword_score=1.0000
  c1: keyword_score=0.0000
  c4: keyword_score=0.0000
  c3: keyword_score=0.0000
  c2: keyword_score=0.0000
-> c5's single, sparse-term match is scaled to keyword_score=1.0: True
   (blended_score = 0.3 * 1.0 for the keyword half -- full weight --
    even though c5 only matched on one term against an otherwise
    unrelated batch of documents.)
```

**Expected.** Per the issue: the collection's chunks fetched inside `retrieve()` should be passed to `keyword_searcher.index()`, so the keyword half of the blend reflects the current collection. Score normalization should not let a batch's only real match scale to a full 1.0 as if it were a strong match.

**Actual.** Both symptoms reproduce exactly as described. `_get_all_chunks()` fetches the collection's chunks (confirmed via the control run, which shows real BM25 matches exist for this data), but `retrieve()` never calls `keyword_searcher.index()` with them (hybrid.py line 65) — so with a freshly constructed, unindexed searcher, the keyword half contributes 0 results, confirmed by `keyword_search_empty_index` in the logs. Separately, when the searcher is indexed, `retrieve()` normalizes keyword scores by `keyword_scores_max`, the max **within that call's own returned batch** (hybrid.py line 74) — so `c5`, whose only signal was a single sparse term match (raw BM25 `1.1543`, against an otherwise-irrelevant batch of 4 chunks scoring `0.0000`), still gets scaled to a full `keyword_score=1.0`, the same normalized value a genuinely strong match would receive.

I did not reproduce this against the live ChromaDB-backed app. I didn't set up Docker or an API key; neither is needed here, because both symptoms are isolated entirely within `HybridRetriever`'s keyword-scoring logic and reproduce identically regardless of the vector store backing it.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. First full run (20 packages): 17/20 agreement, below the 18/20 bar, and the category
   floor unmet (0/1 in `disclosure`). Disagreements: pkg-05 and pkg-12 (gold accept, both
   graded reject on `steps-complete`), and pkg-20 (gold reject, graded accept — the
   `disclosure` package).
2. After loosening `steps-complete` (to accept a precise description as well as literal
   pasted content) and tightening `ai-disclosure-ok` (to assume AI assistance per this
   course's workflow, so silence on a required disclosure fails), re-ran the three
   disagreements plus canaries with `--only pkg-05,pkg-12,pkg-20,pkg-06,pkg-04`: all 5
   graded correctly, including the two canaries (pkg-04, pkg-06), which stayed correctly
   rejected — no regression from the loosened check.
3. Final confirming full run (20 packages, saved via `--save-run eval-run.txt`): 18/20
   agreement — PASS. Category floor held: clear-accept 7/8, disclosure 1/1, no-evidence
   4/4, unfollowable-comms 2/3, wrong-target 4/4. This run traded the three original
   misses for two new ones (pkg-03 and pkg-19), introduced by the `ai-disclosure-ok`
   rewrite — a net improvement (17 to 18) that still clears the bar, though my `--only`
   canary list before the confirming run didn't happen to include those two packages.

**Package analysis**

pkg-20 (source: ghostty-org/ghostty#13604). Gold label: reject (category: `disclosure`).
My rubric's first run: accept — wrong. The repo's `AI_POLICY.md` states that all AI usage
in any form must be disclosed, naming the tool and the extent of assistance, yet the
candidate's claim comment and repro report say nothing about AI at all — neither
confirming nor denying its use. My original `ai-disclosure-ok` check read "silence on AI
use" as passing whenever the comment didn't explicitly admit an undisclosed use, which let
this package through incorrectly. I revised the check to assume every comment was produced
with AI assistance (since that is this course's actual workflow), so when a policy requires
naming the tool and extent, a comment saying nothing about AI is a fail — matching how a
real disclosure-wall repo would be read.

**Check rationale**

From `rubric.md`:

> ai-disclosure-ok | claim comment + repro comment, read against CONTRIBUTING.md / stated
> AI policy | assume the comments were produced with AI assistance (this course's
> workflow). If the repo's policy requires disclosing AI use (naming the tool and the
> extent of assistance), the comments must contain that disclosure; a comment that says
> nothing about AI use when the policy explicitly requires it is a fail. If the policy
> states no AI requirement at all, silence passes | required

I revised this from an earlier version that treated silence on AI use as always passing.
That version missed pkg-20, the one `disclosure`-category package, because it let an
undisclosed AI-assisted-looking comment through whenever it didn't explicitly claim
"no AI was used." Since this course's own workflow is AI-assisted, I decided the check
should assume that baseline rather than take a comment's silence as evidence one way or
the other.

**Trade-offs**

Tightening `ai-disclosure-ok` fixed pkg-20 (the one `disclosure`-category package) but
introduced two new disagreements on the same full run: pkg-03 (gold accept, now failing
`ai-disclosure-ok`) and pkg-19 (gold reject, now incorrectly passing). pkg-03's gold note
says a "human-voiced comment satisfies the repo's AI-comment rule" for that specific repo —
meaning that repo's actual rule is satisfied by writing in plain human voice, not
necessarily by a named-tool disclosure statement, and my rewritten check is too blunt to
tell the two kinds of policy apart. I didn't catch this before the confirming run because
my `--only` canary list (pkg-05, pkg-12, pkg-20, pkg-06, pkg-04) didn't include pkg-03 or
pkg-19 — I canaried the categories I expected `steps-complete` to touch, but not every
`clear-accept`/`unfollowable-comms` package the `ai-disclosure-ok` rewrite could also
affect. Net effect was still a real improvement (17/20 to 18/20, clearing the bar), but it
shows the canary list should have been broader given I changed two checks in the same
revision pass.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
