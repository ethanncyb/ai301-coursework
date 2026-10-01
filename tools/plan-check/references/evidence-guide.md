# Evidence guide: where proof lives in a plan package

The map the rubric reads with. For each family a check names, this file says
where to find the evidence and what good looks like when you do.

A package bundle has fixed sections, and they are the addresses used below:
the header lines (`source`, `captured`), `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Repro evidence`, `## Candidate plan`, and
`## Candidate plan comment`. In live mode the addresses are the issue thread,
the student's posted repro comment, the repo's own documents, and the draft
files. Three rules hold in both modes: the issue defines what the change is
for, the repro evidence defines what is actually known about the behavior, and
evidence absent from the package is absent, however likely it seems.

## Diagnosis

**Where it lives.** The cause or diagnosis line of `## Candidate plan`, usually
its first sentence. Read it against `## Repro evidence`, which is the only
place the behavior is actually established, and against the `## Issue` body,
which says what was reported. `## Thread highlights` matters too: a maintainer
who has already named a cause, or ruled one out, changes what a plan may assert
without saying more. Live mode: the diagnosis in `plan.md`, read against the
student's posted repro comment on the issue and the issue body.

**What good looks like.** Every element of the cause traces to something shown.
The strongest form names the mechanism and points at the artifact that shows
it: "three of the six patterns begin with a literal `\n`, and detection flips
when exactly that character is prepended" is carried by a contrast run. A cause
narrower than the evidence is fine and often better — the evidence may show
three symptoms while the plan localises and fixes one, provided it says that is
what it is doing. The failure shapes are a cause the evidence cannot reach (a
race condition asserted from a single log line), a cause that contradicts the
artifacts (blaming a parser where the trace shows a network timeout), and a
cause about a different behavior than the one reproduced. Watch for the layer
error too: the evidence localises a cause, and the plan patches the visible
effect one layer out without saying why it stopped there.

## Scope

**Where it lives.** The change statement of `## Candidate plan` and whatever it
says about boundaries — an `Out:` clause, a deferral to another issue, a
sentence limiting the change. Read against the `## Issue` body, which states
what was asked for, and `## Repo facts`, whose `bug reports:` and
`contribution policy` lines sometimes name what the project wants kept
separate. Live mode: the scope pair in `plan.md` against the issue body.

**What good looks like.** A reader knows where the change stops without reading
the diff. The boundary is stated in the plan's own words and is specific enough
to check: a named file that will not be touched, a neighbouring issue that owns
a surface, a capability deliberately left out. Count of files is not the
measure. Three files with "Out: any change to the detector itself, #24 owns
that" is bounded; one file with no statement of limit is not. The failure shape
is work that arrives without being asked for: a refactor folded into a fix, a
dependency bumped along the way, a second bug repaired because it was nearby,
a rename for consistency. Each of those may be defensible, and a plan that
says why it rides along passes; silence about it does not.

## Executability

**Where it lives.** The approach and first-step language of `## Candidate
plan`: the files, functions, config keys, or areas it names, and what it says
will happen in each. Read it as a stranger holding the plan, the issue, and the
repository — not as the author. Live mode: the same part of `plan.md`.

**What good looks like.** Someone who has never discussed this issue could open
the repo and start. The location is specific enough that one search finds it,
and the approach says what will be done there: "the push completion callback in
`pkg/gui/controllers/sync_controller.go` adds the commits context to its
post-push refresh scope" is executable. The failure shape is intent without a
target: "refactor the handler", "fix the parsing", "add proper validation",
"clean up the error path". Also a failure: a first step that waits on a decision
the plan never makes, and a location so broad the reader must choose the file.
Length is not the measure — one located sentence beats a page of approach.

## Verification

**Where it lives.** The test plan of `## Candidate plan`. The thing to read it
against is `## Repro evidence`: that block holds the steps that established the
behavior, and the test plan either re-runs them or routes their inputs through
the real path. The `## Issue` body's expected-behavior line is the second
reference point. `## Repo facts` may name a test gate the project requires.
Live mode: the test plan in `plan.md` against the posted repro comment's steps.

**What good looks like.** The plan names what will be observed after the change,
in terms a reader could check, and that observation would read differently
before and after. "At step 3 the colour must flip without leaving the view" is
checkable. So is a named test moving from `xfail` to `pass`, an exit status
changing, a message appearing. The evidence must pass through the real code
path: a check that exercises a copy of the logic proves nothing about the
module. Where the original repro cannot re-run against the change — it was a
standalone script, or the behavior it drove no longer exists in that shape —
the same inputs routed through the real path are the right substitute, and the
plan should say that is what it is doing. The failure shapes are "verify the
fix works", "confirm the suite is green", and any check whose result the change
would not move.

## Honesty

**Where it lives.** Wherever a claim and its backing should meet: the cause
line, the outcome language of the test plan, any risks or unknowns section, and
the assertions in `## Candidate plan comment`. This family is read by pairing —
every sentence that asserts something gets matched to the evidence that would
carry it. Live mode: the same pairing across `plan.md` and the draft comment.

**What good looks like.** Stated certainty tracks actual support. What the
artifacts show is asserted plainly; what the author infers is marked as
inference ("my reading is", "this suggests", "I have not traced"); what is
unknown is named rather than left out. The unknowns that matter are the ones
bearing on the approach: a question put to a maintainer and not yet answered, a
condition the repro could not reach, an assumed dependency behavior. A plan
that names its open questions is stronger than one that appears to have none.
The failure shape is confidence resting on nothing: "obviously", "this will
fix it", "guaranteed", a cause asserted without a trace, a result from one
environment generalised to a release never tested, or a risks section that
lists only risks the author already knows are not real.

## Comms

**Where it lives.** Two surfaces meet here. The words: `## Candidate plan
comment`, read against the plan it accompanies and against `## Thread
highlights`, where maintainers say what they want, ask questions, or rule
directions out. The rules: the `## Repo facts` line `contribution policy`,
which records what the repo states about contributions, branches, test gates,
and AI use. Live mode: the draft comment, the issue thread on GitHub, and the
repo's `CONTRIBUTING.md` in the root, `docs/`, or `.github/`, any AI-policy
file it links, and the issue and PR templates.

**What good looks like.** A maintainer reading the comment alone learns the
diagnosis and the intended change — not that a plan exists somewhere, and not
merely that the author intends to work on it. It says something only this issue
would draw, so substitution exposes boilerplate: if the comment would read the
same on another issue, it carries nothing. Where the thread has already
proposed a direction, the comment adopts it or declines it in words; silently
proceeding past a maintainer's stated preference is the failure this surface
exists to catch, and so is the piggyback — "same approach as above" is not a
plan. An open question the thread never answered may be decided in the comment,
provided the comment says which way it went and leaves it cheap to reverse.

For the rules surface, read the policy for what it actually requires and
separate requirements from terms. A stated branch-naming shape binds a plan
that names a branch. A stated check gate ("`make check && make test-unit`
before a PR") belongs in the test plan. A policy requiring AI disclosure is a
requirement, and work produced in this course is AI-assisted, so a comment that
names the tool and the extent passes while silence fails. Conditions such as
responsibility, understanding your own change, human review, or "fully
AI-generated contributions are not accepted" are terms to honour and carry no
disclosure ask on their own. Read the scope too: a requirement stated for pull
requests does not bind an issue comment, unless the plan says it will skip the
pull request. Where the policy says nothing, nothing is required.
