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
**Pull Request:** https://github.com/davidcasr/colombia-open/pull/19

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

**Unit 7: installed the ship-gate, wrote a manifest, and got a clean PASS on the first try**

I installed the ship-gate (a hook that runs my manifest's checks, then `pr-precheck`, before Claude Code can open a PR) and wrote `ship-manifest.md` for `colombia-open`. My first draft listed `make check`, exactly as `CONTRIBUTING.md` says, but testing it in a fresh terminal revealed `make check` depends on `make build`, which crashed with a `MemoryError` fetching a live dataset — unrelated to my test-only fix. I narrowed the manifest to `make test`/`lint`/`typecheck`/`doctor` and logged the excluded `make build` failure with a dated reason instead of silently dropping it. The ship-gate then passed on the first real run, and `pr-precheck` accepted all four required checks. The only real friction was mechanical: PowerShell's quoting rules broke the literal `gh pr create` command string twice before I switched to describing the command in plain language and letting Claude Code construct its own correctly-quoted call.

**Unit 8: installed the PR monitor, wrote a playbook, and used its draft to close out a merged PR**

The PR monitor read PR #19 (6 passing checks, merged, one maintainer comment) and drafted a short thank-you for the merged thread. I treated it as a starting point: I rewrote it in my own words in `my-reply.md`, dropped its line about issue #14 possibly still being open, and posted it through Claude Code so my voice guard checked it first. What fell short: the report still showed `REVIEW_REQUIRED` and `mergeable: UNKNOWN` on an already-merged PR, the draft hedged about whether #14 auto-closed instead of checking (it had), and its em dashes came out garbled, which looks like a character-encoding glitch. I left my playbook unchanged, because a merged PR never reaches the nudge or walk-away rules, so this run didn't test them. Its thresholds also rest on only one outside-contributor PR (my own, merged in about 5 hours), so I set them well past that wait and would revise them if a slower review proved them wrong.

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

`src/builders/formats.py` holds two functions, `write_parquet_atomic` and
`write_json_atomic`, that write the library's published `.parquet`/`.json` outputs
atomically (temp file, then `os.replace()`, so a crash mid-write never leaves a half-written
file). Despite being the code that produces every file the project's users download, this
module currently has 0% test coverage — no existing test imports it at all.

**Expected Behavior**

The module should have tests covering: the happy path (write then read back with
`pl.read_parquet`/`pl.read_json`, confirming data round-trips correctly, including that a
leading-zero administrative code like `"05"` survives as a string and doesn't silently
become the int `5`); directory auto-creation when the target's parent doesn't exist; no
leftover `.{name}.*.tmp` files after a successful write; and a failed write (simulated via
`monkeypatch`) leaving neither the target file nor the temp file behind.

**Current Behavior**

I confirmed the 0% figure myself: running the full test suite with coverage scoped to this
module produces `Module src.builders.formats was never imported` / `No data was collected`
— not a low percentage, but literal zero executions of any line in the file by any existing
test.

**Affected Components**

`src/builders/formats.py` (the two functions above) and `src/builders/_shared.py`
(`ensure_normalized_dir()`, which both functions call as a side effect and which my tests
need to neutralize with `monkeypatch` so they don't touch the real repo's `data/normalized/`
directory). The fix is purely additive — a new file, `tests/builders/test_formats.py` — with
no production code changes.

**Toolkit**

- Tool: `repro-check` (graded my repro report and claim comment together).
- What it produced: an `accept` verdict, after flagging two sentences that overstated what
  my evidence actually showed (see Reproduction Process Toolkit block above for detail).
- Difficulties: none beyond what's already logged in Reproduction Process — this section is
  a restatement of that same verified finding in the story's required format.
- What changed: nothing new here; see Reproduction Process for the actual revisions.

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

This isn't a bug with a root cause to trace — it's a coverage gap. `write_parquet_atomic`
and `write_json_atomic` in `src/builders/formats.py` were never given tests when written, so
`coverage` reports the module as never imported by any test. Both functions share the same
shape: call `ensure_normalized_dir()` (a side effect in `src/builders/_shared.py` that
creates `data/normalized/`), create the target's parent directory, write to a temp file via
`tempfile.mkstemp`, `os.replace()` it into place, then a `finally` block that removes the
temp file if it still exists (a no-op on success, since `os.replace` already moved it away;
the real cleanup path is on failure).

Confirmed before writing any tests, on `main`:

```
$ .venv/Scripts/pytest tests/ --cov=src.builders.formats --cov-report=term
collected 283 items
[... 283 passed ...]
.venv\Lib\site-packages\coverage\inorout.py:560: CoverageWarning: Module src.builders.formats
was never imported.(module-not-imported)
.venv\Lib\site-packages\coverage\control.py:967: CoverageWarning: No data was collected.
(no-data-collected)
WARNING: Failed to generate report: No data to report.
```

This matches the issue's own claim of 0% coverage exactly — stronger, even, since it shows
the module was never imported at all, not just under-tested.

**Proposed Solution**

Add `tests/builders/test_formats.py` covering exactly the four cases the issue names, and
nothing else. I am **not** changing any production code in `formats.py` or `_shared.py` — no
production code changes are planned; the tests I write are what will show whether the
functions behave correctly. I am also not adding tests beyond the four named cases (e.g. no
new edge cases I thought of myself), to keep the PR scoped to what the issue actually asked
for.

**Implementation Plan**

1. Create `tests/builders/test_formats.py`, following the flat-function style of
   `tests/builders/test_datasets.py` (no custom fixtures/classes, plain `def test_*()`
   functions; pytest's built-in `tmp_path` and `monkeypatch` fixtures are used directly).
2. `test_write_parquet_atomic_roundtrip` / `test_write_json_atomic_roundtrip`: build a small
   `pl.DataFrame` including a leading-zero string code (e.g. `{"cod_dpto": ["05", "08"]}`,
   matching hard rule #2 in `CLAUDE.md`), write it with each function to a `tmp_path`, read
   it back with `pl.read_parquet`/`pl.read_json`, and assert it equals the original
   (including that `cod_dpto` round-trips as strings, not ints).
3. `test_write_atomic_creates_parent_directory`: call with a `path` whose parent doesn't
   exist yet under `tmp_path`, assert the directory gets created and the file is written.
4. `test_write_atomic_leaves_no_temp_file`: after a successful write, assert no
   `.{name}.*.tmp` file remains in the target directory (glob the parent for the temp
   pattern and assert it's empty).
5. `test_write_atomic_failure_leaves_no_debris`: use `monkeypatch` to make
   `pl.DataFrame.write_parquet`/`write_json` raise, assert neither the target file nor any
   temp file exists afterward.
6. In every test, use `monkeypatch` to stub `ensure_normalized_dir()` as a no-op (per the
   issue's own "Ojo" note), so tests never touch the real repo's `data/normalized/`.
7. Re-run the same command as the before-evidence above, and confirm it now reports real
   coverage of `formats.py` instead of "never imported." Also run `make test`, `make lint`,
   `make typecheck`, `make doctor` (my manifest's checks; `make build` is excluded there — a
   pre-existing `MemoryError` fetching a dataset on `main`, unrelated to this fix, disclosed
   in `ship-manifest.md`'s excluded table).

(No deviations recorded yet — this section will be updated if the build departs from this
plan.)

**Toolkit**

- Tool: `plan-check` (grades my plan and draft plan comment together before building; plan
  stays local per the wild scope, never posted to the issue thread).
- What it produced: `reject` on the first pass — two required checks (`diagnosis`, `test`)
  came back `unclear` because my plan asserted the coverage gap without quoting any actual
  command output in the drafts themselves (my Unit 6 repro evidence lived only in this story
  file, not in `plan.md`). `accept` after quoting the real `pytest --cov` run directly in
  both `plan.md` and `plan-comment.md`.
- Difficulties: a voice-guide catch survived even after the accept-worthy fix: "the
  functions already behave correctly" claimed a result my tests hadn't run yet.
- What changed: quoted the coverage-run output in both drafts; reworded the overclaiming
  sentence to state only that no production changes are planned, not that correctness is
  already proven.

---

## Testing Strategy

The observable check is the exact same coverage command, run on `main` before and on my
branch after. **Before:** `coverage` reports `Module src.builders.formats was never
imported` / `No data was collected` — literal zero lines executed. **After:** `5 passed`,
`96%` coverage on `src/builders/formats.py` (one line missed, disclosed below). Full output
of both runs, plus `make test`/`make lint`/`make typecheck`/`make doctor`, is in
`test_evidence.md`.

Beyond the coverage swing itself, each of the 5 new tests proves one specific observable
behavior named in the issue: a round-trip read-back for each format (including that a
leading-zero string code survives, not silently becoming an int), parent-directory
auto-creation, no leftover `.{name}.*.tmp` file after success, and no leftover file (target
or temp) after a simulated failure. The one uncovered line (`write_json_atomic`'s
failure-path cleanup) is disclosed rather than hidden: my single failure test only exercises
`write_parquet_atomic`, matching the issue's own example wording.

The full existing suite (283 tests) plus the 5 new ones (288 total) pass together, so the
new tests don't just pass in isolation — they coexist cleanly with everything else.

**Toolkit**

- Tool: none new — the testing itself was done directly with `pytest`/`coverage`/`make`, not
  through a course skill. `repro-check`'s before/after evidence pattern (from Units 2 and 6)
  is what shaped how I structured this comparison.
- What it produced: the before/after coverage swing in `test_evidence.md`.
- Difficulties: none beyond `make build`'s pre-existing `MemoryError`, already logged.
- What changed: nothing new here.

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**2026-11-06**

Installed the ship-gate (Unit 7) and wrote `ship-manifest.md`. First attempt listed `make
check` as the required gate, following `CONTRIBUTING.md`'s own wording literally — but
running it myself revealed `make check` depends on `make build`, which runs the full live
data-extraction pipeline across every source and crashed with a `MemoryError` fetching the
Saber11 education dataset on this machine. That's unrelated to my fix (test coverage only,
no extractor touched), so I swapped the manifest to the narrower `make test`/`make lint`/
`make typecheck`/`make doctor`, and logged `make build`'s failure in the manifest's
"Excluded" table with today's date and the reason, rather than silently dropping it.

Also decided to start building (writing the actual test file) before the maintainer
assigned the issue, even though my claim comment said I'd wait. No response after about a
day, and Unit 7's course deadline doesn't wait either. This is a deliberate, disclosed
deviation from what I promised in the claim comment — not a silent one — and I'm noting it
here and will note it again in the PR description.

**2026-11-06 (later)**

Wrote `tests/builders/test_formats.py` exactly per the plan: 5 tests (parquet round-trip,
JSON round-trip, parent-directory creation, no-leftover-temp-file, failed-write-leaves-no-
debris), each stubbing `ensure_normalized_dir()` with `monkeypatch` individually (no custom
fixtures, per the plan). Ran clean on the first try: `pytest tests/builders/test_formats.py
--cov=src.builders.formats --cov-report=term-missing` → 5 passed, 96% coverage, one line
missed (line 38, the temp-file cleanup inside `write_json_atomic`'s failure path) — my
single failure test only exercises `write_parquet_atomic`'s failure path, matching the
issue's own wording, which names `write_parquet` as its example. Disclosing this rather
than padding in an extra test to chase 100% the issue didn't ask for. Full manifest
(`make test`, `make lint`, `make typecheck`, `make doctor`) all green: 288 tests passed
(283 existing + 5 new), lint clean, typecheck clean, doctor clean. Committed as
`test: agregar tests para builders/formats.py` on branch `test/14-formats-coverage`.

**2026-11-07**

Opened PR #19 after the ship-gate passed on its first run. The PR description disclosed that I built before being assigned, despite my claim comment saying I'd wait. davidcasr merged it the same day with no change requests, so there was no iteration round.

**2026-11-08**

Installed the PR monitor and wrote my playbook. `--state` showed the PR as merged. The monitor drafted a short merged-thread close. I rewrote it in `my-reply.md`, left out the "issue may still be open" line, and posted it through Claude Code. The draft also flagged that line 38 of `formats.py` (the `write_json_atomic` failure-path cleanup) is the only uncovered line. I chose not to open a follow-up, since the issue didn't ask for it and I already disclosed the gap in the PR.

**Toolkit**

- Tool: none new this entry (plan-check already covered in Solution Approach; this section
  is the build log itself).
- What it produced: a committed, fully-passing test file.
- Difficulties: `make check`'s dependency on `make build` (see first entry above).
- What changed: narrowed the manifest's required checks and disclosed the excluded one.

---

## Pull Request

**PR Link:** https://github.com/davidcasr/colombia-open/pull/19

**PR Description:** Explains the 0% coverage gap (confirmed with a quoted `coverage` run
before any tests existed), what the new `tests/builders/test_formats.py` covers (the four
cases from the issue), the before/after coverage swing (0% → 96%, one line disclosed as
uncovered), every manifest check's output (`make test`/`lint`/`typecheck`/`doctor` green,
`make build` excluded with a named, pre-existing `MemoryError` unrelated to this change), the
AI-use disclosure (Claude Code, named skills used, diff reviewed and understood), and an
explicit note that I built this before receiving an assignment, despite my claim comment
saying I'd wait — disclosed rather than left silent.

**Maintainer Feedback:**

- 2026-11-07: davidcasr merged the PR directly, with no requested changes, and left this
  comment: *"Merged, thanks @faisalkhansk3283! Great PR: clear before/after coverage, all
  four cases covered, and thanks for being upfront about the claim timing. Welcome to
  colombia-open! 🎉"*
- 2026-11-08: I replied with a short thank-you (via `my-reply.md`, checked by my voice guard
  before posting): *"Thanks for merging, and for the kind words — glad the before/after
  coverage and the note about the claim timing were useful rather than awkward. Good first
  contribution to make, and a good project to have made it on."* Issue #14 auto-closed via
  the `Closes #14` line in the PR description.

**Status:** Merged (2026-11-07)

**Toolkit**

- Tool: `ship-gate` (runs my manifest's checks, then `pr-precheck` in live mode, before
  Claude Code is allowed to open the PR).
- What it produced: `ship-gate: PASS` on the first attempt — all 4 manifest checks green,
  and `pr-precheck` accepted on all 4 required checks (`plan-fidelity`, `test-evidence`,
  `diff-quality`, `standards-comms`).
- Difficulties: the hook-wiring test (`gh pr create --help`) only showed one stop
  (ship-gate's) instead of the expected two (voice-guard's too) — both hooks verified
  individually working on their own trigger commands, so I treated this as a display quirk
  in how Claude Code surfaces multiple blocking hooks rather than a real gap, and moved on
  rather than spend more time chasing it. Separately, actually opening the PR needed two
  rounds of fixing shell-quoting: PowerShell doesn't support bash-style backslash-escaped
  quotes, and embedding literal nested quotes in the `claude "run this command: ..."`
  one-liner kept breaking. Fixed by describing the command in plain language instead of a
  literal string, letting the agent construct its own correctly-quoted `gh pr create` call.
- What changed: none to the ship-gate itself — the fixes were all in how I invoked things
  around it, not in the gate's own configuration.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned to write tests for code with filesystem side effects using pytest's `tmp_path` and `monkeypatch`: stubbing `ensure_normalized_dir()` so tests never touched the real `data/normalized/`, and forcing `write_parquet` to raise to check that a failed write leaves no target or temp file. I learned that scoping `--cov` to one module can report "never imported" and "no data collected", which is stronger evidence than a low percentage. I used Polars round-trips to confirm that leading-zero codes like `"05"` stay strings, and I followed the repo's Conventional Commits PR-title rule and its Spanish-language docs.

**Challenges Overcome**

The first scouted issue (`Tracekit#658`) was claimed by someone else between my scan and my read, and two earlier candidates went stale, so I added an `issue-open` check and re-ran the scan. `make check` crashed with a `MemoryError` inside `make build`, unrelated to my change, so I narrowed the manifest to `make test`/`lint`/`typecheck`/`doctor` and logged the exclusion with its reason. On Windows I fixed a broken `python3` Store stub in my hook and PowerShell quoting problems around `gh pr create`.

**What I'd Do Differently Next Time**

I'd re-check an issue's claim state right before acting, not only at scan time. I'd run the repo's full check command in a fresh terminal on day one, before writing a manifest around it. And I'd wait for the assignment the claim comment promised. Building early worked out here because I disclosed it, but it was a risk I took on my own.



---

## Resources Used

- `davidcasr/colombia-open` `CONTRIBUTING.md`: PR-title format, the claim-and-wait rule, the AI-use conditions, and the `make check`/`make doctor` expectations.
- Issue #14 (https://github.com/davidcasr/colombia-open/issues/14): it named the four test cases and the "Ojo" note about stubbing `ensure_normalized_dir()`.
- The repo's `docs/troubleshooting/entorno-windows.md`: the Python 3.11 guidance that made `make bootstrap` work on the first run.
- `tests/builders/test_datasets.py` in the repo: the flat-function style I copied for the new tests.
- davidcasr's merge comment on PR #19: the maintainer feedback recorded under Pull Request.
