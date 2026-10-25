# Syncing

How each doc is read, compared and edited. This reference assumes the ground truth of
[ground-truth.md](ground-truth.md) is at hand; it says what to change and, as much, what to leave.

## Discover

List what exists before reading anything else:

```bash
ls README.md CLAUDE.md AGENTS.md CHANGELOG.md 2>/dev/null
git ls-files 'docs/*' '*/README.md' ':!.claude/' ':!.agents/' ':!.codex/' ':!.gemini/' ':!vendor/'
```

Only the files listed are synced. Docs inside an agent's folder belong to installed skills and tools, not to
this repository, and vendored copies belong to their upstream; both are excluded. A missing README.md,
CLAUDE.md or AGENTS.md is not created; say in the report that it is missing, if it would help. CHANGELOG.md is read for the version of a markdown-only
repository and never edited. Read each doc whole before the first edit, so a fact repeated in several files
is known before any of them changes.

## Classify every claim

Go through each doc and mark every factual claim one of three ways.

| Class | Meaning | Action |
|---|---|---|
| Accurate | the code confirms it | leave it; list it under *verified* in the report |
| Stale | the code says something else, and says what | edit it to what the code says |
| Unverifiable | the code neither confirms nor refutes it, or fixing it needs a decision | leave it; list it under *flagged* with what you looked for |

A factual claim is anything the code can settle: a version, a dependency, a runtime, a command, a path, a
directory, an env variable, a route, a flag, a feature said to exist or not. Prose about why, for whom or
how well is not a claim to sync, and is left alone even when it reads dated.

## Edit in place

- **Replace the fact, keep the sentence.** "Built on Next.js 15 and React 18" becomes "Built on Next.js 16
  and React 19"; the sentence, its punctuation and its position stay.
- **Keep the doc's precision and format.** A major stays a major; a command in a fenced block stays in one;
  a table row stays a row. See *Declared range or resolved version* in [ground-truth.md](ground-truth.md).
- **Remove only what the code proves gone.** A command the repository does not define, whether deleted or
  never added, and with no script of the same purpose to take its name (*Commands* in
  [ground-truth.md](ground-truth.md)); a directory no longer tracked: remove it, and say so in the report. A
  feature the code does not show is flagged, not removed, since the code cannot tell a removed feature from
  one that was never built.
- **Replace, never add.** A sentence keeps its own words around the corrected fact. No clause, sentence or
  command is written to fill the gap a removal leaves or to explain it; the report carries that.
- **No new sections, badges or features.** A fact that has no place in the doc is not added to it; mention
  it in the report if the reader would want it.
- **Do not restyle.** Headings, wrapping, list markers, emphasis and the order of sections are the author's.

## Per file

| File | Sync | Leave |
|---|---|---|
| `README.md` | tech stack and versions, install and run commands, project structure, feature status, the current-release line | the pitch, the tone, contribution and license text, badges unless their value is a stale fact |
| `CLAUDE.md` | stack and versions, every command an agent is told to run, paths an agent is told to edit | conventions and the reasons given for them; never grow the file |
| `AGENTS.md` | the stated conventions against how the code is written now: file locations, naming, the test runner, the formatter | conventions the code cannot show either way |
| `docs/**` | the same facts as the README, where a page states them | tutorials and explanations beyond their facts |
| `CHANGELOG.md` | nothing | everything; `bumpv` owns it |

**CLAUDE.md and AGENTS.md first.** An agent runs what they say on every session, so a stale command there
does harm before a stale line in the README does. Keep them lean: correct a fact in place, do not expand a
line into a paragraph.

**AGENTS.md conventions are checked against the code, not trusted.** If it says components live in
`src/components` and the code has them in `components/`, that is stale. If it says "prefer server
components" and the code has both, that is a judgement the code cannot settle; leave it.

## Consistency across files

Before the report, list every fact that appears in more than one doc (the framework version, the install
command, the project's own version) and confirm each has one value everywhere.

```bash
git grep -n -E "Next\.js [0-9]+|npm run [a-z:-]+" -- '*.md' ':!CHANGELOG.md'
```

A fact you cannot make agree, because the docs describe different parts of a repository or a decision is
needed, is reported with every place it appears.
