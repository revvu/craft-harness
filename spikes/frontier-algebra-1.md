# Spike Artifact — Frontier Expansion & Path-Finding for `math.algebra-1`

Spiked: 2026-08-28.
Assumption under test: curriculum-grounded frontier expansion and prerequisite path-finding are feasible in a declarative/academic domain.

## Verification method

Sources verified live on 2026-08-28.
OpenStax book prefaces/ToCs and AoPS course pages render server-side and were fetched directly (full chapter/topic lists confirmed).
Common Core Appendix A (the "traditional pathway" document) confirmed via the Oregon DOE PDF and corroborating search extracts.
Khan Academy course pages are client-rendered SPAs and could not be fetched as HTML; course existence and unit-level content verified via search-surfaced unit URLs and snippets, not a full ToC fetch.
Community-college prerequisites verified against live catalog pages.
College Board AP Statistics revision page fetched directly.

## PART A — Frontier of `math.algebra-1`

The mastered unit maps almost exactly onto OpenStax *Elementary Algebra 2e* ch. 1–7 + 10 (linear equations/inequalities, graphs, systems, polynomials, factoring, quadratics) and Common Core Appendix A's High School Algebra I course.
Real curricula give five adjacent units.

### 1. `math.geometry`
- **Scope:** Congruence and similarity via transformations, proof, right-triangle trigonometry, circles, coordinate geometry, solid geometry, conditional probability.
- **Adjacency rationale:** The Common Core traditional pathway explicitly sequences Algebra I → Geometry → Algebra II as the standard US course order; Appendix A's Geometry course leans on Algebra I skills (e.g., completing the square reappears in circle equations). Khan Academy's math catalog places High School Geometry directly after Algebra 1.
- **Sources:**
  - Common Core State Standards for Mathematics, Appendix A: Designing High School Mathematics Courses (Traditional Pathway) — https://www.oregon.gov/ode/educator-resources/standards/mathematics/Documents/math-appendix-a-model-course-pathways.pdf
  - Khan Academy High School Geometry — https://www.khanacademy.org/math/geometry
  - AoPS *Introduction to Geometry* (Rusczyk) — https://artofproblemsolving.com/school/course/intro-geometry (verified prerequisite: completion of AoPS Introduction to Algebra)

### 2. `math.algebra-2` (intermediate algebra)
- **Scope:** Rational expressions and functions, radicals/rational exponents, quadratic techniques extended, exponential and logarithmic functions, conics, sequences and series.
- **Adjacency rationale:** Second course of the two-algebra traditional pathway; OpenStax *Intermediate Algebra 2e* directly continues *Elementary Algebra* — its ch. 1–6 are exactly the mastered unit, ch. 7–12 the new frontier material. AoPS sequences Intermediate Algebra after Intro Algebra/Intro Geometry.
- **Sources:**
  - OpenStax *Intermediate Algebra 2e*, ch. 7–12 — https://openstax.org/books/intermediate-algebra-2e/pages/preface
  - Khan Academy Algebra 2 — https://www.khanacademy.org/math/algebra2
  - Common Core Appendix A, Traditional Pathway Algebra II course (same PDF as above)

### 3. `math.exponentials-and-radicals`
- **Scope:** Rational exponents, radical expressions and irrational numbers, exponential growth/decay and exponential functions.
- **Adjacency rationale:** The immediate in-course boundary of the mastered scope: OpenStax *Elementary Algebra 2e* ch. 8–9 come right after factoring, and Khan Academy's Algebra 1 includes exponential growth & decay and irrational numbers units not in the mastered scope.
- **Sources:**
  - OpenStax *Elementary Algebra 2e*, ch. 8 "Rational Expressions and Equations", ch. 9 "Roots and Radicals" — https://openstax.org/books/elementary-algebra-2e/pages/preface
  - Khan Academy Algebra 1 — https://www.khanacademy.org/math/algebra

### 4. `math.statistics-descriptive` (univariate/bivariate data, S-ID)
- **Scope:** Representing and interpreting data on one and two variables: distributions, center/spread, scatterplots, linear fit and correlation.
- **Adjacency rationale:** Common Core Appendix A embeds a "Descriptive Statistics" unit in the Algebra I course itself (interpreting linear models leverages just-mastered linear functions); the traditional pathway spreads statistics across all three courses, making descriptive statistics a curriculum-sanctioned lateral move.
- **Sources:**
  - Common Core Appendix A, Traditional Pathway — same PDF as above
  - Khan Academy High School Statistics — https://www.khanacademy.org/math/probability

### 5. `math.counting-and-probability`
- **Scope:** Counting techniques (casework, permutations, combinations), Pascal's triangle, basic probability, Binomial Theorem.
- **Adjacency rationale:** AoPS's published sequence places *Introduction to Counting & Probability* in its Introductory tier alongside/after Intro Algebra — unlocked by Algebra 1-level mastery. Also the discrete on-ramp toward statistics.
- **Sources:**
  - AoPS *Introduction to Counting & Probability* — https://artofproblemsolving.com/school/course/intro-counting (verified topics and tier placement)

## PART B — Path from `math.algebra-1` to introductory statistics

**Goal unit:** `math.intro-statistics` — hypothesis testing and confidence intervals (plus sampling, distributions, CLT), per OpenStax *Introductory Statistics 2e* ch. 8 (Confidence Intervals) and ch. 9–10 (Hypothesis Testing).

**Minimal verified chain (2 hops):**

1. `math.algebra-1` (mastered)
2. `math.algebra-2` / intermediate algebra
   - Grounding: live community-college catalogs list Intermediate Algebra as *the* prerequisite for introductory statistics — Cuyahoga CC (https://catalog.tri-c.edu/course-descriptions/math/), San Jose City College (https://catalog.sjcc.edu/course-descriptions-information/course-descriptions/math/), San Bernardino Valley College (https://catalog.valleycollege.edu/courses/math/). OpenStax *Introductory Statistics 2e* preface: the book "assumes some knowledge of intermediate algebra" (https://openstax.org/books/introductory-statistics-2e/pages/preface).
3. `math.intro-statistics`
   - Grounding: OpenStax *Introductory Statistics 2e* ToC ch. 1–10; Khan Academy Statistics and Probability (https://www.khanacademy.org/math/statistics-probability); College Board AP Statistics (https://apcentral.collegeboard.org/courses/ap-statistics).

**Deliberately NOT in the chain:** Geometry.
The traditional HS pathway interposes Geometry between Algebra I and Algebra II, but no verified statistics prerequisite chain requires it — community colleges gate stats on intermediate algebra alone, and OpenStax names only intermediate algebra.
Geometry is a co-requisite artifact of school scheduling, not a knowledge prerequisite for statistics.

**Chain-shortening development (verified live):** effective the 2026-27 school year, College Board removes the second-year-algebra prerequisite for AP Statistics, recommending it "for any secondary school student who has successfully completed a first-year algebra course" (https://apcentral.collegeboard.org/courses/ap-statistics/future-revisions).
Under that framing the chain collapses to 1 hop.
The graph should carry the 2-hop chain as primary and the AP 1-hop edge as an alternative with a lower-confidence/newer annotation.

## Spike findings

- **Adjacency is mostly well-defined, but only per-curriculum.**
  Within a single named sequence, "what comes next" is explicit and needed no judgment.
  Judgment entered when merging sequences into one frontier (AoPS unlocks Counting & Probability from Algebra 1; school curricula defer formal probability).
  Edges should be tagged with their originating curriculum rather than pretending one universal graph exists.
- **Unit granularity mismatch.**
  The mastered scope equals OpenStax Elementary Algebra ch. 1–7+10 but is a strict subset of Khan Academy's Algebra 1.
  Lazily-materialized units need scope-matching against source ToCs, not name-matching against course titles.
- **Curriculum disagreement on ordering (real, documented):** traditional vs. integrated pathway in the same Appendix A document; whether Geometry precedes statistics (scheduling says yes, prerequisite structure says no); the AP Statistics prerequisite change means a path materialized last year would already be stale.
  Edges need provenance and revision dates.
- **Verification difficulty:** Khan Academy and OpenStax detail pages are client-rendered SPAs; ToCs could not be fetched directly and were confirmed via search-surfaced URLs and corroborating extracts.
  UNVERIFIED at full-ToC depth: exact current Khan Academy unit titles/ordering.
  A production materializer should use structured APIs or a headless browser; snippet-level verification is a real weak point of the "web search grounds the graph" assumption.
- **Positive surprise:** prerequisite chains toward stats are shorter than intuition suggests — 2 hops (shrinking to 1 under the AP revision), not the 3–4 a naive HS-sequence reading would insert.
