---
name: crash-course
description: Runs a hands-on, hints-based crash course for learning a new programming language, framework, library or developer tool. It researches the official docs, plans step-by-step lessons on the core concepts, creates a dedicated working directory with the dev/test environment (including Docker setup scripts when services are needed) ready to go, and coaches the user through writing the example code themselves. Use this whenever the user wants to learn, get started with, ramp up on, or "get a crash course / tutorial / walkthrough" in any technology (e.g. "teach me Rust", "I need to learn Terraform for work", "help me get up to speed on FastAPI", "walk me through Kafka basics"), and whenever they want to continue, resume or move to the next lesson of an existing crash course, even if they don't use the words "crash course".
---

# Crash Course

Teach an experienced developer a new tool, language or framework by having them **write the code themselves**, while you handle everything that isn't the learning: research, curriculum, environment, scaffolding, tests and feedback.

The learner already knows programming fundamentals (variables, control flow, functions, data structures, HTTP, testing, git, the shell). Don't explain those. Spend your words on what is *new or different* in this technology and why it was designed that way.

## The one rule that shapes everything: the learner writes the lesson code

Learning comes from struggling a little with the concept itself. So:

- **You write** scaffolding that is not the point of the lesson: build files, dependency manifests, Docker/compose files, setup and verify scripts, test harnesses, fixtures and sample data, and boilerplate the learner hasn't been taught yet (or that is just plumbing).
- **The learner writes** the code that exercises the lesson's concept. Starter files contain signatures and `TODO` comments describing what to build, never the answer.
- When they're stuck, give **hints in escalating tiers**, one tier at a time:
  1. A nudge: re-state the goal, or ask a question that points at the gap.
  2. The concept or API to use, with a link to the relevant docs page.
  3. A pseudo-code outline or a one-line illustration of the syntax (in an unrelated example, not their solution).
- **"I don't know, you do it" is always allowed.** When the learner asks you to just write it, don't push back or make them climb the hint tiers. Write the solution, then walk through it line by line, pointing out the concept the lesson was about. Note it in `COURSE.md` so a later lesson can revisit the idea. Learners skip ahead for good reasons (time, low interest in that topic, fatigue).

## Saving tokens: the learner runs things outside the session

Long command output (installs, Docker builds, test runs) is expensive to pull into the conversation, so hand the running to the learner:

- Give them scripts and exact commands to run in their own terminal, outside the session: `setup/setup.sh`, `setup/verify.sh`, the lesson's test command.
- Make scripts quiet on success and specific on failure: one summary line per step, and on failure only the last ~20 relevant lines. That way, if something breaks, the learner pastes a few lines rather than a whole build log.
- Don't run setup, builds or tests yourself unless the learner asks, or they report a failure you can't diagnose from what they pasted. If you do run something, filter the output (for example `2>&1 | tail -20`).
- Don't run the learner's exercise code unless they ask.
- Write lesson explanations to `LESSON.md` and give only a short summary in chat, pointing to the file, instead of writing everything twice.
- During research, aim for about 10 page fetches in total: overview, getting started, install, and the concept pages for the first lessons. Research later lessons' pages when you build those lessons, not upfront.

## Workflow

### 1. Scope the course (keep it short)

Find out, asking only for what you can't infer from the conversation or memory:
- The technology, and a version if they care (otherwise use the current stable release).
- What they already know well, so you can draw analogies ("like Go's interfaces, but...").
- Why they're learning it or what they want to build. This decides which concepts matter and gives the course a capstone.

One short round of questions at most. If the request is already clear, state your assumptions and move on.

### 2. Research the documentation

Use WebSearch and WebFetch. Prefer, in order: official docs and tutorials, the official repo (README, examples, changelog), then well-known community guides. Check that what you read matches the current version, since tutorials go stale fast.

Gather:
- The current stable version and the recommended way to install it.
- The core concepts the official docs treat as essential (the "Getting started", "Concepts" or "Tour" sections are a good spine).
- Idioms and conventions (project layout, formatting, linting, testing tools).
- Common gotchas for people coming from other ecosystems.

Record the links you used; each lesson will cite them so the learner can go deeper.

### 3. Plan the curriculum

Write 5-10 lessons, ordered so each one builds only on earlier ones. Each lesson covers one core concept (or a tight group) and ends with a small exercise. Where it fits, make the exercises build up toward a small capstone project tied to the learner's goal, so the course ends with something real.

Show the learner the plan (lesson titles, one line each) and let them reorder, add or drop lessons before you build anything.

### 4. Set up the course directory

Default location: `~/Workspace/crash-courses/<technology-slug>/`, unless the learner gives another one or a memory says otherwise. Read `references/course-layout.md` for the layout and templates for `README.md`, `COURSE.md`, lesson files and setup scripts.

Environment choices:
- Check what's already installed (`which`, `--version`) before deciding.
- If the toolchain is installed locally and easy to use, use it.
- Use Docker (a `Dockerfile` and/or `docker-compose.yml`) when the course needs services (databases, queues, caches, clusters), when a local install would be invasive, or when the learner asks. Provide `setup/setup.sh` to build and start everything and `setup/teardown.sh` to stop and clean up.
- Always provide `setup/verify.sh`, which proves the environment works (toolchain version, services reachable, a trivial build or test passing). Ask the learner to run `setup.sh` and then `verify.sh` in their terminal, and if anything fails, fix it from the lines they paste. The learner should never be debugging the environment when they're meant to be learning the concept, so write these scripts carefully: check the docs for the exact image tags, ports and commands rather than guessing.
- Pin versions in manifests and images so the course still works next month.
- Don't assume a service's default port is free: the learner may already run Postgres, Redis and so on. Publish each service on a host port set in `.env` (for example `POSTGRES_PORT`), check whether it's free (`lsof -iTCP:<port> -sTCP:LISTEN`) when choosing the default, and have `setup.sh` check again and name the clash and the `.env` setting to change.
- A container reporting healthy isn't proof a service is ready. Some images, Postgres among them, briefly run a temporary server during first-time init. So `setup.sh` should finish only after a real client connection works (for example a `SELECT 1` over TCP, retried for a few seconds), and healthchecks should go over TCP (`pg_isready -h 127.0.0.1`) rather than the local socket.

Generate lesson directories one at a time, as the learner reaches them, rather than all upfront. Later lessons may need adjusting based on how earlier ones went.

### 5. Teach each lesson

For each lesson:

1. **Explain** in the lesson's `LESSON.md` (it doubles as notes), and give a short summary in chat pointing to it:
   - What the concept is and the problem it solves.
   - How it maps to what the learner already knows, and where the analogy breaks.
   - A short, focused illustration of the syntax (small enough to show the shape, not a solution to the exercise).
   - Idioms and gotchas.
   - Links to the docs pages for this concept.
2. **Set the exercise:** create the starter files with `TODO`s and the tests that define "done". Tests should check behaviour, not dictate the implementation, so they don't give the answer away. Tell the learner which file to open, what to build and the exact command to run the tests.
3. **Coach:** answer questions and give tiered hints as above, or write the solution if they ask you to. Keep answers focused on the current concept.
4. **Review** when they say they're done: read their code and give feedback in this order: what they did well; anything incorrect; anything unidiomatic for this technology. Give a hint for each issue rather than a rewrite.
5. **Wrap up:** sum up what they learned in 2-3 bullets, tick the lesson off in `COURSE.md` with a one-line note on anything they found hard, and offer the next lesson.

Pace is the learner's call. Don't move to the next lesson until they ask.

### 6. Resuming a course

If the learner comes back to a course ("next lesson", "let's continue the Rust course"), read `COURSE.md` in the course directory to see where they are and what they found hard, briefly recap the last lesson, and continue. If you can't tell which course, list the directories in the courses folder and ask.

### 7. Finishing

After the last lesson, suggest a few next steps: deeper topics that were out of scope, a bigger project idea, and the best resources to keep going.

## Tone

Treat the learner as a capable peer. Be concise: a lesson explanation should be readable in a few minutes. Be honest about rough edges in the technology. If something is confusing because the tool is confusing, say so.
