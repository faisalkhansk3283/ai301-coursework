# Contribution: test: agregar tests para la escritura atómica de builders/formats.py

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Faisal Rasheed Khan
**GitHub Username:** faisalkhansk3283
**Issue:** https://github.com/davidcasr/colombia-open/issues/14
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: built issue-scout and wrote a search strategy that finds real GitHub issues**

I upgraded my Unit 1 `issue-select` skill into `issue-scout`, writing a `search-strategy.md` with two `gh search issues` queries tied to my fit profile and five ranking filters over free result fields. The first scan exposed a real gap: my rubric had no check for whether an issue was actually a small, bounded piece of code work, so a docs-only README reorganization and a research-scale infrastructure feature both passed. I added a required `bounded-scope` check and a second ranking tie-break (most-recently-updated), then confirmed the fix by re-grading real candidates: it correctly rejected an issue that only described a symptom with no named fix, while still accepting a genuinely bounded one.

**Unit 6: installed the voice guard and wrote a repo-memory file for colombia-open**

I installed the voice guard (a PreToolUse hook that checks every outgoing `gh` comment against my voice guide before it posts) and wrote an Upstream section with rules for claims, questions, and pings. The wiring test initially passed unblocked because the model answered `gh issue comment --help` from its own knowledge instead of actually invoking the Bash tool — once I forced a real tool call, the guard correctly stopped it. Separately, the hook's own command called `python3`, which is a broken Windows Store stub on my machine (the same issue I'd hit back in Unit 1), so I had to change it to `python` and hardcode my absolute home path since `$HOME` wasn't expanding in the hook's execution context. I also wrote `CLAUDE.local.md` for my claimed issue's repo, distilling `CONTRIBUTING.md`'s conventions and a scoped read of the two files my issue touches, and excluded it via `.git/info/exclude` so it never reaches my PR diff.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found this issue through my own scout, `issue-scout`, searching GitHub with two queries
built from my fit profile (Python, ML/NLP/data-science-adjacent work, avoiding pure docs)
and five ranking filters over the free result fields. This wasn't my first pick this week:
my scout's first scan surfaced `Cygnux-Labs/Tracekit#658`, an MCP gateway pagination bug
that fit my profile even better, but a stranger claimed it in the few minutes between my
scan and when I read the thread myself. That's exactly the "a stranger's claim blocks you"
rule the wild scope file warns about, and it's a real cost of the free/cheap search stage:
the world keeps moving between when you grade and when you act.

I also had to re-grade my picks this week after patching my rubric's scope check
(`bounded-scope` → `scope-feasible`, per the unit-6 patch): the old check was tuned to the
unit-1 sandbox's perfectly-bounded issues and would have rejected most real-world work with
any open edge. That re-grade also showed my two prior candidates (a Eurocode steel-formula
issue and a WrenAI deploy bug) had both gone stale — one closed by someone else, one with a
fresh competing claim — which is why I added a required `issue-open` check mid-week.

`davidcasr/colombia-open#14` came from a second, fixed scan: I'd noticed my "most recently
updated" tie-break was letting a keyword-less query dominate every tie, crowding out my
actual domain-matched results, so I added an explicit preference for the domain-matched
query before re-running. This issue asks for tests covering `src/builders/formats.py`'s
atomic `.parquet`/`.json` writers (currently 0% coverage) — real Python code, not docs, with
an active maintainer (committed the same day), and the repo explicitly welcomes Claude
Code/AI-assisted PRs with clear conditions. My scout's verdict was `accept`, and I agree
with it: the issue names exactly what to build, has test-case-level acceptance criteria, and
I read the full thread and `CONTRIBUTING.md` myself before claiming. I hope to get
comfortable reading an unfamiliar codebase's conventions (this one's in Spanish) and writing
tests against behavior I didn't write myself, which is a different muscle than fixing a bug
I already understand.

Honest read of the scout: it did real, useful work (ranking, scope, AI-policy, staleness
checks) but it can't see what changes in the seconds after it grades, and this week exposed
real rubric gaps (no check for whether a claim follows a repo's *own* stated claiming
procedure, no check for a repo's actual adoption/usage). I fixed the ones that mattered for
this choice and logged the rest honestly in `scan-notes.md` rather than letting them block
picking an issue.

**Claim comment:** https://github.com/davidcasr/colombia-open/issues/14#issuecomment-6703115542

**Toolkit**

- Tool: `issue-scout` (my search strategy, rubric, and ranking filters), used across three
  scans this week plus one re-grade of prior picks after the rubric patch.
- What it produced: a ranked, graded shortlist each time, ending in the accepted candidate
  `colombia-open#14` (difficulty: straightforward) after two earlier accepted picks went
  stale between grading and claiming.
- Difficulties: the `bounded-scope` check was too strict for real issues (patched to
  `scope-feasible`); no `issue-open` check existed (added one after a closed issue slipped
  through); the ranking tie-break let an off-topic, keyword-less query dominate ties (fixed
  by preferring the domain-matched query); `-linked:pr` misses PRs that only mention an
  issue instead of formally linking it (caught only by the rubric's own thread-reading
  check, twice).
- What changed: added `issue-open` as a required check, tightened `unclaimed`'s wording for
  conditional claims, and reordered the ranking filters to prefer domain-matched results.
  Left two known gaps unpatched for now (repo-adoption check, docs-only rejection under the
  new scope check) since they didn't affect this week's choice — logged in
  `~/.claude/skills/issue-scout/scan-notes.md`.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

Windows, Python 3.11.15 (the repo's own guidance: avoid 3.12/3.14, which have known
wheel/OpenSSL bugs on Windows). Forked `davidcasr/colombia-open` to
`faisalkhansk3283/colombia-open` and cloned it locally. Set up with:

```
uv venv --python 3.11 .venv
make bootstrap
```

`make bootstrap` installed 114 packages (`pytest==9.2.0`, `pytest-cov==7.1.0`,
`coverage==7.17.2`, `polars==1.44.2`, among others) and registered the repo's pre-commit
hook. No challenges came up — the repo's own Windows note (use 3.11, not 3.12/3.14) was
enough to avoid the known wheel issues, and `make bootstrap` completed cleanly on the first
run.

**Steps to Reproduce**

1. From the clone root, run `.venv/Scripts/pytest tests/ --cov=src.builders.formats --cov-report=term` — the whole existing test suite with coverage scoped to the module the issue names. (The issue's own "Cómo verificar" section targets `tests/builders/test_formats.py` directly, but that file doesn't exist yet — that's the issue — so I ran against the full suite instead, scoped to the same module.)
2. Watch the 283 existing tests run and pass.
3. **Observed result:** `coverage` reports `Module src.builders.formats was never imported` and `No data was collected` — not a low percentage, but literally zero lines of the module ever executed by any existing test, exactly matching the issue's claim of 0% coverage.

**Reproduction Evidence**

- **Commit showing reproduction:** none (no code changes yet — I'm waiting to be assigned per the repo's `CONTRIBUTING.md`, which asks claimants to comment "lo tomo" and wait before starting work)
- **Screenshots/logs:**
  ```
  collected 283 items
  tests\builders\test_datasets.py ...
  [... 281 more passing tests across extractors/, validation/, test_core_api.py,
       test_data_manager.py, test_mcp_server.py ...]

  .venv\Lib\site-packages\coverage\inorout.py:560: CoverageWarning: Module src.builders.formats
  was never imported.(module-not-imported)
  .venv\Lib\site-packages\coverage\control.py:967: CoverageWarning: No data was collected.
  (no-data-collected)

  WARNING: Failed to generate report: No data to report.
  ```
- **My findings:** The coverage gap is real and exactly as described. Reading `src/builders/formats.py` and `src/builders/_shared.py` confirms both `write_parquet_atomic` and `write_json_atomic` call `ensure_normalized_dir()` before writing — a side effect the issue's own "Ojo" note already warns about, meaning my eventual tests need `tmp_path` and a `monkeypatch` stub for that call, not just for the file paths themselves. The 283 unrelated tests passing cleanly shows my environment and dependency setup are correct; it doesn't by itself prove the coverage gap is isolated only to `formats.py` — just that this specific scoped coverage run found none.

**Toolkit**

- Tool: `repro-check` (graded my claim comment and repro report together against the
  issue, the repo's `CONTRIBUTING.md`, and my voice guide).
- What it produced: an `accept` verdict, but with two real catches first: a line claiming I
  followed the issue's exact verification command when I'd actually adapted it (I hadn't
  noticed the file-doesn't-exist-yet mismatch until it was pointed out), and a line claiming
  the passing test suite "confirmed" the coverage gap was isolated to `formats.py`, which
  overstated what that evidence actually showed.
- Difficulties: `behavior-matches` initially seemed like it might not apply cleanly to a
  test-writing issue with no bug to reproduce (flagged in `scan-notes.md` earlier this
  week), but it worked fine in practice: "0% coverage" is itself a checkable behavior, and
  the tool correctly read "never imported" as even stronger evidence than a literal 0%
  figure would have been.
- What changed: reworded both flagged sentences in `repro-draft.md` to state exactly what I
  did (adapted the command, and why) and exactly what the evidence supports (environment
  correctness, not isolation) before finalizing this section.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
