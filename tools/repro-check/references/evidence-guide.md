# Evidence guide: where proof lives in a reproduction package

The map the rubric reads with. For each family of proof a check names, this
file says where to find it and what good looks like when you do.

A package bundle has fixed sections, and they are the addresses used below:
the header lines (`source`, `captured`), `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Candidate claim comment`, and
`## Candidate repro report`. In live mode the addresses are the issue thread,
the repo's own documents, and the student's draft files. Two rules hold in
both modes: the issue defines what counts as the right behavior, and evidence
absent from the package is absent, however likely it seems.

## Environment

**Where it lives.** In a bundle: the opening lines of `## Candidate repro
report`, where an environment line usually leads. Read it against two other
places — the `## Issue` body, which states the version and platform the report
targets, and `## Thread highlights`, where maintainers often say what they
confirmed it on or could not. The `## Repo facts` line `bug reports:` names the
fields that repo asks reporters for, which is a good list of what this project
considers load-bearing. Live mode: the environment line in the draft file, the
issue body and thread on GitHub, and the repo's bug-report template under
`.github/ISSUE_TEMPLATE/`.

**What good looks like.** The product version is named exactly, not as "latest"
or "current". Every dimension the issue's behavior depends on is present: the
OS and its version where the issue is platform-specific, plus whatever
component the issue turns on, such as the driver, shell, terminal, backend,
build profile, or install method. Where the environment differs from what the
issue targets, the report names the difference itself rather than leaving a
reader to notice it: "reproduced on 1.3.1, the issue reports a debug build" is
a pass, and running an old version while writing "confirmed" is the failure
this family exists to catch.

## Steps

**Where it lives.** The steps or commands block inside `## Candidate repro
report`, together with any inputs, files, or config it shows. Check it against
the reproduction steps in the `## Issue` body: the issue usually names the
trigger, and the steps either reach it or stop short. Live mode: the same block
in the draft file, against the issue's own steps.

**What good looks like.** A stranger with the recorded environment could run
the sequence start to finish from the comment alone. The starting state is
reachable: inputs and config are shown inline, or public and named precisely
enough to fetch. Each step is an action with its target, so exact commands,
file contents, or a named UI action, never "set up the project as usual". The
last step reaches the condition the issue names. Length is not the measure:
four exact commands are followable and a page of prose about a private monorepo
is not.

## Behavior shown

**Where it lives.** The output excerpts, logs, screenshots, or pasted
tracebacks in `## Candidate repro report`, usually under or beside the steps,
along with the report's own Expected and Actual lines. The thing to compare
against is in `## Issue`: the error text, exit status, panic, or symptom the
reporter described, plus any correction in `## Thread highlights`. Live mode:
the artifacts in the draft, against the issue body on GitHub.

**What good looks like.** The artifact shows the issue's behavior on the
deciding particulars, not merely a failure from the same tool. Match the
message, the failure mode, and the input path: a graceful argument-validation
error is not a capacity-overflow panic, a compile error is not a runtime path
error, and a terminal printing garbled escape sequences is not a terminal that
crashed. An artifact showing only that the program started, printed its
version, or listed its sessions shows nothing about the bug. For a
cannot-reproduce, the artifacts still carry weight: they should show a real
attempt that reached the trigger conditions, so a reader can see what was run
and where it stopped. A contrast run, where the trigger is removed or varied
and the behavior changes with it, is the strongest form this family takes.

## Honesty

**Where it lives.** Wherever a claim and its backing should meet: the
conclusion lines of `## Candidate repro report` ("Actual:", "confirmed",
"the cause is"), the assertions in `## Candidate claim comment`, and the
artifacts above them. This family is read by pairing, so every sentence that
asserts something gets matched to the evidence that would carry it. Live mode:
the same pairing inside the drafts.

**What good looks like.** Each assertion is carried by something shown. A
reproduction is claimed only where an artifact shows it; a cause or mechanism
is named only where a trace, a diff, or a cited finding from the thread
supports it; and the stated scope stays inside what was actually run, with
untested parts of the issue named as untested. A report that says "I could not
reproduce this, here is what I ran and here is what differed" is a faithful
outcome and reads as a pass. The failure shape is confident language resting on
nothing or resting on the wrong artifact: "obviously the null result thing",
"I verified this race condition", "guaranteed reproducible", or a result from
one machine generalized to a release that was never tested.

## Comms

**Where it lives.** Two surfaces meet here. The words: `## Candidate claim
comment` read against the `## Issue` body and `## Thread highlights`. The
rules: the `## Repo facts` line `contribution policy`, which records what the
repo states about contributions and about AI use, including any separate AI
policy file it references. Live mode: the draft comments, and the repo's
`CONTRIBUTING.md` in the root or `.github/`, any `AI_POLICY.md` or
`AI_USAGE_POLICY.md` it links, and the issue templates.

**What good looks like.** A claim comment says something only this issue would
draw: the symptom seen, the version it was seen on, a file or function, a
pointer picked up from the thread, or the concrete next step. It promises
investigation and a report back, never a fix, a date, or a guarantee, and it
does not ask for the issue to be reserved. Boilerplate is recognizable by
substitution: if the comment would read the same pasted on any other issue, it
carries nothing.

For the rules surface, read the policy for what it actually requires, and
separate requirements from terms. A policy that requires AI usage to be
disclosed is a requirement, and a course package is AI-assisted work, so a
comment that discloses the tool and the extent of its help passes while silence
fails. A policy requiring comments to maintainers in the contributor's own
words is a requirement about voice, met by comments that read as written rather
than generated. Conditions such as responsibility, understanding your own
change, human review, testing, or "fully AI-generated contributions are not
accepted" are terms to honor and carry no disclosure ask on their own. Read the
scope too: a disclosure requirement stated for pull requests does not bind an
issue comment. Where the policy says nothing about AI, nothing is required.
