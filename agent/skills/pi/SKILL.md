---
name: pi
description: |
  Apply when working on the pi harness itself — adding or extending capability
  (tools, commands, extensions, CLIs), or modifying pi's own configuration,
  skills, and policy under ~/.pi/agent. Covers the preferred mechanism hierarchy
  and how pi discovers and loads skills, so changes match how the system
  actually works instead of guessing.
---

# Pi

You run on the **pi coding agent** (`pi-coding-agent`), not Claude Code —
regardless of what the system prompt claims. Don't assume Claude Code
conventions; check pi's own docs or source.

**References, docs first:**

- Docs: <https://pi.dev> and the repo README (sections for Skills, Extensions,
  Prompt Templates, Themes, Pi Packages).
- Source of truth: <https://github.com/earendil-works/pi> — this binary is
  `packages/coding-agent`. When docs are silent, the installed source under
  `@earendil-works/pi-coding-agent/dist/core/*.js` is authoritative.

**Constituent packages** (import targets when building extensions):

- `@earendil-works/pi-ai` — core LLM toolkit.
- `@earendil-works/pi-agent-core` — agent framework (sessions, tools).
- `@earendil-works/pi-tui` — terminal UI components (renderers, widgets).
- `@earendil-works/pi-coding-agent` — the harness itself.

See the README "See Also" for the current set.

## Adding capability: mechanism hierarchy

When reaching for new capability, prefer, in order:

1. **Pi extensions** — well organized, native to the harness. Live in
   `~/.pi/agent/extensions/`.
2. **CLI tools** — usually paired with a skill on how to use them, and/or a
   comprehensive `-h`/`--help` reference so the agent knows it exists and how to
   call it.
3. **Model tools (and MCP) — last resort.** Tools are *heavy*: every tool
   definition sits in context and pollutes it on every turn. MCP is not used on
   this system for the same reason. A tool is occasionally right (e.g. one
   tightly scoped to a single agent), but only after the tradeoffs are
   understood — never reached for off the cuff.

There is no blanket "no tools" rule. Justify the weight; pick the lightest
mechanism that does the job.

### Don't expand scope off the cuff

- **Don't add slash commands** without it being discussed and agreed. The
  command palette is not a junk drawer; keep it deliberate.
- **Don't introduce a new tool** as a reflexive first reach when a lighter
  mechanism would do.

Surface tradeoffs and get agreement before building anything heavier. Nothing is
built ahead of its failing test.

## Modifying pi itself

Pi's config and body live under `~/.pi/agent/`: `AGENTS.md` (auto-loaded every
session — keep it small), `skills/`, `prompts/`, `extensions/`, `settings.json`
(`packages` lists installed `npm:` extensions), and the episodic record. Durable
changes to skills/policy are **proposed, then ratified by Alex** — never
diagnosed and silently enacted in one shot.

### How pi discovers and loads skills

From `dist/core/skills.js` and `resource-loader.js`:

- **Scan locations:** `~/.pi/agent/skills/` (global) and `<cwd>/.pi/skills/`
  (project-local), plus extension-provided paths.
- **Per turn, only the frontmatter is injected** — `name`, `description`, and
  the file `location` — wrapped in `<available_skills>`. The body is **not**
  loaded until the agent `read`s the file on demand. So a skill's *description*
  is a standing per-turn token cost; its *body* is free until used.
- **Triggering is description-driven and model-judged.** Whether a skill fires
  depends on its description matching the task — not on its path. Write
  descriptions specifically so a skill surfaces only when relevant.
- **Discovery / nesting rule:**
  - a directory containing `SKILL.md` is a skill **root** — pi does **not**
    recurse below it (the subtree is sealed; children are reachable only by the
    parent body `read`ing them).
  - a directory **without** `SKILL.md` is a grouping namespace — pi recurses and
    surfaces each child `SKILL.md` independently.
  - So: want siblings auto-surfaced → keep the parent a parentless namespace
    (e.g. `skills/code/` holding `code/conventions/`). Want a subtree lazy and
    hidden → put a `SKILL.md` at the parent and have its body point at children.
- **`disable-model-invocation: true`** in frontmatter removes a skill from the
  prompt entirely (zero per-turn cost); it can then only be invoked explicitly
  via `/skill:name`. Use for present-but-silent / break-glass procedures.

When editing skills, apply the judge from `~/.pi/agent/episodic/0001-conception.md`:
a description earns its standing per-turn cost only if its absence would
recur or be expensive.
