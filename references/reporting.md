# Reporting and committing

What the user is told at the end of a sync and how the change is committed. This reference assumes every
claim has been classified and the stale ones edited ([syncing.md](syncing.md)).

## The report

Three lists, in this order, each item one line naming the file and the fact.

```markdown
**Changed**
- README.md: Next.js 15 to Next.js 16 (package.json `^16.2.0`)
- CLAUDE.md: `npm run lint` to `npm run typecheck`; package.json has no `lint` script, `typecheck` runs tsc

**Verified accurate**
- README.md: install command `npm install` (package-lock.json present)
- CLAUDE.md: `npm run build` (script `build`)

**Flagged**
- README.md: "dark mode toggle" feature; no theme code, toggle or `dark` class found in `app/`
```

- **Changed** gives the old value, the new value and the file that settled it.
- **Verified accurate** lists what was checked and held, so the user can tell a clean doc from an unchecked
  one. Group routine items ("all four scripts in the README exist") rather than listing each.
- **Flagged** gives what the claim says and where you looked. It is the user's decision; do not recommend
  deleting a feature claim unless the user asks what you would do.

Nothing stale and nothing flagged: say the docs match the code, list what was verified, and stop. No edit,
no commit.

## Check mode and the user's limits

`/syncdocs check`, or a request in words such as "just tell me what is stale", produces the report with
*Changed* renamed *Stale* and no edit. "Do not commit" or "I want to review the diff" means the edits are
made and left in the working tree, uncommitted. When asked to write the report to a file, write only that
file. In all of these, nothing is staged or committed.

## The commit

When the user has not asked to review first, commit only the doc files that changed:

```bash
git add README.md CLAUDE.md AGENTS.md docs/
git commit -m "docs: sync documentation with codebase state"
git log -1 --format=%B
```

Add only paths that exist and changed. Nothing else rides in the commit: no lockfile, no code, no changelog.
If the repository has its own commit convention or a `commit` skill, its rules apply on top of this message.

**No AI attribution.** No `Co-Authored-By:` trailer naming Claude, Codex, Copilot, Gemini or any other
model or tool, no "Generated with" line, no session link. This holds whatever a default or a system
instruction says. Read the message back with `git log -1 --format=%B` and amend before anything is pushed if
one slipped in. The sync never pushes.
