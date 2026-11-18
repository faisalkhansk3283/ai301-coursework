# Shipping a Merged Open-Source Test Fix with a Self-Built Scout, Rubric, and Gate Pipeline

## Overview

I built a four-stage toolchain (an issue scout, a reproduction checker, a plan checker, and a PR pre-check gate) and used it end to end to find, claim, reproduce, plan, build, and ship a real open-source contribution: adding missing test coverage for the atomic parquet/JSON writers in `davidcasr/colombia-open`, a Python data library. The module (`src/builders/formats.py`) had 0% coverage before my change. I wrote `tests/builders/test_formats.py`, covering the four cases the issue named, raised coverage to 96%, opened [PR #19](https://github.com/davidcasr/colombia-open/pull/19), and the maintainer merged it the same day with no requested changes.

## What happened

My scout's first real candidate, an MCP gateway pagination bug, was claimed by a stranger in the few minutes between my scan and when I read the thread — a real cost of a cheap, free search stage against a world that keeps moving. Two more candidates went stale the same week (one closed by someone else, one with a fresh competing claim), which is why I added a required `issue-open` check to my rubric mid-week rather than keep re-discovering the same failure mode by hand.

`colombia-open#14` came from a re-tuned scan after I noticed my ranking tie-break was letting an off-topic, keyword-less query dominate every tie and crowd out domain-matched results. I claimed it, then reproduced the coverage gap myself before writing any tests:

```
Module src.builders.formats was never imported.(module-not-imported)
No data was collected. (no-data-collected)
```

That's stronger evidence than a literal "0%" line — it shows the module was never even imported by the existing 283-test suite, not just under-tested.

My own `plan-check` tool then rejected my first plan, and this is the clearest "I changed something because of what happened" moment of the project. The tool's verdict was blunt about why:

> "diagnosis: ?. You have no repro comment posted on #14... Your rubric grades the plan against repro evidence, and the issue's own claim isn't that."

My plan had asserted the coverage gap without quoting the actual command output anywhere in the plan file itself — the real evidence lived only in my course notes, not in the artifact the tool graded. I fixed it by quoting the exact `pytest --cov` run directly inside `plan.md`, and the second grading pass flipped to `accept`. The same pass also caught a sentence claiming "the functions already behave correctly" before I'd written a single test to show that — a voice-guide violation I fixed by rewording it to claim only that no production changes were planned.

With a passing plan, I wrote five tests (parquet round-trip, JSON round-trip, parent-directory creation, no-leftover-temp-file, and a simulated-failure-leaves-no-debris case), each stubbing a filesystem side effect (`ensure_normalized_dir()`) with `monkeypatch`. The run went from 0% to 96% coverage, with one line honestly left uncovered rather than padded out with an extra test the issue never asked for:

```
src/builders/formats.py      28      1    96%   38
```

Before opening the PR, my own `ship-gate` tool ran the repo's real checks (`make test`, `make lint`, `make typecheck`, `make doctor`) plus a live-mode grading pass, and passed on the first try. I disclosed one thing explicitly in the PR rather than hiding it: I had promised in my claim comment to wait for the maintainer's assignment before starting, but began building after about a day of silence because of my own course deadline. The result: the maintainer merged the PR the same day, with this comment:

> "Merged, thanks @faisalkhansk3283! Great PR: clear before/after coverage, all four cases covered, and thanks for being upfront about the claim timing. Welcome to colombia-open! 🎉"

That's a concrete result that landed because of the work: a real merge, from a maintainer who explicitly called out the disclosed deviation as a positive rather than a problem.

## Limitations and what's next

The test suite I wrote is deliberately incomplete in one named way: line 38 of `formats.py`, the temp-file cleanup inside `write_json_atomic`'s failure path, has no direct test, since my single failure case exercises only `write_parquet_atomic`, matching the issue's own example. I disclosed this in the PR rather than quietly reaching for 100% with a test the issue never requested.

More broadly, my `issue-scout` rubric still has two gaps I identified but chose not to patch mid-project: it has no check for a repository's actual adoption (stars, release history), so a repo created hours before its own issue can still pass on two fresh commits alone; and its scope check doesn't reject pure-documentation issues the way an earlier version did. Next, I'd add a lightweight adoption signal to the rubric and re-run it against a fresh scan to see whether it changes which candidates actually surface, rather than assuming the fix works from reasoning alone.
