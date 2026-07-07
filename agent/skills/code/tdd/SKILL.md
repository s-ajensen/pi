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

## Triangulate from use cases — don't justify a predetermined design

Write no more of a test than is sufficient to fail, and make each test express
a **use case**, never a design you've already chosen. The tell: if you find
yourself constructing a test to *force* a particular implementation — killing a
"strawman" simpler version — you've stopped triangulating and started writing
after-the-fact tests for hypothetical code. That's the failure mode wearing
TDD's clothes; the code is imagined, but it's the same sin as testing existing
code retroactively. Let even a "wrong" minimal green stand: the *next* use-case
test is what surprises the implementation into its real shape, and the simpler
thing you didn't expect often survives where your predetermined design would
have over-built.

**A minimal green is often a degenerate implementation. That is expected — and
it is a fact to mark, never a deliverable.** "Write no more of a test than is
sufficient to fail" structurally produces greens that only cover the degenerate
case; noticing this is part of the cycle, not a sign something went wrong.
There is no license to *plan* a naive version and treat its green as done: the
moment you see the green is degenerate, the next red has a name — write it. If
it's passable now, proceed normally. If it needs infrastructure that doesn't
exist yet, write it anyway and park it (see "Reds that push back") — the parked
test is the concrete target that replaces an aetherial intention. **Deferral is
the human's call, not yours**: "ledger it and move on" is a legal resolution
only when the human ratifies the deferral. An agent that quietly resolves a
squint by ledgering it has laundered a known hole into doneness.

## Reds that push back

Situations where a red's resistance is the signal, not an obstacle:

- **The red reveals a prerequisite.** Sometimes trying to green a test exposes a
  design assumption that must be addressed first. Parking the test — commented
  out or recorded as pending, never silently dropped — while you build what's
  underneath, then returning to it, is legitimate discipline: the red did its
  job by surfacing the gap early.
- **The suite contradicts a prior decision.** When a new use case fails against
  an assumption an earlier test enshrined, the suite is acting as the
  specification it is: reassess the assumption and revise the design — don't
  patch the symptom or weaken the test. Deleting a ratified construct because
  the spec pushed back is TDD operating correctly; double-entry bookkeeping is
  what lets you see the true weight of the change.
- **The red you want sits above missing infrastructure.** The prerequisite
  case, mirrored upward: when the use case you actually care about needs layers
  that don't exist yet, write that higher-level test anyway and park it —
  commented out or ignored, never merely imagined. Hold it loosely: it was
  written against assumptions about code that didn't exist, so expect to revise
  it as the real shape develops underneath. This chains — a parked red may
  motivate a lower parked red, recursively, until you reach a test you *can*
  implement; then resolve bottom-up, massaging every layer as the concrete
  tests land. Use cases decompose into increasingly granular use cases, so
  expect the test tree to mirror that decomposition — at integration altitude
  exactly as at unit altitude.

Parked tests are visible debt: an arc or milestone is not "complete" while one
remains parked, except by the human's explicit deferral. The close-out check is
mechanical — search for parked tests; the claim "done" derives from that, not
from memory.

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
