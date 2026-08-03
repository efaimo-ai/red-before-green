# Failure gallery: the shapes a vacuous pass takes

Every entry here produced a green, an empty result, or a "clean" report while
checking nothing. They are collected so you can recognize the shape before it
costs you, and so "make it go red first" has concrete enemies to point at.

## The pipe ate the exit code

```bash
run-the-check | tail -5 ; echo $?
```

`$?` is `tail`'s exit status, which is almost always 0. The check underneath could
have failed and you would never know. The output scrolled past said "1 SUITE
FAILING" and the script reported success. Read the real exit code
(`set -o pipefail`, or check the command directly), and read the output, not just
the status.

## The escape lost a layer in transit

A pattern written in one context and executed in another loses a backslash at the
boundary. `/[\d.]+/` shipped into a shell string, a JSON payload, or a
remote-eval channel arrives as `/[d.]+/`, now a literal `d`-and-dot class rather
than digits, so it catches the wrong characters or nothing at all, and the tool
built on it reports a clean scan of what it never actually searched. Test the
pattern where it will actually run, against a known-positive, before trusting its
silence.

## The replace matched nothing and said nothing

`sed s/old/new/`, a find-and-replace, or an edit keyed on a string that is not
present changes nothing and exits 0. The file is untouched, the command
succeeded, and the next step proceeds as though the edit landed. Confirm the
edit exists in the artifact afterwards; do not infer it from the command's exit
code.

## The check was wired to pass

A gate script with a trailing `|| true`, a missing `set -e`, or a hardcoded
`exit 0` is green by construction. So is a required check that was never added to
the branch's required set. The only way to catch this is to break the rule it
claims to enforce and watch the gate fail; a gate you have never seen fail is not
a gate.

## The corpus was empty

A generator, linter, or report run over a path that matched no files produces "0
items, all clean" and writes a perfectly consistent empty result. Downstream,
that empty file overwrites a real one and every check on it passes, because an
empty set satisfies every "none of them are bad" assertion. Make an empty harvest
a failure, explicitly: zero items examined is a red, not a green.

## The test agreed with the bug

A test written to assert the current output keeps the current output green even
when the current output is wrong. The test and the bug were authored to agree.
Mutating the code should break some test; if a deliberate mutation changes
nothing, the tests are describing the code rather than constraining it.

## The mock passed for the code

A test double satisfied the assertions without the real code path executing. The
suite is green, coverage looks fine, and the integration it claims to exercise has
never run. Periodically feed the real path a known-bad input and confirm it, not
the mock, is what fails.

## The capture was taken too early

A screenshot, slice, or scrape taken before an animation, a lazy render, or an
async fetch populated the region captures an empty strip, which then reads as
"the element is broken" or "there is nothing there". The element was fine; the
observer fired early. Confirm the thing you are measuring is actually present at
capture time before concluding from its absence.

## The summary had no artifact

A report - human or agent - that says "looks good, no issues" with no line
number, no command, no file to open, is a claim with nothing under it. It is
indistinguishable from "I did not look". Require one openable artifact per
load-bearing claim, and open one.

---

The common thread: in every case, the instrument's silence was read as the
absence of a problem, when it was really the absence of a measurement. The fix is
always the same one move - make the instrument produce a positive on purpose, and
watch it, before you trust the negative.
