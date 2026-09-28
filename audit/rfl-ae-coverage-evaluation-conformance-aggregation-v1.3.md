# RFL-AE — Coverage Evaluation & Conformance Aggregation Specification v1.3

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, JSON, graph, pipeline, and table passages collapsed into inline single-backtick runs; line breaks, indentation, and fenced structure restored. Restored from their inline form: the object list and stage chain (§801), the distinction chain (§802), the population JSON (§803), the discovery categories (§804), the identity bindings (§805), the digest formula (§806), the coverage record and coverage entry JSON (§807, §808), the coverage state list (§809), the formal predicates (§810, §840–§842, §927, §928, §969, §981, §982), the partial-coverage tree (§812), the selection criteria (§813), the validity rules (§814), the matching equations (§815, §819), the duplicate-execution tree (§821), the conflict example and its outcomes (§823), the policy dimensions (§825), the selection-exposure JSON (§826), the requirement classes (§827), the result ladder (§832–§838), the conformance-claim JSON (§843), the claim/binding enumerations (§844, §846, §847, §848), the aggregation-policy JSON (§849), the strict-required mapping (§850), the visibility and missingness rules (§853–§855), the negative-requirement example (§856), the mutation record JSON (§860), the metamorphic/property/randomized identity sets (§864–§866), the coverage accounting list (§867–§871), the retry and replay enumerations (§872–§877), the cross-language tree and matrix (§878, §879), the component and dependency chains (§881–§883), the freeze and evaluation identity sets (§887–§889), the invalidation list (§892), the report and schema enumerations (§898, §900–§903), the release-candidate freeze and rejection lists (§910, §911), the propagation chains (§915–§919, §977, §983), the policy-validation and evaluator-identity lists (§922, §959–§963), the evaluation contract (§965), the ambient-state list (§966), the coverage-store and import enumerations (§951–§953), the policy example (§924), the traceability matrix (§984), the fixture and mutation categories (§993, §994), the release record bindings (§996), and the final architecture diagram (§997). Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all thirteen recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Heading level note.** As with v1.2, v1.3 marks sections at `##` under a document-level `#` title. Preserved as supplied.
>
> **Standing.** Begins at **§801**, continuing [`v1.2`](rfl-ae-evidence-execution-record-v1.2.md) (§656–§800). v1.2 ends at §800 and v1.3 opens at §801 — **the range is contiguous**. The effective specification is now **§0–§997 across thirteen supplied documents**. **Citation rule: §1–§28 is occupied twice** (v0.1 §1–§28 and v0.2 §0–§40 share 28 numbers, zero identical headings) — citations in that range MUST name the document. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Note on §997.** The highest section number of the union is now **§997**. This is a numerical coincidence with the audited repository's closed *"997 sections"* cosmetic finding (Parts II–VIII) and has no relation to it: that finding concerned the upstream document's own section count, and it is **CLOSED**. Recorded here so the two are never conflated.
>
> **Status:** NORMATIVE
> **Predecessor:** RFL-AE v1.2 — Evidence & Execution Record
> **Range:** §801–§997
> **Purpose:** Define how immutable evidence is mapped onto a declared fixture population, how coverage is computed, how duplicate and missing evidence are handled, and how fixture-level results are aggregated into scoped conformance without hiding uncertainty or failure.

---

## §801 — Purpose

RFL-AE v1.3 defines the normative semantics of:

```text
CoveragePopulation
CoverageRecord
CoverageEntry
EvidenceSelection
RequirementEvaluation
ConformanceEvaluation
AggregationPolicy
CoverageDigest
ConformanceClaim
```

The purpose is to establish a mechanically auditable relation:

```text
DECLARED POPULATION
        ↓
IDENTITY RECONCILIATION
        ↓
EXECUTED POPULATION
        ↓
EVIDENCE POPULATION
        ↓
FIXTURE RESULTS
        ↓
REQUIREMENT EVALUATION
        ↓
COVERAGE
        ↓
CONFORMANCE
```

A test summary is not a coverage record.

---

## §802 — Fundamental Distinctions

The following are distinct:

```text
DECLARED
DISCOVERED
SCHEDULED
EXECUTED
OBSERVED
EVIDENCE-BEARING
COVERED
CONFORMANT
RELEASE-ELIGIBLE
```

Therefore:

```text
DISCOVERED ≠ DECLARED
EXECUTED ≠ COVERED
COVERED ≠ CONFORMANT
CONFORMANT ≠ RELEASE-ELIGIBLE
```

---

## §803 — Coverage Population

The coverage population is the immutable set of fixture identities against which coverage is evaluated.

```json
{
  "population_id": "population:conformance-v1",
  "fixture_corpus_digest": "sha256:...",
  "fixtures": [
    "fixture:A",
    "fixture:B",
    "fixture:C"
  ],
  "population_digest": "sha256:..."
}
```

The filesystem MUST NOT be treated as the authoritative population.

---

## §804 — Population Authority

The authoritative population MUST originate from the declared fixture manifest.

Filesystem discovery MAY identify:

```text
ORPHAN
MISSING
DUPLICATE
UNDECLARED
```

but MUST NOT silently modify the authoritative population.

---

## §805 — Population Identity

Population identity MUST bind:

```text
fixture_id
fixture_version
fixture_content_digest
kind
class
requirement
protocol_version
```

Changing any identity-bearing member MUST change the population digest.

---

## §806 — Population Digest

The canonical population digest is:

```text
population_digest = H(
  canonical(
    sort(
      fixture identity records
    )
  )
)
```

Fixture ordering MUST NOT affect the digest.

---

## §807 — Coverage Record

A coverage record MUST contain:

```json
{
  "coverage_id": "coverage:...",
  "population_id": "population:...",
  "population_digest": "sha256:...",
  "evidence": [],
  "entries": [],
  "policy_id": "coverage-policy:...",
  "coverage_digest": "sha256:..."
}
```

---

## §808 — Coverage Entry

Each declared fixture MUST have a coverage entry.

```json
{
  "fixture_id": "fixture:A",
  "declared": true,
  "executions": [
    "execution:1"
  ],
  "evidence": [
    "sha256:..."
  ],
  "result": "PASS",
  "coverage_status": "COVERED"
}
```

A fixture MUST NOT disappear from the coverage record merely because it was not executed.

---

## §809 — Coverage States

Required coverage states:

```text
NOT_DECLARED
DECLARED
SCHEDULED
EXECUTED
EVIDENCED
COVERED
UNCOVERED
PARTIAL
BLOCKED
INVALID
```

These states describe coverage, not semantic test result.

---

## §810 — Covered Fixture

A fixture is `COVERED` only when all applicable requirements for that fixture have valid evidence.

Formally:

```text
Covered(f) =
    Declared(f)
  ∧ RequiredEvidencePresent(f)
  ∧ EvidenceValid(f)
  ∧ RequiredChecksEvaluated(f)
```

---

## §811 — Uncovered Fixture

A declared fixture is `UNCOVERED` when required execution or evidence is absent.

Examples:

```text
fixture declared but never executed
```

or:

```text
fixture executed but evidence missing
```

or:

```text
evidence exists but is invalid
```

---

## §812 — Partial Coverage

A fixture is `PARTIAL` when some but not all applicable requirements have evidence.

Example:

```text
fixture:X
  ├── schema check      PASS
  ├── semantic check    PASS
  └── mutation check    MISSING
```

The fixture MUST NOT be represented as fully covered.

---

## §813 — Evidence Selection

A coverage evaluator MUST select evidence according to explicit policy.

Selection MUST consider:

```text
fixture identity
subject identity
protocol identity
verifier identity
predicate identity
evidence validity
execution scope
```

An arbitrary "latest passing result" is not a valid selection policy unless explicitly defined.

---

## §814 — Evidence Validity

Only evidence satisfying the applicable evidence-validation contract MAY contribute to coverage.

```text
INVALID evidence → excluded
STALE evidence → excluded from current coverage
PARTIAL evidence → contributes only where policy permits
UNKNOWN evidence → does not establish PASS
```

---

## §815 — Subject Matching

Evidence contributes to coverage only when:

```text
evidence.subject == population.subject
```

according to the subject identity relation defined by the release contract.

Historical evidence for another subject MUST remain historical.

---

## §816 — Protocol Matching

Evidence produced under an incompatible protocol version MUST NOT automatically satisfy current coverage.

Compatibility MUST be explicit.

---

## §817 — Fixture Matching

Evidence MUST reference the exact declared fixture identity.

Matching only:

```text
fixture name
```

is insufficient.

At minimum, matching MUST consider the immutable fixture identity.

---

## §818 — Predicate Matching

Evidence MUST use the predicate required by the fixture or an explicitly declared compatible predicate.

A different predicate producing the same Boolean result does not automatically satisfy the requirement.

---

## §819 — Verifier Matching

Where verifier identity is requirement-bearing:

```text
evidence.verifier == required.verifier
```

MUST hold.

A different verifier MUST be treated according to explicit compatibility rules.

---

## §820 — Execution Scope Matching

Evidence MUST establish that the executed scope covers the fixture requirement.

A broader claim cannot substitute for missing evidence of the required operation unless the protocol explicitly defines the relation.

---

## §821 — Duplicate Executions

Multiple executions for one fixture are permitted.

```text
fixture:A
  ├── execution:1
  ├── execution:2
  └── execution:3
```

The evaluator MUST preserve all executions.

It MUST NOT silently discard non-PASS results.

---

## §822 — Duplicate Evidence

Duplicate evidence identities MAY appear in storage or transport.

They MUST be deduplicated by immutable identity.

Duplicate storage entries MUST NOT increase coverage counts.

---

## §823 — Conflicting Evidence

If valid evidence for the same fixture produces conflicting results:

```text
execution:1 → PASS
execution:2 → FAIL
```

the evaluator MUST NOT arbitrarily choose PASS.

It MUST apply the declared aggregation policy.

If no policy resolves the conflict:

```text
CONFORMANCE = UNKNOWN
```

or:

```text
CONFORMANCE = BLOCKED
```

as defined by the gate policy.

---

## §824 — Evidence Recency

Recency MUST NOT be an implicit authority rule.

The newest evidence is not automatically the authoritative evidence.

Authority derives from the applicable identity and policy.

---

## §825 — Evidence Priority

If multiple evidence records are eligible, selection MUST be deterministic.

Possible policy dimensions include:

```text
subject identity
artifact identity
verifier identity
predicate identity
execution status
evidence validity
release scope
```

The policy MUST be versioned.

---

## §826 — No Hidden Selection

The evaluator MUST expose which evidence was selected.

```json
{
  "fixture_id": "fixture:A",
  "selected_evidence": [
    "sha256:..."
  ],
  "rejected_evidence": [
    {
      "evidence_id": "sha256:...",
      "reason": "STALE_SUBJECT"
    }
  ]
}
```

This prevents silent evidence substitution.

---

## §827 — Requirement Classes

Fixture requirements remain:

```text
REQUIRED
OPTIONAL
INFORMATIONAL
```

Coverage semantics differ by class.

---

## §828 — Required Fixture

A required fixture MUST be covered for complete conformance.

```text
required ∧ uncovered → incomplete conformance
```

---

## §829 — Optional Fixture

An optional fixture MAY be uncovered without automatically invalidating conformance.

The report MUST still preserve its uncovered state.

---

## §830 — Informational Fixture

Informational fixtures MAY contribute diagnostic information.

They MUST NOT automatically affect the conformance verdict.

---

## §831 — Critical Fixtures

A fixture MAY additionally be marked:

```text
CRITICAL
```

Criticality is orthogonal to requirement class.

A critical fixture MUST satisfy all critical evidence requirements before a release gate may pass.

---

## §832 — Fixture Result

Fixture result is derived from its required checks.

Required values:

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

A fixture-level result MUST NOT be directly supplied by an external summary.

---

## §833 — Fixture PASS

A fixture may be `PASS` only when:

```text
all_required_checks = PASS
```

and:

```text
evidence_closure = VALID
```

and:

```text
fixture_identity = MATCH
```

and:

```text
subject_identity = MATCH
```

and all other applicable requirements hold.

---

## §834 — Fixture FAIL

A fixture is `FAIL` when the applicable predicate evaluation establishes a required negative result.

```text
required predicate → false
```

A failed execution such as a crash is not automatically semantic FAIL.

---

## §835 — Fixture ERROR

A fixture is `ERROR` when evaluation could not complete normally due to an execution or verifier error.

Examples:

```text
checker crash
malformed verifier output
runner infrastructure failure
```

---

## §836 — Fixture UNKNOWN

A fixture is `UNKNOWN` when required information is unavailable and no stronger result can be established.

Examples:

```text
required dependency unavailable
required environment fact unobservable
external service unreachable
```

---

## §837 — Fixture SKIPPED

A fixture is `SKIPPED` only when execution was intentionally omitted under an explicit reason.

Examples:

```text
platform unsupported
conditional requirement not active
policy exclusion
```

A skipped required fixture remains uncovered unless policy explicitly exempts it.

---

## §838 — Fixture BLOCKED

A fixture is `BLOCKED` when a required prerequisite prevents evaluation.

Examples:

```text
authorization unavailable
required dependency unresolved
subject unavailable
verifier unavailable
```

---

## §839 — Result Ordering

The system MUST NOT assume:

```text
PASS > FAIL > ERROR
```

or any other informal severity ordering.

Aggregation semantics MUST be explicitly defined by policy.

---

## §840 — Requirement Evaluation

For every required fixture:

```text
RequirementSatisfied(f) = Covered(f) ∧ Result(f) = PASS
```

unless a versioned policy explicitly defines an alternative acceptable result.

---

## §841 — Complete Coverage

Complete coverage requires:

```text
∀ f ∈ RequiredPopulation:
    Covered(f)
```

A single required fixture missing evidence prevents complete coverage.

---

## §842 — Complete Conformance

Complete conformance requires:

```text
∀ f ∈ RequiredPopulation:
    RequirementSatisfied(f)
```

This is stronger than coverage.

```text
coverage   = evidence exists
conformance = required evidence establishes required result
```

---

## §843 — Conformance Claim

A conformance claim MUST identify:

```json
{
  "claim_id": "claim:...",
  "subject_id": "subject:...",
  "protocol_version": "1.3",
  "population_digest": "sha256:...",
  "coverage_digest": "sha256:...",
  "policy_id": "aggregation-policy:...",
  "status": "CONFORMANT"
}
```

---

## §844 — Claim Scope

A claim MUST state its scope.

Examples:

```text
FIXTURE
FIXTURE_SET
PROTOCOL
IMPLEMENTATION
RELEASE
```

A fixture claim MUST NOT be silently promoted to an implementation claim.

---

## §845 — Claim Derivation

A conformance claim MUST be derivable from:

```text
Population + Coverage + Evidence + AggregationPolicy
```

The claim MUST NOT be an independent input.

---

## §846 — Claim Digest

The canonical claim digest MUST bind:

```text
subject
protocol
population
coverage
policy
result
```

Changing any identity-bearing component MUST change the claim digest.

---

## §847 — Coverage Digest

Coverage digest MUST bind:

```text
population_digest
coverage entries
selected evidence identities
fixture results
coverage policy
```

Example:

```text
coverage_digest = H(canonical(
    population_digest
    + sorted(coverage entries)
    + policy identity
))
```

---

## §848 — Coverage Determinism

Given identical:

```text
population
evidence set
policy
```

the coverage evaluator MUST produce the same canonical coverage record.

---

## §849 — Aggregation Policy

Aggregation policy MUST be an explicit versioned object.

```json
{
  "policy_id": "aggregation-policy:strict-required-v1",
  "version": "1.0",
  "required_fixture_rule": "ALL_REQUIRED_PASS",
  "error_rule": "BLOCK",
  "unknown_rule": "BLOCK",
  "skipped_rule": "UNCOVERED",
  "optional_fixture_rule": "NON_BLOCKING"
}
```

---

## §850 — Strict Required Policy

The reference policy is:

```text
ALL_REQUIRED_PASS
```

with:

```text
FAIL    → FAIL
ERROR   → BLOCKED
UNKNOWN → BLOCKED
SKIPPED → UNCOVERED
BLOCKED → BLOCKED
missing → UNCOVERED
```

This is the recommended baseline for release conformance.

---

## §851 — Policy Is Semantic

Changing aggregation policy changes conformance semantics.

Therefore:

```text
policy-v1 ≠ policy-v2
```

unless semantic equivalence has been established.

---

## §852 — Policy Digest

Aggregation policy MUST have a content digest.

A conformance claim MUST bind the exact policy identity and digest.

---

## §853 — Failures Must Remain Visible

Coverage aggregation MUST preserve individual failure records.

A report MUST NOT reduce:

```text
FAIL
ERROR
UNKNOWN
BLOCKED
```

to:

```text
NOT_PASS
```

without retaining the original state.

---

## §854 — Failure Multiplicity

Multiple failures MUST remain distinguishable.

Example:

```text
F1 → FAIL
F2 → ERROR
F3 → UNKNOWN
```

must remain three separate facts.

---

## §855 — Missingness

Missing evidence is itself a result.

It MUST be represented explicitly.

```text
missing evidence ≠ PASS
missing evidence ≠ FAIL
missing evidence = UNCOVERED
```

---

## §856 — Negative Requirements

Some fixtures intentionally require failure.

Therefore conformance MUST evaluate the fixture's semantic expectation, not simply require a successful process exit.

Example:

```text
expected:   error_code = E_DUPLICATE_ID
actual:     error_code = E_DUPLICATE_ID
result:     PASS
```

The process may exit nonzero while the fixture passes.

---

## §857 — Positive Requirements

Positive fixtures generally require:

```text
expected semantic result == actual semantic result
```

A zero exit code alone is insufficient unless the fixture contract defines zero exit as the complete predicate.

---

## §858 — Near-Miss Fixtures

Coverage systems SHOULD include nearby valid and invalid fixtures.

Purpose:

```text
detect over-broad acceptance
detect over-broad rejection
```

A checker that rejects every input may pass only negative fixtures but is not conformant.

---

## §859 — Negative/Positive Balance

A conformance corpus SHOULD contain both:

```text
positive cases
negative cases
adversarial cases
boundary cases
```

Coverage of one category MUST NOT imply coverage of another.

---

## §860 — Mutation Coverage

Mutation results MUST be represented separately from ordinary fixture coverage.

```json
{
  "mutation_id": "mutation:...",
  "target": "predicate:evidence-valid-v1",
  "detected": true,
  "evidence_id": "sha256:..."
}
```

---

## §861 — Critical Mutation Requirement

For every mutation marked critical:

```text
detected = true
```

is required for release eligibility.

A surviving critical mutation blocks the applicable release gate.

---

## §862 — Mutation Population

Mutation coverage MUST use a declared mutation population.

Filesystem-discovered mutations are diagnostic only.

---

## §863 — Mutation Digest

The mutation population MUST have its own digest.

Changing the mutation population MUST trigger re-evaluation.

---

## §864 — Metamorphic Coverage

Metamorphic requirements MUST identify:

```text
generator
transformation
invariant
seed if applicable
```

A metamorphic fixture is covered only when the invariant was actually evaluated.

---

## §865 — Property Coverage

Property-based tests MUST record:

```text
property_id
generator_id
seed
case_count
failure_count
```

A reported case count without execution evidence is not coverage.

---

## §866 — Randomized Coverage

Randomized testing MUST distinguish:

```text
declared sample count
generated count
executed count
observed count
failed count
```

These values MUST NOT be collapsed into one number.

---

## §867 — Coverage Accounting

The coverage evaluator MUST expose at least:

```text
declared
required
optional
informational
executed
evidenced
covered
passed
failed
error
unknown
skipped
blocked
```

Counts MUST be derived from stable fixture identities.

---

## §868 — Count Invariant

For a declared population:

```text
declared_count = number of unique declared fixture IDs
```

Duplicate storage entries MUST NOT increase the count.

---

## §869 — Partition Invariant

Every declared fixture MUST belong to exactly one current coverage state at evaluation time.

```text
sum(state_counts) = declared_count
```

unless the report explicitly defines orthogonal dimensions.

---

## §870 — Evidence Count

Evidence count MUST count unique evidence identities.

It MUST NOT count:

```text
log lines
stdout occurrences
repeated database rows
```

as separate evidence.

---

## §871 — Execution Count

Execution count MUST count unique execution identities.

A retry with a new execution identity is a distinct execution.

---

## §872 — Retry Semantics

Retries MUST remain visible.

```text
attempt:1 → ERROR
attempt:2 → PASS
```

must not be represented as if only attempt 2 existed.

---

## §873 — Retry Policy

Whether a retry can satisfy a fixture requirement MUST be defined by policy.

Possible policy:

```text
any_valid_pass
```

or:

```text
all_attempts_must_be_successful
```

or:

```text
latest_valid_attempt
```

No policy is implied by implementation convenience.

---

## §874 — Flakiness

A fixture producing both PASS and FAIL across valid executions MUST be identifiable as potentially nondeterministic.

The evaluator SHOULD report:

```text
FLAKY
```

as diagnostic metadata.

Flakiness MUST NOT be hidden by selecting PASS.

---

## §875 — Flaky Conformance

Whether flaky evidence blocks conformance MUST be determined by explicit policy.

A release policy MAY require deterministic reproducibility.

---

## §876 — Reproducibility Coverage

When reproducibility is required, coverage MUST include replay evidence.

```text
original execution + replay execution + comparison
```

is required.

---

## §877 — Replay Result

Required replay outcomes:

```text
IDENTICAL
SEMANTICALLY_EQUIVALENT
DIFFERENT
NON_REPRODUCIBLE
BLOCKED
UNKNOWN
```

---

## §878 — Cross-Language Coverage

Cross-language fixtures MUST record each implementation tested.

Example:

```text
fixture:X
  ├── Rust → PASS
  ├── TypeScript → PASS
  └── Python → PASS
```

A single implementation result MUST NOT satisfy a multi-language requirement.

---

## §879 — Cross-Language Matrix

Where applicable:

| Fixture | Rust | TypeScript | Python | Required |
|---|---|---|---|---|
| F1 | PASS | PASS | PASS | yes |
| F2 | PASS | FAIL | PASS | yes |
| F3 | PASS | — | — | no |

The matrix is a projection of underlying evidence.

---

## §880 — Implementation Coverage

An implementation-level claim requires coverage for the fixture population assigned to that implementation.

Repository-wide test execution is insufficient if required components were not exercised.

---

## §881 — Component Coverage

For multi-component systems, the coverage population MAY be partitioned:

```text
kernel
schema
canonicalization
validation
transition
evidence
coverage
gate
CLI
```

Each partition MUST have explicit membership.

---

## §882 — Partitioned Coverage

Partitioned coverage MUST aggregate upward deterministically.

```text
component coverage
        ↓
subsystem coverage
        ↓
implementation coverage
        ↓
release coverage
```

A higher layer MUST NOT invent missing lower-layer coverage.

---

## §883 — Coverage Dependency Graph

Coverage dependencies MAY be represented as:

```text
C_transition
       ↓
C_execution
       ↓
C_evidence
       ↓
C_coverage
       ↓
C_conformance
       ↓
C_release
```

If an upstream required coverage dependency is invalid, dependent claims MUST be blocked.

---

## §884 — Coverage Monotonicity

Adding valid evidence MAY increase coverage.

Removing valid required evidence MUST NOT leave coverage unchanged.

Changing evidence to invalid MUST NOT preserve a dependent PASS silently.

---

## §885 — Evidence Replacement

Replacing evidence is a new evaluation.

Old coverage records remain historical.

A new coverage digest MUST be produced.

---

## §886 — Coverage Immutability

A frozen coverage record MUST NOT be edited in place.

Any change produces a new coverage identity.

---

## §887 — Coverage Freeze

Before conformance evaluation:

```text
population
evidence
policy
```

MUST be frozen.

Changes after freeze invalidate the previous conformance evaluation.

---

## §888 — Freeze Digest

The evaluator MUST retain:

```text
population_digest
evidence_population_digest
policy_digest
```

These collectively define the evaluation input.

---

## §889 — Conformance Evaluation Identity

Every conformance evaluation SHOULD have:

```text
evaluation_id
```

bound to:

```text
population_digest
evidence_population_digest
policy_digest
subject_identity
```

---

## §890 — Re-evaluation

A changed:

```text
subject
fixture corpus
evidence
verifier
predicate
policy
```

MUST trigger a new conformance evaluation when release semantics depend on that component.

---

## §891 — Stale Coverage

Coverage becomes stale when any identity-bearing input changes.

Required state:

```text
STALE
```

Stale coverage MUST NOT satisfy a current release gate.

---

## §892 — Coverage Invalidation

Coverage MUST be invalidated when:

```text
population digest changes
subject digest changes
required verifier changes
required predicate changes
aggregation policy changes
required evidence is revoked
```

---

## §893 — Historical Coverage

Historical coverage MAY remain queryable.

It MUST retain its original:

```text
subject
population
evidence
policy
evaluation
```

identity.

---

## §894 — Comparison of Evaluations

Two evaluations MAY be compared only after their identity dimensions are checked.

Example:

```text
evaluation A: population P1
evaluation B: population P2
```

Raw percentage comparison is not automatically meaningful.

---

## §895 — Coverage Delta

A coverage delta SHOULD identify:

```text
added fixtures
removed fixtures
new evidence
invalidated evidence
new failures
resolved failures
new blocked items
```

---

## §896 — Delta Is Not Conformance

An improvement in coverage does not imply conformance.

```text
coverage_delta > 0
```

does not imply:

```text
conformance = PASS
```

---

## §897 — Report Generation

Human-readable reports MUST be projections of canonical coverage data.

The report generator MUST NOT independently recompute semantic results using different logic.

---

## §898 — Machine-Readable Output

The normative machine output SHOULD be JSON.

Human-readable formats MAY include:

```text
Markdown
HTML
terminal tables
CSV
```

but remain projections.

---

## §899 — Canonical Coverage JSON

Canonical coverage JSON MUST be deterministic.

Equivalent coverage records MUST produce identical canonical bytes.

---

## §900 — Coverage Schema

The schema MUST distinguish:

```text
population identity
fixture identity
execution identity
evidence identity
result
coverage state
policy identity
```

No single Boolean may replace these dimensions.

---

## §901 — Coverage Validation

Coverage validation MUST verify:

```text
schema
population identity
fixture membership
evidence references
evidence validity
subject matching
policy identity
result derivation
coverage digest
```

---

## §902 — Self-Reported Coverage

A producer-provided:

```json
{
  "coverage": 1.0
}
```

is not authoritative coverage.

The evaluator MUST derive coverage from fixture identities and evidence.

---

## §903 — CI Summary

CI summary output MAY be imported as diagnostic evidence.

It MUST NOT automatically become release-grade coverage unless:

```text
CI execution identity
subject identity
artifact identity
fixture population
verifier identity
evidence identity
```

are bound.

---

## §904 — CI Authority

When CI is designated execution authority:

```text
local run
```

does not establish:

```text
CI execution
```

and:

```text
CI claimed PASS
```

does not establish:

```text
verified evidence
```

without the required execution/evidence chain.

---

## §905 — Local Evidence

Local execution MAY support diagnosis and development.

Release claims requiring CI authority MUST use CI-bound evidence.

---

## §906 — Remote Evidence

Remote execution MUST identify the remote executor sufficiently for the applicable trust model.

A screenshot of a green check is not equivalent to a machine-readable execution record.

---

## §907 — Branch Identity

Branch names are mutable.

Coverage MUST bind to immutable source/artifact identity rather than branch name alone.

---

## §908 — Commit Identity

A commit SHA MAY identify source revision.

It MUST NOT automatically identify the executable artifact.

Source-to-artifact binding remains a separate requirement.

---

## §909 — Tag Identity

Tags MAY be mutable unless protected and policy-bound.

A tag name alone is not immutable evidence identity.

---

## §910 — Release Candidate Coverage

A release candidate MUST freeze:

```text
source identity
artifact identity
fixture population
verifier population
predicate population
evidence population
coverage policy
```

---

## §911 — Release Candidate Rejection

The following MUST block release conformance:

```text
required fixture uncovered
critical evidence invalid
critical mutation survives
required evidence stale
population mismatch
subject mismatch
policy mismatch
coverage digest invalid
```

---

## §912 — Coverage Gate Inputs

A coverage gate MUST consume only canonical machine-readable records.

It MUST NOT parse human prose as authoritative conformance input.

---

## §913 — Gate Purity

The coverage evaluator SHOULD be a pure function:

```text
evaluate(
  population,
  evidence,
  policy
) → coverage
```

The evaluator SHOULD NOT mutate evidence.

---

## §914 — Deterministic Aggregation

For identical inputs:

```text
evaluate(P,E,R) == evaluate(P,E,R)
```

must hold semantically and canonically.

---

## §915 — Error Propagation

Errors MUST propagate upward according to policy.

Example:

```text
checker ERROR
  ↓
fixture ERROR
  ↓
coverage BLOCKED
  ↓
conformance BLOCKED
```

unless policy explicitly defines another transition.

---

## §916 — Unknown Propagation

Unknown information MUST remain unknown.

```text
UNKNOWN
  ↓
UNKNOWN/BLOCKED
```

It MUST NOT become PASS through aggregation.

---

## §917 — Skipped Propagation

Skipped required fixtures remain uncovered.

They MUST NOT become PASS merely because the runner completed successfully.

---

## §918 — Blocked Propagation

Blocked prerequisites MUST prevent dependent claims unless an explicit policy provides a valid exemption.

---

## §919 — Fail Propagation

A required semantic FAIL MUST propagate to conformance failure unless the fixture is explicitly non-blocking.

---

## §920 — Optional Failure

An optional fixture MAY fail without invalidating strict required conformance.

The failure MUST remain visible in the report.

---

## §921 — Informational Failure

Informational failures MUST remain diagnostic.

They MUST NOT affect conformance unless explicitly promoted by policy.

---

## §922 — Policy Overrides

A policy MAY alter default aggregation semantics.

Every override MUST be:

```text
explicit
versioned
digest-bound
auditable
```

---

## §923 — No Implicit Exceptions

The evaluator MUST NOT contain hidden special cases such as:

```text
if fixture_id == ...
    ignore failure
```

unless represented as explicit policy data.

---

## §924 — Policy Explainability

Every conformance result SHOULD expose the rules that produced it.

Example:

```text
required fixtures: 997
covered: 997
passed: 997
failed: 0
blocked: 0
unknown: 0
policy: strict-required-v1
result: CONFORMANT
```

---

## §925 — Claim Explanation

A conformance claim SHOULD expose:

```text
why it passed
why anything failed
why anything was excluded
why anything was blocked
```

This explanation is derived data.

---

## §926 — No Verdict From Counts Alone

Counts cannot establish conformance without identity and policy.

```text
997/997
```

is insufficient if:

```text
population identity unknown
```

or:

```text
evidence identity unknown
```

or:

```text
policy identity unknown
```

---

## §927 — Complete Conformance Predicate

Define:

```text
Conformant(S,P,E,R) =
    SubjectValid(S)
  ∧ PopulationValid(P)
  ∧ EvidenceValid(E)
  ∧ CoverageComplete(P,E,R)
  ∧ RequiredResultsPass(P,E,R)
  ∧ CriticalRequirementsSatisfied(P,E,R)
```

Every predicate component MUST be independently evaluable.

---

## §928 — Release Eligibility Predicate

Release eligibility is stronger:

```text
ReleaseEligible =
    Conformant
  ∧ ArtifactIdentityValid
  ∧ RequiredMutationCoverage
  ∧ RequiredReplayCoverage
  ∧ RequiredCIEvidence
  ∧ ReleaseGatePass
```

This specification does not redefine the release gate itself.

---

## §929 — Conformance vs Release

Therefore:

```text
CONFORMANT ≠ RELEASED
```

A conformant implementation may still lack release evidence.

---

## §930 — Coverage Evidence

Every coverage claim MUST reference the evidence records from which it was derived.

No unbound aggregate result is release-grade evidence.

---

## §931 — Coverage Evidence Digest

Coverage evidence MUST include:

```text
population_digest
evidence_population_digest
policy_digest
coverage_digest
```

---

## §932 — Reproducible Evaluation

A second evaluator receiving the same canonical inputs MUST reproduce the same coverage result.

Cross-implementation disagreement is a conformance defect.

---

## §933 — Cross-Language Coverage Evaluators

If Rust and TypeScript evaluators both exist:

```text
RustEvaluator(P,E,R)
```

and:

```text
TypeScriptEvaluator(P,E,R)
```

MUST agree on canonical coverage semantics.

---

## §934 — Differential Testing

Differential testing SHOULD execute independent evaluators against the same corpus.

Differences MUST be classified:

```text
implementation defect
schema interpretation defect
canonicalization defect
specification ambiguity
```

---

## §935 — Specification Ambiguity

If two conforming evaluators can reasonably produce different results because the specification is ambiguous:

```text
CONFORMANCE = BLOCKED
```

until the ambiguity is resolved.

---

## §936 — Mutation of Aggregation Logic

Aggregation logic MUST be mutation-tested.

Critical mutations include:

```text
ignore required fixture
convert FAIL → PASS
convert UNKNOWN → PASS
convert SKIPPED → PASS
ignore stale evidence
accept wrong subject
accept wrong population
ignore policy digest
drop evidence identity
```

---

## §937 — Aggregation Mutation Requirement

Every critical aggregation mutation MUST be detected.

A surviving mutation blocks release.

---

## §938 — Fixture Manifest Reconciliation

The evaluator SHOULD compare:

```text
manifest population
filesystem population
execution population
evidence population
```

and expose differences.

---

## §939 — Orphan Evidence

Evidence referencing a fixture not present in the declared population MUST be marked:

```text
ORPHAN
```

It MUST NOT increase current coverage.

---

## §940 — Missing Manifest Fixture

A manifest fixture without corresponding execution/evidence MUST remain explicitly uncovered.

---

## §941 — Undeclared Fixture

A filesystem fixture absent from the manifest is:

```text
UNDECLARED
```

It MUST NOT silently expand the population.

---

## §942 — Duplicate Fixture Identity

Two declared fixtures with the same immutable identity are a manifest error.

They MUST NOT be counted twice.

---

## §943 — Duplicate Content, Different IDs

Two fixtures MAY contain identical content but different stable IDs if they have different semantic roles.

The evaluator MUST preserve fixture identity.

---

## §944 — Same ID, Different Content

If:

```text
fixture_id = X
```

but content digest changes:

```text
old_digest != new_digest
```

the fixture identity is changed.

The old and new fixtures MUST NOT be silently treated as the same immutable test subject.

---

## §945 — Corpus Evolution

Changing the fixture corpus creates a new corpus identity.

```text
corpus-v1 → corpus-v2
```

must not rewrite historical conformance results.

---

## §946 — Version Compatibility

A coverage record MUST declare protocol compatibility.

No implicit compatibility between major protocol versions is permitted.

---

## §947 — Evidence Population Freeze

The evidence population MUST be frozen before final conformance evaluation.

Late-arriving evidence produces a new evaluation.

---

## §948 — Late Evidence

Evidence arriving after evaluation:

```text
old evaluation + late evidence
```

MUST NOT mutate the old evaluation.

It creates:

```text
new evaluation
```

---

## §949 — Evaluation Reproducibility

A historical evaluation MUST remain reproducible from its frozen inputs.

If referenced immutable objects disappear, the evaluation becomes non-replayable and MUST expose that fact.

---

## §950 — Missing Historical Evidence

Historical evidence that cannot be retrieved MUST NOT be reconstructed from summary counts.

The evaluation MUST remain:

```text
NON_REPRODUCIBLE
```

or:

```text
INCOMPLETE
```

as applicable.

---

## §951 — Coverage Store

A coverage store SHOULD use immutable identifiers:

```text
coverage_id
population_digest
evidence_population_digest
policy_digest
```

---

## §952 — Coverage Store Integrity

Stored coverage MUST be revalidated before use.

At minimum:

```text
coverage_digest
population_digest
policy_digest
evidence references
```

must be checked.

---

## §953 — Coverage Import

Import sequence:

```text
parse
  ↓
schema validate
  ↓
digest validate
  ↓
population resolve
  ↓
evidence resolve
  ↓
policy resolve
  ↓
semantic evaluate
  ↓
store
```

---

## §954 — Coverage Export

Export MUST preserve:

```text
population identity
evidence identity
policy identity
results
coverage state
digest
```

---

## §955 — Coverage Portability

Coverage MAY be transferred between environments provided all referenced immutable identities remain resolvable.

---

## §956 — Coverage Transport

Transport metadata MUST NOT alter semantic coverage identity.

---

## §957 — Report Integrity

Human-readable reports SHOULD contain:

```text
population_digest
coverage_digest
policy_digest
subject_digest
evaluation_id
```

This allows the report to be traced to canonical data.

---

## §958 — Machine/Human Consistency

Human-readable reports MUST be generated from the same canonical machine record used by the gate.

Two independently computed verdicts are prohibited.

---

## §959 — Audit Trail

The evaluator SHOULD preserve:

```text
input population
input evidence set
policy
evaluation result
evaluation timestamp
evaluator identity
evaluator artifact digest
```

---

## §960 — Evaluator Identity

Coverage evaluation itself is executable verification.

Therefore the evaluator MUST have:

```text
evaluator_id
version
source_revision
artifact_digest
```

where release-grade evidence requires verifier identity.

---

## §961 — Evaluator/Policy Separation

The evaluator implements policy.

The policy defines semantic aggregation.

```text
EVALUATOR ≠ POLICY
```

Changing either may alter the result.

---

## §962 — Policy as Data

Where practical, aggregation policy SHOULD be machine-readable.

This prevents hidden semantic rules in evaluator code.

---

## §963 — Policy Validation

Policy validation MUST verify:

```text
schema
required fields
recognized states
recognized transitions
absence of contradictory rules
```

---

## §964 — Policy Conflict

If policy contains contradictory rules:

```text
POLICY_INVALID
```

and conformance evaluation MUST be blocked.

---

## §965 — Evaluation Contract

The canonical evaluation function is:

```text
Evaluate(
    Population,
    EvidenceSet,
    Policy
) -> CoverageRecord
```

Then:

```text
Conformance(
    CoverageRecord,
    Policy
) -> ConformanceClaim
```

---

## §966 — No Hidden State

Evaluation MUST depend only on declared inputs.

Ambient mutable state MUST NOT influence canonical results.

Examples include:

```text
current wall clock
filesystem ordering
database row ordering
network response
environment variable
```

unless explicitly declared as evaluation inputs.

---

## §967 — Ordering Independence

Where semantics are set-based:

```text
permute(fixtures)
permute(evidence)
```

MUST NOT change the result.

---

## §968 — Duplicate Independence

Adding an exact duplicate of an existing immutable evidence record MUST NOT change conformance.

---

## §969 — Invalid Evidence Monotonicity

Adding invalid evidence MUST NOT improve conformance.

Formally:

```text
Conformance(E ∪ InvalidEvidence)
```

MUST NOT be stronger than:

```text
Conformance(E)
```

---

## §970 — Valid Evidence Monotonicity

Adding valid evidence MAY improve coverage.

It MUST NOT convert an established semantic FAIL to PASS unless the aggregation policy explicitly defines multi-attempt resolution.

---

## §971 — Evidence Removal

Removing required valid evidence MUST NOT preserve complete coverage.

---

## §972 — Population Expansion

Adding a new REQUIRED fixture to the population MUST NOT preserve complete conformance unless valid evidence satisfies the new requirement.

---

## §973 — Population Contraction

Removing a fixture creates a new population identity.

Historical conformance remains bound to the previous population.

---

## §974 — Policy Tightening

A stricter policy MAY invalidate a previously conformant evaluation.

This does not imply that the historical evaluation was incorrectly recorded.

---

## §975 — Policy Relaxation

A weaker policy MAY produce a different conformance result.

The resulting claim MUST identify the weaker policy.

---

## §976 — No Retroactive Semantics

Current policy MUST NOT be applied to historical claims without creating a new evaluation.

---

## §977 — Evidence Revocation Propagation

If evidence is revoked:

```text
Evidence
  ↓
Coverage
  ↓
Conformance
  ↓
Release
```

all dependent records MUST be re-evaluated.

---

## §978 — Subject Revocation Propagation

If the artifact or subject identity is invalidated, dependent coverage and conformance claims MUST become stale or invalid according to policy.

---

## §979 — Verifier Revocation Propagation

If a verifier is revoked or its predicate identity invalidated, evidence depending on it MUST be re-evaluated.

---

## §980 — Predicate Change Propagation

A changed predicate MUST create a new predicate identity.

Historical evidence remains historical.

---

## §981 — Coverage Dependency Closure

A coverage record is valid only if every required dependency resolves.

```text
CoverageValid =
    PopulationValid
  ∧ EvidenceDependenciesValid
  ∧ PolicyValid
  ∧ SubjectValid
```

---

## §982 — Conformance Dependency Closure

A conformance claim is valid only if:

```text
CoverageValid
  ∧ RequiredResultsSatisfied
  ∧ CriticalRequirementsSatisfied
```

---

## §983 — Evidence-to-Claim Traceability

Every conformance claim MUST be traceable:

```text
Claim
  ↓
Coverage
  ↓
Fixture
  ↓
Evidence
  ↓
Execution
  ↓
Observation
```

A claim without this path is not release-grade.

---

## §984 — Claim-to-Fixture Matrix

The evaluator SHOULD provide a traceability matrix:

| Claim | Fixture | Evidence | Result | Requirement |
|---|---|---|---|---|
| C1 | F1 | E1 | PASS | REQUIRED |
| C1 | F2 | E2 | FAIL | REQUIRED |
| C1 | F3 | — | UNCOVERED | REQUIRED |

---

## §985 — Claim-to-Artifact Binding

Implementation claims MUST identify the exact artifact against which evidence was generated.

---

## §986 — Claim-to-Source Binding

Where source identity is required:

```text
claim → artifact → source revision
```

must be traceable.

---

## §987 — Claim-to-Policy Binding

Every conformance claim MUST identify the exact aggregation policy.

---

## §988 — Claim-to-Population Binding

Every conformance claim MUST identify the exact fixture population.

---

## §989 — Claim-to-Evidence Binding

Every conformance claim MUST identify the evidence population or coverage digest from which it was derived.

---

## §990 — Claim-to-Evaluator Binding

Release-grade claims SHOULD identify the evaluator artifact.

This allows independent reproduction.

---

## §991 — Independent Re-Evaluation

A release candidate SHOULD be evaluated by an independent evaluator.

Agreement strengthens the evidence chain.

Disagreement MUST block release until resolved.

---

## §992 — Differential Conformance

Independent evaluators MUST compare canonical:

```text
CoverageRecord
ConformanceClaim
```

rather than human-readable reports.

---

## §993 — Conformance Fixture

The conformance evaluator itself MUST have fixtures.

Required categories include:

```text
all-pass
required-fail
required-missing
required-error
required-unknown
required-skipped
optional-fail
duplicate-evidence
stale-evidence
wrong-subject
wrong-population
wrong-policy
conflicting-evidence
```

---

## §994 — Aggregation Mutation Fixtures

At minimum, the mutation suite MUST detect:

```text
drop-required-fixture
force-pass
ignore-failure
ignore-unknown
ignore-blocked
accept-stale
accept-wrong-subject
accept-wrong-population
ignore-policy
```

---

## §995 — Conformance Evaluation Gate

The evaluator MUST NOT report:

```text
CONFORMANT
```

when a required critical mutation survives.

---

## §996 — Coverage Release Record

A release-grade coverage record SHOULD bind:

```text
protocol_digest
subject_digest
artifact_digest
fixture_population_digest
evidence_population_digest
policy_digest
evaluator_digest
coverage_digest
conformance_digest
```

---

## §997 — Final Coverage Architecture

The complete relation is:

```text
              FROZEN FIXTURE MANIFEST
                         │
                         ▼
                 POPULATION DIGEST
                         │
                         ▼
              ┌─────────────────────┐
              │  EXECUTION/EVIDENCE │
              │      POPULATION     │
              └──────────┬──────────┘
                         │
                         ▼
                 IDENTITY MATCHING
                         │
                         ▼
                  EVIDENCE VALIDITY
                         │
                         ▼
                  FIXTURE RESULTS
                         │
                         ▼
                 COVERAGE RECORD
                         │
                         ▼
               AGGREGATION POLICY
                         │
                         ▼
               CONFORMANCE CLAIM
                         │
                         ▼
                  RELEASE GATE
```

The governing laws are:

```text
NO DECLARED POPULATION → NO COMPLETE COVERAGE
NO FIXTURE IDENTITY → NO STABLE COVERAGE SUBJECT
NO VALID EVIDENCE → NO COVERED FIXTURE
NO REQUIRED FIXTURE COVERAGE → NO COMPLETE CONFORMANCE
NO EXPECTED/ACTUAL SEMANTIC COMPARISON → NO FIXTURE PASS
NO POLICY IDENTITY → NO STABLE AGGREGATION SEMANTICS
NO POPULATION DIGEST → NO IMMUTABLE CONFORMANCE SCOPE
NO EVIDENCE POPULATION FREEZE → NO IMMUTABLE EVALUATION
NO CRITICAL MUTATION DETECTION → NO RELEASE ELIGIBILITY
NO TRACEABILITY → NO RELEASE-GRADE CONFORMANCE CLAIM
```

The central invariant is:

```text
FIXTURES DEFINE THE POPULATION.
EXECUTIONS ESTABLISH WHAT RAN.
EVIDENCE ESTABLISHES WHAT WAS OBSERVED.
COVERAGE ESTABLISHES WHICH REQUIREMENTS HAVE EVIDENCE.
POLICY DEFINES HOW RESULTS AGGREGATE.
CONFORMANCE IS DERIVED.
RELEASE IS A SEPARATE GATE.

NO SUMMARY MAY CREATE EVIDENCE THAT DOES NOT EXIST.
NO AGGREGATOR MAY CREATE COVERAGE THAT DOES NOT EXIST.
NO COVERAGE RECORD MAY CREATE A RESULT THAT ITS EVIDENCE DOES NOT SUPPORT.
```

# End of RFL-AE Coverage Evaluation & Conformance Aggregation Specification v1.3
