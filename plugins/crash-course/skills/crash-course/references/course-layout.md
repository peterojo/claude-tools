# Course directory layout and templates

## Layout

```
<technology-slug>/
├── README.md            # how to set up, run tests, and where things are
├── COURSE.md            # syllabus, progress, sources
├── setup/
│   ├── setup.sh         # install deps / build images / start services
│   ├── verify.sh        # proves the environment works
│   └── teardown.sh      # only when Docker or services are used
├── docker-compose.yml   # only when services are needed
├── Dockerfile           # only when the toolchain runs in a container
└── lessons/
    ├── 01-<topic>/
    │   ├── LESSON.md    # explanation notes for this lesson
    │   ├── <starter files with TODOs>
    │   └── <tests>
    └── 02-<topic>/
```

Adapt to the ecosystem's conventions where they're strong. For example, a Go course might use one module at the root with a package per lesson, and a Rust course might use a Cargo workspace with a crate per lesson. Follow whatever layout the technology's own tooling expects, so the learner learns the real conventions too.

If a later lesson builds on code from an earlier one (for a capstone), say so in its `LESSON.md` and either import the earlier code or copy the learner's version forward. Never replace their code with yours.

## COURSE.md template

```markdown
# <Technology> crash course

**Version:** <version>  ·  **Started:** <date>
**Background:** <what the learner already knows>
**Goal:** <why they're learning it / capstone>

## Lessons
- [ ] 01 <topic>: <one-line summary>
- [ ] 02 <topic>: <one-line summary>
...

## Progress notes
<!-- One line per finished lesson: date, anything they found hard -->

## Sources
- <official docs link>
- ...
```

## README.md template

```markdown
# <Technology> crash course

## Setup
./setup/setup.sh     # one-off
./setup/verify.sh    # checks everything works

## Working on a lesson
Open `lessons/NN-topic/LESSON.md`, then complete the TODOs in <file>.
Run the tests with: <exact command>

## Cleanup
./setup/teardown.sh   # (if Docker is used)
```

## Setup scripts

- Start with `#!/usr/bin/env bash` and `set -euo pipefail`, and `chmod +x` them.
- Make them idempotent: running `setup.sh` twice should be safe.
- Keep output short, because the learner may paste it into the chat: one line per step (`✓ postgres healthy`), noisy commands redirected to a log file under `setup/.logs/`, and on failure the last ~20 lines of that log plus a clear failure line.
- `verify.sh` should check each piece separately and say which one failed (for example "postgres not reachable on localhost:5432"), not just exit non-zero.
- For Docker: use pinned image tags, publish ports from `.env` variables (`"${POSTGRES_PORT}:5432"`), add healthchecks that connect over TCP, and use `docker compose up -d --wait`. Then, before reporting success, retry a real client connection for a few seconds. If the port is taken, fail with a clear message, such as: `port 5432 already in use; set POSTGRES_PORT in .env`.
- Keep secrets out of the scripts. For local-only services, use obvious throwaway credentials in a `.env` file, and say they're for local learning only.

## Starter file conventions

- Give the function or type signatures and leave the bodies as `TODO` comments that describe the behaviour wanted, in plain language.
- The file must compile/parse as given (stub returns are fine), so the learner sees failing tests rather than build errors on the first run, unless the lesson is specifically about fixing build errors.
- Tests should have descriptive names, since a failing test name is the learner's first hint.
