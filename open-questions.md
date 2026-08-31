# Craft Harness — Open Questions

Status as of 2026-08-30, after the neurosymbolic reframe (`craft-harness reframe.md`).
The v1 agent-harness questions are dissolved, transformed, or closed below; the live questions are round 4.

## Round 4 (live — from the reframe, §10 there)

1. **The medium.**
   Where does compose mode live: editor integration, CLI/REPL, notebook, chat harness?
   Dominates the MVP's feel; "lightweight to play out ideas" suggests editor or notebook.
2. **Symbolic lint.**
   A deterministic, zero-LLM flag on anything generated outside realization stacks (e.g., an import not in the registry gets a quiet squiggle).
   Cheap and non-blocking — the frictionless residue of gating, unlike the rejected LLM auditor.
   Wanted?
3. **Provenance mining scope.**
   Which corpus does bootstrap mine (repos, notes, past projects), and what does approval look like — per node, per batch?
4. **The librarian's reveal dial.**
   Where is the spoiler line: source locations only, ecosystem vocabulary, section-level pointers?
   Per-query, per-domain, or global?
5. **Ladder survivals.**
   Confirm assistance tiers (reframe §4) and freshness-based tier decay as the replacement for half-life re-probes.

## Carried forward (transformed by the pivot)

- **Assessment design workstream** → shrunk: provenance from real work is the evidence spine; the spike-validated sandbox/rubric machinery survives only as an optional instrument.
  Open remnant: what the optional instrument is for and when it's worth invoking.
- **Pathfinding residuals** (unchanged, now serving the librarian): coverage-matching softness, cost-weight calibration, SPA-catalog verification needing headless browsing.
- **Frontier pruning and re-ranking** (still deferred).
- **Judgment/taste representation**: arguably *more* important now — taste directly shapes expansion style (the operator's idioms) — still no design pass.

## Closed by the pivot

- **Requirement-extraction paranoia / auditor**: dissolved — the operator rejected the auditor, and the reframe removes the architecture that needed it.
  The red-team spike stands as evidence that subtractive gating leaks; it motivates the compositional architecture.
- **MVP scope (gate on software vs. declarative pilot)**: mooted in its old form — the medium question (round 4, item 1) replaces it.
- **L3 evidence standard / express-assessment format**: mooted as spine; folded into the optional-instrument remnant above.
- **CLAIMED / verify-on-first-use**: subsumed by provenance mining with operator approval.

## Process

- Next pass: operator answers round 4, then the reframe graduates into a clean v2 plan (state machines for binding resolution, provenance ingestion, and the librarian), then `craft-harness implementation.md`.
