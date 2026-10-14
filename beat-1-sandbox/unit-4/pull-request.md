# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/237

**Branch**

fix/6-hybrid-retriever-keyword-index

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test (3 packages, `--limit 3`): 2/3 agreement. `pkg-02` (gold: accept) failed my
   `test-evidence` check, which at that point required a literal pasted command-and-output
   pair for every testing claim — too strict for a quantified claim like "passes (1462
   passed, 12 skipped)" that wasn't wrapped in a `$ command` block.
2. After loosening `test-evidence` to also accept a specific falsifiable fact from a real
   run, re-ran the same 3 packages with `--only pkg-01,pkg-02,pkg-03`: 3/3 agreement.
3. First full run (20 packages): 17/20 agreement, below the 18/20 bar. Category tally:
   `clear-accept 2/7`, `not-tested 4/4`, `silent-drift 4/4`, `standards-wall 2/2`,
   `unreviewable 3/3` — `clear-accept` was clearly the weak category, with 5 genuinely
   complete PRs rejected on `test-evidence` for supplementary checks (lint/codegen/`go
   test`) that were named but not shown with a count.
4. Revised `test-evidence` twice more over several `--only` rounds against the 5 failing
   clear-accept packages plus `not-tested`-category canaries (to confirm vague claims still
   correctly failed): first to accept a named real command plus its stated result (not just
   literal output), then to also accept an inline `#` comment when it quotes exact, specific
   text (not a vague paraphrase) — this is the distinction between calib-03's vague "# after:
   it worked" and pkg-11's "# before: rg: ...dangling '\\'" (an exact real error message).
5. Final confirming full run (20 packages, saved via `--save-run eval-run.txt`): 20/20
   agreement — PASS. Every category fully matched: `clear-accept 7/7`, `not-tested 4/4`,
   `silent-drift 4/4`, `standards-wall 2/2`, `unreviewable 3/3`.

**Package analysis**

pkg-11 (category: `clear-accept`). Gold label: accept. My rubric's run: accept on the final
confirming run — correct, though this package flipped between reject and accept across a
couple of intermediate runs due to model variance on the same rubric wording (confirmed by
re-running it with `--only` and seeing both verdicts occur on unchanged files). The reason
it's instructive: its "Test evidence" section shows a repro re-run as inline `$ command  #
output` lines, where the text after `#` is an exact, real error message
("`rg: ./.gitignore: line 1: error parsing glob 'foo\': dangling '\'`") and an exact test
count ("`cargo test -p ignore` passes (214 tests)"), not a vague paraphrase. My
`test-evidence` check initially treated any `#`-annotated line as non-evidence (matching
the activity's rule against calib-03-style notes), which wrongly failed pkg-11 too. I
revised the check to key on whether the `#` comment quotes exact, falsifiable text, not on
whether it's a `#` comment at all — that's what makes pkg-11 correctly pass while a vague
`#` note (like calib-03's) still correctly fails.

**Check rationale**

From `rubric.md`:

> test-evidence | the description's Testing section and any `test_evidence.md`, read
> against the diff's test files and the plan's test plan | every test the
> description/checklist claims to add is actually present as a file in the diff, AND every
> testing claim is backed by one of: (a) a pasted command and its actual printed output,
> (b) a specific, falsifiable fact or exact quoted observation from a real run (an exact
> pass/skip/fail count, or an exact observed string/behavior, such as a quoted UI message,
> for interactive/TUI changes that have no literal stdout to paste), or (c) the specific
> real command named plus its actual stated result (e.g. "`go generate ./...` produces no
> cheatsheet diff"). A vague claim naming no specific command, fact, or exact observation
> ("tests pass", "looks good", "works correctly", or a `#` comment that paraphrases/
> describes a result with no exact quoted text — e.g. "# after: it worked") is not evidence
> and fails this check. An inline `#` comment IS acceptable evidence when it quotes exact,
> specific text a real run would actually produce — the bar is whether the text is specific
> and falsifiable, not whether it sits on its own output line versus inline after the
> command | required

I started this check much closer to the in-class activity's literal rule ("a note after #
saying what it printed doesn't count"), which is correct for vague paraphrases but was too
blunt once applied to real PRs: it rejected genuinely complete, gold-accept packages
(pkg-05, pkg-08, pkg-11, pkg-13, pkg-16) whose supplementary checks (lint, codegen, a named
test command) don't always produce a literal multi-line output block, or whose TUI/
non-CLI behavior can only be documented as an exact quoted observation. I rewrote the check
three times to separate "is this inline/terse" (irrelevant) from "is this specific and
falsifiable" (the actual bar), which is what the final wording states.

**Trade-offs**

Loosening `test-evidence` from "always require literal pasted output" to "accept a named
command's stated result, or an exact falsifiable quote" trades some mechanical certainty
for correctly accepting real, gold-labeled-accept PRs. The risk this gives up: a dishonest
author could in principle write "`go test ./...` passes" without having actually run it,
and my check has no way to verify the command was truly executed — it can only tell a
specific, named claim from a vague one, not a specific lie from a specific truth. I checked
this trade-off didn't let anything obviously bad back in by re-running the `not-tested`
category's packages (`pkg-04`, `pkg-07`, `pkg-10`, `pkg-14`) as canaries at each revision
step; all four stayed correctly rejected across every run, including the final one (`not-
tested 4/4`), because their testing claims are genuinely vague ("colors work now in paging
mode", with no command or exact text at all) rather than specific-but-unverifiable.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
