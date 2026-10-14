<!-- solve-problem:a9a6e736-ade1-4469-b041-00d6667424fc -->
# Versioned million-problem registry with resolution tracking

A [Tangle Solve problem](https://solve.tangle.tools/problems/a9a6e736-ade1-4469-b041-00d6667424fc).

## What is the problem?

This is not a theorem to prove; it is an engineering and systems-design problem. The goal is to design, build, and validate a registry of open research problems (specifically in algorithms and complexity theory, but with a generalizable schema) satisfying all of the following requirements simultaneously:

1. **Scale:** at least 1,000,000 distinct granular problem records. "Granular" means each record is a single well-posed question (e.g., "Is there an O(n^{1.5}) algorithm for problem X in model M?"), not a broad topic area.
2. **Canonical statements:** each record carries a canonical, ideally machine-checkable formal statement, plus human-readable versions.
3. **Aliases:** explicit equivalence/alias links so that the same question posed in different vocabularies (e.g., a graph problem and its SAT encoding) resolves to one record.
4. **Model restrictions:** each statement is parameterized by its computational model / assumptions (RAM, Turing machine, comparison model, randomized vs. deterministic, etc.).
5. **Provenance:** full attribution of who posed the problem, when, where, and any reformulation history.
6. **Typed dependencies:** a directed graph where edges have types such as "reduces-to," "special case of," "generalizes," "equivalent to," with the graph queryable.
7. **Auditable resolution histories:** versioned records so that when a problem is solved, partially solved, or shown equivalent to another, the full history of the record's state is preserved and attributable.
8. **Validation contract:** the registry must demonstrably prevent duplicate re-proposals of solved problems and make cross-domain opportunities discoverable — these are acceptance criteria, not aspirations.

The implicit formal object is a version-controlled knowledge graph with a schema (record types, edge types, version semantics) plus an ingestion/verification pipeline and a query layer. Ambiguity: the census does not specify what "auditable resolution" means formally (human committee vs. automated proof checking), nor the write/governance model — the solver must choose and justify these.

## Why does it matter?

Solved problems get repeatedly re-proposed, and identical questions live in disjoint subfields under different names; a registry with alias and dependency tracking would make that waste visible and preventable. It would let a researcher or automated agent ask "what is the current best status of X, under model restriction Y, as of date Z?" and get a provenance-backed answer. It is also load-bearing infrastructure for any future effort to point automated theorem-proving agents at a prioritized, deduplicated corpus of open questions; without it, such agents re-tread solved ground.

## What's already known?

Precedents exist at much smaller scale or with weaker contracts:

- **The Open Problems Project (TOPP)** (Mitchell & O'Rourke): ~100 computational-geometry problems, flat list, editorially maintained; status updated in place with dates. No typed dependencies, no versioning contract, no formal statements.
- **SIGACT open-problems columns / Gasarch's archives:** curated prose lists, hundreds of items, no schema or machine-readability.
- **Open problem repositories in other fields** (e.g., the historical Hilbert-style lists, KoL: Erdős problems, OEIS-adjacent question collections): all under ~10,000 items or unstructured.
- **Knowledge-graph infrastructure:** Wikidata and versioned scientific knowledge graphs show that million-node graphs with provenance (e.g., statement-level referencing, Wikidata's revision model) are feasible; None of these systems has the full contract: canonical formal statements + typed dependency edges + resolution audit trails.
- **Formalization efforts** (Lean/mathlib, Mizar) show what canonical machine-checkable statements cost per problem — roughly person-days per statement, which is the central obstacle to reaching one million records.

No existing system meets all eight requirements at scale. The gap is integration plus a validated ingestion/deduplication pipeline, not any single novel data structure.

## What would count as solving it?

A referee (or program committee of an infrastructure track) would accept:

1. **A published schema** specifying record fields (canonical statement in a formal or controlled language, aliases, model-restriction parameters, provenance fields), edge types, and versioning semantics (immutable revisions, append-only resolution events).
2. **A deployed system** holding ≥10⁶ records meeting the schema, with demonstrated ingestion from at least the major problem corpora (TOPP, SIGACT lists, competition problem sets, textbook exercise corpora), OR a rigorously justified scaling argument plus a working system at ≥10⁵ records with measured ingestion cost extrapolating linearly.
3. **Duplicate-detection validation:** an experiment showing that alias/equivalence machinery catches a documented fraction of re-proposed solved problems (e.g., seeded re-submissions with measured precision/recall).
4. **Dependency-graph queries:** demonstration of cross-domain discovery, e.g., recovering a known reduction chain automatically.
5. **Resolution audit:** at least one full resolution event (problem solved/partially resolved) recorded with complete history and no destructive overwrites.

Partial results of interest: schema + dedup algorithm alone; or the alias/equivalence detection problem treated as a standalone ML/NLP subproblem.

## How to start

1. **Read Mitchell & O'Rourke's TOPP introduction and data format** — it is the closest small-scale analogue; enumerate exactly which of the eight requirements it lacks. Then skim a Wikidata schema discussion to understand statement-level provenance and revision models.
2. **Define the schema first, with versioning semantics borrowed from event sourcing** (append-only event log per record; current state = fold over events). This is almost certainly the right architecture; don't invent new versioning semantics.
3. **Prototype the hardest subproblem: alias detection.** Take 100–1000 known-equivalent problem statements (e.g., problems known equivalent via published reductions) and test whether embedding-similarity + structured metadata matching recovers the equivalences. This is the research core; everything else is standard data engineering.
4. **Main obstacle:** canonical statement authorship does not scale to 10⁶. Plan for tiered record quality (formal / controlled-natural-language / raw citation) with the tier recorded in the schema, and be upfront that "canonical" is aspirational for most records.

## Key references

- J.S.B. Mitchell and Joseph O'Rourke, "The Open Problems Project" (computational geometry open problem list, ~2001–ongoing; maintained at http://mitchell.web.unc.edu/open-problems/ or via CCG updates — verify current URL). Curated editorial list of ~100 problems with dated status updates; the closest precedent lacking formal structure.
- William Gasarch's SIGACT News open-problems columns and blog archive (https://blog.computationalcomplexity.org/ and ACM SIGACT News columns). Prose-format curated problem lists in complexity theory; demonstrates the editorial model and its scalability limits.
- Tao (2026), *Open Problems, Open Mathematics* — cited in the census record; (verify existence and full citation, source dated 2026 and could not be confirmed). Reported as a vision for large-scale open-mathematics problem infrastructure.
- Wikidata documentation on statements, references, and revision history (https://www.wikidata.org/wiki/Wikidata:Introduction) — the engineering precedent for million-entity graphs with per-statement provenance and full audit trails.
- Related literature on versioned scientific knowledge graphs and research provenance (e.g., work on nanopublications, Groth et al.) — the formal model for attributable, versioned scientific assertions. (verify specific papers before citing in output work.)

## Warnings

- **This is engineering, not mathematics.** Do not reframe it as a complexity-theoretic question; acceptance is a working validated system plus schema, not a proof.
- **The 10⁶ number is the trap.** Authoring one million canonical statements is infeasible by hand (~person-decades). Success requires tiered formality and bulk ingestion; state this trade-off explicitly rather than silently shipping garbage statements.
- **The Tao (2026) reference could not be verified**; do not cite it as established without checking.
- **Resolution validation ambiguity:** "auditable" can mean anything from committee sign-off to machine-checked proof. Pick a contract early; conflating these will cause scope creep.
- **Common dead end:** building a beautiful schema with no ingestion pipeline. The duplicate-detection and cross-domain discovery experiments are the actual deliverables that demonstrate the system works.

---
*`ALGO-000249` · flagged for review*

## Acceptance guidance

A referee (or program committee of an infrastructure track) would accept:

1. **A published schema** specifying record fields (canonical statement in a formal or controlled language, aliases, model-restriction parameters, provenance fields), edge types, and versioning semantics (immutable revisions, append-only resolution events).
2. **A deployed system** holding ≥10⁶ records meeting the schema, with demonstrated ingestion from at least the major problem corpora (TOPP, SIGACT lists, competition problem sets, textbook exercise corpora), OR a rigorously justified scaling argument plus a working system at ≥10⁵ records with measured ingestion cost extrapolating linearly.
3. **Duplicate-detection validation:** an experiment showing that alias/equivalence machinery catches a documented fraction of re-proposed solved problems (e.g., seeded re-submissions with measured precision/recall).
4. **Dependency-graph queries:** demonstration of cross-domain discovery, e.g., recovering a known reduction chain automatically.
5. **Resolution audit:** at least one full resolution event (problem solved/partially resolved) recorded with complete history and no destructive overwrites.

Partial results of interest: schema + dedup algorithm alone; or the alias/equivalence detection problem treated as a standalone ML/NLP subproblem.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/a9a6e736-ade1-4469-b041-00d6667424fc?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000249.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000249)
