# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-active | maintainer first-response sample / recent default-branch commit dates (Repo facts) | at least one maintainer commit or reply within 90 days of the capture date | required |
| not-abandoned | last push to any branch (Repo facts) | last push within 6 months of the capture date | required |
| unclaimed | assignees, linked PRs, and claim comments in the thread | no assignee; no open linked PR; and if a claim comment was made or accepted, either it has activity (a follow-up comment, commit, or PR update) within 6 months of the capture date, or its linked PR/attempt is still open. A claim with no activity for 6+ months and no open PR counts as stale and does not fail this check | required |
| ai-disclosure-ok | CONTRIBUTING.md / AI policy line (Repo facts) | not an outright ban on AI-assisted contributions; disclosure/testing/review conditions are fine | required |

## Verdict rule

Accept only if every required check (maintainer-active, not-abandoned,
unclaimed, ai-disclosure-ok) grades P. A `?` on any required check counts
as a fail.
