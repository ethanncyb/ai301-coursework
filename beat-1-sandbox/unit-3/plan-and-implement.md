# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

ethanncyb

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/29#issuecomment-5925955024

> The gap is in the tests rather than the detector: `tests/unit/test_prompt_defense.py` builds each payload from the pattern under test, so it confirms the regexes match what they were written from and never asks whether published payloads get blocked. The baseline put a number on that — 0 of 10 PromptInject payloads flagged as published, 0 of 10 embedded mid-sentence, 2 of 2 positive controls flagged — and the mechanism the artifact supports is positional, since three of the six patterns begin with a literal `\n` and detection goes to 8 of 10 when exactly that character is prepended.
>
> So: the corpus becomes `tests/fixtures/injection_attempts/`, and `tests/security/test_prompt_injection.py` records what the defense currently does against it — the undetected payloads marked `xfail(strict=True)` in the style `CONTRIBUTING.md` documents, the positive controls asserted as passing so a dead harness can't look green. Out of scope, deliberately: any change to `prompt_defense.py`. #24 owns the whitespace-before-colon variant, this corpus exposes a third surface again, and a suite that describes the current behavior is more useful to whoever fixes the patterns than a suite written against the behavior we wish they had.
>
> On the CI half — I asked above whether the workflow change should ride along and haven't heard back, so I've scoped it in, since the issue states the suite should run in CI as part of the ask. It's a separate commit, so you can drop it and the suite still stands. One thing I'd take direction on: my `test-security` job runs on every PR rather than only on ones touching `safety/`, because a path filter makes the job skip rather than pass, and the `CI must be green` rule reads as wanting every job to report. Say the word if you'd rather it were filtered.
>
> Two things I should flag rather than have you find them. `strict=True` means that when a marked payload does start being detected, CI goes red with `XPASS(strict)` until the marker comes off — that is the signal I want when #24 lands, but it does couple this suite to the detector's behavior on purpose. And I have not tested the indirect path where an injection arrives inside a retrieved document, so none of this says anything about that route.

---

## Your branch

**Branch**

fix/29-prompt-injection-red-team-suite

Pushed to my fork: https://github.com/ethanncyb/pathreview-ai301-fa26-s1/tree/fix/29-prompt-injection-red-team-suite

**Evidence**

My Unit 2 reproduction drove the corpus through a standalone script
(`injection_baseline.py`). The built change routes the same payloads through
pytest and the real import instead, which is the check through the real code
path: same strings, same module, now inside the suite the issue asked for.

**Before** — on `main` at `f89c06f`, the directory the issue names exists but
holds no tests, so there is nothing to run:

```
$ .venv/bin/python -m pytest tests/security -v
collecting ... collected 0 items

============================ no tests ran in 0.10s =============================
```

**After** — on `fix/29-prompt-injection-red-team-suite` at `18192d4`, the same
command collects the corpus and records what the defense does with it. Each of
the 10 attack payloads runs in three delivery shapes; `XFAIL` is a payload the
defense does not currently detect:

```
$ .venv/bin/python -m pytest tests/security -v --tb=short
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-say-as-is] XFAIL [  2%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-say-prefixed] PASSED [  4%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-say-embedded] XFAIL [  6%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-print-as-is] XFAIL [  9%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-print-prefixed] PASSED [ 11%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[ignore-print-embedded] XFAIL [ 13%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[nevermind-as-is] XFAIL [ 22%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[nevermind-prefixed] XFAIL [ 25%]
tests/security/test_prompt_injection.py::TestCorpusIsBlocked::test_attack_payload_detected[nevermind-embedded] XFAIL [ 27%]

======================== 22 passed, 22 xfailed in 0.15s ========================
```

The before/after that matters is the first line of each block: `collected 0
items` becomes 44 collected cases, and the 10 published payloads that my repro
comment reported as undetected are now recorded as such in the repo's own test
suite rather than in a script beside it.

The 8 `PASSED` attack cases are the control living inside the suite: they are
the prefixed deliveries of the payloads whose keyword lands at the start of a
line, which is what three of the six `INJECTION_PATTERNS` are anchored on. Same
0/10-vs-8/10 contrast the reproduction established, now re-derived by pytest.

**No regression.** The unit suite is unchanged, and the repo's own PR gate is
green:

```
$ make test-unit
================= 375 passed, 53 xfailed, 3 warnings in 6.59s ==================

$ make check
.venv/bin/ruff check .
All checks passed!
.venv/bin/black .
All done! 111 files left unchanged.
.venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
```

`375 passed, 53 xfailed` is the same count as before the change, which is what
I wanted to show: a new suite under `tests/security/` must not disturb
`tests/unit`.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Five passes, in order. The first was free; the harness ran four times.

1. **Hand-grade of three calibration packages** (no credit spent). 3/3
   agreement: `calib-01` accept, `calib-02` and `calib-04` reject. I did this
   before spending anything because the rubric had just been written. `calib-04`
   was the useful one — strong diagnosis, explicit scope pair, thread-aware
   comment, and it still rejects on `test-plan-observable` alone, because its
   whole test plan is "Run the full test suite (`cargo test --workspace`) and
   make sure nothing regresses." That is the clause I had written for exactly
   that shape, so the check earned its place before it cost anything.
2. **Smoke run**, `--limit 3`. **3/3 scored items**, no bar printed. This
   confirmed the skill loaded all three authored components — the header line
   reads `rubric.md + evidence-guide.md + procedure.md` — rather than falling
   back to a template.
3. **Full run 1**. **18/20, bar PASS.** Categories: clear-accept 5/7,
   scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4.
   The category floor held on the first run, including the 2-package
   `thread-convention` category. Two disagreements, both false rejects on clear
   accepts: `pkg-08` failed `confidence-matches-evidence`, and `pkg-14` failed
   `cause-follows-evidence`, `plan-executable` and `confidence-matches-evidence`.
4. **Canary re-run**, `--only pkg-08,pkg-14,pkg-01,pkg-16,pkg-10,pkg-17,pkg-04,calib-02
   --include-calibration`. **6/7 scored** plus the calibration trap. All three of
   my revisions loosened a check, so I re-ran the two I was fixing plus one
   already-agreeing package from every category the loosening could reach --
   `pkg-01` and `pkg-16` for wrong-cause, `pkg-10` and `pkg-17` for unbuildable,
   `pkg-04` for the 2-package thread-convention floor. Nothing flipped. `pkg-08`
   was fixed; `pkg-14` still rejected, now on `confidence-matches-evidence`
   alone instead of three checks.
5. **Confirming full run**, `--save-run eval-run.txt`. **20/20, bar PASS**,
   every category matched: clear-accept 7/7, scope-creep 4/4,
   thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4. This is the run
   committed beside this file, and its agreement line reads
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

One thing I am not going to round off: `pkg-14` rejected in run 4 and accepted
in run 5 with **no change to any component between them** — the sha256
fingerprints in `eval-run.txt` are the files the canary graded. So the last
disagreement closed by grader variance on a borderline package, not by anything
I wrote. I report 20/20 because that is the run I committed, but the honest
reading of my rubric's behavior on `pkg-14` is "sometimes accepts", not
"accepts".

**Package analysis**

**pkg-14** (zellij#4241, category `clear-accept`). The gold label is
**accept**. My rubric said **reject** on the first full run, failing
`cause-follows-evidence`, `plan-executable` and `confidence-matches-evidence`;
after revision it accepted on the confirming run. It is worth naming because it
is the package that taught me what was actually wrong with my checks, and
because it is still the one I trust least.

The plan diagnoses a reattach handshake: zellij issues OSC colour queries on
reattach but wires the client's stdin to the session before the responses are
consumed, so they echo into the pane. The repro evidence is entirely black-box
-- SSH reattach cycles, raw `rgb:1c1c/1c1c/1c1c` strings in a pane, a clean
0.44.1 control, and a cache-clear observation.

All three of my failures came from one mistake. I had written the checks to
credit *shown* evidence and nothing else, so a mechanism one layer below what
the artifacts can see read as unsupported however carefully it was argued. But
the plan says "Grounded in the repro:" and then names its grounds: fresh attach
performs the same queries and is clean, the leak starts exactly at reattach,
and 0.44.1 predates the reattach-path change and is clean on the same setup.
That is a regression window plus two controls. It is inference, it is marked as
inference, and it is the strongest form a diagnosis can take when the symptom
is observable and the cause is not. `plan-executable` failed for the sibling
reason: the plan names crates rather than functions and defers the exact site
to "after tracing the query issuance with debug logs, which I have working". I
had read that as vagueness. It is sequencing, and the route to the site is
stated, which is what a stranger actually needs.

So the rubric now says, in `cause-follows-evidence`, that "black-box evidence
does not bar a code-level diagnosis; an ungrounded one does", and
`plan-executable` accepts a named area plus a stated method of locating the
exact site. Both changes are narrow: they name the forms of support that count,
rather than relaxing the requirement that support exist.

What I still do not like: `pkg-14` flipped to accept between two runs with
identical components, so it sits right on my `confidence-matches-evidence`
boundary. I left it there deliberately rather than loosen a third time — that
check is the only thing standing between a `wrong-cause` package and an accept,
and trading four reliable rejects for one arguable accept is a bad exchange.

**Check rationale**

From `tools/plan-check/rubric.md`, the `test-plan-observable` row, its pass
condition exactly as it now reads:

> All of: (a) **re-runs the proof** — the test plan exercises the behavior the repro evidence established, through the real code path, not a restatement that the change will work; (b) **states the expected-after** — it says what will be observed once the change lands, in terms someone could check: a value, a message, an exit status, a rendered state, a test that moves from failing to passing; (c) **the observation would actually distinguish** the fixed state from the current one. Fail a test plan that only asserts the suite still passes, one that names no observable outcome ("verify it works", "confirm the fix"), and one whose check would read the same before and after the change. A plan that cannot re-run the original repro passes on (a) by routing the same inputs through the real path instead, where it says so.

Two decisions in it, and the second is the one I would defend hardest.

Clause (c) and the first failure example exist because of `calib-04`, which I
hand-graded before spending any credit. That plan is good everywhere else: the
diagnosis is grounded in the owner's own thread analysis, the scope pair is
explicit and defers the hard rework with a reason, the approach names
`crates/core/flags/hiargs.rs`. Its entire test plan is "Run the full test suite
(`cargo test --workspace`) and make sure nothing regresses." A suite that was
green before the change and green after it distinguishes nothing, and the gold
label rejects the package. Without clause (c) my rubric would have accepted a
plan whose verification step cannot fail for the right reason.

The last sentence is the part I nearly left out. An earlier draft required the
test plan to re-run the original repro steps, full stop. That would have
rejected my own plan for #29: my week-2 reproduction was a standalone script,
and the change replaces it with a pytest suite, so the literal steps cannot
re-run. The course materials name that move explicitly — turn the same inputs
into a check through the real code path — so a rubric that punished it would
be grading the shape of the proof rather than the proof. The escape clause is
narrow on purpose: it requires the plan to *say* it is substituting, because a
plan that silently swaps its verification is the thing clause (a) is for.

**Trade-offs**

Three give-ups, in the order they cost me something.

**The canary spend.** All three of my revisions loosened a check, and a
loosened check can flip a package that agreed before. Rather than discover that
on a $4 confirming run, I spent about $1.60 on `--only` first: the two packages
I was fixing plus `pkg-01` and `pkg-16` (wrong-cause, the category most exposed
to a looser `cause-follows-evidence`), `pkg-10` and `pkg-17` (unbuildable,
exposed to a looser `plan-executable`), `pkg-04` (the 2-package
thread-convention floor), and `calib-02` free via `--include-calibration`.
Nothing flipped. The confirming run agreed.

**The loosening I am least comfortable with** is in
`confidence-matches-evidence`, which now says "a finding cited from the issue
or thread, a mechanism narrowed by control runs, and a regression window that
dates a change all carry an assertion". `pkg-08` and `pkg-14` both need it. But
a plan can now cite a thread confidently and pass, and threads contain wrong
analyses. My check reads whether a source is *named*, not whether the source is
*right* — so a plan that inherits a maintainer's mistaken diagnosis, says so,
and builds on it will pass this check and go on to fail in review. I accept
that because the alternative rejects every plan whose cause sits below the
black-box surface, which is most real bug plans.

**The check I did not write.** I drafted one that tested whether a plan named a
branch matching the repo's `<type>/<issue-number>-<desc>` convention, and threw
it away. It is structure-shaped — the rubric template warns those are what make
graders disagree with themselves — and a plan comment has no reason to mention
a branch name at all, so it would have failed good plans for an omission that
costs nothing. `conventions-respected` now reads branch shape only where the
plan actually names a branch.

**What my procedure will miss, and how I know.** My own tool told me during the
live gate. `procedure.md` is written against eval-mode section addresses and
covers live mode in a single note: its read-order step 2 says to "record the
`contribution policy` line verbatim", and on a live issue there is no such line
-- the executor has to go find `docs/CONTRIBUTING.md` and the templates itself.
It also never says to read `scope.md` or `voice-guide.md`; the gate run took
that order from `SKILL.md` instead and said so.

The sharper gap it found on the second gate: my procedure never says how to
grade a plan that carries a `## Deviations` section. `SKILL.md` says to re-grade
the updated package after a deviation, but no step of mine decides whether a
check grades the original statement, the deviated reality, or both. The gate
graded the test plan on its stated clauses and treated the deviation as part of
the plan, then pointed out that a different reader could do it differently —
which is precisely the situation my own closing line in `procedure.md` says
means the rubric is underspecified. It is invisible to the eval set, because
every package there is a plan captured before any build, so no bundle contains
a deviations section at all. That is the shape of the miss: a surface the eval
set cannot test, found only by running the tool on real work.

The procedure is complete for the eval set it was tuned on and thinner than it
should be for live runs. I
left it as it stands rather than edit after the confirming run, because the
sha256 fingerprints in `eval-run.txt` match the files in `tools/plan-check/`
exactly, and I would rather submit a committed run that provably graded the
submitted files than a quietly better file the run never saw.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
