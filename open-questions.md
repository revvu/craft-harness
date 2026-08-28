# Craft Harness — Open Questions

Status as of 2026-08-28.
Resolved decisions live in `craft-harness plan.md` §10; this file tracks what is still open, for the next refinement pass.

## 1. Assessment design (major workstream)

Direction is fixed: constructive assessment — build an artifact that performs against a pre-registered baseline — preferred over question-answering wherever the domain allows (core concepts §7).
Still to design:

- **Baseline construction and calibration**: where do baseline opponents / acceptance criteria come from, and how do we know a baseline corresponds to a mastery level (an Othello player that "beats most humans" vs. one that beats a provided reference player)?
- **Artifact ↔ unit-cluster mapping**: which units does one constructive artifact certify, and at what levels? One Othello player plausibly evidences game-tree search, heuristics, and eval-function design — who decides the fan-in, and how is it audited?
- **Express-assessment format** for verify-on-first-use: what does a ~10-minute assessment look like per domain, and is it fast enough not to make the worker unusable in week one? (Spike pending: time a full claim → verify → execute cycle.)
- **Declarative-domain analogs**: the constructive form for philosophy/psych ("apply framework X to a novel case, defend against pre-registered objections") needs the same rigor of pre-registration and grading as the performance-graded form.
- **Grading mechanics**: how much LLM judgment survives in grading once baselines are performance-graded; what the rubric artifact looks like for non-performance domains.
- **L3 evidence standard** (deferred from round 1; reshaped by the above): recommendation on file is one proctored cold solve + one adversarial explain-back — re-evaluate once constructive assessment is designed.

## 2. Requirement-extraction paranoia (deferred round 3)

The plan-step → `requires:` mapping is LLM-performed and is the weakest link in the worker gate — under-declaration smuggles unmastered premises past the deterministic check.

- Full adversarial auditor pass from day one, or trust + spot-checks for the MVP?
- Spike pending: red-team a worker plan for smuggled undeclared premises to measure how bad under-declaration actually is.

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

- Next pass per the refinement loop: run pending spikes (§2 red-team, §1 express-assessment timing), then open the assessment-design workstream.
- After design converges: write `craft-harness implementation.md` bridging current state (nothing built) to the plan.
