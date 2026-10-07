# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

Where it lives: the plan's "Diagnosis" section (the stated cause), read
against the package's "Repro evidence" section — specifically, the
numbered steps and what each one's input/setting change produces as an
observable result.

What good looks like: the stated cause names the same
input/setting/mechanism that the repro evidence's steps show actually
varies alongside the behavior change (e.g. "with vs. without a flag,
the slowdown disappears"). A diagnosis that names a plausible-sounding
mechanism the repro evidence never isolates, or that a specific repro
step directly contradicts, does not follow from the evidence even if it
reads confidently.

## Scope

Where it lives: the plan's "Scope" section (its in-scope and
not-in-scope statements, and the "Changes" section's named files), read
against the files/areas the Diagnosis and Repro evidence actually
implicate.

What good looks like: every file the diagnosis implicates is named as
in scope, and the not-in-scope statement does not wave off a file or
area that the diagnosis/evidence actually points at. A scope that
excludes the very mechanism the evidence shows is responsible is a
drive-by rewrite of the wrong area, not a bounded fix.

## Executability

Where it lives: the plan's "Changes" section (the ordered list of edits
it intends to make) and "Summary".

What good looks like: a stranger could start making the first edit
without asking the author what it means — named files or functions, a
concrete action per step ("call X before Y"), not just a restated goal
("fix the bug").

## Test plan

Where it lives: the plan's "Test plan" section, read against the Repro
evidence's steps and the specific symptom they demonstrate.

What good looks like: the test plan says it will re-run (or
automate) the same observable check the repro evidence used — the same
symptom, not a nearby or generic one ("add a test" with no named
behavior does not count).

## Honesty

Where it lives: any "Risks and unknowns" section of the plan, and (post
-build) the "Deviations" heading.

What good looks like: unknowns are named as unknowns ("I have not
verified X"), not stated as settled facts. A deviation entry says
plainly what changed and why, including "nothing changed" when that is
true — a blank Deviations section is not an honest account.

## Comms

Where it lives: the "Candidate plan comment" (or, live, the student's
draft `comment.md`), read against the package's "Thread highlights"
(or the live issue thread) and the "Repo facts" block's stated
contribution policy/templates/AI-use disclosure requirement.

What good looks like: the comment does not contradict or ignore
something a maintainer/collaborator already said in the thread (a
ruled-out cause, a confirmed approach), and it follows whatever the
repo's conventions require. Silence on a policy the repo doesn't state
is not a violation; ignoring one the repo does state is.
