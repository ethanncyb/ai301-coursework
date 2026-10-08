# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

PENDING — paste the PR page URL (https://github.com/codepath/pathreview-ai301-fa26-s1/pull/<n>) after opening it.

**Branch**

`fix/29-prompt-injection-red-team-suite`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Run 1 (full, `--save-run eval-run.txt`): **19/20 scored items** (bar: 18/20: PASS). Categories: clear-accept 6/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3. The one disagreement was pkg-05.

After it, I ran one partial canary check (`--only calib-04 --include-calibration`, which never writes eval-run.txt and carries no score). calib-04 agreed (reject), and I made no rubric change. Run 1 is therefore my only full run, and the file I committed holds it: `agreement: 19/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

**pkg-05** (nushell/nushell#18848, category clear-accept). My rubric's verdict: **reject**. Gold label: **accept**.

My rubric rejected it on a single check, `repro-rerun-before-after`, with the evidence line: "(b)/(c) plan's second case (same-name same-key: one row plus warning) has no captured before/after output, only the prose 'Same-key redefine prints the one-time warning.'" Every other required check passed: the diff matches the plan, the description matches the diff, `cargo test -p nu-protocol` passes (312 tests) is reported, there is no debris, and the template is filled.

The plan's test plan names two cases: re-run the issue's script and expect both `atuin` rows, and re-run with two same-name same-key bindings and expect one row plus a warning. The PR shows captured before/after tables for the first case only. The second appears only as one prose sentence, though the diff adds a test for it. My clause (c) says "every failure mode or case the plan's test plan names is exercised, or its omission is disclosed", and clause (b) asks for captured output. So the rubric reads the prose sentence as an unevidenced case and fails it. The gold label treats the issue's own repro, shown before and after, as the decisive evidence, and the warning case as secondary behavior covered by the added test. The difference is that my rubric weighs every named test-plan case equally, where the gold label weighs the reproduced bug above the new behavior the fix introduces.

**Check rationale**

The check, quoted as it reads in `tools/pr-precheck/rubric.md`:

> | repro-rerun-before-after | The test-evidence section (and any before/after in the description), read against the plan's test plan and the reproduction's own steps. Evidence guide: Test evidence. | All of: (a) **the plan's proof is re-run:** the evidence executes the reproduction or test plan on the changed code path, the input that broke, not a neighbouring input or a control alone that never broke. (b) **before and after are observable:** both the broken state and the fixed state are shown as captured output (a value, a message, an exit status, a test moving from failing to passing). Alternatively, the after is shown and the before is the repro's captured output that the evidence explicitly cites. (c) **coverage:** every failure mode or case the plan's test plan names is exercised, or its omission is disclosed. Fail "tested locally", "works on my machine", "verified working", and "colors work now", with no captured output. Fail evidence that exercises only the unchanged path, and evidence that covers one of the plan's named cases and is silent on another. A test file in the diff does not replace the re-run unless the evidence shows that test failing before and passing after. | required |

Why it reads this way: I started from my Unit 3 `test-plan-observable` check (re-runs the proof, states the expected-after, would distinguish fixed from broken), and rewrote it for a PR, where the question is whether the evidence was actually *produced*, not whether a plan promises it. Three choices came from the not-tested packages. Clause (a) says "the input that broke, not a neighbouring input or a control alone", because pkg-07 runs only the single-file control and pkg-14 runs a GET when the bug was a POST: both look like evidence and exercise the unchanged path. Clause (b) names "tested locally", "works on my machine" and "verified working" explicitly, because pkg-04 and pkg-10 replace output with exactly those sentences. Clause (c) is the coverage clause, which I added for calib-04: a solid fix whose plan names two failure modes and whose evidence re-runs only one. I rejected a looser version that passes when "the main repro" is shown, because that would accept calib-04. The last sentence ("a test file in the diff does not replace the re-run…") is there because a new test can itself test the wrong path, as pkg-14's added casing test does.

**Trade-offs**

The coverage clause (c) in `repro-rerun-before-after` gives up pkg-05, and I accept that miss. It asks for captured evidence of every case the plan's test plan names, so pkg-05 (a gold accept) fails because its second case, the same-key warning, is shown only in prose. The same clause is what catches calib-04, whose plan names two failure modes and whose evidence re-runs only one. I checked that with a canary: `python3 run_eval.py --skill ~/.claude/skills/pr-precheck --only calib-04 --include-calibration` graded calib-04 **reject**, agreeing with gold, with the evidence "(c) coverage: plan's repro 2 (`-j 200000 -x echo .`) is not run and its omission is undisclosed; only repro 1 has before/after."

Loosening (c) so that one shown repro is enough would likely flip pkg-05 to accept. It would also turn the check blind to calib-04's kind of miss, and since a loosened check can flip a package that agrees today, it would need canaries from not-tested (pkg-07, pkg-14) as well. At 19/20 with every category matched, I kept the stricter check. It would rather hold a well-tested PR that left one case in prose than pass one that silently skips a failure mode its own plan named.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
