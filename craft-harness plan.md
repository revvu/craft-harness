# Craft Harness — Plan

> **SUPERSEDED (2026-08-30):** the agent-harness architecture in this document (worker/checker/auditor, gated execution) has been replaced by the neurosymbolic framing in `craft-harness reframe.md`.
> This file is retained as design history; §8 of the reframe lists which concepts survive and in what form.

Status: DRAFT (refinement loop, pass 2).
Open decisions are marked in §10 and must be resolved before this plan is final.

## 1. Problem Statement

AI agents act using the model's pretrained knowledge, which is disconnected from what their operator actually understands.
The result is output the operator cannot fully vouch for, and a workflow that erodes rather than builds the operator's own competence.

The goal is a system in which an agent's usable capabilities are exactly the operator's demonstrated capabilities:

> Human mastery grants agent capability.

The system must:

1. Maintain a verified inventory of what the operator understands (knowledge), can do (skills), and holds as standards (judgment/taste).
2. Gate every agent action so that all premises and procedural primitives trace to verified mastery.
3. Surface the frontier — every blocked action becomes a visible, queued learning opportunity.
4. Grow: demonstrated learning unlocks new agent capability, closing the loop.

The objective is not maximal agent capability.
It is maximal agent capability **subject to the constraint** that capability remains a legible amplification of the operator's own understanding.

## 2. Governing Principles

**Authorization policy, not brain model.**
The registry is an authorization policy informed by assessment, not a model of the operator's mind.
Design choices are judged only by whether they make allow/deny decisions more accurate and wrong decisions cheaper to correct.

**Lazy materialization.**
The knowledge space is never represented globally — that is intractable.
Only what has mattered so far is materialized; everything else stays an implicit cloud.
The registry grows outward from demonstrated mastery, like a curriculum generated step by step on the fly.

**No distillation.**
The system must never become a pipeline for distilling an LLM's pretrained knowledge into the operator.
All learning content and all curricular structure must be grounded in established, effective, engaging human materials.

**Two gates, one loop.**
The worker gate keeps agent output inside the operator's mastery.
The teacher gate keeps the operator's learning inside the human canon.
The LLM supplies reasoning, orchestration, and assessment mechanics at every step, but is never the source of record on either side.

**Provenance chain.**
Agent output ← operator mastery ← evidence artifacts ← established human materials.
Every arrow has an explicit gate; nothing bottoms out in model weights.

## 3. Knowledge-Space Representation

The space has three regions:

```text
INTERIOR    verified mastery: units with evidence, unlocked at a level
FRONTIER    materialized candidates: named, sourced, connected — not yet mastered
CLOUD       everything else — implicit, unrepresented
```

### 3.1 Materialization events

A unit moves from cloud to frontier only through one of three events:

1. **Frontier expansion** (supply-driven): when a unit is unlocked, the teacher automatically materializes its neighbors — the units established curricula say come next — as options.
   (Know algebra 1 → algebra 2 appears; the rest of math stays cloud.)
   Fan-out is an organic score threshold (source support × goal relevance × edge-type diversity), typically ~4, never a hardcoded top-k.
2. **Capability gap** (demand-driven): the worker's plan is blocked on an unregistered or locked unit; that unit is materialized as a goal.
3. **Aspiration** (pull-driven): the operator names a distant goal directly ("I want quantum chemistry").

Events 2 and 3 may name targets deep in the cloud.
Then the teacher **path-finds**: it constructs the minimal prerequisite chain from the current interior (or claimed units, §3.3) to the target, using established curricular sequences, and materializes only that chain.
All three events converge on the same routine: materialize a node or chain, with sources and edges.

### 3.2 What a frontier node carries

- unit id and one-line scope, **scope-matched against source tables of contents** (course titles lie; "Algebra 1" means different scopes in different curricula)
- adjacency/prerequisite edges into the existing graph — **typed and provenance-tagged** (§3.2a)
- `sources:` — the specific established materials it would be learned from, found by research, cited, and verifiable (§3.2b)
- the materialization event that created it (expansion / gap / aspiration) and a revision date

Adjacency is not a global fact; different curricula order material differently.
Adjacency is defined **relative to cited sources**: frontier expansion is a research act with citations, not a graph lookup.
The knowledge space's implicit structure lives in the world's curricula; the system materializes views of it on demand.

#### 3.2a Edge model (from spike 1 findings)

Edges carry a **type** — the spikes surfaced at least five flavors, and conflating them makes frontiers mushy:

- `mechanism-beneath` (transactions → MVCC internals)
- `deepening` (basic locking → full lock-mode matrix)
- `operational-consequence` (MVCC → vacuum/bloat)
- `generalization` (postgres isolation → cross-engine transaction theory)
- `next-in-sequence` (algebra 1 → algebra 2)

Edges carry **provenance**: the curriculum that asserts them, and a revision date.
Curricula genuinely disagree on direction (theory-first academic sequences vs. practice-first practitioner ones; traditional vs. integrated pathways), and prerequisites change (AP Statistics dropped its second-year-algebra prerequisite for 2026-27).
There is no global DAG truth; the graph stores curriculum-relative claims.

The path-finder must distinguish **knowledge prerequisites from scheduling artifacts**: geometry sits between algebra 1 and statistics in school sequences, but no verified prerequisite structure requires it.
Real minimal chains are shorter than naive course-sequence reading suggests (algebra 1 → intro statistics is 2 hops, not 4).

#### 3.2b Citation robustness (from spike findings)

- Primary key for a source is edition + chapter/section **title** + stable URL slug; bare chapter numbers rot across editions and doc versions.
- Every source carries `verified_against: <URL>` and a verification tier: `verified` (ToC fetched), `partially-verified` (existence confirmed; paywalled or client-rendered catalog), never `from-memory`.
- Courseware is pinned by semester/version.
- Sources rarely tile units 1:1 — the format tolerates one source backing multiple units and multiple sources jointly backing one.

### 3.3 The interior is lazy too

The operator already has large mastered regions (software, biology, philosophy, ...) that must not require upfront enumeration.

- The operator may mark a unit `CLAIMED` — asserted prior mastery, no evidence yet.
- Claimed units anchor path-finding (curricula chains do not force re-learning algebra) but grant **nothing at the worker gate**.
- **Verify on first use** (proposed — OPEN DECISION 2): the first time the gate actually needs a claimed unit, an express assessment converts it to `UNLOCKED`.
  Claims are cheap; verification is deferred to the moment it matters.

### 3.4 Foundation floor, lazily

Prerequisite chains must bottom out.
During path-finding the operator may mark a presupposed unit `GRANTED` — foundational, assessment would be absurd (language, arithmetic, general reasoning).
Granted units live in one small, explicit, visible file; the floor is built lazily like everything else, never assumed implicitly.

### 3.5 Future work (explicitly deferred)

- Pruning stale frontier branches (options materialized long ago and never pursued).
- Re-ranking frontier options by relevance to active goals.

## 4. Architecture

Three stores, two agents, one human, one loop.

**Stores**

- Interior registry: unlocked units, levels, evidence artifacts, edges.
- Frontier: materialized candidates with sources.
- Queue: gaps and aspirations awaiting learning, plus parked tasks.

**Teacher agent** (world → operator).
Unrestricted world access.
A librarian and coach, not an oracle: researches established materials, materializes frontier nodes and paths with citations, orchestrates learning against those materials, generates and grades assessments, recommends promotions.
Cannot write mastery levels.

**Worker agent** (operator → world).
World access denied by default.
Plans tasks, declares required unit ids per step, executes only what the deterministic checker authorizes, emits CAPABILITY GAP otherwise.
Its working context is **built from** unlocked unit content (notes, idioms, taste files) — enforcement has a negative half (deny unmastered premises) and a positive half (ground output in mastered material).

**Operator.**
The only writer of mastery levels.
Approves sources before learning, reviews evidence before promotion, answers gaps by learning.

## 5. State Machine A — Capability Unit Lifecycle

```text
CLOUD (implicit)
    ↓ frontier expansion | capability gap | aspiration
FRONTIER               (named, sourced, edged; zero agent access)
    ↓ operator selects; sources approved
LEARNING               (operator studies the cited materials; teacher orchestrates)
    ↓ operator claims readiness
UNDER_ASSESSMENT       (rubric pre-registered, then proctored task)
    ↓ teacher grades, recommends level
EVIDENCED(L)           (evidence artifact exists; not yet usable)
    ↓ operator reviews evidence and approves
UNLOCKED(L)            (gate may authorize at level L; neighbors expand per §3.1)
    ↓ half-life elapses without personal use
STALE                  (flagged for re-probe; level treated as reference-only)
    ↓ re-assessment            ↓ operator revokes
UNLOCKED(L')           REVOKED
```

Parallel path for prior mastery:

```text
CLOUD → CLAIMED (operator assertion; anchors path-finding, grants nothing at gate)
CLAIMED → UNDER_ASSESSMENT (express, on first gate demand) → UNLOCKED
CLOUD → GRANTED (foundational floor; operator-marked, no assessment)
```

Structural operations available on any materialized unit: `SPLIT` and `MERGE` (cheap, first-class; evidence re-attributed or re-earned per child).

Invariants:

- No path to `UNLOCKED` bypasses assessment and operator approval (only `GRANTED` is exempt, and it is a separate, visible category).
- Agents can never write mastery levels.
- Every `UNLOCKED` unit carries timestamped evidence; every `FRONTIER` unit carries verifiable sources.

## 6. State Machine B — Gated Task Execution

```text
TASK RECEIVED
    ↓
WORKER PRODUCES PLAN        (each step carries a `requires:` list of unit IDs)
    ↓
AUDIT                       (adversarial pass: "what does this plan presuppose
    ↓                        that is not declared?" — OPEN DECISION from round 1)
DETERMINISTIC CHECK         (required ⊆ unlocked-at-sufficient-level)
    ↓ pass                       ↓ fail
EXECUTE + TRACE             CAPABILITY GAP
(trace records the             ↓
 authorizing unit IDs)      GAP MATERIALIZED as goal (enters machine C)
    ↓                          ↓
DONE                        TASK PARKED (resumable after unlock)
```

Invariants:

- Deny by default: undeclared or unregistered premises block execution.
- The gate checks premises and procedures, never conclusions — novel composition of unlocked primitives is always permitted.
- Every execution trace is auditable back to authorizing unit IDs.

## 7. State Machine C — Acquisition Loop

```text
GAP or ASPIRATION
    ↓
TEACHER PATH-FINDS          (minimal prerequisite chain from interior/claimed
    ↓                        to target, from cited curricula)
CHAIN MATERIALIZED          (frontier nodes with sources)
    ↓
PATH REVIEW                 (operator approves chain + sources in one step)
    ↓
OPERATOR LEARNS ALONG CHAIN (machine A per node)
    ↓
TARGET UNLOCKED
    ↓
PARKED TASKS RE-CHECKED     (blocked plans re-enter machine B)
    ↓
FRONTIER EXPANDS            (new neighbors appear as options)
```

The path-finding algorithm — graph construction by LLM-as-researcher producing cited edge claims, deterministic AND/OR minimal-closure search over those claims, and the full determinism ledger — is specified in `craft-harness core concepts.md` §5.

The loop is the product.
The system's health metric is throughput of this loop, not agent output volume.

## 8. Assessment Model

Mastery ladder with operational definitions:

| Level | Meaning | Demonstration | Agent access |
|---|---|---|---|
| 0 | frontier | none | none |
| 1 | can explain | adversarial explain-back (probing "why", edge cases) | reference only |
| 2 | can solve with help | proctored solve, hints permitted and logged | planning only |
| 3 | can independently apply | proctored cold solve of a fresh task instance | use |
| 4 | transfer / taste | transfer variant under changed constraints, or expert critique of flawed work | generalize |

Evidence integrity requirements:

- Assessments are **constructive wherever the domain allows**: build an artifact that performs against a pre-registered baseline (e.g., certify classical AI search by building an Othello player that beats a provided opponent), rather than answering questions.
  The baseline + acceptance criteria are the rubric; one artifact may certify a cluster of units.
  Declarative domains use the constructive analog (novel-case application defended against pre-registered objections).
  Detailed design is the assessment workstream (§11 open item 1).
- Assessment tasks are **novel instances**, anchored in the standard of the approved sources.
- Assessment sessions are **assistant-free and logged**; the log is the evidence artifact.
- Rubrics are **pre-registered** — written before the attempt.
- Evidence is timestamped and decays on a **half-life** (RESOLVED, §10): a unit unused by the operator personally for its half-life goes `STALE` and is flagged for re-probe; agent use of a skill does not count as operator practice.
- Promotion requires the operator to view the evidence artifact.

Accepted, mitigated integrity risks:

- Operator self-certification → pre-registered rubrics, durable artifacts; residual rubber-stamping risk acknowledged.
- Delegation accelerates decay (the worker exercising a skill means the operator stops exercising it) → surfaced honestly via staleness, not hidden.

## 9. Granularity Policy

Granularity is set by the gate, not by pedagogy, and is now additionally **anchored by sources**: frontier units inherit their natural size from the units of established materials (a chapter, a course module, a problem-set cluster).

A unit is right-sized when it is masterable in days-to-weeks, assessable with 1–3 concrete tasks, and plausibly appears alone on a `requires:` line.

Granularity remains demand-driven: false blocks → unlock or split; false authorizations → split and narrow.

## 10. Multi-Domain Regimes

- In procedural domains (software), the gate governs **procedures**: what the agent may do.
- In declarative domains (biology, philosophy, psychology, chemistry), the gate governs **premises**: which facts, concepts, and frameworks the agent may invoke while reasoning and writing on the operator's behalf.
- Schema, mastery ladder, and lifecycle are uniform across domains; per-domain **assessment playbooks** are the plug-in point
  (chemistry: novel problem sets; philosophy: novel-case application at L3, steelman-and-respond at L4).
- One namespace: domains interconnect (chemistry → biology prerequisites); domain tags are metadata, never silos.
- The registry has standalone value per domain as a verified map of understanding, independent of any worker agent.
- Sequencing: validate the gate on software first (execution makes gaps undeniable), then add one declarative domain to prove schema generality before spreading wide (§11 open item 4).

## 11. Decision Log

Resolved:

1. **Decay policy**: half-life on evidence; stale → flagged for re-probe, level treated as reference-only until re-verified. (round 2)
2. **Granularity**: gate-set, demand-driven, source-anchored. (round 2)
3. **Representation**: lazy materialization — interior/frontier/cloud, three materialization events, path-finding for distant targets. (round 2)
4. **Foundation floor**: built lazily as `GRANTED` marks during path-finding; small explicit file. (round 2, subsumes round-1 decision 3)
5. **Source approval gate**: yes — operator approves the teacher's proposed materials before learning begins; folded into path review (core concepts §5.1 step 6). (round 3)
6. **CLAIMED semantics / verify-on-first-use**: confirmed provisionally — claims anchor path-finding, grant nothing at the gate, express assessment on first gate demand. Revisit if the express-assessment friction proves wrong in practice (spike 3 in §12). (round 3)
7. **Frontier fan-out**: automatic on unlock; organic score threshold (source support × goal relevance × edge-type diversity), typically ~4, never a hardcoded top-k. (round 3)
8. **Assessment direction**: constructive assessment preferred — "build a thing that performs against a pre-registered baseline" over question-answering, wherever the domain allows (core concepts §7). Direction fixed; full assessment design is its own major workstream below. (round 3)

Open:

1. **Assessment design** (elevated from a decision to a workstream).
   Constructive-assessment direction is fixed; still to design: baseline construction and calibration, unit-cluster ↔ artifact mapping (which units one Othello player certifies, at what levels), express-assessment format for verify-on-first-use, declarative-domain analogs, grading mechanics.
   This interacts with the L3 evidence standard (below) and will get its own refinement pass.
2. **Requirement-extraction paranoia** (deferred to a later pass by operator).
   Full adversarial auditor from day one vs. trust + spot-checks for MVP.
3. **Evidence standard for L3** (deferred; will be reshaped by the assessment-design workstream).
4. **MVP scope** (deferred).
   Recommendation on file: gate on software; learning pilot on one non-software domain.

## 12. Assumptions to Spike

1. **Curriculum-grounded frontier expansion is feasible and high-quality.**
   ✅ SPIKED (2026-08-28), assumption holds.
   Sample expansions for `postgres.transactions` (6 units, 14 citations, all live-verified) and `math.algebra-1` (5 units), plus a path-find from algebra 1 to intro statistics (minimal verified chain: 2 hops via intermediate algebra, with a 1-hop alternative under the 2026-27 AP revision).
   Design consequences folded into §3.2a–b: typed edges, curriculum-relative provenance with revision dates, title-based citations with verification tiers, scope-matching against source ToCs, knowledge-prerequisites vs. scheduling-artifacts.
   Spike artifacts: `spikes/frontier-postgres-transactions.md`, `spikes/frontier-algebra-1.md`.
   Residual weak point: client-rendered course catalogs (Khan Academy, OpenStax detail pages) resist ToC verification via plain fetch — the materializer needs a headless browser or structured APIs (implementation concern).
2. Requirement extraction can be made honest enough with an auditor pass.
   ✅ SPIKED (2026-08-30) — the auditor is load-bearing, not optional.
   A worker planning against a 10-unit mock registry honestly parked the famous gaps (password hashing, sessions — even refusing a mechanically-buildable pseudo-session workaround) but smuggled 3 substantive unnamed premises (sqlite commit semantics, a check-then-insert uniqueness race, XSS-safety-by-autoescape); the deterministic id-subset checker passed every step of both plans.
   Headline: **the worker parks what it knows it lacks, and smuggles what it doesn't know it's using.**
   The adversarial audit caught all of it at ~2.5 min per task.
   Evidence attached to the still-open paranoia decision (§11 open item 2); recommendation: auditor from day one.
   Spike artifact: `spikes/red-team-requirement-extraction.md`.
3. Express assessment ("verify on first use") is fast enough not to make the worker unusable in week one.
   ✅ SPIKED (2026-08-30) — machinery holds; human time budget is the open risk.
   Generated express assessments for `git.branching` (deterministic sandbox + scripted grader) and `math.algebra-1` (rubric); blind grading matched ground truth 4/4 on competent/flawed attempts.
   Machinery overhead ≈ 5 min per cycle.
   But as-generated operator time was 20–24 min — 2–2.5× the ≤10-min express budget (the generator applied the budget per item, not per assessment).
   UNVERIFIED: actual human solve time — mandatory live step: the operator sits one timed express assessment before verify-on-first-use is declared viable.
   Spike artifact: `spikes/express-assessment-cycle.md`.

## 13. Out of Scope for This Plan

- Concrete storage formats, tooling, and agent harness wiring (implementation doc).
- Embeddings, knowledge graphs, or ontologies beyond the lazily-materialized graph.
- Frontier pruning and re-ranking (deferred, §3.5).
- Multi-user operation; the system serves exactly one operator.
