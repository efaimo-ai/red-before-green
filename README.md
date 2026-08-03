# red-before-green

An Agent Skill for the moment a check comes back clean and you are about to
believe it.

A green test, an empty grep, a linter with zero problems, a CI gate that passed,
a subagent that reported "no issues". Each of these is either evidence or a
decoration, and from the outside they look identical. The skill is one move that
tells them apart: before you trust the green, make the check go red on purpose.

## The problem

A check that passes and a check that never ran produce the same output: silence.
"0 problems" and "the pattern was misspelled so it matched nothing" are the same
bytes. "All tests pass" and "the test file was never collected" print nearly the
same summary. The most expensive failures are not the ones a check catches - they
are the ones a check was supposed to catch and silently did not, because it was
aimed at the wrong file, ran against an empty set, or was wired to pass no matter
what.

You cannot tell a working check from a broken one by looking at its green. You
can only tell by watching it go red on something you know is bad.

## Install

Claude Code and other agents that read `SKILL.md` from a skills directory:

```bash
git clone --depth 1 https://github.com/efaimo-ai/red-before-green \
  ~/.claude/skills/red-before-green
```

Or vendor the directory anywhere your agent loads skills from. There is nothing
to build and no dependencies.

## What it contains

| file | what it is |
|---|---|
| `SKILL.md` | the move, the tells, and one positive to feed each kind of instrument |
| `references/recipes.md` | per-instrument recipes, each with the trap that hides a non-result |
| `references/failure-gallery.md` | the shapes a vacuous pass takes in the wild |

`SKILL.md` is small on purpose: it is what an agent loads at trigger time. The
references load only when a specific instrument needs them.

## When it fires

A search returns no matches. A test suite, linter, or type-checker reports zero
problems. A CI gate is green. A query comes back with an empty set. A validation
passes. A subagent or tool reports success. In each case, before you conclude the
task is done or report "no issues found", you make the check produce a positive
first.

## The part worth reading even if you never install it

An instrument's silence is not the absence of a problem. It is the absence of a
measurement, and those are only the same thing when the instrument works. The
single most reliable way to earn a green is to have just seen the same instrument
produce a red. A check you have never watched fail is not a check; it is a
comment that happens to be executable.

This generalizes one line that turns up everywhere in careful work - "a guard you
have never seen fail is not a guard" - into a standing discipline that applies to
every clean result, not just the ones you already suspected.

## Scope

This is a procedure, not a linter. There is nothing to run.

It does not tell you your work is correct. It tells you whether the check that
just told you your work is correct can be believed - which is a different, smaller,
and usually skipped question.

## Related

[`claim-sweep`](https://github.com/efaimo-ai/claim-sweep) applies one corner of
this to the case of a fact that changed and left stale copies behind.
[`efaimo`](https://github.com/efaimo-ai/efaimo) audits the quality and context
cost of MCP servers and Agent Skills, including this one.

## License

Apache-2.0. See [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).
