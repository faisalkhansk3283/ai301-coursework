# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | the repro report's environment record | names the tool/version and the OS used to test; if these differ from what the issue states, the report says so explicitly | required |
| steps-complete | the repro report's preparation/execution steps | the starting input and exact command(s) run are specified precisely enough — either as literal pasted content, or as an unambiguous description naming the exact values/flags/parameters involved — that a stranger could reconstruct the same reproduction without guessing any detail relevant to the reported behavior | required |
| behavior-matches | the report's output/artifact, read against the issue's stated behavior | the actual output/error/panic shown is the same failure (same error type or message) the issue describes, not an adjacent or different error | required |
| honesty | the report's stated conclusion, read against its own shown evidence | the conclusion (bug present / cannot reproduce) is directly supported by the evidence shown; an evidenced "cannot reproduce" passes, a conclusion that claims more than the output demonstrates fails | required |
| ai-disclosure-ok | claim comment + repro comment, read against CONTRIBUTING.md / stated AI policy | assume the comments were produced with AI assistance (this course's workflow). If the repo's policy requires disclosing AI use (naming the tool and the extent of assistance), the comments must contain that disclosure; a comment that says nothing about AI use when the policy explicitly requires it is a fail. If the policy states no AI requirement at all, silence passes | required |

## Verdict rule

Ready only if every required check (env-recorded, steps-complete,
behavior-matches, honesty, ai-disclosure-ok) grades P. A `?` on any
required check counts as a fail.
