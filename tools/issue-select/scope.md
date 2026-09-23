# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1` <!-- paste your section's repo from the Unit 1 Check-In page -->

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

Python is my main language, and most of what I have shipped is Python services
and research code: FastAPI backends with Docker and nginx, and a
prompt-injection research codebase built on PyTorch and transformers where I
added multi-config run support, offline plotting and tokenizer fixes, and
maintained its pytest suite. My background is cybersecurity: I competed in the
DEF CON Robotic Hacking Community AI CTF, where I wrote my own prompt-injection
harness against a robot's LLM assistant and a rosbridge sniffer that pulled an
unlisted ROS 2 topic, and I work against indirect-prompt-injection benchmarks
(BIPIA, AgentDojo, InjecAgent) in my current research. I also write TypeScript
and Next.js for real sites, but that is not where I want to grow.

What I want to get better at is adversarial AI — attacking and hardening LLM
systems systematically, with attack taxonomies and coverage I can defend rather
than one-off probes — and production Python backends: the service, data and
migration layer around a model rather than the notebook in front of it. I write
pytest and have less practice wiring suites into CI so they gate pull requests,
which is a gap I would rather close than route around.
