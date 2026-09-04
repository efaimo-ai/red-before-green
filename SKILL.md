---
name: red-before-green
description: Use before trusting any check that came back clean - a grep or search with no matches, a test suite or linter reporting zero problems, a green CI gate, a build or type-check that passed, a query that returned an empty set, a validation that succeeded, or a subagent or tool that reported success. Applies whenever you are about to call a task done or report "no issues found". An empty result and a check that never ran look identical.
license: Apache-2.0
metadata:
  version: "0.1.0"
  homepage: "https://efaimo.ai"
  verified_against: "2026-09-04"
---

# red-before-green

A check that passes and a check that never ran produce the same output: silence.
"0 problems", "no matches", "all tests pass", and "the pattern was misspelled so
it matched nothing" are indistinguishable from the outside. A green result is
only evidence when it comes from an instrument you have just watched produce a
positive. So before you believe a green, make it go red.

This is not skepticism for its own sake. It is the difference between a check and
a decoration. The most expensive bugs are not the ones a check catches; they are
the ones a check was supposed to catch and silently did not, because the check
was pointed at the wrong file, ran against an empty set, had a typo in its
pattern, or was wired to pass no matter what.

## The move

1. **Name the positive.** In one sentence, state the failing case this check
   exists to catch. If you cannot, you do not yet know what its green means.
2. **Feed it that positive.** Sabotage the input, or point the check at a case
   that MUST fail, and run it.
3. **Watch it go red.** A check that stays green on a known-bad input is not
   measuring what you think. That is the finding, not the input.
4. **Restore, then trust the clean result.** The green means something now,
   because you have seen the red.

If step 2 is impossible - you cannot construct any input that makes the check
fail - stop. The check is not measuring anything, and neither you nor anyone
reading its green knows what it verifies.

## Why this matters more now

Agents generate and run enormous amounts of verification - tests, linters,
type-checks, greps, CI gates - and hand whole investigations to subagents that
report back "done" or "clean". The more checking is automated and the less of it
anyone watches, the more the dominant failure becomes the vacuous pass: the check
that reported clean because it examined nothing. Volume of green is not evidence.
One watched red is.

## The tells

A clean result is probably a non-result if:

- a search returned nothing on the first try, before you confirmed the pattern
  matches anything at all;
- a file is unchanged after a command that was supposed to edit it, and nothing
  errored;
- a checker finished suspiciously fast, or reported on zero items;
- a test passes and you have never once seen it fail;
- a subagent reports "no issues" with no example, no line number, nothing you can
  open;
- a diff is empty right after a change you know you made.

## What to feed each instrument

Full recipes, with the traps that disguise a non-result, are in
[references/recipes.md](references/recipes.md).

| the clean result | the positive to feed it first |
|---|---|
| search / grep: no matches | the pattern against a line you KNOW contains it |
| test suite: all pass | one assertion negated, or one mutation to the code |
| linter / type-check: 0 problems | one deliberate violation |
| CI gate: green | one rule broken in a scratch commit |
| schema / contract: valid | a payload that must be rejected |
| subagent / tool: success | one load-bearing claim, spot-checked yourself |
| query / API: empty set | a query you know returns rows, same connection |
| monitor / alert: quiet | a synthetic event that must trigger it |

For the shapes these non-results take in the wild - the pipe that ate the exit
code, the regex that lost a backslash in transit, the mock that passed without
exercising anything - see [references/failure-gallery.md](references/failure-gallery.md).

## What this is not

There is nothing to install; this is a discipline, not a tool. It does not tell
you your work is correct. It tells you whether the thing that just told you your
work is correct can be believed. That is a smaller claim, and it is the one that
was missing.

<!-- generated:siblings -->

## Siblings

Every skill in this set is about a report that was true about the wrong thing. The set: https://efaimo.ai/skills

- `denominator` - once the check can fail, ask how much of the world it actually looked at.
- `read-back` - the same move aimed at writes rather than checks: a success that never applied.

<!-- /generated:siblings -->
