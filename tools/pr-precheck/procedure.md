# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

1. Read the plan's Scope section and `## Deviations` notes first (if
   any). Note every file/folder it names as in scope, and anything the
   Deviations notes disclose as added, skipped, or changed from the
   original plan. This has to come first: every later side-by-side read
   (diff vs. plan, description vs. plan) depends on knowing what the
   plan actually authorized before looking at what the PR did.
2. Read the diff next (every changed file and hunk). Note each changed
   file and, for each one, whether it was named in step 1.
3. Read the PR description and its Testing section (or `test_evidence.md`
   in live mode). Note every claim it makes about what changed and every
   testing claim, separately.
4. Read the repo's PR template and `docs/CONTRIBUTING.md`'s stated
   policy (the repo-facts block in eval mode; the real files in live
   mode) last, now that the PR's own content is in hand to compare
   against them.

## Evidence gathering

For each check, pull exactly this and record it before grading:

- **plan-fidelity**: the full list of files the diff touches (from step
  2) and the full list of files/folders the plan names or discloses as
  a deviation (from step 1). Also pull the description's exact fidelity
  sentences (e.g. "no changes beyond it", "no options added") verbatim.
- **test-evidence**: every test file the description/checklist claims
  to add, matched against whether that file actually appears in the
  diff. Separately, every line in the Testing section that claims an
  observable result (output matches, a control is unchanged, a suite
  passes) — record whether each such line is a pasted command plus its
  printed output, or just a sentence/comment describing the result.
- **diff-quality**: any hunk in the diff that is not inside a
  plan-named file/folder and is not explained in the Deviations notes;
  any commented-out code or debug print left in the diff.
- **standards-comms**: each section the PR template requires, and
  whether the description's content under that heading is real and
  specific (not an empty checkbox or a copy-pasted placeholder);
  whether `docs/CONTRIBUTING.md` requires an AI-use disclosure, and if
  so, whether the description contains one.

## Check execution

Grade in this order, using the evidence already gathered above; do not
re-read the whole package per check.

1. **plan-fidelity**: pass if every diff-touched file is plan-named or
   deviation-disclosed, and every fidelity sentence in the description
   is true of the diff. Fail if any file is touched without being named
   or disclosed, or if a fidelity sentence is contradicted by the diff.
   `unclear` only if the plan's scope statement itself is too vague to
   tell whether a file was authorized.
2. **test-evidence**: pass if every claimed test file is present in the
   diff AND every testing claim is backed by one of: a real pasted
   command-and-output pair, a specific falsifiable fact or exact quoted
   observation from a real run, or the specific real command named plus
   its actual stated result (per `rubric.md`'s test-evidence row). Fail
   if any claimed test is missing from the diff, or any testing claim is
   only a vague sentence/comment with no
   real output shown.
3. **diff-quality**: pass if no hunk is both unexplained and outside
   the plan's named scope, and no debug/commented-out code remains.
   Fail otherwise.
4. **standards-comms**: pass if every template section has real content
   and any required AI-use disclosure is present. Fail if any section is
   empty/placeholder, or a required disclosure is missing.
5. If a check's required evidence is genuinely absent from the package
   (not just terse), grade that check `unclear` rather than guessing.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if plan-fidelity,
   test-evidence, diff-quality, and standards-comms all grade pass. An
   `unclear` grade on any of them counts as a fail, same as a fail
   grade.
2. If any required check fails or is unclear, the verdict is reject.
3. When more than one required check fails, quote the first failing
   check in rubric order (plan-fidelity, then test-evidence, then
   diff-quality, then standards-comms) as the deciding check in the
   output's evidence line — this is the one most likely to invalidate
   everything checked after it, since plan-fidelity establishes what
   the PR was even supposed to do.
