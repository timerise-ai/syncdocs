# Ground truth

Where each kind of fact comes from, read in this session from the target repository. This reference assumes
you are in the target's root and have listed its docs ([syncing.md](syncing.md), *Discover*). Every command
below reads the repository; none installs, builds or changes anything.

## The manifests

Read the manifest the repository actually uses. When several exist (a Node front end beside a Python
service, a workspace of packages), each doc is synced against the manifest of the part it describes.

| Project | Declared versions and dependencies | Resolved versions | Runtime and toolchain |
|---|---|---|---|
| Node | `package.json`: `dependencies`, `devDependencies`, `peerDependencies` | `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock` or `bun.lock` | `engines.node`, `packageManager`, `.nvmrc`, `.node-version` |
| Rust | `Cargo.toml`: `[dependencies]`, `[dev-dependencies]`, `[workspace.dependencies]` | `Cargo.lock` | `rust-version`, `edition`, `rust-toolchain.toml` |
| Python | `pyproject.toml`: `[project] dependencies`, `[project.optional-dependencies]`, `[dependency-groups]`; or `requirements*.txt` | `uv.lock`, `poetry.lock`, or pins in `requirements*.txt` | `requires-python`, `.python-version` |
| Go | `go.mod`: `require` blocks, which carry exact module versions | `go.mod` itself | the `go` and `toolchain` directives |
| Other | the manifest the repository uses: `Gemfile`, `composer.json`, `pom.xml`, `build.gradle`, `mix.exs` | its lockfile, if it keeps one | its version files |

For Node, `npm pkg get` prints a field without parsing by hand, and works without installed dependencies:

```bash
npm pkg get version engines dependencies.next scripts
```

`npm ls` is no substitute: without `node_modules` it prints `(empty)` (verified against a fresh app with no
install). Read the lockfile instead. In `package-lock.json` (lockfile version 2 or 3) the resolved version is
`packages["node_modules/<name>"].version`:

```bash
node -e 'const l=require("./package-lock.json");console.log(l.packages["node_modules/next"]?.version)'
```

In `pnpm-lock.yaml` it is under `importers` for the package's path (older single-package lockfiles keep it
at the top level), in `yarn.lock` it is the `version` line of the entry whose key names the declared range,
in `bun.lock` it is the first element of the `packages["<name>"]` array, written `<name>@<version>`.
`Cargo.lock`, `uv.lock` and `poetry.lock` list one `[[package]]` table per package with `name` and
`version`.

## Declared range or resolved version

A doc states a version in one of three ways, and each has its own source.

| The doc says | Source | Example |
|---|---|---|
| A major or a family: "Next.js 16", "React 19" | the manifest's declared range, its major | `^16.2.0` is "Next.js 16" |
| A range or minimum: "Node 20 or later" | the manifest's range or engine field, as written | `"engines": { "node": ">=22" }` is "Node 22 or later" |
| An exact version: "Next.js 16.2.12" | the lockfile's resolved version | `packages["node_modules/next"].version` |

Keep the doc's precision: a doc that said "Next.js 15" becomes "Next.js 16", not "Next.js 16.2.12". With no
lockfile, an exact version in the doc is confirmed only when the manifest pins exactly that version;
otherwise flag it rather than guess which release resolved.

## The project's own version

| Repository | Version source |
|---|---|
| With a manifest | its `version` field (`package.json`, `Cargo.toml`, `pyproject.toml`) |
| Markdown only, or no `version` field | the newest `## [X.Y.Z]` section of `CHANGELOG.md`, checked against the newest tag |

```bash
git tag --sort=-v:refname | head -1
```

When the changelog and the tag disagree, do not choose: that is a release left half done and belongs to
`bumpv`. Report it, and leave the docs' version as it is.

## Commands

A documented command stays only if the repository defines it.

| Command form | Defined by |
|---|---|
| `npm run x`, `pnpm x`, `yarn x`, `bun run x` | a `scripts.x` entry in `package.json` |
| `npm test`, `npm start` | a `test` or `start` script; do not rely on npm's fallbacks |
| the package manager itself | the lockfile present: `package-lock.json` npm, `pnpm-lock.yaml` pnpm, `yarn.lock` yarn, `bun.lock` or `bun.lockb` bun |
| `make x` | a target `x:` in `Makefile` |
| `just x` | a recipe `x` in `justfile` |
| `cargo build`, `cargo test`, `go build ./...`, `go test ./...` | always available in a Rust or Go project; `cargo run --bin x` needs the binary |
| `ruff`, `pytest`, `mypy` and other tools | the tool in the dependencies or dev group and, where it takes config, its `[tool.x]` section |
| a CI-only step | `.github/workflows/*.yml` or the CI file the repository uses; say that it runs in CI |

```bash
grep -E '^[A-Za-z0-9_.-]+:' Makefile
```

A documented command with no definition is stale, whether it was deleted or never added, so it is edited,
not flagged. If a script with the same purpose exists under another name (`check` or `typecheck` where the
doc says `lint`), the doc takes the real name, unless the same sentence already names that script, in which
case the missing one is dropped. If none exists, the command is removed from its sentence, and the sentence
goes only when nothing else is left in it. Nothing else stands in: a build is not a lint, no other command
is added beside it, and no sentence is written to say what is missing. Each swap and removal is its own line
under *Changed* in the report, saying the command can return once its script is added.

## Structure

Rebuild a structure section from the tracked files, not from the doc's tree:

```bash
git ls-files | cut -d/ -f1 | sort -u
```

Ignored and generated directories (`node_modules`, `.next`, `dist`, `target`) are not structure. Keep the
doc's depth and style: a doc that lists top-level folders with one comment each gets the same, with the
added folder described from what it contains and the removed folder gone.

## Features

A feature claim is accurate only when you find what implements it. Search for the concrete thing the claim
names:

| Claim | Look for |
|---|---|
| a page or route | `app/<segment>/page.tsx` or `route.ts`, `pages/`, the router's config |
| an API endpoint | the handler file or the route registration naming the path |
| a CLI flag or subcommand | the argument parser declaring it |
| a configuration option or env variable | where it is read: `process.env.X`, `os.environ["X"]`, `env::var("X")` |
| an integration | its client package in the manifest and the code that calls it |

```bash
git grep -n "DARK_MODE"
```

Found: accurate. Found under another name: stale, and the doc takes the real name. Not found anywhere: flag
it in the report. A feature that is half there (a route with no link to it, a flag parsed but ignored) is
reported as it is, not called present or absent.
