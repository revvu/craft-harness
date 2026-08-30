# Spike Artifact — Express-Assessment Cycle (Verify-on-First-Use)

Spiked: 2026-08-30.
Assumption under test: the express assessment used by verify-on-first-use is fast enough not to make the worker unusable, and its grading machinery actually discriminates mastery from plausible non-mastery.

## Design

Four stages, each a fresh agent:

1. **Generator** (teacher role): produce express assessments for two claimed units — `git.branching` (hands-on, deterministic sandbox repo + scripted grader) and `math.algebra-1` (4 written problems + itemized pre-registered rubric).
2. **Generator self-test**: the generator E2E-tested its own harness before delivery (sandbox setup run twice → identical SHAs; grader scored a reference solution 11/11 PASS and an untouched sandbox 2/11 FAIL; algebra keys verified numerically).
3. **Solver**: execute a competent and a flawed attempt per assessment — real git commands in real sandboxes for the git unit; faithful handwritten-style transcripts with internally consistent misconceptions for algebra.
4. **Blind grader**: graded the algebra attempts against the rubric only, with no knowledge of which attempt was which.

## Discrimination results

| Attempt | Ground truth | Score | Verdict | Correct? |
|---|---|---|---|---|
| git, competent | mastery | 11/11 | PASS → unlock L3 | ✓ |
| git, flawed (kept wrong conflict side, leftover `>>>>>>>` marker, ended on wrong branch) | non-mastery | 9/11 | FAIL (conflict-resolution gate C7 + C12 caught both planted defects) | ✓ |
| algebra A | mastery | 40/40 | PASS | ✓ |
| algebra B (4 consistent misconceptions: bad negative distribution, subtraction sign error, incomplete GCF, discriminant sign error) | non-mastery | 4/40 | FAIL (all three threshold conditions violated) | ✓ |

4/4 agreement between blind verdicts and ground truth.
Notably, the flawed git attempt scored 9/11 — above a naive 80% bar — and still correctly failed because the pass condition gated on the conflict-resolution checks specifically.
Thresholds tied to load-bearing sub-skills beat flat percentage cuts.

## Timing results

Machinery (measured wall-clock):

- Assessment generation, including the generator's own E2E harness test: ~3 m 47 s.
- Git grading: seconds (a shell script; zero discretion).
- Algebra blind grading: ~1 m 50 s of agent time.
- Total machinery overhead for a claim → verify → execute cycle: **≈ 5 minutes**.

Operator time:

- As generated: 20 min (git) and 24 min (algebra) — **2–2.5× over the ≤10-minute express budget**.
  The generator applied the budget per item, not per assessment: a specification bug worth pre-empting, and evidence that assessments naturally bloat toward thoroughness.
- **UNVERIFIED**: actual human solve time. Simulated attempts verify the grading machinery, not the human experience.
  Mandatory live verification step: the operator sits one real express assessment and times it before verify-on-first-use is declared viable.

## Findings

1. **The grading machinery discriminates.** Deterministic state-based checks (git) needed zero discretion; the written-work rubric held up blind, with the grader matching ground truth exactly.
2. **Performance-graded beats judgment-graded**, concretely: the git sandbox grader is a script; the algebra rubric, though it survived, generated five grader notes of ambiguity — two substantive (an internal contradiction in the "single slip = 4/10 vs. work-points-only" clause; a hole in the partial-credit ladder for "numeric GCF only"). At the margins, a borderline attempt would have hit those. Direct support for the constructive-assessment direction.
3. **Rubrics need their own red-team before pre-registration.** The ambiguities were found by grading synthetic flawed attempts — so make that the pipeline: generator must test its rubric against at least one synthetic flawed attempt before the assessment is registered. (The generator spontaneously did this for the git harness and not for the written rubric; the difference showed.)
4. **Express assessments need a hard per-assessment time budget** stated as a total, with item count derived from it (e.g., git express = one conflict-merge task + one branch-pointer probe; algebra express = 2 problems), not a per-item budget.
5. **Gate pass conditions on load-bearing checks, not flat scores.** The 9/11-but-FAIL git result is the pattern to keep: name the sub-skills that must pass outright.

## Cycle-time estimate (with the UNVERIFIED term marked)

claim → verify → execute ≈ 5 min machinery + [human solve: target ≤10 min, as-generated 20–24 min, ACTUAL UNVERIFIED] + seconds-to-2-min grading.
Verdict on the assumption: machinery is a non-issue; viability rests entirely on holding the human time budget, which requires finding 4's fix and one live timed sitting.
