---
name: tdd
description: |
  Apply whenever writing or changing code, not only when writing tests. The
  mandatory test-first doctrine on this system: red/green/refactor, classicist
  (Chicago) style, fakes over mocks, fully isolated suites, src/spec layout, and
  coverage judgment. Load before starting any implementation so the design falls
  out testable instead of being retrofitted.
---

# TDD

Test-first is mandatory here, in every case (see `code-conventions`). This skill
is the doctrine behind that rule. The point is not green assertions for their
own sake — it is that writing the test first **forces the architecture**.
Obeying TDD makes SOLID and sensible design patterns fall out without consciously
reaching for them; making code testable is what makes it well-structured. Treat
a comprehensive unit suite as double-entry bookkeeping: if accountants hold that
standard, software can too.

## The cycle: make it work, make it right, make it fast

1. **Work** — red test, then the simplest code that goes green.
2. **Right** — refactor on green.
3. **Fast** — optimize *only after green*. Performance work (e.g. replacing a
   quadratic scan with a hash lookup) comes last, never before the tests pass.

Run the whole suite after every cycle so regressions surface immediately.

## Classicist (Chicago), not London

- **No mock-heavy interaction testing.** Test real behavior through real
  collaborators wherever possible.
- **Fakes over mocks**, and only when a fake is actually necessary.
- **Minimize the faked surface area.** Before reaching for a fake, check whether
  the real API already exposes a measurable field you can assert on (e.g. does
  calling the real `registerCommand` leave an observable trail?). Prefer
  asserting on real effects.
- **Inject small fakes into a real context.** Keep the real orchestration/SDK
  code in play and thread minimal fake methods into it to leave an assertable
  trail — rather than standing up a parallel mock world.

## The suite is hermetic

A non-negotiable property: the full suite is **fully exercisable in isolation**.

- **No dependence on the local filesystem** or anything outside the repo.
- **No lasting side effects** on the system, and nothing outside the repo may
  affect a run.
- **A crash mid-run leaves no cruft.** If a test can leave files behind on
  failure, that's a design defect — prefer an in-memory / virtual-fixture
  interface over touching the real FS.
- **Pull dependencies in** rather than reaching out to system state. Never make
  tests depend on, or mutate, an existing installation on the machine.
- This is *not* a license for mocks — stay classicist; solve isolation with
  fakes-into-real-context and virtual fixtures, not by mocking everything.

## Fast, and run often

Tests must be fast so they run constantly. Every FS-dependent test slows the
suite and burdens cycle time, so keep them to a minimum. A heavier end-to-end
"belt and suspenders" test is at most an optional, separately-run nicety at the
very end — and often skippable, since at that point you're mostly testing the
SDK itself.

## Layout

- **Parallel trees**, not adjacent files: a `src/` tree and a mirroring `spec/`
  tree (prefer `spec/` over `test/`). Don't sit a spec next to its source.
- **Helpers live at the scope they serve.** Prefer a `helpers/` directory (or
  `fixtures/` where that fits better) located at the relevant scope — not a
  catch-all like `spec/support/`.
- **Order within a spec**, mirroring the source's simple-to-complex top-down
  composition: simplest→most-complex **error** cases first, then
  simplest→most-complex **success** cases.

## Coverage judgment

- **1-to-1 source/spec parity is the default**, not an absolute. Don't scatter a
  source file's tests across unrelated spec files — that dissipates references
  and makes things harder to track, not more principled. If logic is separate
  enough to test apart, it deserves its own source file; if it isn't, organize
  its tests within the existing spec.
- **A purely declarative composition root needs no test.** A `main` that only
  plugs tested components together (Uncle Bob's declarative wiring) is exempt
  *while it stays declarative*.
- **But builder/wrapper functions are testable and must be tested.** Wrapping a
  platform call in a specific way, with conditional logic, is real behavior.
  "Mostly declarative" does not exempt the builders a declarative root calls —
  scope them and test them. If a root accumulates enough such builders, ask
  whether they belong in their own file or package.
- **Integration tests follow the same buildup as units.** A complex failure mode
  is a composite of basic cases; build up to it from those, same principles and
  ordering as the unit specs.
