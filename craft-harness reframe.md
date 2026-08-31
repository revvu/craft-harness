# Craft Harness — Neurosymbolic Reframe (v2, pass 1)

Status: DRAFT — this document supersedes the *architecture* of `craft-harness plan.md` (the agent-harness framing: worker/checker/auditor).
Concepts that survive the pivot are listed in §8 with their new forms.
Open decisions for the next refinement pass are in §10.

## 1. The Inversion

The v1 framing tried to **filter a general intelligence down**: an agent does the work; a deterministic checker and an adversarial auditor subtract everything the operator hasn't earned.
The requirement-extraction red-team spike showed why that architecture carries irreducible friction: subtractive enforcement leaks (the worker smuggles what it doesn't know it's using), and leaky subtraction needs police.
The auditor was not incidental to the gating frame — it was constitutive of it.

The v2 framing **composes a personal intelligence up**.
The system is not an agent wrapper.
It is a **neurosymbolic representation of the operator's understanding** — a medium that makes the ideas, concepts, and skills the operator provably knows easily leverageable and composable.

Nothing needs to be denied, because nothing outside the operator's space is offered.

The stakes change with the executor: v1 delegated work to an agent, so leakage was a breach of delegated authority (high stakes, heavy machinery).
In v2 the operator is the executor and the system is the medium; residual leakage is an assist-quality issue (low stakes, no machinery).

## 2. The Two Layers

**Symbolic layer** — the registry as a graph:

- concept/skill nodes with concrete **realizations** (the operator's proven implementations, stacks, idioms, artifacts)
- composition edges (what a node is built from; what it enables)
- provenance (the real past work that grounds it) and freshness (last personal use)
- deterministic binding resolution: given the graph, the same intent resolves the same way

**Neural layer** — the LLM as:

- intent understanding (mapping fuzzy expression onto registry terms)
- expansion *within* bindings (generating in the operator's idioms, seeded by their artifacts)
- autocomplete graduated by assistance tier
- librarian routing (§6)

The claim/decision split survives as the discipline between the layers: neural proposes bindings and extractions; the symbolic layer records them; resolution thereafter is deterministic and auditable.

## 3. Node Anatomy

```yaml
id: web-scraping.python
kind: skill
realizations:
  - stack: [scrapy]
    canonical: true
    artifacts: [<links to past spider projects>]
    idioms: "how I structure spiders, pipelines, settings"
  - stack: [requests, beautifulsoup4]
    artifacts: [<links>]
composes_from: [python.core, http.basics, html.dom]
enables: [data-pipelines.ingestion]
provenance:
  - project: <real shipped work>
    date: 2024-11
assistance: full-expansion      # derived from provenance strength + freshness
freshness: last personally exercised 2026-05
```

The decisive difference from v1 units: the registry stores not just *that* the operator knows web scraping, but ***how* the operator does it** — their canonical stacks, idioms, and actual prior code.
"Web scrape, generalized" therefore always lowers to *their* implementation, never to some other one.

## 4. Binding Semantics (the core operation)

The operator expresses intent at any altitude.
Each term resolves against the registry:

1. **Realized** → expand into the operator's canonical realization (their stack, their idioms, seeded from their artifacts).
2. **Unrealized but decomposable** → assist only at the constituent level (e.g., Python with good autocomplete), and surface the missing concept as a **hole** — a named undefined symbol, first-class and visible, doubling as a frontier pointer into the learning loop.
3. **Outside the space** → the medium stays plain.

The system never *prevents*; it *declines to assist* beyond the operator's space.

**Graduated assistance** (the v1 mastery ladder, repurposed):

| Tier | Meaning | The medium provides |
|---|---|---|
| none | not in registry | nothing — plain medium |
| reference | encountered/explained | recall of the operator's own notes only |
| structural | mastered base skill | syntax/structure autocomplete (the "Python with nice autocomplete" tier) |
| full-expansion | realized skill with provenance | generation in the operator's idioms from their artifacts |
| generalize | strong transfer | novel composition and adaptation across contexts |

## 5. Evidence = Provenance from Real Work

"Provably know" changes its primary meaning: **mined from real past work**, not examined.

- **Bootstrap**: point the system at past repos, projects, and notes; it extracts candidate nodes + realizations with citations into the actual artifacts; the operator approves.
  (This subsumes v1's CLAIMED / verify-on-first-use machinery.)
- **New knowledge**: learned via the librarian, then proven by *shipping real work that uses it* — the work is the assessment; the artifact is the provenance; the node and its realization enter the registry together.
- **Freshness** replaces half-life machinery: nodes carry last-personal-use; stale nodes drop assistance tier gently rather than triggering exams.
- Formal assessments (the spike-validated sandbox/rubric machinery) remain available as an *optional instrument* — e.g., when the operator wants to certify something not yet shipped — but they are no longer the spine.

Friction budget: the loop is just doing one's work.

## 6. The Librarian (search without answers)

The complementary feature: AI-powered search that never reveals the answer.

**Contract: reveal sources and vocabulary, never synthesis.**

- Allowed: "HTML parsers exist for Python; here are the parser docs" — discovering what the ecosystem contains is what a good library does.
- Forbidden: "use beautifulsoup4, here's the code" — synthesis of the solution.
- The operator drives the decomposition themselves: scrapy docs → realize a parser is needed → look up parsers → arrive at bs4 by reading.

The librarian's backend already exists and is spike-validated: the curriculum-grounding and pathfinding engine (verified citations, typed prerequisite edges, minimal chains).
The no-distillation principle comes through the pivot *strengthened*: all learning happens against primary materials by construction.

A **reveal dial** needs design (§10): source locations only ↔ ecosystem vocabulary ↔ section-level pointers.
Worked examples and generated solutions are never on the dial.

## 7. The Loop (v2)

```text
COMPOSE in the medium
    ↓ hit a hole (undefined symbol)
LIBRARIAN routes to primary sources (no answers)
    ↓ operator learns by reading and building
SHIP real work using it
    ↓ artifact = provenance
NODE + REALIZATION enter the registry
    ↓
RICHER COMPOSITION (and the frontier around the new node materializes)
```

The three pressures survive intact: **holes** (demand — the v2 form of capability gaps), **aspirations** (pull), **adjacency expansion** (supply).

## 8. What Survives, Transforms, Dissolves

**Survives as-is:**

- lazy materialization (interior / frontier / cloud)
- three pressures on registry growth
- pathfinding with verified citations, typed curriculum-relative edges
- claim/decision split (neural proposes, symbolic decides)
- no-distillation principle
- granularity set by demand, corrected by split/merge
- provenance chain (now: medium output ← operator mastery ← real-work artifacts ← established materials)

**Transformed:**

- mastery ladder → assistance tiers
- capability gap → hole / undefined symbol
- teacher agent → librarian (routing only, never teaching from weights)
- half-life → freshness with gentle tier decay
- positive grounding (v1's "worker context built from unit content") → the entire system
- CLAIMED / verify-on-first-use → provenance mining with operator approval

**Dissolves:**

- worker agent as executor (the operator executes; the system is the medium)
- the deterministic permission checker as the spine
- the LLM auditor
- proctored exams as the evidence spine (demoted to optional instrument)

## 9. The Explicit Trade

v2 gives up the autonomous worker: no agent performing tasks inside the operator's mastery unattended.
Leverage now means a radically better medium for the operator's own execution, not delegation.
An agent layer can be added later *on top of* the compositional substrate — a far sounder foundation than gating — but that is deliberately out of scope now.

## 10. Open Decisions (round 4)

1. **The medium.**
   Where does compose mode live: editor integration (autocomplete/expansion in the IDE), a CLI/REPL, a notebook, a chat harness?
   This decision dominates the MVP's feel; "lightweight to play out ideas" suggests editor or notebook.
2. **Symbolic lint (the frictionless residue of gating).**
   Generation is grounded by construction, but the neural layer can still exceed the registry.
   Option: a deterministic, zero-LLM lint — e.g., any import/library not in a realization stack gets a quiet flag.
   Cheap, symbolic, non-blocking; very different from the rejected LLM auditor.
   Wanted?
3. **Provenance mining scope.**
   What corpus does bootstrap mine (repos, notes, past projects), and what does the approval flow look like — per node, per batch?
4. **The librarian's reveal dial.**
   Where exactly is the spoiler line: source locations only, ecosystem vocabulary, section-level pointers?
   And is the dial per-query, per-domain, or global?
5. **Ladder survivals.**
   Confirm assistance tiers as specified in §4, and freshness-based tier decay as the replacement for half-life re-probes.
