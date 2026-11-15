# Changelog

All notable changes to red-before-green are documented here. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); this project uses
[Semantic Versioning](https://semver.org/).

## [Unreleased]

> **Not published yet.** This entry describes what is on `main` and is
> dated when it ships. The first publish of `red-before-green` cannot carry provenance:
> npm's trusted publishing is configured from an existing package's settings
> page, so it cannot create a package that has never been published, and the
> authenticated session that can create one cannot mint an OIDC attestation.
> From the version after that the release workflow owns it and every release
> carries SLSA provenance naming the commit that built it. Install it from the
> repository in the meantime; the README carries the command.

### Added

- **The skill is installable.** `npx -y --allow-git=all github:efaimo-ai/red-before-green` copies `SKILL.md` and its
  `references/` into `./.claude/skills/red-before-green/`, with `--global`, `--dir`,
  `--check`, `--print`, `--uninstall` and `--force`. The installer reads every
  byte back after writing it and fails if what landed is not what it wrote, and
  it prints how many files it installed rather than only that it succeeded.
- Three exit codes rather than two: 0 installed or already identical, 1 present
  and different, 2 could not run at all. "It differs" and "I could not do this"
  are different answers and collapsing them is how a broken install reads as a
  clean one.
- CI on four cells (Ubuntu and Windows, Node 20 and 24), including an end to end
  run that packs the package the way npm would, installs it from that tarball,
  and installs the skill from there.

### Changed

- The description draws its boundary: a write that reported success goes to
  `read-it-back`, and how many items a passing check covered goes to
  `denominator`. It no longer fires on every "I am about to call this done".
- New "Sabotage safely": break a copy, a fixture or a scratch branch, ask before
  pushing a commit meant to turn CI red, restore in the same step, and call the
  result unverified when you cannot run the check yourself.
- New "Report it": a three-line template that shows the red you saw.
- The CI gate recipe asks before pushing, and the failure gallery uses generic
  tool names and validators anyone can run (`skills-ref validate`,
  `claude plugin validate`).
- "Why this matters more now" and "What this is not" are gone; they argued and
  changed nothing an agent does.

### Notes

- The package version and the version in `SKILL.md` frontmatter are one number,
  pinned by a test.

[Unreleased]: https://github.com/efaimo-ai/red-before-green/commits/main
