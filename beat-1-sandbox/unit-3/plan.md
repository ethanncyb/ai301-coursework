Cause: not a defect in the detector but a coverage gap in how it is tested.
`tests/unit/test_prompt_defense.py` builds each payload from the pattern under
test, so the suite confirms the regexes match the strings they were written
from; it never asks whether published attack payloads are blocked. My repro
comment on #29 pins the consequence: `is_injection_attempt` returns `False` on
10 of 10 PromptInject payloads in their published form and 0 of 10 when they
are embedded mid-sentence in a longer single-line prompt, while 2 of 2 positive
controls return `True`. The mechanism the artifact supports is positional —
three of the six `INJECTION_PATTERNS` begin with a literal `\n`, and detection
flips to 8 of 10 when exactly that character is prepended. The directory the
issue names, `tests/security/`, holds only `__init__.py`, and
`tests/fixtures/injection_attempts/` does not exist.

Change: add the corpus as fixtures, a red-team suite that records what the
defense actually does against it, and one CI job so the suite runs.

In: `tests/fixtures/injection_attempts/` (the payloads as data, with their
provenance); `tests/security/test_prompt_injection.py` (parametrised over that
corpus, marked `security`, with the currently-undetected payloads carrying
`@pytest.mark.xfail(strict=True, reason="issue #29: ...")` in the convention
`docs/CONTRIBUTING.md` documents, and the positive controls asserted as passing
so a dead harness cannot look green); one `test-security` job in
`.github/workflows/ci.yml`, modelled on `test-unit`, as its own commit.

Out: any change to `safety/prompt_defense.py`. Issue #24 owns the
whitespace-before-colon variant, and the leading-newline gap this corpus
exposes is a third surface again; this suite records the current behavior
rather than changing it, so the detector's own patterns stay untouched and
whoever fixes them inherits a suite that already describes the target. Also
out: the other modules under `safety/`; the indirect path where an injection
arrives inside a retrieved or ingested document; and any assertion about
whether an undetected payload leads anywhere downstream.

Test: re-run the repro steps against the built change, through pytest rather
than the standalone script the repro used. Before, at `f89c06f`:
`python -m pytest tests/security -v` reports `collected 0 items`. After: the
same command collects the corpus and reports every payload the defense does not
currently detect as `xfailed`, with the same payload strings the repro comment
published, and the positive controls as `passed`. As built that is 22 passed and
22 xfailed, because each payload runs in three delivery shapes; see Deviations
for why the shape count changed and what the number was in this plan as posted. Also re-run `make test-unit`, which must
stay at its current `375 passed, 53 xfailed`, since a new suite under
`tests/security/` must not disturb the unit suite; and `make check`, because
`docs/CONTRIBUTING.md` gates a PR on `make check && make test-unit` and the new
files have to satisfy ruff, black and mypy as they land.

Risks and unknowns: the CI-scope question is open. My claim comment asked
whether the workflow change should ride along with the tests and got no answer,
so I have scoped it in — the issue states the suite "should run in CI on every
PR that touches `safety/`", which reads as part of the ask rather than
alongside it — and put it in its own commit so it can be dropped without
touching the suite. Second, `strict=True` means that if a payload I have marked
`xfail` ever starts being detected, CI fails with `XPASS(strict)` until the
marker is removed. That is the repo's documented intent and the signal I want
when #24 or a later fix lands, but it does deliberately couple this suite to
the detector's behavior, and a maintainer should know that before merging.
Third, I have not established how the `security` marker should be wired for the
"on every PR that touches `safety/`" condition specifically: my job runs on
every PR rather than only on `safety/` changes, because path filtering would
make the job skip rather than pass and the repo's own `CI must be green` rule
reads as wanting all jobs to report. I would take direction on that.

## Deviations

One real deviation, in the test-plan numbers rather than in the approach.

The plan said the built suite would report "the 10 published payloads as
`xfailed` and the 2 positive controls as `passed`". It reports **22 passed, 22
xfailed**. The reason is that while writing the suite I kept all three delivery
shapes from the reproduction instead of only the as-published one: each of the
10 attack payloads runs as-is, with a leading newline, and embedded
mid-sentence, which is 30 attack cases rather than 10. Of those, 22 are
currently undetected (10 as-is, 10 embedded, and 2 of the prefixed cases) and 8
are detected — the prefixed deliveries of the eight payloads whose keyword
lands at the start of a line.

I kept the three shapes because dropping two of them would have thrown away the
part of the reproduction that carries the finding. The single-shape suite the
plan described would have recorded "10 payloads not blocked" with no evidence
of *why*; the three-shape suite encodes the contrast that localised it, so the
8 prefixed passes are the control run living inside the test suite rather than
beside it. The substance the plan promised is unchanged and visible in the same
output: all 10 payloads as-published are undetected.

Two smaller things the build settled that the plan had left open:

`black` reformatted `tests/security/test_prompt_injection.py` on first run, so
the committed file is black's formatting rather than mine. Expected — the repo
gates on it — and recorded because it is a difference between what I wrote and
what landed.

`make check` failed locally on 9 ruff errors that turned out to be in
`injection_baseline.py`, the standalone script from my week-2 reproduction,
which was sitting untracked in the repo root. It was never committed, so CI
would never have seen it, but it made the repo's own gate command fail on my
branch. I moved it out of the clone. Nothing tracked changed, and `make check`
is green: ruff, black and mypy all pass.

What did not change: the scope pair held exactly. `safety/prompt_defense.py` is
untouched, the two paths the issue names are the two the suite uses, and the CI
job is its own commit as promised. `git status --porcelain` on the finished
branch is empty, and `plan.md` was never in the working tree — it lives in the
course repo, not on the branch.
