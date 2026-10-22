# CLAUDE.md

Global rules for every project. Conflicts are resolved by the Precedence section below.

Core idea: agents take the shortest path. Make the shortest path the correct one, and prove work with evidence, not claims.

## Task size

Classify every task first.

- **Small** (questions, one-off scripts, notebooks, single-file edits, renames, config tweaks): just do it and run the affected code once. No plan, no setup questions.
- **Large** (multi-file features, refactors, cross-module bugs, long-lived projects): apply every section.

When unsure, treat it as small and say so in one line. §2 applies at every size.

Topic rules in `~/.claude/rules/` load with this file; each states when it applies.

## Precedence

1. The user's explicit instruction in this conversation.
2. Project-level CLAUDE.md.
3. §2 (No Shortcuts) and §7 (Evidence) of this file. Skills and plugins never override these.
4. Skills and plugins: follow them for domain how-to, but adapt their templates to the project's existing structure (§4).
5. The rest of this file and `rules/`.

When a rule says "ask first" and no user is available (loop, schedule, unattended run), treat the action as blocked: stop and report.

## 0. Language

- Reply in Vietnamese. Code, identifiers, and commit messages in English.
- Docs, READMEs, reports, notebook text: the language the user asks for (default English).

## 1. Understand Before Coding

- Read the relevant code before claiming a cause, and cite file:line. Not read yet → say "unverified".
- Investigate first (read, run, check tests). Ask only when the repo can't answer and a wrong guess is costly: ambiguous requirements, irreversible actions, public API changes.
- State assumptions. List interpretations instead of picking one silently. Say so if a simpler approach exists.

## 2. No Shortcuts

- Never hardcode values or expected outputs, or special-case test inputs, to make a test or demo pass.
- Never weaken, skip, or delete tests, assertions, or lint rules. Never silence failures with `# noqa`, `# type: ignore`, `pytest.skip`, or broad `except`. Exception: a suppression scoped to the exact rule, with a reason on the same line, for a genuine external cause.
- Constants live once in a config/constants module, not inline.
- Never edit guardrail config (lint, import contracts, CI, pre-commit) unless asked.
- Never call paid APIs or start jobs longer than ~5 minutes without asking first.
- Can't fix it properly → stop and report. A reported blocker beats a disguised workaround.

## 3. Simplicity

- Minimum code for the request. No speculative features, abstractions, or config.
- Handle errors only at real boundaries: I/O, network, user input.
- Simple never means shortcut: §2 wins.

## 4. Follow the Architecture

- Find how the codebase already solves this kind of problem and follow it. One way per thing: reuse existing helpers, clients, config, logging.
- Respect layer boundaries and dependency direction. No new top-level folders, layers, or "utils" dumps without asking.
- Structure doesn't fit, or several conflicting patterns exist → propose the change or ask which pattern is canonical before writing code.

## 5. Surgical Changes

- Touch only what the task needs. Match existing style. Don't refactor or reformat neighbors.
- Mention unrelated dead code; don't remove it. Remove only what your own change orphaned.
- Every changed line must trace to the request.

## 6. Comments

- Default: none. Allowed: a short "why" for non-obvious decisions.
- Never: narrating code, change history, or quoting the conversation as if it were a global rule.
- Coursework, tutorials, learning notebooks: docstrings for every function and comments explaining each non-trivial step.

## 7. Verify With Evidence

- Bug → failing test first, then fix, then pass. Feature → tests for valid and invalid input. Refactor → tests pass before and after.
- Run it; never claim success from reading code. Before finishing, run the project's full check (`make check` or equivalent).
- Report the command and its output (fail → pass). State anything left unverified.
- Large tasks: give a short plan (step → check). Show the user the new tests before implementing them. When done, have a fresh subagent review only the original request and the diff, not your reasoning, and report its findings.

## 8. Feedback Becomes Guardrails

- A mistake or review comment that repeats → propose a lint rule, import contract, type constraint, or test so the build fails next time. Don't just add more text here.

---

**Working if:** small diffs, no hardcoded hacks, new code follows existing patterns, every "done" has evidence, and questions come before mistakes rather than after.
