# Rubric: is this a good first issue?

Four required checks and one preferred, applied in order. Every recency
threshold is measured against the bundle's stated capture date in eval mode,
and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-is-alive | Repo-facts block: the `archived:` flag on the repo line, and the dates *and authors* of the "last 5 default-branch commits". Live mode: the equivalent surfaces in `references/evidence-guide.md` family 1 (recent commits, who is committing) and family 2 (archived flag). | Not archived AND at least one of the last 5 default-branch commits is dated within 90 days of the capture date AND at least one such in-window commit is human-authored — a commit whose author name ends in `[bot]` counts only where it merged a human's pull request. A repo with no published releases still passes: commits carry liveness. | required |
| scope-fits-newcomer | The issue body, its labels, its author's association (OWNER / MEMBER / COLLABORATOR / CONTRIBUTOR / NONE), the full comment thread, and the state of each entry on the repo-facts `linked PRs:` line. | All four: (a) **bounded** — the body names a deliverable and what done looks like; several files are still one bounded change when the issue names them, and optional "consider also" follow-ons or a list of suspected causes do not count against it; fail only an explicit umbrella or tracking list of sub-items meant to be split into separate pull requests, or an open-ended standing solicitation ("PRs welcome, big and small"). (b) Not a pure usage or support question. (c) **wanted** — for a new feature or behavior change, a maintainer has signalled they want it: they filed it, applied a maintainer label (`good first issue`, `help wanted`, `enhancement`), or endorsed it in the thread; and where the thread debates the design, a maintainer has settled it. Bug reports and documentation fixes need no separate endorsement. (d) At most one abandoned attempt — two or more closed, unmerged linked PRs in the issue's history means the work is harder than it looks. | required |
| nobody-on-it | Repo-facts `this issue: assignees:` and `linked PRs:`, plus the comment thread. Where the sidebar and the thread disagree, believe the thread. | No assignee AND no open pull request, whether formally linked or only mentioned in the thread AND no live claim comment ("I'll take this", "working on this", `/assign`). A claim is live unless it was retracted, or it is more than 12 months old at the capture date with no open pull request behind it and no follow-up from the claimant since; an expired claim like that does not block. | required |
| ai-work-is-allowed | The repo-facts "contribution policy" line. Live mode: `CONTRIBUTING.md` in the repo root or `.github/` and the contributor docs it links out to, any `AI_POLICY.md` / `AI_USAGE_POLICY.md`, and PR/issue templates — the fifth surface in `references/evidence-guide.md`. | The policy does not flatly ban AI-generated or AI-assisted contributions. Silence passes: most repos state nothing, and that is not a restriction. Conditions pass: disclosure, human review, personal understanding, testing, and "fully AI-generated contributions are not accepted, assistive use is allowed" are terms to follow, not reasons to walk away. Fail only an outright ban on AI-written code or documentation, because this course's workflow is AI-assisted. | required |
| maintainers-answer | Repo-facts "maintainer first-response sample". Live mode: `references/evidence-guide.md` family 1, issue response latency. | At least one sampled issue got a maintainer first response within 30 days. The sample is small and sometimes carries fewer rows than its header claims, so a miss is weak evidence of neglect rather than proof of it — which is why this check ranks and never rejects. | preferred |

## Verdict rule

Accept if and only if all four required checks pass. Any single required fail
rejects the issue. `unclear` counts as fail: a first issue you cannot verify is
not a first issue you should take.

`maintainers-answer` is preferred and never changes a verdict. Report its
grade, and among accepted candidates prefer the ones that pass it — a repo that
answers its issues is a repo that will review your pull request — then order
those by the fit profile in `scope.md` (live mode only).
