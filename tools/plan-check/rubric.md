# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | the plan's Diagnosis section, read against the Repro evidence section | the stated cause explains the specific behavior the repro evidence shows (what changes between steps, e.g. with vs. without a variable toggled), and is not contradicted by any step in the repro evidence. Merely naming *a* plausible-sounding cause is not enough if the evidence points elsewhere | required |
| scope | the plan's Scope section, read against the repro evidence's call stack / the files implicated by the diagnosis | names each file it will change, and explicitly states what it will not touch; what it says it won't touch must be consistent with what the repro evidence and diagnosis actually implicate (not an unrelated area mentioned for its own sake) | required |
| test | the plan's Test plan section, read against the Repro evidence section | the test plan re-exercises the same behavior the repro evidence demonstrated (the same observable symptom), not an unrelated or merely adjacent behavior | required |
| comment-fits-thread | the plan comment, read against the Thread highlights and the repo's stated contribution conventions | the comment does not contradict anything a maintainer/collaborator already said in the thread (e.g. a cause or approach they already ruled out), and follows any stated conventions (e.g. required disclosure, issue/PR template expectations) | required |

## Verdict rule

Ready only if every required check (diagnosis, scope, test,
comment-fits-thread) grades P. A `?` on any required check counts as a
fail.
