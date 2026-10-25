# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

An [Agent Skill](https://agentskills.io) package: markdown only. There is no `package.json` here and nothing
in this repository executes. It teaches an agent to sync a repository's documentation with its code: every
fact (version, dependency, command, structure, feature) re-read from the manifests, lockfiles, scripts and
files, and the prose left alone.

Keep the two straight: the commands in `references/` (`npm pkg get`, `git ls-files`, `git grep`, the
lockfile one-liner) run against the **target** repository whose docs are synced, not this one.

The skill was written by the engineer who maintains it. `references/provenance.md` is the rationale layer:
what the audit of the earlier implementation, a single `SKILL.md`, fixed, what was kept deliberately, and
what was added and never exercised.

## Structure

- `SKILL.md`: entry point, loaded whole on every activation, so it stays between 130 and 160 lines, the
  closing index line aside. Frontmatter is `name` and `description` only, the description being the trigger
  surface; the body carries the pipeline diagram and the seam paragraph that stands in for `adaptation.md`,
  six critical facts, seven hard rules, the invocation table, the quick start, the reference directory and
  the closing index line.
- `README.md`: the human-facing front door, in the section order of the index's STANDARD.md: install,
  activation, file table, seven non-negotiables, *Not this*, contributing, footer.
- `references/ground-truth.md`, `syncing.md`, `reporting.md`, `provenance.md`: one topic each, loaded on
  demand.
- `evals/`: `prompts.md` holds what a user types after installing, in their words. The fixture is an empty
  Next.js app with no stale docs, so prompt 1 carries them; prompt 2 names a public repository to clone. The
  checks only confirm the app was left intact and the notes carry the score. Every other file there is one
  eval run: measured frontmatter that is never edited, then the notes of the person who ran it. Add a prompt
  rather than rewording one that has results. The procedure is section 10 of the index's STANDARD.md.
- `.github/workflows/agent-eval.yml`: the caller of the index's reusable eval workflow, copied verbatim from
  STANDARD.md and the same in every skill. Never edit it, and never add a trigger on `push` or
  `pull_request`.

## Editing conventions

- **Code blocks name their destination or their target.** The one-liners run in the target repository's
  root; say so where a block could be mistaken for one that runs here.
- **The one-liners run.** After editing one, run it against a repository of the kind it reads and record how
  in `provenance.md`.
- **Identifiers are shared across files.** The three classes (accurate, stale, unverifiable), the report's
  three lists (changed, verified accurate, flagged), the invocations `/syncdocs`, `/syncdocs check` and
  `/syncdocs <path>`, the commit message `docs: sync documentation with codebase state`, and the sibling
  skill names `bumpv`, `commit` and `code-audit` appear in several files. Change them in all or none.
- **Keep the three tables in sync** with `references/`: the reference directory and the quick start in
  `SKILL.md`, and the file table in `README.md`. Links are relative.
- **The hard rules and the non-negotiables move together**: the seven hard rules in `SKILL.md` and the seven
  non-negotiables in `README.md` state the same seven things in the same order, and neither is ever
  presented as optional.
- **The odd-looking parts stay.** Flagging rather than deleting a missing feature, keeping a doc's version
  precision, never committing when nothing is stale: each has an entry in `provenance.md`. Read it before
  simplifying.
- **Mark additions as additions.** Anything not exercised in a run belongs under *Added* in `provenance.md`.
- **Nothing is renamed by the target.** The target's manifests, scripts and paths are read as they are;
  every name in this skill is its contract.
- **Plain punctuation, wrapped at 110 columns.** No em-dashes, en-dashes, arrows, middle dots or smart quotes
  in prose or tables; ranges are written "1 to 4".
- Commits follow Conventional Commits with no AI attribution trailers; releases follow the index's
  STANDARD.md.
