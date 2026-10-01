# Procedure: how this skill grades a plan package

These are the operating steps. Follow them in order. Where a step says record
something, write it down before moving on: the later checks read what the
earlier steps recorded, and a check graded from memory of the plan rather than
from the recorded evidence is the one that goes wrong.

## Read order

1. Read `## Issue` first — the body and, if present, the expected-behavior line.
   Record two things: **what was asked for** (the change the issue wants) and
   **what behavior it reports**. These bound every later judgement about scope.
2. Read `## Repo facts`. Record the `contribution policy` line verbatim, plus
   any branch-naming shape, test gate, or AI-use requirement it states. Record
   "policy silent on AI" explicitly if that is what it says, so the comms check
   does not later mistake silence for an unmet requirement.
3. Read `## Thread highlights`. Record every direction a maintainer proposed,
   every question they asked, and anything they ruled out. Mark each as
   answered or unanswered in the thread. If the thread is empty, record that.
4. Read `## Repro evidence` **before the plan**, and record what it establishes:
   the behavior shown, the artifacts that show it, the environment it ran in,
   and what it explicitly did not reach. If there is no repro-evidence block, or
   it contains no artifacts, record "no established behavior" — that record is
   what `cause-follows-evidence` and `test-plan-observable` grade against.
5. Read `## Candidate plan` last of the plan-side parts, then `## Candidate plan
   comment`.

The order matters and is not a preference. Reading the plan before the evidence
primes you to accept its cause: a well-written diagnosis reads as true, and you
will then go looking for evidence that confirms it rather than asking what the
evidence actually showed. Recording what the evidence establishes *first* means
the cause arrives to be tested against a fixed record instead of setting the
terms itself. The same applies to the thread: a direction a maintainer already
named must be in your notes before you read a comment that may quietly ignore
it.

## Evidence gathering

One pull per check. Gather all of it before grading any check, so no check is
graded against a half-read package.

- **cause-follows-evidence** — Quote the plan's cause sentence. Beside it, quote
  the single line of repro evidence that would carry it, or write "nothing in
  the evidence reaches this" if there is none. Put the two side by side in your
  notes before grading. Also note whether the evidence localises a cause deeper
  than the one the plan names.
- **change-bounded** — Quote what the plan says it will change, and separately
  quote any sentence stating what it will not. If no such sentence exists,
  record "no boundary stated". Then list each element of the change and mark it
  against the issue's ask recorded in read-order step 1: asked-for, or extra.
- **plan-executable** — List every location the plan names (file, function,
  config key, area). For each, note whether one search in the repo would find
  it. Then quote the first action the plan calls for and note whether it depends
  on a decision the plan leaves open.
- **test-plan-observable** — Quote the test plan. Quote the repro evidence's
  steps beside it. Note whether the test exercises the real code path or a
  stand-in, and quote the stated expected-after — the specific thing that will
  be observed. If no expected-after is stated, record its absence.
- **confidence-matches-evidence** — Walk the plan and the comment sentence by
  sentence. For every assertion about cause, mechanism, scope of effect, or
  outcome, write the assertion and what carries it. Flag any assertion with
  nothing beside it. Separately, list the unknowns the plan names, and note any
  open thread question from read-order step 3 that the plan does not mention.
- **comment-carries-the-plan** — Quote the comment. Note whether the diagnosis
  and the intended change are both recoverable from the comment alone. Apply
  the substitution test: would this comment read the same pasted on a different
  issue? Then check each recorded thread direction against it.
- **conventions-respected** — Take the requirements recorded in read-order step
  2 one at a time. For each, note whether it binds at planning time (a
  pull-request-scoped rule usually does not), then whether the package meets it,
  quoting the part that does or noting the absence.
- **alternative-considered** — Scan for a rejected approach and its reason.
  Quote it or record its absence.

In live mode, the addresses change but the moves do not: `references/evidence-guide.md`
names where each family lives on a real issue. The reproduction evidence is the
student's posted repro comment on that issue. If the student has no posted repro
comment and the drafts quote none, record "no established behavior" exactly as
in eval mode — the absence is graded, not excused.

## Check execution

Grade in this order. It runs evidence-dependent checks while the evidence
record is freshest, and it puts the two comms checks last because they read
notes the earlier checks produced.

1. `cause-follows-evidence`
2. `change-bounded`
3. `plan-executable`
4. `test-plan-observable`
5. `confidence-matches-evidence`
6. `comment-carries-the-plan`
7. `conventions-respected`
8. `alternative-considered` (preferred; grade it, never let it move the verdict)

For each check: read the pass condition in `rubric.md` clause by clause, test
the gathered evidence against each clause, and assign exactly one grade —
`pass`, `fail`, or `unclear`. Record a one-line evidence string: the quote or
the fact that decided it, not a restatement of the rule. When a check fails on
one clause of several, name that clause in the evidence string.

Grade from the notes gathered above, not by re-reading the package. Re-read one
part only when a check's evidence string would otherwise be a paraphrase — a
quote must be exact, so go back for the words themselves rather than
reconstructing them.

When evidence for a check is genuinely absent — nothing in the package speaks
to it either way — grade `unclear` and let the evidence string quote the
absence ("no sentence in the plan states what it will not change"). Do not
grade `unclear` as a hedge when the evidence is present and points one way:
that is a `pass` or a `fail`. Absence of evidence the rubric says to grade
against the absence (a missing repro-evidence block, a missing boundary) is a
`fail` per that check's pass condition, not an `unclear`.

A check never reads another check's grade. If two checks would fail on the same
underlying defect, both fail; the verdict rule does not double-count, because
any single required fail already rejects.

## Verdict assembly

1. Collect the seven required grades. Convert every `unclear` to a fail, per the
   rubric's verdict rule.
2. If all seven required checks pass, the verdict is `accept`. If any one of
   them fails, the verdict is `reject`. There is no third verdict and no
   weighing of how many passed: the rule is a conjunction.
3. `alternative-considered` is reported with its grade and never enters step 2.
4. Identify the **deciding check**. On a reject, it is the first required check
   in execution order that failed; its evidence string is what the summary
   leads with, so a reader learns what held the plan without reading the whole
   table. On an accept, there is no deciding check; lead with the check whose
   evidence was thinnest while still passing, so the author knows where the plan
   is weakest.
5. Emit the per-check summary, then the fenced JSON block last, with every
   check's name, grade, and evidence string, and the verdict. The harness parses
   the last fenced JSON block, so nothing follows it.

The same grades must always produce the same verdict. If applying this section
requires a judgement call, the rubric's verdict rule is underspecified and the
rubric is what to fix — not this step.
