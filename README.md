# red-before-green

[![npm](https://img.shields.io/npm/v/red-before-green?color=0b7285&label=npm)](https://www.npmjs.com/package/red-before-green)
[![license](https://img.shields.io/badge/license-Apache--2.0-0b7285)](LICENSE)
[![grade](https://img.shields.io/badge/efaimo%20check--skill-A%20(100)-0b7285)](https://efaimo.ai/skills)
[![house-style](https://github.com/efaimo-ai/red-before-green/actions/workflows/house-style.yml/badge.svg)](https://github.com/efaimo-ai/red-before-green/actions/workflows/house-style.yml)

An Agent Skill for the moment a check comes back clean and you are about to
believe it.

A green test, an empty grep, a linter with zero problems, a CI gate that passed,
a subagent that reported "no issues". Each of these is either evidence or a
decoration, and from the outside they look identical. The skill is one move that
tells them apart: before you trust the green, make the check go red on purpose.

<!-- generated:install -->

## Install

```sh
npx red-before-green                 # into ./.claude/skills/red-before-green/
npx red-before-green --global        # into ~/.claude/skills/red-before-green/
npx red-before-green --check         # installed, and current?
```

The package is the skill: `SKILL.md` and its `references/`, nothing else. The
installer copies them, reads every byte back, and fails if what landed is not
what it wrote. It refuses to overwrite a directory whose contents differ unless
you pass `--force`, and installing the same version twice is a success rather
than a conflict.

Or take it by hand. It is markdown; `npx red-before-green --print` writes `SKILL.md` to
stdout, and the repository is the whole thing.

<!-- /generated:install -->

## Why a green result is ambiguous

```mermaid
flowchart LR
    A["a check ran, looked at<br/>everything, found nothing"] --> X{{"the output"}}
    B["a check never ran, or<br/>could not have failed"] --> X
    X --> Y["<b>0 problems</b>"]
    Y --> Z["make the instrument produce<br/>a positive on purpose"]
    Z --> V1["it goes red<br/><i>now the green means something</i>"]
    Z --> V2["it stays green<br/><i>it was never watching</i>"]
    classDef pass fill:#0b728522,stroke:#0b7285;
    classDef fail fill:#c9282822,stroke:#c92828;
    class V1 pass;
    class B,V2 fail;
```

The two paths on the left produce the same bytes. Nothing downstream can tell
them apart, which is why the only way to read a green is to have watched it be
red first.

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


<!-- generated:pipeline -->

## What installing it does to a session

A skill is not free just because it is markdown. Its frontmatter is loaded at
the start of every session for every skill you have installed, whether or not it
ever fires.

```mermaid
flowchart LR
    N["npx red-before-green"] --> D[/".claude/skills/red-before-green/"/]
    D --> M["frontmatter<br/><b>every session, always</b>"]
    D --> B["SKILL.md body<br/><i>only when it triggers</i>"]
    D --> R["references/<br/><i>only if the agent reads them</i>"]
    M --> S(["your context window"])
    B -.->|"on trigger"| S
    R -.->|"on demand"| S
    classDef always fill:#c9282822,stroke:#c92828,stroke-width:1px;
    classDef lazy fill:#0b728522,stroke:#0b7285,stroke-width:1px;
    class M always;
    class B,R lazy;
```

In this skill's case, measured by [efaimo](https://github.com/efaimo-ai/efaimo) `weigh` (v0.5.0, 2026-09-04):
**115 tokens always resident**, 984 when it triggers, 2,146 across 2 reference files if the agent reads to the end.

<!-- /generated:pipeline -->

## The set

Seven skills, each one a discipline that cost something to learn.

| skill | the question it asks |
|---|---|
| **`red-before-green`** (this one) | can this check fail at all? |
| [`denominator`](https://github.com/efaimo-ai/denominator) | how much of the world can it see? |
| [`read-back`](https://github.com/efaimo-ai/read-back) | did the write actually apply? |
| [`claim-sweep`](https://github.com/efaimo-ai/claim-sweep) | what else still asserts the old value? |
| [`unreleased-guard`](https://github.com/efaimo-ai/unreleased-guard) | does the copy describe what shipped? |
| [`honest-chart`](https://github.com/efaimo-ai/honest-chart) | is the picture proportional to the data? |
| [`mcp-stateless-migration`](https://github.com/efaimo-ai/mcp-stateless-migration) | does this server match the 2026-07-28 spec? |

All of them are audited by [`efaimo`](https://github.com/efaimo-ai/efaimo), the
CLI that measures the quality and context-window cost of MCP servers and Agent
Skills. The index of every public skill it can find, graded, is at
[efaimo.ai/skills](https://efaimo.ai/skills).

## License

Apache-2.0. See [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).
