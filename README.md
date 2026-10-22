# claude-code-rules

Personal global rules for [Claude Code](https://claude.com/claude-code) that stop two common failure modes of coding agents:

- **Shortcuts**: hardcoding values, special-casing test inputs, or weakening tests just to get green.
- **Architectural drift**: on long projects, each task invents a new pattern until the codebase becomes chaos.

## Philosophy

Agents always take the shortest path. Instead of asking them not to, **make the shortest path the correct one**, and require **evidence, not claims**.

Guardrails come in layers, from soft to hard:

| Layer | Example | Reliability |
|---|---|---|
| Soft | `CLAUDE.md`, rules, skills | The agent can forget or skip them |
| Hard | Lint rules, type checks, import contracts, tests, CI | The build fails; the agent can't argue |
| Hardest | An architecture where the right way is the only way | Wrong code is impossible to write |

These files are the soft layer. Their main job is to push work toward the hard layers: verify with real checks, never loosen guardrails, and turn repeated feedback into lint rules or tests.

## Structure

Mirrors `~/.claude/`:

```
claude-code-rules/
├── CLAUDE.md                 # Core rules, loaded in every session
└── rules/
    ├── llm-code.md           # LLM / agent / ML code: cost control, mocking, property-based tests
    └── python-guardrails.md  # Sets up ruff, pytest, mypy, import-linter for long-lived Python projects
```

| Section | Purpose |
|---|---|
| Task size | Small tasks stay lightweight; full process only for large ones |
| Precedence | User > project file > §2 and §7 > skills/plugins > other rules; "ask first" with no user means blocked |
| §1 Understand | Read code before claiming a cause; investigate before asking |
| §2 No shortcuts | No hardcoding, no silenced failures, no edits to guardrail config |
| §3 Simplicity | Minimum code, but simple never means shortcut |
| §4 Architecture | Follow the existing pattern; one way per thing |
| §5 Surgical changes | Every changed line traces to the request |
| §6 Comments | "Why" only; no history or conversation quotes |
| §7 Evidence | Fail → pass proof, tests shown first, independent subagent review |
| §8 Guardrails | Repeated feedback becomes a lint rule or test |

## Install

```bash
git clone https://github.com/phido0410/claude-code-rules.git
cd claude-code-rules

# Back up your current global file, if any
[ -f ~/.claude/CLAUDE.md ] && cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.bak-$(date +%Y%m%d)

mkdir -p ~/.claude/rules
cp CLAUDE.md ~/.claude/CLAUDE.md
cp rules/*.md ~/.claude/rules/
```

Prefer symlinks if you want `git pull` to update your rules:

```bash
ln -sf "$(pwd)/CLAUDE.md" ~/.claude/CLAUDE.md
for f in rules/*.md; do ln -sf "$(pwd)/$f" ~/.claude/rules/; done
```

Restart Claude Code, then run `/memory` to confirm all three files are loaded.

## Customize

- **Language**: §0 makes Claude reply in Vietnamese. Change it to your language.
- **Not using Python**: delete `rules/python-guardrails.md`.
- **Project-specific rules**: put them in the project's own `CLAUDE.md`; it overrides these where they conflict.

## Credits

- Base guidelines (think before coding, simplicity, surgical changes, goal-driven execution) adapted from [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills), inspired by Andrej Karpathy's observations on LLM coding mistakes.
- Guardrail philosophy (shortest path = correct path, soft vs hard layers, verification with evidence, review comments → lint rules) inspired by Lauren Tan ([@poteto](https://github.com/poteto)) and her [pstack](https://github.com/cursor/plugins/tree/main/pstack).

## License

MIT
