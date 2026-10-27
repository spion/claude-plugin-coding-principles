# coding-principles

A Claude Code plugin that injects a set of coding principles into every session and subagent.

## Goals

The plugin steers Claude towards code that is:

- **Readable.** A reader can tell what each value holds and why each operation exists without tracing the code.
- **Compact.** Lines are spent, not produced. Established libraries are preferred over hand-written code.
- **Economic.** Every line, abstraction and test has to justify its cost. YAGNI is the default.
- **Strictly composed.** The system is designed top down from units with stable APIs. Concerns stay separated.
- **Rigorous on quality.** Types are strict. Business rules are covered by property tests. Tests target unit APIs, not internals.

## Approach

The instructions live in [`instructions.md`](instructions.md).
They cover these areas:

| Section | Outline |
|---|---|
| Coding Philosophy | Strong typing, economy of code, abstractions that earn their weight, top-down design. |
| Readability | Effort scales with complexity. Declarative structure in the order of the idea. Clear domain names. |
| Error Handling | Language conventions, or EffectTS conventions in Effect projects. |
| Testing | A unit is a stable API worth a README. Property tests for business rules. Integration tests where units don't fit. |
| Project Structure | Existing patterns first, then domain-driven organization, then language idioms. |
| Communication Style | References, direct challenges to the request, one thought per sentence. |
| Flow of work | Decompose into units, write a README, state the business rules, write tests, implement, review for readability. |

The principles are general on purpose.
They describe what good code looks like rather than listing specific mistakes to avoid.

### How it is loaded

Two hooks in [`hooks/hooks.json`](hooks/hooks.json) inject `instructions.md` as context:

- `SessionStart` adds it to every main session.
- `SubagentStart` adds it to every subagent.

Hooks are used instead of a skill because skills load only on demand.
These principles should apply to all code Claude writes, not only when a skill gets triggered.
A `CLAUDE.md` inside a plugin is not loaded, so hooks are the plugin's way to provide always-on context.

Hook output is capped at 10,000 characters, so `instructions.md` must stay below that.

## Installation

```sh
claude plugin marketplace add spion/claude-plugin-coding-principles
claude plugin install coding-principles@coding-principles
```

Inside a session, the same works with `/plugin marketplace add spion/claude-plugin-coding-principles` and `/plugin install coding-principles@coding-principles`.

To update to the latest version:

```sh
claude plugin marketplace update coding-principles
claude plugin update coding-principles@coding-principles
```

For a local checkout, pass its path to `marketplace add` instead.
To try it in a single session without installing, use `claude --plugin-dir /path/to/claude-plugin-coding-principles`.

The hooks need `cat` and `jq` on the `PATH`.

## Configuration

There is none.
Enable or disable the plugin to turn the principles on or off.

## Current caveats

- It has only been tested with Opus 5.5.
- It still sometimes misjudges when a declarative functional style pays off.
  It errs towards slightly unreadable loops rather than functional code with a performance overhead.
- It is not very good at finding business rules for property tests.

## Future work

The plugin does not include workflow skills yet.
The flow of work is currently described in the instructions only.
Skills that drive individual steps, such as writing a unit README or stating business rules for property tests, may be added later.
