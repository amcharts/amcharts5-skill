# amCharts 5 skill: working guide

The skill teaches AI assistants to write amCharts 5 code.
- `amcharts5-skill/SKILL.md`: the core rules, setup and pitfalls.
- `amcharts5-skill/references/*.md`: one reference per chart family, plus JSON configs and UI elements.
- `cursorrules`: the same guidance in one file for Cursor and other tools that take a single rules file. When a rule changes in SKILL.md or a reference, change it in `cursorrules` too.

The amCharts MCP server (repo amcharts/amcharts5-mcp) serves SKILL.md and the references, and builds the Claude and ChatGPT plugins from them. Its `AGENTS.md` describes that side. Releases are done by the amCharts team.

## Rules for what the skill says

- **Describe only the latest amCharts release.** The skill states the release in "Latest release covered here".
  - No version history: no "since 5.x", "fixed in", "before 5.x", "older versions", "no longer", and no "(5.21.0)" tags.
  - When a release fixes a bug, its pitfall and any workaround are removed, not annotated.
  - SKILL.md keeps one "New in <latest>" list, because models are trained before new settings exist. Each release replaces it instead of adding to it.
  - Version numbers stay only where they decide what code to write: a package's minimum version (e.g. a framework package's peer range), and the CDN path or version pin a page should load.
- **No workarounds for library bugs.** A bug found while checking the skill goes to the amCharts library team; the skill doesn't carry a workaround or a "still broken" note. Documented behaviour, and recipes built from the public API for things the library has no setting for, belong here.
- **Verify every claim against the released library before writing it.**
  - Settings and types come from the release's typings. Follow the full `extends` chain.
  - Defaults resolve in the library's order: the class's soft defaults (`_setSoft`), theme rules (`*DefaultTheme`), the class's own defaults (`_setDefault`), then `@default`. A `get()` fallback in the code is the default only when none of these sets one.
  - Check behaviour in a browser against `https://cdn.amcharts.com/lib/version/<version>/`.
  - Changelog prose is not proof.
- **Every chart example** starts with `am5themes_Animated` and `am5themes_ResponsiveMax`, with a palette or `Dark` after them. The exception is `Micro` sparklines, which keep `Micro` alone.
- **Write plainly.** Short sentences, current behaviour, lists and tables for options and steps. Say what to do, not what changed.

## Changing the skill

- **`main` is public.** Anyone installing the skill from GitHub gets `main`. Prepare changes for an unreleased library version on a branch, or keep them local, and push them to `main` with the library release.
- **Review.** Have changes reviewed before they go out, by someone or an independent agent other than the author.
- **Commits.** No AI attribution trailers.
- **In the MCP repo** (where this repo is the `amcharts5-skill/` submodule):
  - run `npm run build:plugin` after each skill commit. The MCP repo's `npm test` fails while its plugin copy differs from the skill;
  - push the skill with `git push origin HEAD:main`.
