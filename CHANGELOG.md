# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
