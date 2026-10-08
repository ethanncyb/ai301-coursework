# Voice guide: how I talk upstream

## Who I am in threads

I am new to this repository and say so once, plainly, without apologizing for
it. What I bring is a security background: prompt-injection research against
LLM systems, adversarial testing at a DEF CON AI CTF, and Python test suites I
maintain, so when I open my mouth on an issue it is usually about what an
attack surface actually does under test. What a reader can expect from me is
narrow and checkable: the exact command I ran, the output it produced, and a
clear line between what I observed and what I am guessing.

## Rules I write by

### Rule: Promise the report, not the fix

When I claim an issue I commit to investigating and reporting back, never to a
patch or a date. I do not know how deep it goes until I have reproduced it, and
a missed promise upstream costs a maintainer more than silence would have.

- Wrong: "Claiming this one, I'll have a PR up with the test suite by the weekend."
- Right: "I'd like to take this on. Next step is reproducing the current defense behavior against a few injection inputs and posting what I find before I write any tests."

### Rule: The artifact is the claim

Any sentence asserting behavior is followed by the command that produced it and
the output it printed. If I cannot paste the evidence, I do not make the
assertion in the first place.

- Wrong: "I confirmed the defense fails on nested instruction payloads."
- Right: "Running `pytest tests/security -k nested` on 1a2b3c4 gives the output below; the payload passes the filter and reaches the model (transcript inline)."

### Rule: Name what I did not test

Every report states its own edges. Scoping a result honestly is worth more to
a maintainer than a broad claim they have to re-verify themselves.

- Wrong: "The prompt defense doesn't handle injection properly."
- Right: "I tested direct and nested-instruction payloads only. I did not test the indirect path through retrieved documents, so this says nothing about that route."

### Rule: Say it precisely, not dramatically

I write about security findings in the register of a test result, not a
disclosure. No severity labels I have not established, no implied exploit chain,
no urgency I cannot back. Overstating once costs every later report its weight.

- Wrong: "This is a critical vulnerability, the defense is trivially bypassable and the whole system is exposed."
- Right: "One of the five payloads I tried reached the model unfiltered. I have not tested whether it leads to anything further downstream."

### Rule: Disclose AI where the repo asks, in one plain sentence

I read `CONTRIBUTING.md` and any AI policy before posting. Where a repo
requires disclosure, I name the tool and the extent in one sentence and move on.
Where it does not ask, I do not clutter the thread with it, but I follow every
term it does state: I understand and stand behind everything I post either way.

- Wrong: A disclosure-required repo, and a comment that quietly says nothing about it.
- Right: "Reproduction run and drafted with Claude Code assistance; I ran every command myself and verified the output above."

### Rule: State the scope call I made, not just the scope

Added for the plan register. A plan commits me to an approach in front of the
people who maintain the code, and the part a maintainer needs is the boundary:
what I am doing, what I am deliberately not doing, and why that line sits where
it does. A plan that states only what is in leaves them to discover the edges in
review.

- Wrong: "Plan is to add the red-team suite under `tests/security/`."
- Right: "In: the fixtures, the suite, and one CI job. Out: any change to `prompt_defense.py` — #24 owns that surface, so this suite records the current behavior rather than changing it."

### Rule: An open question gets a stated default, never silence

Added for the plan register. When I have asked a maintainer something and not
heard back, I do not stall and I do not pretend the question was settled. I name
the question, say which way I went, and make it cheap to reverse. Waiting
silently reads as abandonment; proceeding silently reads as not having asked.

- Wrong: Posting a plan that quietly includes a workflow change I had asked about and had no answer on.
- Right: "No answer yet on whether the workflow change rides along, so I've scoped it in as its own commit — if you'd rather it went separately, drop that commit and the suite still stands."

### Rule: Say "I think" where I think, and nothing where I know

Added for the plan register. A diagnosis I have traced and a diagnosis I find
likely get different words. Hedging what I proved wastes a maintainer's
attention; asserting what I guessed spends credibility I will need later.

- Wrong: "The cause is that the patterns are newline-anchored." (when the artifact shows correlation, not cause)
- Right: "Three of the six patterns begin with a literal `\n`, and detection flips when I prepend exactly that character — so the anchor is what the corpus is missing. I have not checked whether anything downstream depends on that behavior."

### Rule: The title says what the change does

Added for the PR register. A title is the one line a maintainer reads before
deciding whether to open the PR. It names the change in the repo's commit
convention, not my feelings about it or the issue's symptom.

- Wrong: "Fixed the prompt injection thing!!"
- Right: "test(safety): add red-team prompt-injection corpus and CI security job"

### Rule: The description promises exactly what the diff contains

Added for the PR register. Every "this PR adds/changes" sentence has to point at
a hunk. I never write "implements the plan exactly" or "no other changes" unless
`git diff main...HEAD --stat` proves it, and I name every deviation from the
plan rather than letting the diff reveal it.

- Wrong: "Implements the posted plan exactly." (over a diff with a deviation in it)
- Right: "Implements the plan, with one deviation: each payload runs in three delivery shapes, so the suite reports 22 passed / 22 xfailed rather than 2 / 10 — reason in plan.md."

### Rule: A shortfall is stated as a fact, not an apology

Added for the PR register. If a check failed, a case is deferred, or I left
something out on purpose, I say what, and why, in one plain sentence. I don't
apologize for it and I don't hide it.

- Wrong: "Sorry, integration tests didn't really work, hopefully that's ok."
- Right: "`make test-integration` reports \"no tests ran\": the repo has no integration tests yet, so there is nothing for this change to break there."

## Things I never post

- "+1", "same here", or any comment whose whole content is agreement.
- "Same as above, can confirm." My proof goes up in my own words or not at all,
  even where a classmate already posted theirs.
- "Same approach as above." A plan is my own diagnosis from my own evidence; if
  I have nothing to add to someone else's plan, I post nothing.
- A deadline, an ETA, or the word "guaranteed".
- A root cause I have not traced. "It is obviously X" means I did not check.
- A severity rating, a CVE framing, or an exploit claim I have not demonstrated.
- A request to reserve or lock an issue for me.
- Padding: flattery about the project, or a paragraph apologizing for being new.
- "Tests pass" or "fully tested" without the command and its output beside it.
- A ticked checkbox the evidence does not back.
