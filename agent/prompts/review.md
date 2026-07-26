---
description: Audit the implementer's landed work against the tree before commit
argument-hint: "[scope of the work under review]"
---
Review the work just landed: ${@:-the arc since the last commit}.

Audit the tree, not the report — read the actual diff; the landing report is a
claim, not evidence. Then deliver:

1. **Verdict first** — sound or not, faithful to what was ratified or not,
   in one sentence before any detail.
2. **Defects, classified** — name each with its severity; say plainly which
   are ship-blocking for this commit and which can ride.
3. **What you checked and found clean** — the audit's negative space, so the
   verdict's coverage is legible and nobody re-checks it.
4. **What a green suite can't show** — trace at least one path end-to-end
   (data flow, event delivery, failure route) and report what you find,
   including deliberate costs and non-defects, named so they don't get filed
   as bugs later.
