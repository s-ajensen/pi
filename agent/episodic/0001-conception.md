# 0001 — Conception

The system that maintains this directory was designed in one conversation
(May–June 2026). This node is its distillate and the rationale for the rules
in `AGENTS.md`. Raw derivation:
`~/.pi/agent/sessions/--home-sajensen--/2026-05-30T22-29-40-525Z_019e7b01-de2d-745a-9678-56584e109bb6.jsonl`

## The judge (the one fixed criterion)

**Expected avoidable cost, bidirectional.** Everything else evolves; this does not.

- **Absence**: `P(needed) × cost-if-absent`. High product → promote into policy
  (a skill, a line in AGENTS.md). Evidence: observed recurrence (Alex repeated
  himself) or confident prediction (asserted facts like infra layout).
- **Presence**: `P(loaded-and-unused) × carry-cost`. High product → prune or
  narrow scope. Wrong assertions are caught retroactively by disuse.
- Break-glass facts (rarely used, catastrophic to lack) survive because the
  criterion is the *product*, not frequency.

The loudest detector: **Alex giving the same instruction twice**. The second
occurrence is a red bar. Silent cross-session repetition is the failure that
killed every prior system.

## Operating rules

1. **Retrieval-first, never extraction-first.** Knowledge earns storage only
   when its absence caused a concrete cost, or recurrence is confidently
   predicted. No speculative capture. No bulk-seeding from old logs — that was
   tried and produced unnavigable cruft (signal without feedback loop).
2. **Red/green separation for self-modification.** A correction record (red)
   is ratified by Alex. A policy edit (green) is proposed by an agent and
   reviewed by Alex before landing. Never both in one unreviewed shot.
3. **Two layers, opposite costs.** Policy (small, scoped, proactively loaded —
   pi skills ARE this layer) vs. episodic (large, raw, fetched on demand —
   the corpus paths in AGENTS.md). Promotion between them is the triage.
4. **Tools crystallize from usage.** Meta-passes are typed as plain requests
   first. A request typed repeatedly earns a slash command. A second command
   needing shared code earns the shared substrate. Nothing is built ahead of
   its failing test.
5. **View vs. record.** Pi sessions are append-only trees. Context-event
   rewrites change what the model sees (ephemeral view); branch/compaction
   changes the durable record. Pruning from view is deprecation, not deletion.
   History is never destroyed.
6. **Cognitive isolation applies to the system itself.** The object-agent
   works undistracted; judgment about persistence happens in invoked
   meta-passes, off the hot path. No standing self-check instructions, no
   per-turn LLM calls for memory bookkeeping.

## Deferred — each waits for its failing test

- Subagent-based parallel meta-channel (trigger: in-session meta-passes
  polluting the window enough to annoy)
- Slash commands for scan/prune (trigger: typing the same request ~5 times)
- Episodic index / semantic search (trigger: repeated painful manual retrieval)
- Recurrence/context-bloat detectors as extensions (trigger: manual noticing
  becoming itself a recurring cost)
- Correction-log format (trigger: the first real correction — write it
  wherever feels natural; that becomes the convention)

## Why this exists (compressed history)

Alex built an OpenCode multi-agent TDD system (Sisyphus et al.) whose audit
checks and agent definitions accreted from his live corrections — but
subagents were opaque one-shot tasks, corrections couldn't reach them, and
the system was abandoned. A successor "vault" (Obsidian knowledge graph,
extraction agents, locking protocols) was empirically benchmarked
(markdown beat sqlite; tool familiarity beat theoretical efficiency) but
died of extraction-first design: eager storage, no retrieval demand, no
feedback. The diagnosis that produced this system: **every prior failure was
an open loop — signal captured without a judge, structure designed before
use.** Hence: one fixed judge, everything else emergent, nothing built before
its failing test, and the human in the loop until trust is earned.
