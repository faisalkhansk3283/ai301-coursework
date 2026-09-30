# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

Where it lives: the repro report's "Environment" section (or equivalent
heading), in an eval package or a live draft.

What good looks like: names the tool's version and the OS tested on. If
the version tested differs from the version the issue names, the report
says so explicitly rather than leaving the reader to notice.

## Steps

Where it lives: the report's "Preparation" and "Execution" sections (or
equivalent): the input used, and the exact command(s) run.

What good looks like: a stranger could reconstruct the same run without
guessing any detail relevant to the reported behavior. The input and
command can be shown as literal pasted content, or as an unambiguous
description that names the exact values, flags, and parameters involved
(e.g. "a minimal YAML with a `category:` key" plus the exact command
run) — a precise description is sufficient; it does not have to be a
literal file dump.

## Behavior shown

Where it lives: the report's shown output/error/panic (the "Execution"
or "Actual" section), read against the issue's own quoted output/error.

What good looks like: the output shown is the *same* failure the issue
describes — same error type or message/panic signature — not merely
"a failure occurred." A different error message (e.g. a syntax/parse
error where the issue reports a panic) is a different bug, not a match,
even if the report calls it confirmation.

## Honesty

Where it lives: the report's stated conclusion (its "Analysis" /
"Expected" / "Actual" section) read against the evidence shown just
above it in the same report.

What good looks like: the conclusion claims no more than the shown
evidence supports. An honest "I could not reproduce this" backed by a
real attempt is a pass. A conclusion asserting the bug is "confirmed" or
"reproduced" when the shown output does not actually match the issue's
described behavior is a fail, regardless of how confident the wording
sounds.

## Comms

Where it lives: the claim comment and the repro comment, read against
the repo's CONTRIBUTING.md / stated contribution policy (including any
AI-use disclosure requirement) from the repo-facts block.

What good looks like: assume every comment was produced with AI
assistance, since that is this course's workflow. If the repo's policy
requires disclosing AI use (naming the tool and the extent of
assistance), the comment must contain that disclosure explicitly; a
comment that says nothing about AI use fails this check when the policy
demands it. If the repo states no AI-use requirement at all, silence
passes.
