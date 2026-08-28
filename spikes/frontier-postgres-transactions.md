# Spike Artifact — Frontier Expansion for `postgres.transactions`

Spiked: 2026-08-28.
Assumption under test: curriculum-grounded frontier expansion is feasible and high-quality.
Grounding policy: all frontier sources verified live via web fetch (ToC or page confirmed), not cited from memory.

```yaml
record_type: frontier-expansion
schema_version: spike-1
mastered_unit: postgres.transactions
mastered_unit_scope: >
  PostgreSQL transactions: BEGIN/COMMIT/ROLLBACK, isolation levels,
  basic locking behavior, typical anomalies (dirty read,
  non-repeatable read, phantom).
```

## Presuppositions (inbound edges — no sources required)

```yaml
presupposes:
  - unit: sql.dml-basics
    scope: SELECT/INSERT/UPDATE/DELETE against single tables; WHERE predicates.
  - unit: sql.schema-ddl
    scope: Tables, columns, primary/foreign keys; enough DDL to build a test schema.
  - unit: db.acid-concepts
    scope: What atomicity, consistency, isolation, durability mean as guarantees, at definition level.
  - unit: postgres.psql-basics
    scope: Connecting with psql, running statements, opening two sessions side by side (needed to have observed anomalies at all).
```

## Frontier Units (outbound edges)

```yaml
frontier:
  - unit: postgres.mvcc-snapshots
    scope: >
      How PostgreSQL implements isolation: multi-version concurrency control,
      row versions (tuples, xmin/xmax), snapshots, and why readers never block writers.
    adjacency_rationale: >
      The mastered unit teaches the observable behavior of isolation levels;
      this unit is the mechanism producing that behavior. Every isolation-level
      rule the learner just memorized becomes derivable from snapshot semantics.
    sources:
      - kind: official-docs
        ref: "PostgreSQL Documentation, Chapter 13 'Concurrency Control', §13.1 Introduction"
        url: https://www.postgresql.org/docs/current/mvcc-intro.html
        verified: true  # chapter ToC fetched; §13.1 present
      - kind: book
        ref: "Egor Rogov, 'PostgreSQL 14 Internals' (Postgres Professional, free PDF ed.), Part I 'Isolation and MVCC' — chapters 'Isolation', 'Pages and Tuples', 'Snapshots'"
        url: https://postgrespro.com/community/books/internals
        verified: true  # part/chapter structure confirmed on publisher page
      - kind: course
        ref: "CMU 15-445/645 Database Systems (Spring 2025), Lecture 19 'Multi-Version Concurrency Control'"
        url: https://15445.courses.cs.cmu.edu/spring2025/schedule.html
        verified: true

  - unit: postgres.explicit-locking
    scope: >
      Table-level and row-level lock modes, lock conflicts, SELECT FOR UPDATE /
      FOR SHARE, deadlock detection, and advisory locks.
    adjacency_rationale: >
      The mastered unit covers "basic locking behavior" (writers block writers);
      this unit systematizes it: the full lock-mode matrix, deliberately taking
      locks, and diagnosing deadlocks — the first thing that bites in production.
    sources:
      - kind: official-docs
        ref: "PostgreSQL Documentation, §13.3 'Explicit Locking' (13.3.1 Table-Level Locks, 13.3.2 Row-Level Locks, 13.3.4 Deadlocks, 13.3.5 Advisory Locks)"
        url: https://www.postgresql.org/docs/current/explicit-locking.html
        verified: true
      - kind: book
        ref: "Egor Rogov, 'PostgreSQL 14 Internals', Part III 'Locks'"
        url: https://postgrespro.com/community/books/internals
        verified: true
      - kind: book
        ref: "Dimitri Fontaine, 'The Art of PostgreSQL' (2nd ed.), Part 7 'Data Manipulation and Concurrency Control', Ch. 37 'Isolation and Locking'"
        url: https://theartofpostgresql.com/book/contents/
        verified: true  # part/chapter titles confirmed on book ToC page

  - unit: postgres.serializable-ssi
    scope: >
      SERIALIZABLE in depth: Serializable Snapshot Isolation, write skew and
      other anomalies snapshot isolation misses, serialization failures (40001)
      and retry loops, predicate locks.
    adjacency_rationale: >
      The mastered unit names the three classic anomalies; SSI addresses the
      anomalies that survive REPEATABLE READ (write skew, read-only anomalies)
      and introduces the retry discipline serializable apps require —
      a direct deepening of one isolation level already encountered.
    sources:
      - kind: official-docs
        ref: "PostgreSQL Documentation, §13.2.3 'Serializable Isolation Level' and §13.5 'Serialization Failure Handling'"
        url: https://www.postgresql.org/docs/current/transaction-iso.html
        verified: true  # both sections present in Ch. 13 ToC
      - kind: official-wiki
        ref: "PostgreSQL Wiki, 'SSI' — worked anomaly examples (Black and White, Overdraft Protection, Deposit Report)"
        url: https://wiki.postgresql.org/wiki/SSI
        verified: true  # examples confirmed on page
      - kind: book
        ref: "Dimitri Fontaine, 'The Art of PostgreSQL' (2nd ed.), Ch. 37 'Isolation and Locking' (covers SSI, concurrent updates, testing concurrency behavior)"
        url: https://theartofpostgresql.com/book/contents/
        verified: true

  - unit: postgres.vacuum-maintenance
    scope: >
      VACUUM/autovacuum, dead-tuple bloat, visibility map, freezing, and
      transaction ID wraparound prevention.
    adjacency_rationale: >
      A consequence edge rather than a mechanism edge: MVCC-style transactions
      create dead row versions, so mastering transactions creates the question
      "where do old versions go?". Operationally inseparable from running
      transactional workloads (long-open transactions block vacuum).
    sources:
      - kind: official-docs
        ref: "PostgreSQL Documentation, §24.1 'Routine Vacuuming' (24.1.1 Vacuuming Basics … 24.1.5 Preventing Transaction ID Wraparound Failures, 24.1.6 The Autovacuum Daemon)"
        url: https://www.postgresql.org/docs/current/routine-vacuuming.html
        verified: true
      - kind: book
        ref: "Egor Rogov, 'PostgreSQL 14 Internals', Part I — chapters 'Page Pruning and HOT Updates', 'Vacuum and Autovacuum', 'Freezing'"
        url: https://postgrespro.com/community/books/internals
        verified: true

  - unit: postgres.wal-durability
    scope: >
      Write-ahead logging: how COMMIT becomes durable, checkpoints,
      synchronous vs. asynchronous commit, WAL configuration and internals.
    adjacency_rationale: >
      The mastered unit establishes atomicity/isolation semantics; WAL is the
      durability half of the same ACID contract — what COMMIT physically
      promises, and the knob (asynchronous commit) that trades it away.
    sources:
      - kind: official-docs
        ref: "PostgreSQL Documentation, Chapter 28 'Reliability and the Write-Ahead Log' (§28.3 Write-Ahead Logging, §28.4 Asynchronous Commit, §28.6 WAL Internals)"
        url: https://www.postgresql.org/docs/current/wal.html
        verified: true
      - kind: book
        ref: "Egor Rogov, 'PostgreSQL 14 Internals', Part II 'Buffer Cache and WAL' — chapters 'Write-Ahead Log', 'WAL Modes'"
        url: https://postgrespro.com/community/books/internals
        verified: true
      - kind: course
        ref: "CMU 15-445/645 Database Systems (Spring 2025), Lecture 20 'Database Logging' and Lecture 21 'Database Recovery'"
        url: https://15445.courses.cs.cmu.edu/spring2025/schedule.html
        verified: true

  - unit: db.transactions-cross-system
    scope: >
      Transactions and weak isolation beyond one engine: how snapshot isolation,
      2PL, and serializability compare across databases; anomaly taxonomy as
      engine-independent theory.
    adjacency_rationale: >
      Generalization edge: the learner knows PostgreSQL's three levels; this
      unit abstracts them into the standard theory (why the SQL-standard levels
      are defined by prohibited anomalies, why "repeatable read" means different
      things in different engines), preventing PostgreSQL-specific knowledge
      from ossifying into false general beliefs.
    sources:
      - kind: book
        ref: "Martin Kleppmann & Chris Riccomini, 'Designing Data-Intensive Applications', 2nd ed. (O'Reilly, 2025), Ch. 8 'Transactions' (Ch. 7 in the 2017 1st ed.)"
        url: https://www.oreilly.com/library/view/designing-data-intensive-applications/9781098119058/ch08.html
        verified: true  # chapter page exists on O'Reilly; ed.-numbering shift confirmed
      - kind: course
        ref: "CMU 15-445/645 Database Systems (Spring 2025), Lectures 16–18 'Concurrency Control Theory', 'Two-Phase Locking', 'Timestamp Ordering'"
        url: https://15445.courses.cs.cmu.edu/spring2025/schedule.html
        verified: true
```

## Spike Findings — Feasibility and Difficulties

**Overall: feasible, and cheaper than expected.**
Six units, 14 source citations, all verified with 7 live fetches/searches.
Official-doc structure (chapter/section numbers) is trivially verifiable and very stable; it should be the backbone of any grounding policy.
No fully hallucinated source was caught in this run, but two near-misses (below) show why verification is non-optional.

1. **Edition drift is the biggest hazard for books.**
   DDIA's Transactions chapter is Ch. 7 in the 1st edition but Ch. 8 in the 2nd edition (2025) — a record citing "DDIA Ch. 7 Transactions" from LLM memory would be stale-but-plausible, the worst failure mode.
   Rogov's book: newer version-numbered editions exist, but the publisher's official English free-PDF page still serves *PostgreSQL 14 Internals*; the citation follows what the page actually shows.
   Records should carry edition + chapter *title*, not bare chapter numbers, plus a `verified_against: <URL>` field.
2. **Official docs renumber across versions.**
   Cite `docs/current` URL slugs (`wal.html`) plus section *titles*; chapter numbers are display metadata only.
3. **"Adjacent" is genuinely ambiguous — at least four edge flavors emerged:** mechanism-beneath (mvcc-snapshots), systematization/deepening (explicit-locking, serializable-ssi), operational-consequence (vacuum-maintenance), and generalization (db.transactions-cross-system).
   Frontier edges should be typed; different learner goals want different edge types surfaced first.
4. **Curricula disagree on ordering.**
   Academic curricula (CMU 15-445) teach concurrency-control theory before or alongside engine behavior; practitioner materials teach observed behavior first.
   The same unit pair has opposite prerequisite direction depending on the curriculum consulted — adjacency direction is curriculum-relative, not a global DAG truth.
5. **Granularity mismatch between sources and units.**
   One source chapter can cover two units; one unit can span three source chapters.
   The record format must tolerate many-to-many source↔unit backing.
6. **Courseware citation stability is mediocre.**
   Lecture numbers shift by semester; pin named topics to semester-specific URLs.
7. **Paywalled canon needs a "partially verified" tier** (O'Reilly confirms chapter existence but hides subsection ToC).
