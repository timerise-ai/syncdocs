---
prompts:
  - prompt: |
      Our docs are out of date. Save these three files at their paths first, then bring the docs in line with the code. Leave it uncommitted, I want to review the diff.

      README.md:
      # Acme

      Acme is our booking front end. It is built on Next.js 14 and React 18, and we keep it deliberately small.

      ## Getting started

      ```bash
      yarn install
      yarn dev
      ```

      Run `npm run lint` before you push, and `npm test` for the unit tests.

      ## Structure

      - `src/app/`: the routes
      - `src/components/`: shared components

      ## Features

      - A dark mode toggle in the header
      - Server-rendered pages

      CLAUDE.md:
      # CLAUDE.md

      Next.js 14 app. Before finishing a task, run `npm run lint` and `npm run typecheck`. Routes live in `src/app/`.

      CHANGELOG.md:
      # Changelog

      ## [0.1.0] - 2026-01-10

      - First version on Next.js 14.
  - prompt: "Clone https://github.com/timerise-ai/site-pin-gate into a scratch folder and check whether its README.md and CLAUDE.md still agree with its files and its changelog. Do not change that repository; write what you find to docs-check.md here."
---

# Prompts

What a user types after installing this skill, in their own words. An agent eval installs the skill into an
empty Next.js app, gives the agent one of these prompts and no further help, then type-checks, builds and
tests the result; the first prompt runs before every release. The fixture has no stale docs of its own, so
prompt 1 carries them and asks for the diff to be left uncommitted: what a run shows is in its notes, scored
on whether the agent took every fact from the code, kept the prose, flagged the feature it could not find,
created no new doc and left CHANGELOG.md alone. Prompt 2 works on a public repository with no manifest and
must leave both it and the app unchanged apart from the report. The results are the other files in this
folder. Section 10 of [STANDARD.md](https://github.com/timerise-ai/skills/blob/main/STANDARD.md) says how a
run is made.
