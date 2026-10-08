# Procedure: how this tool grades a PR package

These are the operating steps. Follow them in order. Where a step says record
something, write it down in your notes before moving on. The checks are graded
from those notes, and most of them are side-by-side comparisons: a check graded
from a general impression of the PR, rather than from two recorded lists laid
next to each other, is the one that goes wrong.

## Read order

Read the parts that set the bar before the parts that are graded against it,
and read the PR's own claims last.

1. **Repo facts** (eval: `## Repo facts`; live: `.github/PULL_REQUEST_TEMPLATE.md`,
   `docs/CONTRIBUTING.md`, and the house rules in `scope.md`). Record word for word:
   the `pull requests:` asks (each template section, each checklist item, the
   issue-link form, any changelog/whatsnew/release-note entry), every stated
   test or check gate (a command or checklist item), the commit convention if
   any, and the `contribution policy` AI terms. If the policy says nothing about
   AI, write "AI policy silent". If no test gate is stated, write "no stated gate".
2. **Issue** (`## Issue`; live: the issue page). Record what behavior it reports
   and what it asks for.
3. **Thread highlights** (live: the issue's comments). Record each maintainer
   direction, question, or ruling, and anything the plan must respect.
4. **Plan context** (live: `plan.md`). Record four lists:
   - **Promised changes**: every element the plan says it will change (files,
     functions, a warning, a docs update, a test, a CI job).
   - **Out of scope**: everything it says it will not do, or defers.
   - **Test plan**: each named case, input, or failure mode, plus its stated
     expected-after.
   - **Deviations**: every deviation or deferral note and its reason.
   Also record the repro's captured "before" output.
5. **Diff and commits** (live: `git diff main...HEAD --stat`, `git diff main...HEAD`,
   `git log --oneline main..HEAD`). Build the **hunk list**: one line per hunk,
   with its file and what it does in plain words.
6. **Test evidence** (live: `test_evidence.md`). Build the **evidence list**:
   each command run, on which code (before or after), with what output.
7. **Title and description** last (live: `pr_draft.md`, where line 1 is the
   title). Build the **claims list**: each sentence asserting what the PR
   changes, documents, tests, or leaves alone, with fidelity claims marked.

Why this order: the plan and the repo's asks are the fixed record. If the
description is read first, its framing ("implements the plan exactly") sets the
terms, and the diff then gets read for confirmation instead of compared. Reading
the hunk list before the description means a hunk the description does not
mention has already been seen.

## Evidence gathering

One pairing per check. Finish every pairing before grading any check.

- **diff-within-plan**: Walk the hunk list. Tag each hunk `planned`
  (matches a promised change, or is a test, fixture, changelog, or docs entry
  for it), `deviation` (matches a recorded deviation note), `out-of-scope`
  (touches something the plan excludes), or `extra` (none of these: new
  option or flag, rename pass, rewrite, reworded unrelated text). Then walk the
  promised-changes list and tag each item `in diff`, `disclosed absent`
  (deferral in the plan, or stated in the description), or `silently absent`.
- **description-matches-diff**: For each entry on the claims list, find the
  hunk that makes it true and note it, or write "no hunk". For each fidelity
  claim, check it against the `extra` and `out-of-scope` tags from the line
  above.
- **repro-rerun-before-after**: For each test-plan case, find the evidence-list
  entry that runs it. Note whether it runs on the changed path with the input
  that broke, whether a captured before and a captured after are both present
  (or the before is the cited repro output), and quote the after. A case with
  no entry is written "not run". Also note any "works on my machine" or
  "tested locally" sentence that stands in for output.
- **repo-checks-shown**: For each stated gate recorded in read-order step 1,
  find the evidence or description line that reports its outcome with a named
  command. Quote it, or write "not reported". If step 1 recorded "no stated
  gate", record that.
- **diff-free-of-debris**: Read every hunk line by line, `+` and `-`, and list
  any debug print, commented-out code, dead or unused function, new TODO or
  FIXME, import reorder, whitespace or re-indent churn, removed-and-re-added
  identical lines, or unrelated hunk. Quote the line for each item. Note the
  commit messages.
- **template-asks-met**: For each ask recorded in read-order step 1, quote the
  description text that meets it. For a changelog, whatsnew, or release-note
  ask, name the diff file that carries it. Otherwise write "missing" or
  "placeholder". For each ticked checkbox, name the evidence entry that backs it.
- **ai-disclosure-met**: Quote the AI terms from read-order step 1. Quote any
  sentence in the description that discloses AI use, with the tool and extent,
  or write "no disclosure".
- **commits-tell-the-story**: Quote the commit messages.

In live mode, the addresses change and the moves do not. Run the git commands
named above from the working copy's top folder. Grade only committed changes,
and never count `plan.md`, `pr_draft.md`, `test_evidence.md`, or `comment.md`
as part of the diff. If a live input is missing, record its absence. The
absence is graded, not excused.

## Check execution

Grade in rubric order, one check at a time:

1. `diff-within-plan`
2. `description-matches-diff`
3. `repro-rerun-before-after`
4. `repo-checks-shown`
5. `diff-free-of-debris`
6. `template-asks-met`
7. `ai-disclosure-met`
8. `commits-tell-the-story` (preferred; it never moves the verdict)

For each check, read its pass condition in `rubric.md` clause by clause. Test
the recorded pairing against each clause, then assign exactly one grade: `pass`,
`fail`, or `unclear`. Write a one-line evidence string: the quote or fact that
decided it, naming the failing clause when one fails, for example
"(a) extra: hunk adds new `resolve_symlinks` config option not in plan".

Grade from the recorded pairings. Re-open the package only to fetch an exact
quote for the evidence string, never to re-judge from a fresh impression.

Use `unclear` only when the package truly says nothing either way about what a
check needs. Do not use it as a hedge when the evidence points one way. When
the rubric says absence fails (an undisclosed missing element, a stated gate
never reported, a required disclosure absent), grade `fail`, not `unclear`.

A check never reads another check's grade. One defect may fail two checks (for
example, an extra hunk that also contradicts "no changes beyond the plan").
Grade both: the verdict rule makes a single required fail enough, so nothing is
double-counted.

## Verdict assembly

1. Collect the seven required grades and convert each `unclear` to `fail`.
2. If all seven pass, the verdict is `accept`. If any one fails, it is `reject`.
   No weighing and no counting: the rule is a conjunction.
3. Report `commits-tell-the-story` with its grade. It never enters step 2.
4. Name the **deciding check**. On a reject, it is the first failing required
   check in rubric order; lead the summary with its evidence string. On an
   accept, lead with the required check whose pass was thinnest, so the author
   sees where the PR is weakest.
5. In live mode, add Voice notes (from `voice-guide.md`) and any Procedure-gap
   notes after the per-check lines. Neither affects the verdict.
6. Emit the per-check summary, then the fenced JSON block last, with all eight
   checks in rubric order and the verdict. Nothing follows the block.

The same grades always produce the same verdict. If this section ever needs a
judgement call, the rubric's verdict rule is what to fix, not this step.
