# Recipes: how to make each instrument go red first

One rule underneath all of these: run the check against an input whose answer you
already know, and confirm the check gives that answer. If it does not, the check
is broken or misaimed, and its green tells you nothing about your real input.

Each recipe names the positive to feed and the trap that most often hides a
non-result as a pass.

## Search / grep returned no matches

The empty match is the single most common vacuous pass, because so many things
produce it: a typo in the pattern, the wrong directory, an unescaped
metacharacter, a file encoding the tool skipped, an error swallowed by a
redirect.

- **Positive:** run the same pattern against a line you know contains the target
  (paste one in, or point at a file you know has it). It must match. Only then is
  "no matches elsewhere" meaningful.
- **Traps:**
  - `grep -rn "X" -- '*.svg'` passes a literal filename, not a filter. Use
    `--include='*.svg' .`.
  - A pattern built in one language and run in another (a shell string, a JSON
    field, an injected snippet) can lose a backslash crossing the boundary, so
    `\d` becomes `d` and matches nothing.
  - `... 2>/dev/null` hides "no such file or directory" when your path was wrong,
    so a zero-match on a path that does not exist reads like a clean sweep.
  - A second `grep` filtering `grep -rn` output must account for the `path:line:`
    prefix; anchoring on `^` after that prefix matches nothing, silently.

## Test suite: all tests pass

A green suite proves the tests ran and passed. It does not prove they can fail,
that they cover the change, or that they are not asserting the bug.

- **Positive:** negate one assertion, or introduce one small mutation into the
  code under test, and confirm a test goes red. Restore it.
- **Traps:**
  - A test that asserts the current (buggy) behavior agrees with the bug; both
    are green together.
  - A mock or stub can satisfy the test without the real code path ever running.
  - A test that was skipped, or whose file was never collected, contributes a
    silent zero to the pass count.
  - "0 tests ran" and "all tests passed" print nearly the same summary. Read the
    count.

## Linter / type-checker: 0 problems

- **Positive:** introduce one violation you know the tool covers (an unused
  variable, a bad type) and confirm it is flagged.
- **Traps:** a config that excludes the files you care about, a ruleset with the
  relevant rule disabled, or a tool that silently skips files it cannot parse -
  each yields "0 problems" over code it never examined.

## CI gate: green

- **Positive:** in a scratch branch or commit, break exactly the thing the gate
  claims to enforce, push, and confirm the gate goes red. This is the only proof
  that a required check is actually required and actually wired.
- **Traps:** a gate whose script exits 0 regardless (a `|| true`, a missing
  `set -e`, an exit code read from the wrong stage of a pipe); a check that is not
  in the branch's required set; a matrix cell that was skipped.

## Schema / contract validation: valid

- **Positive:** feed a payload that violates the schema and confirm it is
  rejected. A validator that accepts everything accepts your real payload for the
  wrong reason.
- **Traps:** additionalProperties left open, a `$ref` that resolved to nothing, or
  validation that ran in warn-only mode.

## Subagent / tool reported success

A delegated "done" or "clean" is a claim, not a verified fact, and you are the one
who vouches for it when you relay or act on it.

- **Positive:** take one load-bearing claim from the report and check it yourself,
  cheaply and independently - open the one line it cites, re-run the one command,
  confirm the one file exists. If that one holds, your confidence in the rest is
  earned; if it does not, the report is decoration.
- **Traps:** a confident summary with no artifact you can open; a result that
  cannot be reproduced by the step it describes; a "no issues found" that is
  indistinguishable from "did not look".

## Query / API returned an empty set

- **Positive:** run a query you know returns rows against the same connection and
  credentials. If that also comes back empty, the connection or the auth is the
  reason, not your filter.
- **Traps:** a wrong database or tenant, an over-narrow `WHERE`, a pagination
  cursor already past the end, a permission that filters rows out before you see
  them.

## Monitor / alert stayed quiet

- **Positive:** emit a synthetic event that must trip the alert, and confirm it
  fires and reaches you. An alert nobody has seen fire is a dashboard, not an
  alarm.
- **Traps:** a threshold never reached, a notification channel that was muted or
  misrouted, a rule evaluating a metric that stopped being emitted.

## After you have seen the red

Restore whatever you sabotaged, and re-run the check once more to confirm it is
green again for the right reason. A check you left red, or left in a modified
state, is a new problem you introduced while proving the old one.
