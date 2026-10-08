---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request (its title,
description, commit list, unified diff, and test evidence) read
against the plan it claims to implement (including that plan's
deviation notes) and the issue that plan belongs to, together with the
repo's stated asks for pull requests. Answer only that question, for
exactly one package per run. Do not review the code's style, propose a
better fix, grade the plan itself, or grade any other PR. Do not answer
from gut feel: answer by executing `procedure.md`, which applies the
checks in `rubric.md` to evidence located per
`references/evidence-guide.md`.

## Inputs and modes

Decide the mode first, from what you were given.

- **Live mode**: the student is checking their own branch before
  opening the PR. Run from the top folder of their fork's working copy
  and read exactly these inputs:
  - `plan.md` in that folder: the posted plan, including everything
    under `## Deviations`. The plan is the change list, the
    out-of-scope list, and the test plan you grade against.
  - The diff on the branch: run `git diff main...HEAD` (three dots),
    which is every committed change the branch makes relative to
    `main`. Also run `git diff main...HEAD --stat` for the file list
    and `git log --oneline main..HEAD` for the commit list.
    Uncommitted and untracked files are not part of the PR: do not
    grade them, and never treat `plan.md`, `pr_draft.md`,
    `test_evidence.md`, or `comment.md` as part of the diff.
  - `pr_draft.md` in that folder: the first line is the PR title;
    everything below it is the PR description.
  - `test_evidence.md` in that folder: the captured test output.
  - The issue: read its thread from the URL the student gives you
    (`gh issue view <number> --repo <repo> --comments`, or the web
    page), and record maintainer directions and questions.
  - The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and
    `docs/CONTRIBUTING.md` in the working copy (plus any AI-policy file
    they link).
  A house-chain student has no plan or repro of their own: read the
  house plan and the house repro pack they were given in place of
  `plan.md` and their week-2 repro. The same checks grade the same
  things. If any input above is missing, say which one, and grade the
  checks that need it against that absence. Do not substitute a file
  you found elsewhere.
- **Eval mode**: you were given a package bundle (a markdown file with
  `## Repo facts`, `## Issue`, `## Thread highlights`,
  `## Plan context`, and `## Candidate PR`). The bundle is the whole
  world: every fact comes from its text. Fetch nothing, run no
  commands against any repo, and read no other files. Eval mode always
  grades the complete package: every check in `rubric.md`, with the
  full verdict rule. The `item` in the JSON is the bundle id (for
  example `pkg-07`).

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. If its `Repo:` line still shows a bracketed placeholder (such as
`<ORG>/<PATH-REVIEW-REPO>`), stop without grading. Tell the student to
fill the `Repo:` line in `scope.md` with their section's Path Review
repo, and emit nothing else. Otherwise, confirm that the issue URL and
the branch's upstream both belong to the repo named there. If they do
not, refuse to grade, say which repo is in scope, and stop. Never guess
a scope and never widen it. The house rules in `scope.md` (one PR per
issue per student, the PR comes from the student's own fork branch,
every template section gets real content, the branch name has the
form `<type>/<issue-number>-<slug>`) are part of the standards the
standards checks read in live mode. In eval mode, ignore `scope.md`
entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`, the student's own rules for
how they write upstream. Hold the outgoing PR text against those
rules: the title (the first line of `pr_draft.md`) and the description
(the rest). For every rule the draft breaks, report it in the summary
under a "Voice" heading. Quote the rule's name and the sentence of the
draft that breaks it. The voice guide never changes the verdict on its
own: voice is personal, and readiness is decided by the rubric. A
voice note is advice for the author, not a check grade. In eval mode,
ignore `voice-guide.md` entirely.

## Component reads

Read the components in this order, every run, before grading:

1. `rubric.md` holds the checks (name, evidence, pass condition,
   weight) and the verdict rule. It is the only source of checks:
   never add, drop, rename, or merge one.
2. `references/evidence-guide.md` is the map. For each evidence
   family, it says where the evidence sits in a bundle and in a live
   working copy, and what good looks like there.
3. `procedure.md` holds the operating steps: read order, evidence
   gathering, check execution, verdict assembly. Execute it as
   written, in order.

Where the procedure is silent on a step you need, do not improvise
around it. Take the most literal reading of the rubric's pass
condition, and add a "Procedure gap" line to the summary naming what
was missing. A gap you report is feedback the author needs. A gap you
paper over is invisible.

Refusal rule: if `rubric.md` has no filled check rows, or
`procedure.md` has no written steps under its headings (instruction
comments do not count as content), stop and say which file is empty.
Do not grade, and do not invent checks or steps. An empty tool that
improvises produces verdicts that look like judgment and are noise.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit, and
`reject` means hold it. There is no third verdict and no score, and
reservations go in check evidence lines, not in the verdict. First
print a short readable summary: one line per check with its grade
and evidence, the deciding check named per `procedure.md`, and in
live mode any Voice notes and Procedure-gap notes. Then end the reply
with exactly one fenced JSON block in the schema below, with one entry
per rubric check in rubric order. The block must be valid JSON and
must come last, with nothing after it. The harness parses the last
fenced JSON block.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  exact quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse PR whose diff matches
  its plan and whose evidence shows the fixed behavior is ready. A
  polished, confident PR whose diff does something else is not. Read
  the diff against the plan, the evidence against the test plan, and
  the description against the diff. Never grade length, formatting,
  or tone.
- **The description is a claim, not evidence.** What the diff and the
  captured output show decides. What the description says about them
  is what gets checked.
- **The rubric decides, not you.** If a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary; the fix belongs in the rubric, not in the run.
- **The procedure decides how, not you.** Follow `procedure.md` as
  written and report its gaps instead of inventing steps.
- **Honest shortfalls are not failures.** A PR that does less than
  everything, and says so in its plan's deviation notes or its
  description, is graded on what it claims.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's
  verdict rule directs. Where the rule is silent, an unverifiable
  claim is a failing one: a PR you cannot verify from the package is
  not ready to submit.
