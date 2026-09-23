# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/29

(#29 — Implement a red-teaming test suite for the prompt injection defense)

**Verdict output**

Live-mode run of the installed `issue-select` skill on three candidates from the Path
Review repo (#29, #24, #31), pasted verbatim. Issue #29 is graded `accept` and ranked
first on fit. The same `rubric.md` that produced the 20/20 run in `eval-run.txt` was
used unchanged.

````
All three are in scope (`codepath/pathreview-ai301-fa26-s1`). Graded each independently against `rubric.md`, then ranked the accepted ones by the fit profile.

**Repo-level evidence (shared):** not archived, last 5 default-branch commits all human-authored by `Aburke225` (COLLABORATOR), newest 2026-09-16 — 7 days ago. No `CONTRIBUTING.md` at root/`.github/`; `docs/CONTRIBUTING.md` covers branch naming, CI, and seeded-bug xfail rules and says nothing about AI use — silence passes. Maintainer replied on issue #43 six days after it was opened, so `maintainers-answer` passes repo-wide. The only PR in the repo (#74) targets #60, so none of these three has a linked PR.

### Accepted, in fit order

**1. #29 — Red-teaming test suite for the prompt injection defense.** Best fit by a distance: it's a curated attack corpus against a safety layer *plus* wiring that suite into CI so it gates PRs touching `safety/` — precisely the two things the fit profile names as growth goals (defensible attack coverage, and pytest suites that gate pull requests). Caveat worth stating: at `tier-3` / 7–10h it's the heaviest of the three, but the rubric sets no effort ceiling, so it passes as written.

**2. #31 — Integration tests for the full safety middleware chain.** Pytest + fixtures across the service layer (prompt defense → content filter → bias detector → PII scrubber), which matches the "production Python backend, the service layer around the model" goal, but with no adversarial-taxonomy or CI-gating component.

**3. #24 — Injection detection misses role-switch lines with whitespace before the colon.** Maintainer-filed `bug`, single file, prompt-injection adjacent — but a regex tightening in `INJECTION_PATTERNS` is closest to what the profile already does well, so least growth.

No rejections.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/29",
    "checks": [
      {"name": "repo-is-alive", "grade": "pass", "evidence": "Not archived; newest default-branch commit 2026-09-16 by human Aburke225 (7 days before today)."},
      {"name": "scope-fits-newcomer", "grade": "pass", "evidence": "Bounded deliverable naming tests/security/test_prompt_injection.py + fixtures dir with done = all curated attacks blocked and suite running in CI; filed by COLLABORATOR Aburke225 and labeled enhancement; no closed unmerged PRs."},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: []; zero comments; repo's only PR (#74) targets issue #60."},
      {"name": "ai-work-is-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and .github/PULL_REQUEST_TEMPLATE.md state no AI restriction; no AI_POLICY.md exists."},
      {"name": "maintainers-answer", "grade": "pass", "evidence": "Issue #43 opened 2026-09-10 got a COLLABORATOR reply from Aburke225 on 2026-09-16 (6 days)."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/31",
    "checks": [
      {"name": "repo-is-alive", "grade": "pass", "evidence": "Not archived; newest default-branch commit 2026-09-16 by human Aburke225 (7 days before today)."},
      {"name": "scope-fits-newcomer", "grade": "pass", "evidence": "One named deliverable, tests/integration/test_safety_middleware.py, with pass/fail fixtures per layer; filed by COLLABORATOR Aburke225 and labeled enhancement; no closed unmerged PRs."},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: []; zero comments; timeline shows only labeling events; no linked PR."},
      {"name": "ai-work-is-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and .github/PULL_REQUEST_TEMPLATE.md state no AI restriction; no AI_POLICY.md exists."},
      {"name": "maintainers-answer", "grade": "pass", "evidence": "Issue #43 opened 2026-09-10 got a COLLABORATOR reply from Aburke225 on 2026-09-16 (6 days)."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/24",
    "checks": [
      {"name": "repo-is-alive", "grade": "pass", "evidence": "Not archived; newest default-branch commit 2026-09-16 by human Aburke225 (7 days before today)."},
      {"name": "scope-fits-newcomer", "grade": "pass", "evidence": "Bug report in safety/prompt_defense.py naming the defect and the fix ('Allow whitespace between the role word and the colon'); bug label; bug reports need no separate endorsement; no closed unmerged PRs."},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: []; zero comments; 2026-09-16 timeline entry is a 'renamed' event by the maintainer, not a claim; no linked PR."},
      {"name": "ai-work-is-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and .github/PULL_REQUEST_TEMPLATE.md state no AI restriction; no AI_POLICY.md exists."},
      {"name": "maintainers-answer", "grade": "pass", "evidence": "Issue #43 opened 2026-09-10 got a COLLABORATOR reply from Aburke225 on 2026-09-16 (6 days)."}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Two runs, in order:

1. `--only issue-01,issue-09,issue-12,issue-14,issue-15,issue-19,issue-20` (partial canary on the
   seven bundles my revision was aimed at) — **7/7**. Partial runs print no bar and write no file.
2. Full run with `--save-run` — **20/20**, the score in the committed `eval-run.txt`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`.

Before those two runs I hand-graded all 24 bundles against the three checks I brought out of the
calibration activity, which is where the four defects below came from; the canary was the cheap
confirmation that the rewrite fixed them before spending a full run.

**Issue analysis**

`issue-14` (LegalQuants/lq-ai#490). **My rubric: `accept`. Gold label: `accept`.** They agree now,
but the three-check rubric I brought out of the calibration activity rejected it, and the reason is
the most useful thing I learned this unit.

That rubric's liveness check read:

> Not archived AND at least one default-branch commit dated within 90 days of the capture date AND
> at least 1 of the 5 sampled issues got a maintainer first response within 30 days.

The bundle's repo-facts block satisfies the first two clauses easily — `archived: no`, and five
default-branch commits dated `2026-08-01` through `2026-08-05` against a `2026-08-05` capture. The
third clause fails, but not because the repo is neglected. The header promises a sample of five
issues and the block carries exactly one row:

> `#489 (opened 2026-08-04): no maintainer comment in thread`

A tracker quiet enough to hold one recently-updated issue, opened the day before capture, cannot
demonstrate a 30-day response time in either direction. My check read that sampling artifact as
proof of death and rejected a repo that had shipped commits on the capture date itself — an issue
whose body names six files, gives before/after strings for each, carves out a seventh
("*leave this one alone*"), and supplies a `grep -rni discord` verification command.

Why my current rubric returns `accept`, check by check, from the committed run's own evidence
lines: `repo-is-alive` passes on "commits 2026-08-01 through 2026-08-05 by human authors
SaifAlYounan/sergiomaldo, within 90 days of capture date 2026-08-05"; `scope-fits-newcomer` passes
because the "issue lists exactly 6 files to edit plus explicit checking instructions and 'leave
this one alone' on the 7th", is labelled `documentation, good first issue`, and has no abandoned
attempts; `nobody-on-it` passes on "assignees: none; linked PRs: none; comments (0 total)";
`ai-work-is-allowed` passes because the contribution policy carries "no statement on AI or
contribution tooling — silence passes". `maintainers-answer` grades `fail`, and the verdict is
`accept` anyway, because it is weighted `preferred`.

The same clause was doing the same damage on `issue-01`, `issue-09` and `issue-16`: all three are
conda/conda, whose sample's only answered row is `#16275 ... 32.9 days` — 2.9 days past my
threshold, on a repo with five commits the day before capture. Four of the eight `clear-accept`
golds were being rejected by one conjunct that was measuring sample size, not repo health. So I
demoted it rather than deleting it: it is now the `maintainers-answer` preferred check, and the
full run shows it grading `fail` on issue-01, issue-09, issue-14 and issue-16 while all four still
come out `accept`, because preferred checks never touch a verdict.

**Check rationale**

From the `rubric.md` uploaded to `tools/issue-select/`, quoted as currently written:

```
| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| ai-work-is-allowed | The repo-facts "contribution policy" line. Live mode: `CONTRIBUTING.md` in the repo root or `.github/` and the contributor docs it links out to, any `AI_POLICY.md` / `AI_USAGE_POLICY.md`, and PR/issue templates — the fifth surface in `references/evidence-guide.md`. | The policy does not flatly ban AI-generated or AI-assisted contributions. Silence passes: most repos state nothing, and that is not a restriction. Conditions pass: disclosure, human review, personal understanding, testing, and "fully AI-generated contributions are not accepted, assistive use is allowed" are terms to follow, not reasons to walk away. Fail only an outright ban on AI-written code or documentation, because this course's workflow is AI-assisted. | required |
```

I added this check because my three-check rubric could not see a whole category. Three checks cover
four evidence families and leave the fifth surface — the contribution policy — ungraded, which I had
already flagged on the calibration worksheet when p5.js's AI Usage Policy in `calib-01` went unread.
`issue-12` (bookwyrm-social/bookwyrm#1133) is the eval set's proof that this matters: it passes
liveness, passes scope, and passes claims, and its contributing docs still say

> "Meaningful human interaction is the whole point of BookWyrm. We do not accept AI-generated code
> or documentation."

It is the only item in the `policy` category, so without this check the category floor rests on
whether some other check happens to misfire on that one bundle.

The hard part was the pass condition, not the idea. Most policies in the set are not silent and not
bans. Of the 24 bundles, 16 are silent and 8 state something: three (`issue-01`, `issue-09`,
`issue-16`) say "generative AI tools welcome"; two (`issue-08`, `issue-15`) say contributors "must
personally understand, test, and be able to explain every change"; `calib-01` says "fully
AI-generated contributions are not accepted; assistive AI use is allowed"; `issue-10` discourages;
only `issue-12` bans. A check worded as "no AI restrictions" would have failed the first six as
well as the ban, and `issue-01`, `issue-09` and `issue-16` are all gold `accept`. So the condition is deliberately
asymmetric — silence passes, conditions pass, only a flat ban fails — and it names the "fully
AI-generated not accepted, assistive allowed" shape explicitly so the grader does not mistake it for
a ban. In the full run it fails exactly one bundle, `issue-12`, and it is the only check that fails
it.

**Trade-offs**

What `ai-work-is-allowed` gives up is everything between silence and a flat ban. The clearest case
it will miss is `issue-10` (tldr-pages/tldr#18405), whose policy line reads:

> "strongly discourages generative AI for new pages (output is often inaccurate); pull requests
> suspected of being made wholly or partly with generative AI or machine translation without human
> review are closed"

That is a repo I would be unwise to bring AI-assisted work to, and my check passes it, because the
closure is conditioned on "without human review" and "strongly discourages" is not "we do not
accept". I accepted that deliberately. Drawing the line at discouragement would have put the check
in the business of weighing tone, and the same wording would then have to be kept from firing on
the six bundles that welcome AI subject to review, understanding and testing — conditions I intend
to follow anyway. `issue-10` costs me nothing here: it is gold `reject` and the full run rejects it on
`scope-fits-newcomer` (a self-described "megaissue") and `nobody-on-it`, so the miss is invisible in
the score. It would cost me on a healthy, unclaimed, bounded tldr-pages issue, and my answer there
is that the policy check is a floor, not the whole judgment.

The canary confirms this is the check's only behavioral edge on the set. On the seven bundles I
re-ran with `--only`, `ai-work-is-allowed` graded `pass` on six; on `issue-12` it graded `fail`,
with this evidence line in the run's `--out` results:

> "CONTRIBUTING.md 'Generative AI' section: \"We do not accept AI-generated code or documentation.\""

`issue-12` is also the only bundle in the full run where it is the sole failing check, so adding it
changed one verdict and left the other nineteen where they were.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

**1. Fit to my interests and to the time available.** Prompt injection is what I
already work on — my research codebase attacks and probes injection defenses, and at
the DEF CON Robotic Hacking Community AI CTF I wrote a prompt-injection harness against
a robot's LLM assistant. #29 asks for a curated corpus of known injection attacks run
against `safety/prompt_defense.py`, which is the same shape of work on someone else's
codebase instead of my own. The second half of the issue is the part I have least
practice with: wiring the suite into CI so it gates every PR touching `safety/`. I have
written pytest suites but rarely made them block a merge, so this issue is half
familiar and half the thing I want to learn. On time: it is labelled `tier-3` at an
estimated 7–10 hours, the heaviest of the three I graded. I am taking it anyway,
because the attack-corpus half is work I can do quickly and the CI half is what I am
paying the hours for.

**2. What the verdict identified correctly, and what I weighed that the rubric could
not.** The rubric got the facts right: the repo is alive (human commit 2026-09-16, a
week old), the issue names a real deliverable with a home that already exists
(`tests/security/` and `tests/fixtures/` are both present, `safety/prompt_defense.py`
is the target), the maintainer filed it themselves and labelled it `enhancement`, and
nobody is on it — no assignee, no linked PR, zero comments. `docs/CONTRIBUTING.md` says
nothing about AI use, so my `ai-work-is-allowed` check passed it on silence.

What the rubric cannot see is effort. Nothing in it reads `tier-3` or "7–10 hours", and
nothing weighs the fact that #29 is the only one of the three not labelled `good first
issue`. My `scope-fits-newcomer` check asks whether the work is *bounded*, not whether
it is *small*, and those came apart here: #29 is perfectly well specified and still
three times the job that #24 is. It also cannot see that the CI half is where my own
gap is — the fit profile only reorders issues the rubric has already accepted, so it
moved #29 to the top, but it could never have warned me off. I chose it knowing both of
those, and if I run out of time the attack corpus without the CI wiring is still a
useful PR.

One thing I got wrong before running it: I expected `maintainers-answer` to fail,
because the six issues I spot-checked by hand had comments only from classmates. The
skill sampled differently and found issue #43, where the maintainer replied in six
days, so the check passed. That is a small lesson in how much a five-issue sample can
swing — the same fragility that made me demote that check to `preferred` in the first
place.

**3. Anticipated difficulty in claiming.** Low. #29 has no assignee, no linked PR and
no comments at all, so I am not racing anyone, and the Path Review house rule means
classmates' claim comments would not block me even if some appeared. The real risk is
after the claim, not during it: `docs/CONTRIBUTING.md` warns that a first PR may need a
maintainer to start CI, and since the whole point of this issue is a suite that runs in
CI, I cannot verify the deliverable until someone approves that first workflow run.
There are only two maintainer comments in the entire tracker, so I should claim early
and expect to wait.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
