# Rubric: is this pull request ready to submit?

Seven required checks and one preferred. Each one reads the PR side by side
with something it promised or something the repo asked for: the diff against
the plan, the description against the diff, the evidence against the test plan,
the description and diff against the repo's stated asks. None of them grade the
write-up's shape. A three-line description over a diff that matches its plan,
with the repro re-run before and after, is ready. A sectioned, confident one
over a diff that does something else is not.

In eval mode the bundle is the whole world: `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Plan context`, and `## Candidate PR` (title,
description, commits, diff, test evidence). In live mode the same parts come
from the locations in `references/evidence-guide.md`: `plan.md` with its
`## Deviations`, `git diff main...HEAD`, `pr_draft.md`, `test_evidence.md`,
the issue thread, the PR template and `docs/CONTRIBUTING.md`.

"Disclosed" below always means: stated in the plan's deviation or deferral
notes, or stated in the PR description, in words a reviewer would find
without reading the diff.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diff-within-plan | Every file and hunk in the diff, read against the plan's change list, its stated out-of-scope list, and its deviation notes. Then every element the plan promised, looked for in the diff. Evidence guide: Plan fidelity. | Both directions hold. (a) **Nothing extra:** every hunk serves a change the plan names or a disclosed deviation. A new config option, flag, setting, public rename, or rewrite of code the fix does not need fails, and so does rewording unrelated text, even when it is adjacent to the fix. A hunk that touches something the plan explicitly scopes out fails outright. Tests, fixtures, and a changelog or docs entry for the planned change count as part of the change. (b) **Nothing missing in silence:** every element the plan promised (a code change, a warning, a docs update, a second fix) is in the diff, or its absence is disclosed. A disclosed deferral passes (a narrower PR is not a failing one when it says so). An undisclosed gap fails. Fail also a diff that never implements the plan's fix at all. | required |
| description-matches-diff | Each sentence of the PR description (and the title) that says what the PR changes, adds, documents, or leaves alone, read against the diff hunks. Evidence guide: Plan fidelity. | Every claim about the change is visible in the diff. Fidelity claims ("implements the plan exactly", "no changes beyond the plan", "no functional changes outside X") hold only if the diff contains nothing outside the plan. A claim that the docs, tests, changelog, or behavior now do something is true only if the hunk that does it is in the diff. Fail a description that claims more than the diff delivers, and one that hides what the diff does by calling it fidelity. A description that is terse but true passes, and leaving a minor hunk unmentioned is not a false claim unless the description asserts there is nothing else. | required |
| repro-rerun-before-after | The test-evidence section (and any before/after in the description), read against the plan's test plan and the reproduction's own steps. Evidence guide: Test evidence. | All of: (a) **the plan's proof is re-run:** the evidence executes the reproduction or test plan on the changed code path, the input that broke, not a neighbouring input or a control alone that never broke. (b) **before and after are observable:** both the broken state and the fixed state are shown as captured output (a value, a message, an exit status, a test moving from failing to passing). Alternatively, the after is shown and the before is the repro's captured output that the evidence explicitly cites. (c) **coverage:** every failure mode or case the plan's test plan names is exercised, or its omission is disclosed. Fail "tested locally", "works on my machine", "verified working", and "colors work now", with no captured output. Fail evidence that exercises only the unchanged path, and evidence that covers one of the plan's named cases and is silent on another. A test file in the diff does not replace the re-run unless the evidence shows that test failing before and passing after. | required |
| repo-checks-shown | The repo facts' stated gates (PR checklist items such as "tests pass" or "pre-commit passes", a contributing-guide command such as `yarn test`, `make check`, `cargo test`), read against the test evidence and the description. Evidence guide: Test evidence. | Where the repo states a test or check gate, the evidence reports its outcome against a named command or suite. A one-line result such as "`go test ./...` passes (4108 tests)" counts. A gate that was run and failed passes this check when the output or the reason is reported honestly. Fail when a stated gate is never mentioned, and when the evidence says only "tests pass" with nothing named. Where the repo states no test or check gate, pass. | required |
| diff-free-of-debris | Every hunk of the diff, plus the commit list. Evidence guide: Diff quality. | The diff contains only lines the change needs. Fail if any hunk adds or leaves any of these: a debug print or log line (`eprintln!("DEBUG`, `console.log`, `print(` left in for debugging), commented-out code (including a commented-out first attempt or debug line), a dead function or `allow(dead_code)` / `_unused` helper, a TODO or FIXME the change introduces, an import reorder or restructure the fix does not need, whitespace, re-indent, or re-wrap churn on lines the change does not touch, a hunk that removes and re-adds identical lines, or any hunk unrelated to the change. One instance is enough to fail. Reformatting that the repo's formatter forces on lines the change itself writes is not churn. | required |
| template-asks-met | The repo facts' `pull requests:` line (the template's sections and checklist, a changelog or whatsnew entry, the issue-link form such as `closes #xxxx`, a type-of-changes item, before/after asks) and any contributing-guide ask, read against the description AND the diff. Live mode: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and the scope's house rules. Evidence guide: Standards and comms. | Every ask the repo states for a pull request is met with real content. Each required template section or checklist item is present and filled truthfully (not left as the template's placeholder). The issue is linked in the form the repo asks for. A required changelog, whatsnew, or release-note entry is present as a file change in the diff, not just promised in prose. A check ticked in a checklist must be backed by the evidence. Where the repo has no template and asks only for small, focused PRs that reference the issue, a description that names the issue passes. Fail any stated ask that is missing or left as boilerplate. | required |
| ai-disclosure-met | The repo facts' `contribution policy` line (and, live, any AI-policy file the contributing guide links), read against the PR description. Every package is treated as AI-assisted work. Evidence guide: Standards and comms. | If the repo's policy requires AI usage to be disclosed, the description discloses it in the author's own words, naming the tool and the extent of the assistance. If the policy is silent on AI, or welcomes AI without asking for disclosure, pass. Course rule for live mode: the course always asks for an AI-use disclosure in Notes for Reviewers, so in live mode a description with no disclosure fails. Fail a required disclosure that is absent, and one that is a vague gesture with no tool or extent named. | required |
| commits-tell-the-story | The commit list. Evidence guide: Diff quality. | Each commit message says what that commit changes (and follows the repo's stated commit convention, if any). "wip", "fix", "misc", or "cleanup while debugging" do not. Never changes the verdict: debris in the diff is graded by `diff-free-of-debris`, not here. | preferred |

## Verdict rule

Accept if and only if all seven required checks pass. Any single required fail
rejects: a PR is a request for a maintainer's review time, and one broken
promise (drift, missing proof, debris, an ignored ask) is enough to send it
back. `unclear` counts as fail: a PR whose readiness cannot be verified from the
package is not ready to submit, and in eval mode evidence absent from the
bundle is absent.

`commits-tell-the-story` is preferred and never changes the verdict. Report its
grade.

Honest-outcome rule: a shortfall the PR discloses (a deferred case, a check that
failed with its reason, a deviation from the plan with its reason) is graded as
disclosed and does not by itself fail any check. Only an undisclosed gap, or a
claim the diff or evidence contradicts, fails.
