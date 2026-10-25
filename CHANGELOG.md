# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.1] - 2026-10-25

Fix release, from scoring the prompt-1 agent eval runs against 0.1.0.

### Fixed

- A command with no script had two answers: `references/ground-truth.md` flagged and kept it,
  `references/syncing.md` removed it. Both now say it is stale, takes the name of a script of the same
  purpose or is removed from its sentence, and nothing is added in its place; critical fact 2 in `SKILL.md`
  says the same.

### Changed

- Hard rule 2 in `SKILL.md` and non-negotiable 2 in `README.md`: no sentence or command is added to fill a
  gap.
- Quick start step 4 in `SKILL.md` and `references/reporting.md`: the three-list report is the final
  message, one line per fact with the file, the old value, the new value and its source, every removal
  included.
- `references/provenance.md`: entry 5 under *Fixed*.

## [0.1.0] - 2026-10-25

First release: the documentation sync restructured to the Timerise skill standard, with its ground truth,
editing rules and report split into references.

### Added

- `SKILL.md`: the sync pipeline, six critical facts, seven hard rules, the invocation table with
  `/syncdocs check` and `/syncdocs <path>`, the quick start and the reference directory.
- `references/ground-truth.md`: manifests and lockfiles per ecosystem, declared range or resolved version,
  the project's own version with or without a manifest, commands, structure and features.
- `references/syncing.md`: discovering the docs outside agent and vendored folders, the three-way
  classification, editing in place, the per-file table and consistency across files.
- `references/reporting.md`: the changed, verified and flagged report, check mode, and the doc-only commit
  with no AI attribution.
- `references/provenance.md`: what was fixed, kept deliberately and added.
- `README.md`, `CLAUDE.md`, `LICENSE`, `evals/prompts.md` and the agent eval workflow caller.

### Changed

- `SKILL.md` frontmatter: `name` and `description` only; the rule against creating missing docs, held
  before by leaving `Write` out of `allowed-tools`, is a hard rule.
- The commit step: no commit when the user asks to review the diff first, or in check mode.
