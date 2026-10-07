# Plan: Hybrid retriever skips keyword indexing and normalizes BM25 scores per batch (#6)

## Diagnosis

From my Unit 2 reproduction (`repro_issue6.py`, posted as the repro comment on this issue):

1. **Keyword indexing is skipped.** `HybridRetriever.retrieve()` fetches the current
   collection's chunks via `_get_all_chunks()` (`hybrid.py:65`), but the result is
   discarded (`# noqa: F841`) instead of being passed to `keyword_searcher.index()`. With
   a freshly constructed `KeywordSearcher`, this means the keyword half of the blend
   contributes 0 results — confirmed by the `keyword_search_empty_index` log line and
   `retrieve() found 0 results at all: True`, even though a control run (indexing the same
   chunks directly) found real BM25 matches (`c2: bm25_score=0.6097`, etc.).
2. **Normalization is per-batch, not corpus-wide.** `keyword_scores_max` (`hybrid.py:74`)
   is computed as the max score **within that call's own returned batch**. My repro showed
   a chunk with a single, sparse-term match (raw BM25 `1.1543`, against 4 otherwise
   irrelevant chunks scoring `0.0000`) still gets scaled to `keyword_score=1.0000` — the
   same normalized value a genuinely strong match would receive.

Both symptoms match the issue's own description exactly: "the keyword half of the blend
contributes nothing" and "the best keyword match would scale to 1.0 even when it's weak."

## Scope

**In scope:**
- `rag/retriever/hybrid.py` — `HybridRetriever.retrieve()`: pass the fetched chunks to
  `keyword_searcher.index()`, and replace the per-batch normalization with the fixed
  absolute-scale transform described below.

**Not in scope:**
- `rag/retriever/vector_store.py` and the vector-similarity scoring path — the repro
  evidence isolates both symptoms entirely within the keyword-scoring path (my repro used
  a stub vector store with zero vector hits and still reproduced both symptoms
  identically), so the vector half is not implicated.
- Any ingestion/chunking/embedding code — not touched by either reproduced symptom.

## Approach

1. In `HybridRetriever.retrieve()`, call `self.keyword_searcher.index(all_chunks)` right
   after fetching `all_chunks` via `_get_all_chunks()`, before calling
   `self.keyword_searcher.search(...)`. This ensures the keyword index always reflects the
   current collection's chunks for this call, fixing symptom 1.
2. Replace the per-batch max normalization (`keyword_scores_max = max([r.get("bm25_score",
   0) for r in keyword_results], ...)`) with a fixed, absolute-scale transform that does
   not depend on any max computed from the batch or corpus:
   `normalized = raw_bm25_score / (raw_bm25_score + 1)`. This saturates any raw BM25 score
   into (0, 1) — a weak match (e.g. raw `1.1543`) lands near `0.54`, not `1.0`, while a
   genuinely strong match (raw score in the tens) approaches `1.0` — and critically, it
   does not depend on what else is in the returned batch, so it fixes the actual defect.
   (I initially proposed re-deriving a "corpus-wide max," but since `KeywordSearcher.search()`
   sorts descending and then truncates to `top_k`, the batch's max score is always the
   corpus's max score too — that approach would not have changed anything. An absolute
   scale avoids this because it never computes a max at all.)
3. Leave the vector-score normalization and the blending formula (`vector_weight` /
   `keyword_weight`) unchanged, since neither symptom implicates them.

## Test plan

Re-run my Unit 2 reproduction script (`repro_issue6.py`) against the fixed code, both
before (already posted in my Unit 2 repro comment) and after:

- **Symptom 1 check**: with a freshly constructed `KeywordSearcher` passed into
  `HybridRetriever`, `retrieve()` should no longer log `keyword_search_empty_index` or
  return 0 results — it should find the same real BM25 matches the control run finds
  (`c2`, `c1`, `c4` for the "python comprehension" query).
- **Symptom 2 check (weak match isn't inflated)**: for the "borrowing" query against the
  same 5-chunk corpus, `c5`'s `keyword_score` (raw BM25 `1.1543`) should land at roughly
  `1.1543 / (1.1543 + 1) ≈ 0.536`, not `1.0000` — regardless of whether the rest of the
  batch is relevant or not, since the new normalization never looks at the batch at all.
- **Symptom 2 check (relative relevance is preserved)**: for the "python comprehension"
  query, the transform should preserve the same ranking as the raw BM25 scores
  (`c2 > c1 > c4`): `0.6097/1.6097 ≈ 0.379 > 0.5622/1.5622 ≈ 0.360 > 0.2362/1.2362 ≈ 0.191`.
  This confirms the fix distinguishes real relevance differences within a query, rather than
  flattening everything toward the same value. (I compare within one query rather than
  across queries, since BM25 scores from different queries — e.g. a rare single-term query
  like "borrowing" vs. a multi-term one — aren't directly comparable on raw magnitude
  alone.)

## Risks and unknowns

- I have not confirmed the exact normalization scheme the maintainer intends. My plan uses
  a fixed saturating transform (`score / (score + 1)`) because it's the simplest
  batch-independent option, but the `+ 1` constant is an arbitrary choice — a maintainer
  may want it tuned against real BM25 score distributions in this corpus, or may prefer a
  different absolute-scale function entirely.
- The issue's wording ("each side is normalized by its own batch's top score") describes
  both the vector and keyword sides, but my plan only changes the keyword side, since my
  repro evidence isolated both symptoms to the keyword-scoring path with a stub vector
  store returning zero hits. I'm deferring the vector-side normalization as out of scope
  for this fix, since I have no repro evidence that it exhibits the same problem.
- Changing the normalization reference could shift relative rankings for other, currently-
  passing queries, since every call's blended scores depend on it. I have not tested this
  against the full PathReview test suite (no existing unit tests cover `HybridRetriever`
  directly, per my search of `tests/unit/`).
- I have not reproduced this against the live ChromaDB-backed app (Docker + API key), only
  against the isolated keyword-scoring path, consistent with my Unit 2 reproduction.

## Deviations

Nothing changed between this plan and the build. The implementation matches Approach steps
1 and 2 exactly: `HybridRetriever.retrieve()` now calls `self.keyword_searcher.index(all_chunks)`
right after fetching the chunks, and keyword score normalization uses
`raw_bm25_score / (raw_bm25_score + 1)` in place of the per-batch max. Scope stayed limited
to `rag/retriever/hybrid.py`; no change to `keyword_search.py` or the vector-similarity path
was needed. Re-running my Unit 2 reproduction script (`repro_issue6.py`) against the built
change confirmed both predicted outcomes from the Test plan: symptom 1 now returns real
results (`c2=0.3788`, `c1=0.3599`, `c4=0.1911`, matching the predicted `score/(score+1)`
values) instead of 0, and symptom 2's `c5` now scores `0.5358` instead of `1.0000`, exactly
matching the Test plan's predicted ≈0.536.
