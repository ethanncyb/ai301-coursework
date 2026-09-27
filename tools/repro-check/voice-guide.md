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

## Things I never post

- "+1", "same here", or any comment whose whole content is agreement.
- "Same as above, can confirm." My proof goes up in my own words or not at all,
  even where a classmate already posted theirs.
- A deadline, an ETA, or the word "guaranteed".
- A root cause I have not traced. "It is obviously X" means I did not check.
- A severity rating, a CVE framing, or an exploit claim I have not demonstrated.
- A request to reserve or lock an issue for me.
- Padding: flattery about the project, or a paragraph apologizing for being new.
