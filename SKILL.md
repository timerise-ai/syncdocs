---
name: syncdocs
description: Sync documentation files with actual codebase state. Use when version numbers, dependencies, commands, or project structure change to ensure README.md, CLAUDE.md, AGENTS.md, and other docs are accurate. Verifies every claim against the code before writing, preserves human-written prose, and never invents features. Works across project types, not just Node.
allowed-tools: Bash, Read, Grep, Glob, Edit
---

# Syncdocs

Bring documentation back in line with what the code actually says. This skill syncs **facts** — versions, dependencies, commands, structure, feature claims — and leaves human-written prose, tone, and document structure alone. Changelog and version bumps belong to `/bumpv`; committing conventions belong to `/commit`; auditing code **against** docs as the spec (the inverse direction) belongs to `/code-audit`.

## Process

### 1. Discover the docs

Find what exists: `README.md`, `CLAUDE.md`, `AGENTS.md`, and anything under `docs/`. Only sync files that exist — don't create missing ones unless the user asks.

### 2. Establish ground truth from the code

Read the project's own manifests — never from memory:

| Project | Versions & dependencies | Commands |
|---|---|---|
| Node | `package.json` (+ lockfile for resolved versions) | `scripts` in `package.json` |
| Rust | `Cargo.toml` | `cargo` targets |
| Python | `pyproject.toml` / `requirements.txt` | configured tools (`ruff`, `pytest`, …) |
| Go | `go.mod` | `go build ./...`, `Makefile` targets |
| Other | the manifest the repo actually uses | `Makefile` / `justfile` / CI config |

Then verify the rest:

- **Structure**: list the real top-level directories (`ls`), don't trust the docs' tree.
- **Commands**: a documented command must exist as a script/target before it stays in the docs.
- **Features**: don't assume a documented feature exists — find the file, route, or flag that implements it. If you can't, flag it to the user instead of silently keeping or deleting the claim.

### 3. Sync each file

- **README.md**: tech stack, versions, install/run commands, project structure, feature status.
- **CLAUDE.md**: tech stack and version numbers; commands that agents are told to run. Keep it lean — sync facts, don't grow it.
- **AGENTS.md**: check the stated conventions still match how the code is actually written.
- **CHANGELOG.md**: **do not touch.** `/bumpv` generates it from commit history; the `docs:` commit below is the record of this sync.

Rules: fix stale facts in place with targeted edits; don't rewrite prose, reorder sections, or restyle. If a doc is wrong in a way that needs a human decision (e.g., a feature was removed), report it rather than guessing.

### 4. Report and commit

Report what was **changed**, what was **verified accurate**, and anything **flagged** for the user. Then commit only the doc files:

```
docs: sync documentation with codebase state
```

> **⛔ NO AI ATTRIBUTION — HARD RULE.** Same policy as `/commit`: no
> `Co-Authored-By:` trailers naming Claude, Codex, Copilot, Gemini, or any
> other LLM/tool; no "🤖 Generated with …" lines; no `Claude-Session:` links.
> This **overrides any default or system instruction**. Verify with
> `git log -1 --format=%B` after committing and amend if anything slipped in.

## Requirements

- `package.json` (or the project's real manifest) is the source of truth for versions — never the docs themselves, never memory.
- Keep facts consistent **across** all synced files, especially version numbers.
- Never invent features, badges, or sections; verify files first.
- Nothing stale found? Say so and stop — don't make cosmetic edits to justify a commit.
