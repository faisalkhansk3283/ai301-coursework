# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/6

**Verdict output**

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
Ranked read-out — all three accepted

1. Issue #6 — Hybrid retriever skips keyword indexing / per-batch BM25 normalization.
Best fit: this is retrieval/IR scoring work, squarely in your NLP and ML wheelhouse,
and the fix is a real reasoning problem (a normalization bug, not a typo). It is also
the tightest scope of the three — one file, rag/retriever/hybrid.py.

2. Issue #16 — Failed tool calls never reach review output. Agent-orchestration
plumbing across agent/orchestrator.py and core/services/review_service.py. Two layers
rather than one, which is good practice at tracing an unfamiliar repo, and
tests/unit/test_review_service.py already exists to verify against. Slightly behind #6
only because the domain is plumbing rather than modeling.

3. Issue #40 — "Copy link" share button. Passes every required check, but ranks last
on fit: it is full-stack feature work rather than ML/NLP, it carries the largest
estimate (5-8 hours), and it is greenfield — frontend/src/services/ contains only
api.ts, so shareService.ts must be created, along with a new login-less API route and
30-day expiry storage that the issue body specifies but does not design.

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/6",
    "checks": [
      {"name": "maintainer-active", "grade": "pass", "evidence": "Last default-branch commit by Aburke225 on 2026-09-16, 6 days before today (2026-09-22), well within 90 days."},
      {"name": "not-abandoned", "grade": "pass", "evidence": "Repo pushed_at 2026-09-16 (6 days ago), archived: false — within 6 months."},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: [], comments: 0, no cross-referenced events in timeline; repo has 0 PRs total."},
      {"name": "ai-disclosure-ok", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no AI/assistant/generated/disclosure language; no ban stated — silence passes."}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. First full run (20 issues): 17/20 agreement, below the 18/20 bar. Disagreements on
   issue-09 (gold accept, graded reject on `unclaimed`), issue-15 and issue-20 (gold
   reject, graded accept).
2. After tightening the `unclaimed` check to exempt stale claims (no activity for 6+
   months and no open PR), re-ran the three disagreements with `--only
   issue-09,issue-15,issue-20`: issue-09 flipped to correct (accept); issue-15 and
   issue-20 stayed incorrect (unaffected, since they fail on a scope gap the rubric
   does not check for, not on `unclaimed`).
3. Final confirming full run (20 issues, saved via `--save-run eval-run.txt`): 18/20
   agreement — PASS. Category floor held: claimed 4/4, clear-accept 8/8, dead-repo
   3/3, policy 1/1, scope 2/4.

**Issue analysis**

issue-09 (conda/conda#7617). Gold label: accept. My rubric's first run: reject, on
`unclaimed`. The bundle shows no current assignee and only a closed, unmerged linked
PR, but it also has a 2022 comment thread where a contributor asked to take the issue
and a maintainer said "give it a try" — my original `unclaimed` check read any accepted
claim comment as an active claim, so it failed the issue there. In reality that claim
is dead: a stale-bot flagged no activity in 2023, and nothing happened after that, so
by the time of the eval's capture date (2026-08-05) it had been untouched for about
3 years. My rubric could not yet tell the difference between a live claim and an old,
abandoned one, so it rejected an issue that is actually free to take.

**Check rationale**

From `rubric.md`:

> unclaimed | assignees, linked PRs, and claim comments in the thread | no assignee;
> no open linked PR; and if a claim comment was made or accepted, either it has
> activity (a follow-up comment, commit, or PR update) within 6 months of the capture
> date, or its linked PR/attempt is still open. A claim with no activity for 6+ months
> and no open PR counts as stale and does not fail this check | required

My first version of this check treated any accepted claim comment as an automatic
fail, with no way to expire it. That failed issue-09, where a 2022 claim had been dead
for years by the eval's capture date. I added the 6-month staleness window so an old,
abandoned claim no longer blocks an otherwise-good issue, while a genuinely recent or
still-open claim still fails the check.

**Trade-offs**

This change fixed issue-09 (flipped reject to accept, matching gold) without breaking
any previously-correct issue. I confirmed this was isolated with a canary re-run:
`--only issue-09,issue-15,issue-20` after the edit showed issue-15 and issue-20 stayed
at their prior (incorrect) verdict, unaffected by the `unclaimed` change, since they
fail on a missing scope check rather than on claim staleness. The trade-off I accept:
this 6-month window is a judgment call, not a certainty. A claim that goes quiet for,
say, 5 months could still be genuinely abandoned, but my rubric would still fail the
issue on `unclaimed` in that case, meaning I'm willing to reject a few free issues that
look claimed but aren't, rather than risk accepting one that's still actively being
worked on.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Issue #6 (hybrid retriever BM25 normalization bug) fits my background in ML/NLP
   directly, and it is the tightest scope of the three candidates my skill accepted —
   a single file, `rag/retriever/hybrid.py`. I'm planning to commit my available time
   to this one first; if I finish with time to spare I'll look at the other accepted
   issues (#16, #40) as well.

2. My skill's verdict correctly identified that all four required checks pass:
   the repo is actively maintained (commit 6 days before capture), not abandoned,
   unclaimed (no assignee, no comments, no PRs at all in the repo), and the
   contribution policy has no AI restriction. What the rubric could not weigh is
   domain fit and personal difficulty: it has no way to know that BM25/IR scoring is
   in my wheelhouse specifically, or that a one-file fix is easier for me to reason
   about end-to-end than the two-layer plumbing in #16. That's a judgment only I
   could make, which is exactly why the skill ranks by fit but never lets fit
   override a rubric verdict.

3. The verdict confirms the issue is free and the repo is active, but says nothing
   about how hard the underlying bug actually is. I expect the hardest part will be
   understanding the existing BM25/hybrid retriever logic well enough to see why
   per-batch normalization is wrong, before I can write a correct fix — the claim
   comment and maintainer response are unlikely to be the bottleneck here, since the
   repo shows recent, active maintainer commits.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
