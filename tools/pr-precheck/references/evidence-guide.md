# Evidence guide: where evidence lives in a PR package

The map for every check in `rubric.md`. For each of the four evidence families
below: where it lives in an eval bundle (`eval/packages/*.md`), where it lives in
live mode (the top folder of your fork's working copy, on your branch, plus the
issue on GitHub), and what good looks like as a condition someone else could
apply. The families line up one to one with the harness categories. A package
that fails none of them is a clear accept.

## Plan fidelity (harness category: silent-drift)

**Checks:** `diff-within-plan`, `description-matches-diff`.

**Where it lives: eval bundle.**
- The plan's promises: `## Plan context`, in the `Plan:` paragraph (the change,
  often with a `Files:` list), its `Not in scope:` / out-of-scope sentences, and any
  deviation or deferral note. The `Repro evidence` paragraph above it says what
  the plan was built on.
- What actually changed: `## Candidate PR` → `### Diff` (each `--- a/` / `+++ b/`
  file header and each `@@` hunk) and `### Commits`.
- What the PR claims: `### Title` and `### Description`. Watch for fidelity
  sentences ("implements the posted plan exactly", "no changes beyond the plan",
  "no functional changes outside…") and for "now documents / now warns /
  adds…" sentences.

**Where it lives: live mode.**
- `plan.md` in the working copy's top folder: the `Change:`, `In:`, `Out:` and
  `Test:` paragraphs, and everything under `## Deviations`.
- `git diff main...HEAD --stat` (the file list), `git diff main...HEAD` (the
  hunks), and `git log --oneline main..HEAD` (the commits). Only committed
  changes count. `plan.md`, `pr_draft.md`, `test_evidence.md`, and `comment.md`
  are never part of the diff.
- `pr_draft.md`: line 1 is the title, and the rest is the description (Summary
  and Changes carry most of the claims).

**What good looks like.** Every file and hunk in the diff traces to a promised
change or a deviation note, and every promised change is in the diff or
disclosed as deferred. Tests, fixtures, and a changelog entry for the planned
change count as part of it. Every "the PR does X" sentence in the description
points at a hunk that does X.

**What drift looks like.** *More than the plan:* a new config option or flag, a
rename pass, a rewrite of an untouched function, reworded unrelated help text,
or a second file the plan never names. Each of these is worse under a
description claiming "exactly as planned". *Less than the plan:* the plan
promised a warning and a docs update, the diff has only the warning, and the
description says the docs "now document the limitation". *Honest instead of
drift:* the plan or description says "X is deliberately deferred because Y".
That re-ties the gap and passes.

## Test evidence (harness category: not-tested)

**Checks:** `repro-rerun-before-after`, `repo-checks-shown`.

**Where it lives: eval bundle.**
- What must be proven: `## Plan context`, in its `Test plan:` sentence (each case,
  input, or failure mode, and the expected-after) and its `Repro evidence`
  (the captured before).
- The proof: `## Candidate PR` → `### Test evidence`, plus any before/after
  block in `### Description`.
- The repo's own gate: `## Repo facts`, on its `pull requests:` line (checklist
  items like "tests added and passed", "all code checks passed") and its
  contributing-guide asks (for example "pass the test suite (yarn test)").

**Where it lives: live mode.**
- `plan.md`, in its `Test:` paragraph (and any number changes under `## Deviations`).
- `test_evidence.md`: the before (on `main`, or the week-2 repro output) and
  after (on the branch) of the repro steps, plus the pasted output of
  `make test-unit`, `make test-integration`, `make lint`, and `make typecheck`.
- The PR template's Testing checklist in `.github/PULL_REQUEST_TEMPLATE.md`
  names those four gates. `docs/CONTRIBUTING.md` adds
  `make check && make test-unit`.

**What good looks like.** For every case the test plan names, captured output
shows the broken input on the changed path before and after, and the after
matches the plan's stated expected-after. The repo's stated gates appear with a
named command and a visible result. One line like "`go test ./...` passes (4108
tests)" counts. A gate that failed, reported with its output or its reason, is
honest evidence.

**What not-tested looks like.** "Tested locally, works now", "verified working
on my machine for a full day", "tests pass" with no suite named. Evidence that
runs only the control or the unchanged path (a single-file case when the bug
needs two files, a GET when the bug was a POST). Evidence that re-runs one of
the plan's two named failure modes and says nothing about the other. A stated
gate (`yarn test`) that is never shown.

## Diff quality (harness category: unreviewable)

**Checks:** `diff-free-of-debris`, `commits-tell-the-story` (preferred).

**Where it lives: eval bundle.** `## Candidate PR` → `### Diff` (every `+` and
`-` line, not just the hunk headers) and `### Commits`.

**Where it lives: live mode.** `git diff main...HEAD`, read hunk by hunk, and
`git log --oneline main..HEAD`.

**What good looks like.** Every `+` and `-` line belongs to the fix, its tests,
or its required docs or changelog. A reviewer can see the change without
skipping anything. Commit messages say what each commit does, in the repo's
convention.

**Debris tells (any one fails).** `eprintln!("DEBUG …")` or `console.log` or
debug `print(` lines. Commented-out code, including a commented-out first
attempt. A dead function (`_unused_…`, `#[allow(dead_code)]`). A new
`TODO`/`FIXME`. An import reorder or restructure the fix doesn't need.
Re-indent, whitespace, or re-wrap churn. A hunk that removes and re-adds
identical lines. An unrelated drive-by edit. Commits named `wip`, `fix`,
`fmt + cleanup`, or `misc cleanups while debugging` are a symptom, graded by
the preferred check only.

## Standards and comms (harness category: standards-wall)

**Checks:** `template-asks-met`, `ai-disclosure-met`.

**Where it lives: eval bundle.** `## Repo facts`: the `pull requests:` line (template
sections, required checklist, `closes #xxxx` form, changelog/whatsnew entry,
type-of-changes item, before/after asks) and the `contribution policy` line (AI
terms: silent, welcomed, or "all AI usage must be disclosed, stating the tool
and extent"). Then `### Description` for the filled sections and disclosure,
and `### Diff` for any required changelog or whatsnew file.

**Where it lives: live mode.**
- The asks: `.github/PULL_REQUEST_TEMPLATE.md` (Summary, `Issue: Closes #`,
  Changes, the Testing checklist including the xfail-marker item, Screenshots /
  Demo, Notes for Reviewers), `docs/CONTRIBUTING.md` (branch name
  `<type>/<issue-number>-<slug>`, Conventional Commits, "fill out the PR
  template completely"), and the house rules in `scope.md`.
- The course's standing ask: an AI-use disclosure in your own words, under
  Notes for Reviewers.
- The answers: `pr_draft.md`, and the branch name from `git branch --show-current`.

**What good looks like.** Every template section has real content (not the
`<!-- -->` placeholder and not a bare `-`). The issue is linked in the asked
form (`Closes #29`). Only true checkboxes are ticked, and each one is backed by
`test_evidence.md`. A required changelog entry is a file in the diff. Where the
policy asks for it, the AI disclosure names the tool and the extent in the
author's own words.

**What a standards wall looks like.** A required checklist ignored (no
`closes #`, no checklist, no whatsnew file in the diff). A strict "disclose all
AI usage" policy, and a description with no disclosure at all. A template
section left as its placeholder. (Whether the description's claims match the
diff is plan fidelity, above, not this family.)
