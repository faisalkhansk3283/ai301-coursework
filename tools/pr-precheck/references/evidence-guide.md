# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the plan-context block's "Files:"
scope line and any deviation notes, read against the candidate PR's
diff file list and the description's fidelity sentences. In live mode,
your `plan.md` (its Scope section and `## Deviations` heading), your
branch's diff (`git diff main...HEAD`), and your draft `pr_draft.md`
description.

What good looks like: every file the diff touches is named by the plan
(the file itself, its folder, or — for a changelog — "a changelog
entry" naming the changelog file) or is disclosed as a deviation. A
description sentence claiming fidelity ("implements the plan exactly",
"no options added") is true of the diff exactly as written. Silent
drift looks like an extra changed file the plan never named and the
Deviations section never discloses (more than the plan), or a planned
change that is simply missing with no note (less than the plan, silent)
— an honestly disclosed shortfall is not drift, a quiet one is.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the candidate PR's Testing/Test
evidence section and checklist, read against the plan-context block's
test plan and the diff's actual test files. In live mode, your
`test_evidence.md` and the Testing section of `pr_draft.md`, read
against `plan.md`'s Test plan section and your branch's diff.

What good looks like: every test the description/checklist claims to
add exists as an actual file in the diff. Every claimed observable
result is backed by one of: a pasted command and its real printed
output; a specific, falsifiable fact or exact quoted observation from
a real run (an exact pass/skip/fail count, or — for interactive/TUI
changes with no literal stdout to paste — an exact quoted UI message
or described observed behavior, like "toast '...' instead of the
prompt"); or the specific real command named plus its actual stated
result (e.g. "`go generate ./...` produces no cheatsheet diff" or
"fmt and clippy clean" — acceptable specifically for checks that print
nothing when clean, as long as the real command is named). Not
acceptable: a vague sentence claiming a pass with no command, fact, or
exact observation named ("tests pass", "looks good", "works
correctly"), or a `#` comment that paraphrases a result with no exact
quoted text (e.g. "# after: it worked"). An inline `#` comment IS
acceptable when it quotes exact, specific text a real run would
actually produce — an exact error message, an exact exit code, an
exact count — the bar is specificity and falsifiability, not whether
the text sits on its own line versus inline after the command. A
failing check reported honestly, with the failure shown, still counts
as evidence; a check claimed to pass with nothing concrete behind it
does not.

## Diff quality (harness category: unreviewable)

Where it lives: the unified diff itself (`git diff main...HEAD` in
live mode; the candidate PR's diff in an eval bundle), read file by
file and hunk by hunk.

What good looks like: every hunk is either inside a file/folder the
plan named, or is explicitly explained in the plan's Deviations notes
— a reviewer should never have to guess why a hunk exists. Debris tells
that make a diff unreviewable: debug print statements, dead or
commented-out code, unrelated formatting churn, or a drive-by edit to a
file the issue never touched and the plan never named.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the repo-facts block's PR template
sections and stated contribution/AI-use policy, read against the
candidate PR's description. In live mode, the real
`.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the
Path Review repo, read against your `pr_draft.md`.

What good looks like: every section the template asks for (Summary,
Issue, Changes, Testing, Screenshots/Demo, Notes for Reviewers) holds
real, specific content — not an unticked checkbox left as-is or a
copy-pasted placeholder sentence. Where the repo's stated policy
requires disclosing AI use, the description names how AI was used,
in the author's own words, not a boilerplate line. (Whether the
description's content is *true* against the diff is plan fidelity,
above; this family only asks whether the repo's stated asks were
addressed at all.)
