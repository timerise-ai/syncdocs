# syncdocs

[![Agent Skills](https://img.shields.io/badge/Agent_Skills-open_format-059669)](https://agentskills.io)
[![skills.sh](https://img.shields.io/badge/skills.sh-npx_skills_add-059669)](https://www.skills.sh)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-059669)](https://docs.claude.com/en/docs/claude-code/skills)
[![Codex CLI](https://img.shields.io/badge/Codex_CLI-compatible-059669)](https://developers.openai.com/codex/skills)
[![Gemini CLI](https://img.shields.io/badge/Gemini_CLI-compatible-059669)](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/skills.md)

An [Agent Skill](https://agentskills.io) that brings a repository's documentation back in line with its code.
It syncs the facts in README.md, CLAUDE.md, AGENTS.md and `docs/` (versions, dependencies, commands, project
structure and feature claims) against the repository's own manifests, lockfiles, scripts and files, in a
Next.js App Router app or in any other project, and leaves the human-written prose alone.

**Every fact is re-read from the code before it is written, and nothing else is touched.** A version comes
from the manifest, a command from the scripts, a tree from `ls`, a feature from the file that implements it.
A claim the code can neither confirm nor refute goes to the person in the report, not into an edit.

This skill was written by the engineer who maintains it, from the documentation passes they run on their own
repositories. What it carries holds by construction: every value written is one read in the same session
from a manifest, lockfile, script or file; a doc keeps its own precision, so a major stays a major; one fact
has one value across every doc; no doc is created and the changelog is never edited; and the report separates
what changed, what was verified and what needs a decision.
[`references/provenance.md`](references/provenance.md) has the record.

## Install

One command, via the [skills.sh](https://www.skills.sh) CLI, which installs the skill into every
skills-compatible agent it detects, including Claude Code, Codex CLI and Gemini CLI:

```bash
npx skills add timerise-ai/syncdocs
```

Name the agents instead with `-a`, for example `npx skills add timerise-ai/syncdocs -a claude-code -a codex`.

### Manual install

Nothing here is Claude-specific: the skill is a plain [Agent Skills](https://agentskills.io) folder,
`SKILL.md` plus markdown references with no file that calls a model, so cloning it into an agent's skills
directory is all an install is. For Claude Code:

```bash
git clone https://github.com/timerise-ai/syncdocs.git ~/.claude/skills/syncdocs
```

To scope it to a single project instead, clone it into that project's `.claude/skills/` directory. For another
agent, clone into that agent's skills directory, or symlink the Claude Code copy so one `git pull` updates
every agent:

```bash
mkdir -p ~/.agents/skills
ln -s ~/.claude/skills/syncdocs ~/.agents/skills/syncdocs
```

Update the skill with `git pull` in its directory. The current release is **0.1.0**. See
[CHANGELOG.md](CHANGELOG.md). The [skills index](https://github.com/timerise-ai/skills) lists the other
Timerise Skills and how to install them all at once.

## Activation

The skill activates automatically when you ask to sync, update or check the docs, when a version, script,
directory or feature changed and the docs may not match, or when an agent instruction file names a command
that may not exist. Invoke it explicitly with `/syncdocs` in Claude Code, `$syncdocs` in Codex CLI, or from
`/skills` in Gemini CLI. `/syncdocs check` reports what is stale without editing, and `/syncdocs <path>`
works on another repository.

Each host matches a task against the description its own way, so invoke the skill explicitly on a first run
rather than assuming it fired. Only `SKILL.md` is read up front; the `references/` files load on demand.

## What's inside

| File | Contents |
|---|---|
| `SKILL.md` | Entry point: the sync pipeline, the seam, critical facts, hard rules, invocation, quick start and reference directory |
| `references/ground-truth.md` | Where each fact comes from per ecosystem: manifests, lockfiles, declared range or resolved version, the project's own version, commands, structure, features |
| `references/syncing.md` | Discovering the docs, classifying every claim, editing in place, what each file syncs and leaves, consistency across files |
| `references/reporting.md` | The changed, verified and flagged report, check mode and the user's limits, the commit with no attribution |
| `references/provenance.md` | The engineering ledger: what the audit of the earlier implementation fixed, what was kept deliberately, and what was added here and never exercised |
| `README.md` | This file |
| `CHANGELOG.md` | One section per release, newest first |
| `CLAUDE.md` | The editing conventions, for an agent editing this repository |
| `LICENSE` | MIT |
| `evals/` | The prompts a user types after installing (`prompts.md`) and one file per agent eval: the skill installed into an empty Next.js app, one prompt, no help, then type-checked, built and tested |
| `.github/workflows/agent-eval.yml` | The caller of the index's reusable eval workflow, run on every published release and on a maintainer's dispatch |

The skill builds no code, so its evals score the agent's fidelity to the hard rules: the checks only confirm
the app was left intact, and the notes on each run carry the score. The seam is the target repository
itself: its manifests decide the versions, its scripts and targets the commands, its directories the tree,
and its commit convention and changelog stay with the `commit` and `bumpv` skills.

## The seven non-negotiables

These are never optional. Each is stated as a hard rule in `SKILL.md`, in the same order:

1. **Ground truth comes from the code, never from the docs or from memory.** A doc's own value is the claim
   under test, so it cannot also be the evidence; every value written is one read in the session.
2. **Only facts change.** Prose, tone and section order are the author's judgement, which the code cannot
   check; a targeted edit keeps the diff reviewable line by line, and no sentence or command is added to
   fill a gap.
3. **No doc is created.** A missing file is a decision about what the project documents, so it is offered in
   the report and not written.
4. **CHANGELOG.md is never touched.** `bumpv` writes it from the commits; two writers would contradict each
   other, and the `docs:` commit is the sync's own record.
5. **An unverifiable claim is flagged, never silently kept or deleted.** The code cannot tell a removed
   feature from one that was never built, so the report names the claim and where it looked.
6. **Nothing stale means no edit and no commit.** A cosmetic change made to justify a commit is a change
   nobody asked for.
7. **Only doc files are committed, only when the user has not asked to review, with no AI attribution.** The
   message is fixed so a sync is recognisable in any history, and `git log -1 --format=%B` verifies it.

Everything else is the target repository's: its manifests, its scripts, its commit convention, its prose.

## Not this

| Not this | Use instead |
|---|---|
| Writing the changelog or bumping a version | `bumpv` |
| Committing with the house conventions | `commit` |
| Checking the code against the docs as the spec | `code-audit` |
| Writing a README, CLAUDE.md or guide that does not exist yet | a documentation skill |
| Hardening a skill from its agent evals | [skill-eval-loop](https://github.com/timerise-ai/skill-eval-loop) |

## Contributing

Issues and pull requests are welcome here. Pure markdown, with no build step, but the shell and Node
one-liners in the references are checked: each is run against a repository of the kind it reads, a fresh
Next.js app for the npm commands and the discover pathspec, a version 3 `package-lock.json` for the lockfile
read. Claims in this skill are meant to be verifiable: if you change a factual claim about a manifest,
lockfile format or CLI, say how you verified it, whether against the tool's documentation, its `--help`, or a
reproduction.

Adding, removing or renaming a file in `references/` means updating the quick start and the reference
directory table in `SKILL.md`, the file table above, and any relative cross-links.
`references/provenance.md` is the ledger that must stay truthful: anything not exercised in a run is marked
as added, and anything you change gains an entry. Commits follow Conventional Commits and releases follow
[STANDARD.md](https://github.com/timerise-ai/skills/blob/main/STANDARD.md) in the index; `CLAUDE.md` carries
the full editing conventions.

## Part of the Timerise Skills

This is one of the [Timerise Skills](https://github.com/timerise-ai/skills): modules for **Next.js App
Router** apps written by our own senior engineers from the modules they have shipped, not synthetic, each
published as its own repository and indexed there. They share one layout, so an agent that has read one knows
how to read the next: a `SKILL.md` entry point, `references/` loaded on demand, and a seam contract carrying
the module's non-negotiables. This one keeps the documentation of any repository, these or another, in line
with its code.

## Author

Built and maintained by [Timerise](https://timerise.ai).

## License

MIT. See [LICENSE](LICENSE).
