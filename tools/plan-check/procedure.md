# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the Issue section first. Note the reported symptom and any
   stated expected-vs-actual behavior.
2. Read the Thread highlights next. Note anything a collaborator or
   maintainer already stated as a cause, an approach they ruled out or
   confirmed, or a convention they mentioned. This must come before the
   plan so a plan that contradicts the thread is caught, not assumed
   correct.
3. Read the Repro evidence section. Note exactly what varies between
   the reproduction's steps (which input, flag, or setting is toggled)
   and what observable behavior each step produces. This is the ground
   truth every other section gets checked against.
4. Read the Repo facts block. Note the stated contribution policy or
   conventions (templates, AI-use disclosure, etc.).
5. Read the Candidate plan (Diagnosis, Scope, Changes, Test plan) and
   the Candidate plan comment last, now that the evidence to check them
   against is already in hand.

## Evidence gathering

For each check, pull exactly this and record it before grading:

- **diagnosis**: from Repro evidence, the specific input/setting that
  varies between steps and the behavior change it produces. From the
  plan's Diagnosis section, the stated cause. Write both down side by
  side.
- **scope**: from the plan's Scope section, the list of files/areas
  named "in scope" and "not in scope". From the diagnosis and repro
  evidence, the file(s)/area(s) actually implicated by the confirmed
  cause.
- **test**: from the plan's Test plan section, the behavior it says it
  will verify. From Repro evidence, the exact observable symptom
  demonstrated (what a reader would see change).
- **comment-fits-thread**: from Thread highlights, anything stated as a
  cause, a ruled-out approach, or a convention. From the Candidate plan
  comment, what it actually says.

## Check execution

Grade in this order, using the evidence already gathered above; do not
re-read the whole package per check.

1. **diagnosis**: P if the stated cause matches the input/setting the
   repro evidence shows actually causes the behavior change, and is not
   contradicted by any individual repro step. F if a different cause is
   named, or any repro step contradicts it. `?` only if the repro
   evidence itself doesn't pin down a cause clearly enough to compare.
2. **scope**: P if every file/area the diagnosis implicates is listed
   as in scope, and nothing listed as "not in scope" is actually
   implicated by the diagnosis/evidence. F otherwise.
3. **test**: P if the described test re-exercises the same observable
   symptom recorded from Repro evidence. F if it tests an unrelated or
   merely adjacent behavior, or nothing observable at all.
4. **comment-fits-thread**: P if the plan comment does not contradict
   anything already stated in the thread and follows any stated
   conventions. F otherwise.
5. If a check's required evidence is genuinely absent from the package
   (not just terse), grade that check `?` rather than guessing.

## Verdict assembly

1. Apply the rubric's verdict rule: ready only if diagnosis, scope,
   test, and comment-fits-thread all grade P. A `?` on any of them
   counts as a fail, same as an F.
2. If any required check is F or `?`, the verdict is hold.
3. In the output, quote the deciding check's evidence directly: for
   diagnosis, the stated cause next to the repro step that contradicts
   it; for scope, the "not in scope" line next to the file it wrongly
   excludes; for test, the test plan's described check; for
   comment-fits-thread, the thread statement it conflicts with.
