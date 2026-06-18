---
name: code-conventions
description: |
  Apply when writing or editing code in any language. Governs comments,
  function naming, and how to name things generally (modules, vars, files,
  services). Load this before producing or refactoring code so the output
  matches Alex's standing preferences rather than getting re-corrected.
---

# Code Conventions

Standing preferences extracted from repeated corrections. These are defaults,
not absolutes — but deviating from them needs a concrete reason, not habit.

## Comments are a last resort

Never scatter explanatory comments through the code. They are not free:

- **Comments rot.** They drift out of sync with the code and start lying.
- **LLM comments are worse.** They tend to embed context-specific information
  that makes no sense once you're reading the code in isolation later.

If a block of code looks like it needs a comment, that is a signal it should be
**extracted into a function, variable, or block with a self-descriptive name**.
Small, bite-sized, self-descriptive units beat comments. Reach for a comment
only as a genuine last resort, and sparingly.

## Functions are verbs

Name a function for the action it performs. `resolveElideRegion`, not
`elideRegion` as a noun-y label; `findMetaChild`, not `metaChild`. If a function
name isn't a verb (or verb phrase), rename it.

## Names are plain, not clever

Name things with plain, literal nouns that say what they are. Look at the
existing services and projects on this system for the house style: `code`,
`media`, `finance`, `git`, `chat`, `cask`, `pi`, `meta` — single, concrete
words. No wordplay, no compounds that strain to be evocative (`code-craft` is
exactly the kind of "cute" to avoid).

The cleverness Alex values lives in the *design*, not in the labels. This
applies broadly — code identifiers, skills, services, files, directories — not
just code.
