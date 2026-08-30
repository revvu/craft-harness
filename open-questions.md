# Craft Harness — Open Questions

Status as of 2026-08-30.
Resolved decisions live in `craft-harness plan.md` §11; this file tracks what is still open, for the next refinement pass.
Both pending spikes have been run; their evidence is folded into the items below (artifacts: `spikes/red-team-requirement-extraction.md`, `spikes/express-assessment-cycle.md`).

## 1. Assessment design (major workstream)

Direction is fixed: constructive assessment — build an artifact that performs against a pre-registered baseline — preferred over question-answering wherever the domain allows (core concepts §7).
Still to design:

- **Baseline construction and calibration**: where do baseline opponents / acceptance criteria come from, and how do we know a baseline corresponds to a mastery level (an Othello player that "beats most humans" vs. one that beats a provided reference player)?
- **Artifact ↔ unit-cluster mapping**: which units does one constructive artifact certify, and at what levels? One Othello player plausibly evidences game-tree search, heuristics, and eval-function design — who decides the fan-in, and how is it audited?
- **Express-assessment format** for verify-on-first-use: SPIKED — grading machinery discriminates (4/4 blind verdicts correct; deterministic sandbox grading needed zero discretion) and machinery overhead is ~5 min/cycle.
  Remaining: hold the human time budget — as-generated assessments ran 20–24 min against a ≤10-min budget, so the format needs a hard per-assessment (not per-item) time budget with item count derived from it; and rubrics need a red-team against a synthetic flawed attempt before pre-registration (the untested written rubric carried two substantive ambiguities; the self-tested git harness carried none).
  UNVERIFIED and mandatory before declaring viability: the operator sits one real timed express assessment.
- **Declarative-domain analogs**: the constructive form for philosophy/psych ("apply framework X to a novel case, defend against pre-registered objections") needs the same rigor of pre-registration and grading as the performance-graded form.
- **Grading mechanics**: how much LLM judgment survives in grading once baselines are performance-graded; what the rubric artifact looks like for non-performance domains.
- **L3 evidence standard** (deferred from round 1; reshaped by the above): recommendation on file is one proctored cold solve + one adversarial explain-back — re-evaluate once constructive assessment is designed.

## 2. Requirement-extraction paranoia (deferred round 3 — now with decisive evidence)

The plan-step → `requires:` mapping is LLM-performed and is the weakest link in the worker gate — under-declaration smuggles unmastered premises past the deterministic check.

- Full adversarial auditor pass from day one, or trust + spot-checks for the MVP?
- SPIKED: the worker honestly parked famous, nameable gaps but smuggled 3 substantive unnamed premises (commit semantics, a uniqueness race, safety-by-framework-default); the deterministic checker passed everything; the auditor caught all of it at ~2.5 min/task.
  The failure mode is the worker's unknown-unknowns, not deception — so spot-checking is sampling for something the worker itself can't see.
  **Recommendation: auditor from day one**, prompted specifically at unnamed/ambient premises; decision remains the operator's.
- Follow-on design inputs from the spike: unit scopes should state exclusions, not just inclusions (they are the audit substrate); under-declaration of already-unlocked units is benign bookkeeping; a registry gap can hide a reachable-but-unknown safe design, so gating does not substitute for design review.

## 3. MVP scope (deferred round 3)

- Recommendation on file: run the worker gate on software (where execution makes gaps undeniable) and pilot the learning loop on one non-software domain the operator is actively curious about.
- Which non-software domain to pilot?

## 4. Path-finding residuals

- **Coverage matching** is the soft spot: semantic scope-matching ("is this discovered prereq covered by my interior?") has no citation to verify against.
  Current containment: recorded verdicts, operator veto at path review, self-correction via gate errors → split/merge.
  Is that enough, or does it need corroboration (e.g., two independent match verdicts)?
- **Cost-function weights** (`kind_penalty` ~3× for sequence-position edges, confidence/staleness penalties, effort estimates) are placeholders; they need calibration against real paths.
- **SPA-rendered course catalogs** (Khan Academy, OpenStax detail pages) resist ToC verification via plain fetch; the materializer needs a headless browser or structured APIs (implementation concern, noted from spike 2).

## 5. Deferred design areas (acknowledged, not started)

- Frontier pruning: retiring stale materialized options never pursued (plan §3.5).
- Frontier re-ranking by relevance to active goals.
- Judgment/taste units: modeled in the original concept doc, but assessment and gating for taste (vs. knowledge and skills) have had no design pass yet.
- Half-life values per unit type: what decay constants are reasonable, and does personal use tracking need its own mechanism?

## 6. Process

- Both pending spikes are done (2026-08-30).
- Next pass per the refinement loop: operator rules on §2 (auditor vs. spot-checks — evidence now strongly favors auditor) and §3 (MVP scope), operator sits one timed express assessment (§1's UNVERIFIED item), then open the assessment-design workstream.
- After design converges: write `craft-harness implementation.md` bridging current state (nothing built) to the plan.
