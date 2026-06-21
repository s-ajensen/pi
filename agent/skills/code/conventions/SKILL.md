---
name: code-conventions
description: |
  Apply when writing or editing code in any language. Governs comments,
  function naming, naming generally (modules, vars, files, services),
  documentation/READMEs, and the mandatory use of TDD. Load this before
  producing or refactoring code so the output matches Alex's standing
  preferences rather than getting re-corrected.
---

# Code Conventions

Standing preferences extracted from repeated corrections. These are defaults,
not absolutes — but deviating from them needs a concrete reason, not habit.

## TDD is mandatory

All code is written test-first, in every case. There is no "too small to test"
exception. The full doctrine — the red/green/refactor cycle, classicist
(Chicago) style, fakes over mocks, suite isolation, layout, and coverage
judgment — lives in the `tdd` skill (`skills/code/tdd/SKILL.md`). Load it
whenever you write or change code, not just when writing tests.

## Comments are a last resort

Never scatter explanatory comments through the code. They are not free:

- **Comments rot.** They drift out of sync with the code and start lying.
- **LLM comments are worse.** They tend to embed context-specific information
  that makes no sense once you're reading the code in isolation later.

If a block of code looks like it needs a comment, that is a signal it should be
**extracted into a function, variable, or block with a self-descriptive name**.
Small, bite-sized, self-descriptive units beat comments. Reach for a comment
only as a genuine last resort, and sparingly.

## Never commit on Alex's behalf

Do not `git commit`, push, or otherwise mutate version-control history. Leave
your changes in the working tree for Alex to review and commit himself. This
holds even when the change is finished and correct — the commit is his call.

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

## Documentation says only what a reader without our context needs

A README (or any doc) is read by someone who was not in the room. Write for
them, not for yourself:

- **Never a "Files"/structure section.** If they're reading the README they have
  the repo — filenames and contents should be self-explanatory enough to poke
  around without a map. Such a map goes stale and starts lying immediately, like
  a comment.
- **No conversation-context residue.** Don't include sections that only mean
  something because of how the project evolved (a design shift you lived
  through, an internal surface like a custom renderer). Users *experience*
  internals; they don't read about them. If a line only lands for someone who
  watched it get built, cut it.
- Same root principle as comments: prose that restates what the code/structure
  already shows rots fast. Document the *why* and the *how-to-use*, not the
  *what-is-where*.
