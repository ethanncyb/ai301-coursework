# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

ethanncyb

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/29#issuecomment-5853106834

> Hi, I'd like to take this one on as my first contribution here.
>
> Reading the repo at `f89c06f`, here is how I read the shape of the work, plus one thing I want to check with you before building it.
>
> **What is already there.** `tests/unit/test_prompt_defense.py` covers `PromptDefense` fairly densely: a case for each entry in `INJECTION_PATTERNS`, plus the `sanitize` cases. What it does not do is the thing this issue asks for. Each of those cases uses a string built to match the pattern under test, so the suite confirms the regexes match what they were written from. A curated red-team corpus asks the other question: do known attack payloads get blocked? `tests/security/` currently holds only `__init__.py`, and there is no `tests/fixtures/injection_attempts/` yet.
>
> **One thing to flag early.** The `test-unit` job in `.github/workflows/ci.yml` runs `pytest tests/unit`, so a suite added under `tests/security/` would not execute in CI as the workflow stands today. Meeting the "should run in CI on every PR that touches `safety/`" requirement looks like it needs a workflow change alongside the tests. If that workflow change should ride along with the tests, say so and I'll scope it in.
>
> **Staying clear of #24.** The `xfail` on `test_whitespace_variations_detected` points at issue #24, the newline/whitespace variant gap. My reading is that #24 owns fixing that and this issue owns the corpus and the harness around it, so I plan to record what the current defense does without proposing changes to `prompt_defense.py` here.
>
> **Next step.** Set the project up per `SETUP.md`, run the existing suite, and record what `is_injection_attempt` and `sanitize` actually do against a small curated set of published injection payloads. I'll post that baseline in this thread before writing any tests, so the corpus is built on observed behavior rather than on what the patterns look like they should catch. I'm not putting a date on it, and I'll report back either way, including if the baseline turns out less interesting than I expect.

**Reproduction comment**

[Link to the comment where you posted your reproduction. It must record the environment
(OS, relevant versions, code state), steps a stranger could follow, and what you observed.
**Then paste the text of that comment underneath the link** — the pasted text is what this
field is graded on, so copy across what you actually posted.]

## Eval iterations

**Run history**

Five passes, in order. The first was free and by hand; the rest used the harness.

1. **Hand-grade of the four calibration packages** (no credit spent). 4/4 agreement:
   calib-01 accept, calib-02/03/04 reject. I did this before spending anything because
   my rubric had just been written and I wanted the wording tested on packages whose
   answers the class had already argued out. calib-04's gold note reads "master rejects
   on env-recorded", which is the name I had given that check, so the surface matched.
2. **Smoke run**, `--limit 3`. 3/3 scored items, no bar printed (partial runs never
   print one). This confirmed the skill loaded my rubric and emitted a parseable JSON
   block rather than answering on its own.
3. **Full run 1**. **18/20, bar PASS.** Categories: clear-accept 6/8, disclosure 1/1,
   no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4. Two disagreements, both
   false rejects on clear accepts: pkg-05 failed `steps-rerunnable`, pkg-10 failed
   `artifact-shows-issue`. The category floor already held, including the single
   disclosure package.
4. **Canary re-run**, `--only pkg-05,pkg-10,pkg-20,pkg-18,pkg-14,pkg-02,calib-03
   --include-calibration`. 6/6 scored plus calib-03. Both revisions loosened a check, so
   I re-ran the two I was fixing plus one already-agreeing package from every category
   the loosening could touch. Both fixes landed and nothing flipped.
5. **Confirming full run**, `--save-run eval-run.txt`. **20/20, bar PASS**, every
   category matched: clear-accept 8/8, disclosure 1/1, no-evidence 4/4,
   unfollowable-comms 3/3, wrong-target 4/4. This is the run committed beside this file.

**Package analysis**

**pkg-20** (ghostty-org/ghostty#13604, the `disclosure` category). My rubric said
**reject**; the gold label is **reject**; they agree.

What makes it worth naming is that it is the only package my rubric rejects while
passing every proof check on it. Its report records the environment (ghostty 1.3.1
release build, Fedora 42, GTK/Wayland), gives the exact config and launch command
inline, and shows the deciding artifact: querying the colour scheme with a single
non-conditional theme returns `^[[?997;2n` (light) even though the window was launched
with `--window-theme=dark`, against a control run with a conditional theme pair that
correctly returns `^[[?997;1n`. `env-recorded`, `steps-rerunnable`,
`artifact-shows-issue`, `outcome-honest`, `claim-specific-and-modest` and the preferred
`control-run-shown` all pass.

It rejects on `conventions-respected` alone. Ghostty's stated policy is that all AI
usage in any form must be disclosed, naming the tool and the extent of the assistance,
and neither candidate comment discloses anything. My rubric reads course packages as
AI-assisted work, so silence there is a miss rather than an exemption.

That is exactly why the category floor exists and why it has teeth this week. This is
the set's only disclosure package. A rubric built purely around proof quality reads
pkg-20 as one of the best reproductions in the set, accepts it, and cannot buy the miss
back on volume no matter how well it scores elsewhere. Getting it right required a check
that reads the repo's stated policy rather than the report.

**Check rationale**

From `tools/repro-check/rubric.md`, the `artifact-shows-issue` row, clause (b) of its
pass condition, exactly as it now reads:

> or (b) **evidenced cannot-reproduce** — the report states it could not reproduce, shows the attempt it actually ran with its artifacts, and accounts for the gap: either the attempt reached the issue's trigger conditions, or the report names the specific condition it could not satisfy and why that condition looks necessary. A cannot-reproduce need not reach the trigger; it must show the attempt and locate what was missing.

The second sentence is there because of a specific failure. In my first full run this
clause read that a cannot-reproduce needed artifacts showing "a real attempt that
reached the issue's trigger conditions", and that rejected pkg-10, a gold accept. pkg-10
is an honest cannot-reproduce on starship#7648: the reporter's setup is macOS with fish,
the attempt ran on Linux with zsh, and the report says plainly that "a fish shell
resolving `PWD` logically looks necessary to hit the `contract_repo_path` failure; I did
not have one available for this attempt."

So the report's whole finding was that it could not reach the trigger, and my wording
rejected it for exactly the thing that made it honest. The clause was demanding a
reproduction from a package whose value is that it refused to fake one. I rewrote it to
accept either shape: the attempt reaches the trigger, or the report names the specific
condition it could not satisfy and why that condition looks necessary.

I also scoped the failure sentence in the same row, from "if the artifact shows only
that the tool ran" to "if the report **claims a reproduction** whose artifact shows only
that the tool ran". Without that, pkg-10 would still have failed: its artifact is a
prompt rendering normally, which is precisely a tool running. The rejection I did not
want to lose was pkg-14, where the artifacts also show only that the tool runs but the
report asserts a reproduction anyway. Tying that sentence to the claim being made
separates the two, and pkg-14 still rejects.

**Trade-offs**

Both revisions above loosened a check, and a loosened check can flip a package that
agreed before. Rather than discover that on a $4 confirming run, I spent about $1.40 on
a `--only` canary run first: the two packages I was fixing, plus one already-agreeing
package from every category the change could reach — pkg-20 for `disclosure`, pkg-18 for
`unfollowable-comms`, pkg-14 for `no-evidence`, pkg-02 for `wrong-target`, and calib-03
free via `--include-calibration`. Nothing flipped, and the confirming full run agreed.

The loosening I am least comfortable with is in `steps-rerunnable`, which now accepts
inputs "described precisely enough to rebuild". pkg-05 needs it: it describes its
`env.yml` in prose rather than pasting it. The line between that and pkg-18's private
monorepo is a judgement about whether a reader could reconstruct the input, and a
determined writer could sit on the wrong side of it while sounding specific. I accept
that exposure because the alternative rejects a gold accept for not pasting a file whose
triggering element it names.

I also deliberately did not write a check I had drafted: testing comments against the
fields a repo's bug-report template asks for. It is a structure-shaped check, which the
rubric template warns produces graders who disagree with themselves, and it would have
falsely rejected pkg-01, whose comment never confirms it searched for duplicates even
though its reproduction is exact. The substance that check was reaching for is already
covered by `env-recorded` and `steps-rerunnable`, which read the same information as
evidence rather than as a checklist.

What `conventions-respected` will miss: it reads only stated policy. A repo whose
disclosure norm lives in maintainer comments, a pinned issue, or custom rather than in
`CONTRIBUTING.md` passes it, and my rubric will call that package ready when a human
reviewer would not.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/repro-check/`.
