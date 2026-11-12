# claude-tools

Skills and agents for [Claude Code](https://claude.com/claude-code), packaged as a plugin marketplace.

## Install

```
/plugin marketplace add <github-user>/claude-tools
/plugin install crash-course@claude-tools
/plugin install code-cleaner@claude-tools
```

Install only the plugins you want. To get updates later, run `/plugin marketplace update claude-tools`.

## Plugins

### crash-course (skill)

Runs a hands-on, hints-based crash course for learning a new programming language, framework, library or developer tool. It researches the official docs, plans 5-10 lessons, sets up a course directory with the dev/test environment (including Docker setup scripts when services are needed), and coaches you through writing the lesson code yourself, with escalating hints when you're stuck.

Try:

```
I know Go and Python well. Give me a crash course in Rust.
```

Courses are created in `~/Workspace/crash-courses/<technology>/` by default; ask for another location if you prefer. Say "next lesson" or "let's continue the Rust course" to resume.

The `evals/` folder holds the test cases used to develop the skill.

### code-cleaner (agent)

A read-only reviewer that scans code and reports concrete readability, security, best-practice and performance issues, ordered by severity, with before/after code for each.

Tools: `Read`, `Grep`, `Glob` only. It never modifies files.

Try:

```
Use the code-cleaner agent to review src/
```

## License

[MIT](LICENSE)
