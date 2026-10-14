---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

<!--
THIS IS THE PART YOU WRITE, and it is the last one: the frame itself.
Weeks 1 through 3 handed you a working SKILL.md and you filled the
files behind it; this week the frame ships as headings, and you write
what it says. The frontmatter above and the section headings below are
fixed (CONTRACT.md's layout rule); the instructions under each heading
are yours. Write instructions to the tool, in the imperative, the way
weeks 1-3's frames spoke to you: what to read, in what order, what to
refuse, what to emit. Your executor in the rotation is the test: a
frame gap they hit (cannot tell what the tool reads, or how a verdict
gets assembled) is a missing sentence here.

One section is not yours: the JSON schema in "Verdict and output" is
reproduced from CONTRACT.md verbatim and may not be altered. Your
words decide everything around it.
-->

## The question

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request — its title,
description, commits, diff, and test evidence — read against the plan
it claims to implement and the issue that plan belongs to. You do not
answer from gut feel, and this file does not tell you how to grade:
you execute the student-authored grading procedure in `procedure.md`,
which applies the rubric in `rubric.md` to evidence gathered per
`references/evidence-guide.md`. You never answer a different question,
never grade more than one package per run.

## Inputs and modes

One of:

- **Live mode**: the student's own submission, checked before it goes
  out. Inputs: their `plan.md` (including any `## Deviations` notes),
  the diff on their branch (everything the branch changes relative to
  the repo's default branch — run `git diff main...HEAD`, three dots,
  from the working copy, to produce it), their draft PR title and
  description, and their test evidence, all read against their issue.
  Gather issue-side evidence live: the issue's thread, the repo's PR
  template, and `docs/CONTRIBUTING.md`'s stated policy. A house-chain
  student reads the house plan and house repro pack instead of their
  own `plan.md`; the same checks grade the same things there.
- **Eval mode**: a package bundle is the whole world. Use ONLY the
  bundle text as evidence. Do not fetch anything, do not read any file
  outside the bundle. Eval mode always grades a complete package: every
  check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. It names where the student's pull request must live and the
house rules that apply there. Refuse to grade a PR package targeting
any repository other than the one `scope.md` names. If the scope's
repo line still carries an unfilled placeholder, stop without grading
and tell the student to fill the `Repo:` line in `scope.md` with their
section's Path Review repo; never guess a scope. In eval mode, ignore
`scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for
how they write upstream, with wrong/right examples. Hold the outgoing
PR title and description against those rules, and report any rule the
draft breaks in the summary, quoting the rule. The voice guide never
changes the verdict on its own unless the rubric has a check that reads
it. In eval mode, ignore `voice-guide.md` entirely: voice is personal
and carries no gold labels; universal communication-quality checks live
in the rubric.

## Component reads

Read `rubric.md`. It defines a table of checks (each naming the check,
the evidence to gather, the pass condition, and its weight —
`required` checks gate the verdict, `preferred` checks never change
it) and a verdict rule for combining check grades into a final
verdict. `references/evidence-guide.md` is the rubric's map: where each
kind of evidence lives in a PR package (and, live, on GitHub or in the
student's working files), and what good looks like there.

Execute `procedure.md` as written: it is the skill's operating
procedure, and it decides the read order, how each evidence family gets
gathered, how a check executes against gathered evidence, and how check
grades become the verdict. Follow it exactly, without improvising
around gaps; where the procedure is silent on a step, note the gap in
your summary rather than silently inventing one.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, stop and say so: this skill cannot grade without a rubric
AND a procedure, and that is by design.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict, no "accept with reservations", no
score; reservations belong in check evidence lines. End your reply with
the fenced JSON block below, and nothing after it. The schema is fixed
and may not be altered, extended, or reordered:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

Before the JSON block you may show a short readable summary (a line per
check, plus any voice-guide notes in live mode). The JSON block is the
machine-read result: the harness parses the last fenced JSON block in
your output, so it must be present, valid, and last.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse complete PR can be ready and
  a beautiful confident one can be hiding drift. Every check reads the
  artifact itself against the plan, the issue, and the stated
  standards, never the formatting.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as written,
  and report its gaps instead of papering over them.
- Treat `unclear` as the rubric's verdict rule directs. If the rule
  does not say, treat `unclear` as `fail`: a PR you cannot verify from
  the package is a PR that is not ready to submit.
