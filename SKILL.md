---
name: syncdocs
description: >
  Sync a repository's documentation with what its code actually says: versions, dependencies, commands,
  project structure and feature claims in README.md, CLAUDE.md, AGENTS.md and docs/, each verified against
  the manifest, lockfile, scripts and files before it is written, with human prose, tone and section order
  left as they are. Use when: (1) a dependency, framework or runtime version changed and the docs still
  name the old one, (2) scripts, Makefile targets or the directory layout changed, (3) a feature was added,
  renamed or removed and the docs may no longer match, (4) an agent instruction file tells agents to run a
  command that may not exist, (5) the user mentions: sync docs, update the README, docs are stale, docs out
  of date, outdated version in README, fix CLAUDE.md, AGENTS.md, package.json, lockfile, pyproject.toml,
  Cargo.toml, go.mod, "npm run lint" missing script, "docs: sync documentation with codebase state".
  Carries the ground-truth table per ecosystem, the rule for declared range against resolved version, the
  flag-don't-guess rule for unverifiable feature claims, and a three-part report of changed, verified and
  flagged. Works on any project type, Node, Rust, Python, Go or a repository with no manifest at all; the
  repository's own manifests and files are the seam. Not a changelog or version bump tool, not a doc
  writer for missing files, and not an audit of the code against the docs.
---

# Syncdocs: the code is the spec for every fact in the docs

Documentation carries two kinds of text: facts that the code can confirm or refute, and prose that only a
person can judge. The idea this skill turns on is that **every fact is re-read from the code before it is
written, and nothing else is touched.** A version comes from the manifest, a command from the scripts, a
tree from `ls`, a feature from the file that implements it. Prose, tone and section order stay the
author's, and a claim the code can neither confirm nor refute goes to the person, not into an edit.

Written by the engineer who maintains it, from the documentation passes they run on their own
repositories; [provenance.md](references/provenance.md) has the record.

## When to use

After a change to versions, dependencies, scripts, structure or features, when the docs may no longer
match; before a release; or when asked to check whether the docs are accurate.

| Invocation | Meaning |
|---|---|
| `/syncdocs` | Sync every existing doc in the working directory, report, then commit the doc files |
| `/syncdocs check` | Verify and report only: no edit, no commit |
| `/syncdocs <path>` | The repository at `<path>` |

The user's words override the default: "do not commit", "I want to review the diff" or "just tell me" mean
no commit, or no edit, exactly as asked. A repository named but not present is cloned from its public URL
into a scratch directory; that clone and the package registry are not external services in an eval's
sense. Leave a working directory that is not the target as it was, apart from a report file if asked.

## When NOT to use

| Instead of this | Use |
|---|---|
| Writing the changelog or bumping a version | `bumpv` |
| Committing with the house conventions | `commit` |
| Checking the code against the docs as the spec | `code-audit` |
| Writing a README, CLAUDE.md or guide that does not exist yet | a documentation skill, on request |

## Architecture

```
discover docs --> README.md, CLAUDE.md, AGENTS.md, docs/** (only what exists)
      |
ground truth  --> manifest + lockfile, scripts/targets/CI, ls, the implementing file
      |
compare       --> each claim: accurate | stale (code says X) | unverifiable
      |
sync          --> targeted edits to stale facts, the same fact equal in every file
      |
report        --> changed / verified accurate / flagged
      |
commit        --> doc files only, "docs: sync documentation with codebase state"
```

The seam is the target repository itself: its manifests decide the versions, its scripts and targets
decide the commands, its directories decide the tree, and its own commit convention and changelog owner
stay with `commit` and `bumpv`. There is no `adaptation.md`; this paragraph stands in for it.

## Critical facts

1. **A declared range is not an installed version.** `"next": "^16.2.0"` permits many releases; the
   lockfile says which one resolved. A doc that names an exact version takes the lockfile's; a doc that
   names a range or a major takes the manifest's ([ground-truth.md](references/ground-truth.md)).
2. **A documented command is real only if the repository defines it.** `npm run lint` with no `lint`
   script fails on the reader's first try; the script, target or CI step must exist before the line stays.
   A missing one is stale, never flagged: it takes the name of a script of the same purpose (`lint` to
   `typecheck`), or is removed from its sentence, and nothing is added in its place.
3. **The docs' tree is a claim like any other.** Directories move without anyone editing the README, so
   the structure section is rebuilt from `ls` and the tracked files, never from the doc's own tree.
4. **Agent instruction files are executed, not read.** CLAUDE.md and AGENTS.md tell agents what to run;
   a stale command there is run on every session, so those files are checked first and kept lean.
5. **One fact, one value, in every file.** A version or command that appears in several docs is synced in
   all of them in the same pass, or the disagreement is reported; a half-synced fact is a new stale fact.
6. **A repository without a manifest still has ground truth.** For a markdown-only repository the newest
   changelog section, the tracked files and the git tags are the facts; the changelog is read, never edited.

## Hard rules

> **Ground truth comes from the code, never from the docs or from memory.** Every value written is one you
> read in this session from a manifest, lockfile, script, target, CI file or `ls`.

> **Only facts change.** Fix a stale fact in place with a targeted edit; never rewrite prose, reorder
> sections, restyle, or add sentences, commands, badges, sections or features.

> **Never create a doc that does not exist.** Sync README.md, CLAUDE.md, AGENTS.md and `docs/` only where
> they already are; offer a missing file in the report instead of writing it.

> **Never touch CHANGELOG.md.** `bumpv` writes it from the commit history; the `docs:` commit is the
> record of the sync.

> **An unverifiable claim is flagged, never silently kept or deleted.** A feature whose file, route or flag
> you cannot find, or a removal that needs a decision, goes in the report for the person to settle.

> **Nothing stale, no edit and no commit.** Say the docs are accurate and stop; no cosmetic change is made
> to justify a commit.

> **Commit only doc files, only when the user has not asked to review first, and with no AI attribution.**
> The message is `docs: sync documentation with codebase state`, with no `Co-Authored-By:` trailer naming
> any model or tool, no generated-by line and no session link; check `git log -1 --format=%B` after.

## Quick start

1. List the docs that exist, leaving out agent folders such as `.claude/` and vendored copies, and read each
   one whole: [syncing.md](references/syncing.md), *Discover*.
2. Establish ground truth from the repository's own manifests, scripts, tree and implementing files:
   [ground-truth.md](references/ground-truth.md).
3. Classify every factual claim as accurate, stale or unverifiable, then edit the stale ones in place,
   file by file: [syncing.md](references/syncing.md).
4. Report changed, verified and flagged as the final message, one line per fact with the file, the old value,
   the new value and what settled it, every removal included; then commit the doc files or stop as the user
   asked: [reporting.md](references/reporting.md).

## Reference directory

| Scenario | Trigger keywords | Reference |
|---|---|---|
| Where each fact comes from | package.json, lockfile, pyproject.toml, Cargo.toml, go.mod, Makefile, justfile, CI, scripts, ls, declared range, resolved version, no manifest | [ground-truth.md](references/ground-truth.md) |
| Editing each doc | README.md, CLAUDE.md, AGENTS.md, docs/, targeted edit, prose, structure tree, feature claim, cross-file consistency | [syncing.md](references/syncing.md) |
| What to tell the user and commit | report, changed, verified, flagged, check mode, review first, commit message, attribution | [reporting.md](references/reporting.md) |
| Where this came from | provenance, fixed, kept deliberately, added | [provenance.md](references/provenance.md) |

Part of the [Timerise Skills](https://github.com/timerise-ai/skills) index, which lists the sibling skills.
