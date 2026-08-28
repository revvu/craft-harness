# Personal Capability-Gated AI Agents

## Core Idea

Build an AI agent that can **only act using knowledge, skills, and judgment that I have personally mastered**.

The model may have strong reasoning ability, but every factual premise, procedural primitive, and actionable capability it uses must trace back to something I have demonstrably learned.

The governing rule is:

> **Human mastery grants agent capability.**

This is stronger than simply restricting an agent to my notes. The system should model not just what information I have encountered, but what I actually understand, can apply, and have developed taste around.

---

## 1. Treat This as a Capability System, Not Just a Knowledge Base

A useful structure is:

```text
my-brain/
├── knowledge/       # things I understand
├── skills/          # things I can actually do
├── judgment/        # what I think "good" looks like
├── evidence/        # proof that I know/can do them
└── inbox/           # things I've encountered but haven't mastered
```

These are meaningfully different categories.

### Knowledge

```yaml
id: db.transactions
status: verified

understand:
  - atomicity
  - isolation
  - rollback
  - race conditions
```

### Skill

```yaml
id: db.safe-schema-migration

requires:
  - db.transactions
  - sql.postgres
  - db.indexes

can:
  - add nullable columns
  - backfill data safely
  - add indexes without blocking production

cannot:
  - design distributed migrations
  - operate Cassandra

tools:
  - psql
  - migrations

status: unlocked
```

### Judgment / Taste

```yaml
id: api.design-taste

principles:
  - prefer boring REST endpoints
  - avoid unnecessary abstractions
  - optimize for debuggability
  - APIs should be obvious from curl
```

Taste should be modeled separately from factual knowledge.

---

## 2. Model Mastery Explicitly

Do not use a binary "know / don't know" distinction.

A simple ladder:

| Level | Meaning | Agent access |
|---|---|---|
| 0 | encountered | none |
| 1 | can explain | reference only |
| 2 | can solve with help | planning |
| 3 | can independently apply | agent may use |
| 4 | strong transfer / taste | agent may generalize |

The important threshold is **Level 3**.

Reading something does not unlock it.

Taking notes does not unlock it.

Demonstrating competence does.

---

## 3. Require Every Agent Action to Carry a Capability Proof

Before the agent executes a task:

```text
USER
↓
TASK
↓
AGENT PRODUCES PLAN
↓
CAPABILITY CHECKER
↓
EXECUTION
```

For example:

```yaml
steps:

  - action: create session table
    requires:
      - postgres.schema-design
      - auth.session-storage

  - action: issue secure cookie
    requires:
      - http.cookies
      - auth.cookie-security

  - action: rotate sessions
    requires:
      - auth.session-rotation
```

The system checks the required skills against the unlocked registry:

```text
✓ postgres.schema-design
✓ auth.session-storage
✓ http.cookies
✓ auth.cookie-security
✗ auth.session-rotation
```

Execution stops at the missing capability.

The agent should then surface:

> **CAPABILITY GAP:** session rotation is required, but this capability has not been unlocked.

This creates a useful concept: **proof-carrying agent execution**.

Every action must be justified by explicit skills that authorize it.

---

## 4. Enforce the Constraint Outside the Model

Do not rely only on a system prompt like:

```text
Only use things I know.
```

The model will not reliably self-police this boundary.

Instead, enforce access externally.

A worker agent might have:

```text
Internet              ❌
web search            ❌
npm package discovery ❌
StackOverflow         ❌

verified knowledge    ✓
verified skills       ✓
repository            ✓
terminal              conditional
```

Even terminal usage can be mediated.

The principle is similar to capability security:

> Give the agent only the authority it has earned through my demonstrated mastery.

---

## 5. Use Skills as the Main Unit of Capability

Represent each skill as a directory:

```text
skills/
  postgres-schema-design/
      SKILL.md
      mastery.yaml
      examples/
      tests/

  python-data-analysis/
      SKILL.md
      mastery.yaml
      examples/
      tests/

  react-state-management/
      SKILL.md
      mastery.yaml
      examples/
      tests/
```

Example mastery metadata:

```yaml
mastery:
  level: 4

evidence:
  - type: independent-project
    artifact: projects/foo
  - type: explanation
    artifact: notes/react-state.md
  - type: challenge
    score: 9/10

verified_by: human
```

Important constraint:

> **The LLM cannot modify its own mastery level.**

Only I can promote a skill.

---

## 6. Separate the Teacher Agent from the Worker Agent

This separation is essential.

### Teacher Agent

The teacher can access:

```text
internet ✓
books ✓
documentation ✓
papers ✓
```

Its job is:

> Help me increase my capabilities.

It can teach, quiz, assign projects, challenge explanations, and recommend what to learn next.

### Worker Agent

The worker is constrained to:

```text
verified knowledge
verified skills
verified judgment
```

Its job is:

> Amplify what I can already do.

It should not:

- browse documentation on my behalf
- discover libraries I do not know
- silently repair gaps using pretrained knowledge
- introduce concepts I have never learned

A useful conceptual separation is:

```text
Research / Teacher Agent: world → me
Me: understanding / judgment / taste
Worker Agent: me → world
```

Instead of:

```text
world → AI → finished artifact
```

---

## 7. Promotion Should Require Demonstrated Work

Knowledge should move through a lifecycle:

```text
encounter
   ↓
learn
   ↓
explain
   ↓
practice
   ↓
demonstrate
   ↓
extract capability
   ↓
I approve
   ↓
unlock
```

Example:

```yaml
candidate_skill:
  unix.file-permissions

evidence:
  - explained chmod numerically
  - diagnosed three permission failures
  - independently configured SSH key permissions
```

Then:

```text
Promote unix.file-permissions to level 3?
```

I approve.

From then on, worker agents may use that capability.

This makes learning behave like unlocking abilities.

---

## 8. Allow Novel Composition

The agent should not be limited to reproducing things I have literally done before.

Suppose I know:

```text
Markov chains
Python
NumPy
simulation
queueing theory
```

The agent should be allowed to invent a new queueing simulator.

That is legitimate because:

```text
known primitives
        ↓
novel reasoning
        ↓
new result
```

What should be forbidden is:

```text
unknown external premise
        ↓
agent secretly knows it
        ↓
solution
```

So the restriction is:

> **Every premise and procedural primitive must bottom out in something I have mastered.**

The model can still be much better than me at composition, search over possibilities, deduction, planning, and critique.

---

## 9. Keep the MVP Simple

Do not begin with embeddings, a knowledge graph, or a complex ontology.

A good first version could be:

```text
Markdown
+
YAML frontmatter
+
Git
+
SQLite skill registry
+
small Python capability checker
+
Claude Code / Codex
```

The agent proposes required skills.

A deterministic checker verifies:

```python
required_skills <= unlocked_skills
```

Every execution trace records the skill IDs that authorized it.

Over time, dependencies can form a graph:

```text
                    ┌─ indexes
postgres ─ queries ─┤
                    └─ transactions ─ migrations
                                      │
                                      ▼
                             production-db
```

This graph becomes a representation of my actual competence.

---

## 10. Prior Ideas This Combines

This architecture sits at the intersection of several existing traditions.

### Mastery Learning / Knowledge Tracing

Question:

> What does this person know?

Useful idea:

Track individual knowledge components and only unlock later capabilities after demonstrated mastery.

### Capability Security

Question:

> What is this program authorized to do?

Useful idea:

Give programs only the authority they genuinely need rather than trusting them to behave correctly.

### Cognitive Mirror / Engineered Ignorance

Question:

> How do we prevent an AI's superior knowledge from replacing the learner's cognition?

Useful idea:

Deliberately restrict the AI's usable knowledge scope so that it reflects the learner rather than acting like an omniscient oracle.

### Modern Agent Skills

Question:

> How do we package reusable agent competence?

Useful idea:

Represent abilities as modular, testable skill packages that can be loaded when required.

### Unifying Rule

The new connection is:

> **Human mastery → grants agent capability**

---

## 11. The Best MVP

Start with **software engineering**.

Create roughly 20–30 skills that I already confidently possess.

Examples:

```text
python.basic-debugging
python.virtual-environments
git.branching
git.rebase
http.rest
http.cookies
postgres.basic-queries
postgres.transactions
react.components
react.state
nextjs.routing
ssh.basic-operation
linux.permissions
dns.cname-records
```

Then run a worker agent behind the capability checker for real projects.

Every time it reaches something unsupported, it emits:

```text
CAPABILITY GAP
```

That gap goes into a learning queue.

---

## 12. The Core Feedback Loop

```text
             ┌──────────────┐
             │    BUILD     │
             └──────┬───────┘
                    ↓
             capability gap
                    ↓
             ┌──────────────┐
             │    LEARN     │
             └──────┬───────┘
                    ↓
              demonstrate
                    ↓
               unlock skill
                    ↓
             agent becomes
             more powerful
                    │
                    └──────────────→ BUILD
```

The important design choice is to make **CAPABILITY GAP** a first-class output rather than an error.

The system should actively show me the frontier of what I know.

---

## Design Principle

The overall objective is not:

> Make the AI as capable as possible.

It is:

> **Make the AI as capable as possible subject to the constraint that its capabilities remain a legible amplification of my own understanding, skill, judgment, and taste.**

That preserves the cognitive work required to become better while still extracting enormous leverage from AI.
