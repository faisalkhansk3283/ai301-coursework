# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | the diff's changed files, read against the plan's Scope section (and any `## Deviations` notes), and the description's fidelity claims (e.g. "implements the plan exactly") read against the diff | every file the diff changes is named by the plan (the file itself, its folder, or — for a changelog — "a changelog entry" counts as naming the changelog file) or is disclosed as a deviation; the description's claims about what it does and doesn't change match the diff exactly. An honestly disclosed shortfall or added-but-noted change passes; an undisclosed extra or missing change fails | required |
| test-evidence | the description's Testing section and any `test_evidence.md`, read against the diff's test files and the plan's test plan | every test the description/checklist claims to add is actually present as a file in the diff, AND every testing claim is backed by one of: (a) a pasted command and its actual printed output, (b) a specific, falsifiable fact or exact quoted observation from a real run (an exact pass/skip/fail count, or an exact observed string/behavior, such as a quoted UI message, for interactive/TUI changes that have no literal stdout to paste), or (c) the specific real command named plus its actual stated result (e.g. "`go generate ./...` produces no cheatsheet diff", or "`go test ./...` passes"). A vague claim naming no specific command, fact, or exact observation ("tests pass", "looks good", "works correctly", or a `#` comment that paraphrases/describes a result with no exact quoted text — e.g. "# after: it worked") is not evidence and fails this check. An inline `#` comment IS acceptable evidence when it quotes exact, specific text a real run would actually produce (an exact error message, an exact exit code, an exact count) — the bar is whether the text is specific and falsifiable, not whether it sits on its own output line versus inline after the command | required |
| diff-quality | the diff itself | the diff contains no debug prints, commented-out code, or hunks unrelated to the plan's named files; every changed hunk is either inside a plan-named file/folder or explicitly explained in the plan's Deviations notes, so a reviewer can tell why each hunk exists without asking | required |
| standards-comms | the PR description, read against the repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`) and `docs/CONTRIBUTING.md`'s stated policy | every section the template asks for has real, specific content (not a placeholder or an empty checkbox left unaddressed), and where the repo's policy requires an AI-use disclosure, the description contains one naming how AI was used | required |

## Verdict rule

Accept only if every required check (plan-fidelity, test-evidence,
diff-quality, standards-comms) grades pass. An `unclear` grade on any
required check counts as a fail: a PR whose evidence cannot be verified
from the package is not ready to submit.
