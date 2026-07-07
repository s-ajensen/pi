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

Name things with plain, literal nouns that say what they are. Look at how the
existing services and projects on this system are named: `code`,
`media`, `finance`, `git`, `chat`, `cask`, `pi`, `meta` — single, concrete
words. No wordplay, no compounds that strain to be evocative (`code-craft` is
exactly the kind of "cute" to avoid).

The cleverness Alex values lives in the *design*, not in the labels. This
applies broadly — code identifiers, skills, services, files, directories — not
just code.

## Architecture screams use case, not technical layer

The hierarchy — crates, packages, top-level directories — is read before any
code is. It should tell that reader what the system *does* and what they can
*do with it*, not what technical kinds of code it contains. Grouping by
technical layer (`core`, `utils`, `macros`, `types`) says nothing and becomes a
grab bag; group by capability and let the layers fall out of use. A
toolchain-forced technical crate (e.g. a proc-macro crate in Rust) is
tolerated, not emulated.

The arrows matter as much as the names. **Depend in the direction of
stability** — and stability is measurable, not a vibe: how much depends on a
module, and how costly it is to change. Adding or moving a dependency edge is a
design decision, never plumbing: before it lands, say it in concept terms —
"X depends on Y because X needs *concept Z*, which sits below it." "That's
where the function already lives" or "it compiles this way" are inadmissible.
The red flag to check every time: does a more-depended-upon, more stable layer
now know the *name* of a concept above it? Then the placement is wrong,
whatever the convenience (SDP — DIP at module altitude).

Placement is the other half: **home code by reason-for-change, not by kind or
convenience.** A thing lives with what changes when it changes (common
closure), and a module must not force dependents to drag in concepts they
don't use — if reaching for the small thing imports the big unrelated one, the
home is wrong (common reuse). These pull against release granularity, and the
pull shifts over a project's life: early on, group what you develop together;
as pieces become independently shippable, release pressure pulls splits back
out. That movement is the dial working, not churn — but each split must be
forced by a real release need, never by taxonomy.

## Written artifacts carry no conversational context

A README, a skill, a commit message — any durable artifact — is read by someone
who was not in the room, including a future agent. Write for them, not for the
conversation you're in:

- **Never a "Files"/structure section.** If they're reading the README they have
  the repo — filenames and contents should be self-explanatory enough to poke
  around without a map. Such a map goes stale and starts lying immediately, like
  a comment.
- **No conversation-context residue.** Don't include sections that only mean
  something because of how the project evolved (a design shift you lived
  through, an internal surface like a custom renderer). Users *experience*
  internals; they don't read about them. If a line only lands for someone who
  watched it get built, cut it.
- **This governs skills too, not just READMEs.** A skill is a durable artifact
  read cold by a future agent. Don't smuggle in this-session framing — the
  motivating scenario you just lived through ("the gym outage," "the case that
  prompted this"), narrated rationale, or a play-by-play of how the rule was
  derived. State the rule, not its origin story.
- Same root principle as comments: prose that restates what the code/structure
  already shows rots fast. Document the *why* and the *how-to-use*, not the
  *what-is-where*.

## Read the source before reverse-engineering

When something opaque misbehaves — an API returns 401, a config is ignored, a
tool acts surprising — read its docs or source *before* theorizing from
symptoms. Guessing from the outside (poking inputs, inventing explanations for
the behavior) burns turns and produces plausible-but-wrong stories. The
authoritative answer is usually one `read` away. This is mechanism-before-assent
applied to other people's systems: don't assert why something behaves as it does
until you've looked at the thing that decides it.
