# Provenance

The engineering ledger for the person editing this skill. It separates what the audit of the earlier
implementation changed and how the skill now holds it, what was kept deliberately and why it is safe, and
what was designed in the skill and has never run. Read it before simplifying anything; add an entry for
anything you change.

The earlier implementation was a single `SKILL.md`, the maintainer's own instructions for a documentation
pass, used before this repository followed the Timerise skill standard. Release 0.1.0 restructured it into
this package. Five entries below were fixed, the fifth by the 0.1.0 agent evals, five kept, and ten added.

## Fixed

### 1. A tool list only one agent reads
The frontmatter carried `allowed-tools: Bash, Read, Grep, Glob, Edit`. It held the rule against creating
missing docs only in Claude Code, by leaving `Write` out; another agent ignored the field and the rule with
it. **Now:** the frontmatter has `name` and `description` only, and "Never create a doc that does not exist"
is a hard rule in `SKILL.md`, read by every agent.

### 2. An unconditional commit
The process ended "then commit only the doc files", whatever the user had asked. A user who wanted to review
the diff got a commit instead, and an unattended run left an empty working tree to inspect. **Now:** the
last hard rule commits only when the user has not asked to review first, and `/syncdocs check` makes no edit
at all ([reporting.md](reporting.md), *Check mode and the user's limits*).

### 3. Version precision left open
"`package.json` is the source of truth for versions" did not say whether a doc's version is the declared
range or the resolved one, so the same doc could be synced to `^16.2.0`, `16` or `16.2.12`. **Now:**
critical fact 1 and the precision table in [ground-truth.md](ground-truth.md), which keep the doc's own
precision.

### 4. No ground truth for a repository without a manifest
The ecosystem table ended at "the manifest the repo actually uses", which a markdown-only repository does
not have. **Now:** critical fact 6 and *The project's own version* in [ground-truth.md](ground-truth.md): the
newest changelog section checked against the newest tag, with a disagreement reported to `bumpv` rather
than resolved.

### 5. Two answers for a command with no script
Found by the 0.1.0 agent evals. *Commands* in [ground-truth.md](ground-truth.md) said an undefined command
with no same-purpose script is flagged and kept; *Edit in place* in [syncing.md](syncing.md) and critical
fact 2 said it is removed. One agent kept `npm test` and flagged it, another removed it, and a third swapped
`npm run lint` for `npm run build` and wrote a new sentence saying no lint or test scripts exist. **Now:**
both references and critical fact 2 say the same thing: the command is stale, it takes the name of a script
of the same purpose or is removed from its sentence, nothing stands in for it, and each swap or removal is
its own line under *Changed*. Hard rule 2 names sentences and commands among what is never added, and quick
start step 4 says the three-list report, one line per fact, is the final message.

## Kept deliberately

- **CHANGELOG.md is never touched.** Two tools writing one changelog produce duplicate or contradictory
  sections; `bumpv` writes it from the commits, and the `docs:` commit is the sync's own record.
- **Flag, never delete, an unverifiable feature.** The code cannot tell a removed feature from one that was
  never built or lives in another repository; only the person can, so the claim waits for them.
- **Nothing stale, no commit.** A cosmetic edit made to justify a commit is a change nobody asked for, and it
  hides the next real sync in a noisier history.
- **The fixed commit message.** `docs: sync documentation with codebase state` is greppable across every
  repository, so a sync is recognisable in any history.
- **The boundaries with `bumpv`, `commit` and `code-audit`.** Each owns one direction or one artefact; the
  skill that only syncs facts stays small enough to trust.

## Added

Designed in this skill and never exercised in a recorded run:

- **The three-way classification** of accurate, stale and unverifiable, and the report's three lists that
  follow from it. The earlier implementation named the same three outcomes only in its report step.
- **Critical facts 2, 4 and 5**: commands must be defined, agent instruction files first, one value per fact
  across files.
- **The per-file table** in [syncing.md](syncing.md), extending the earlier implementation's one line per
  file with what each file leaves alone.
- **`/syncdocs check` and `/syncdocs <path>`**, and the clone of a named public repository into a scratch
  directory.
- **The lockfile readers** in [ground-truth.md](ground-truth.md). The `package-lock.json` lookup was checked
  against a version 3 lockfile; the pnpm, yarn, bun, Cargo, uv and poetry locations are written from each
  format and were not checked in a run. `npm ls` printing `(empty)` without an install was reproduced on a
  fresh Next.js app.
- **The `npm pkg get` read**, checked on a fresh Next.js app with no install.
- **The feature search table** in [ground-truth.md](ground-truth.md).
- **The agent-folder exclusion** in [syncing.md](syncing.md), *Discover*, since an installed skill brings its
  own README.md and CLAUDE.md into the repository. The pathspec was checked on a fixture with a skill folder
  under `.claude/skills/`.
- **The cross-file consistency grep** in [syncing.md](syncing.md).
- **This skill's own agent evals.** The fixture is an empty Next.js app, so prompt 1 carries the stale docs
  it syncs, and prompt 2 names a public repository to clone.
