# RFL-AE — Evidence & Execution Record Specification v1.2

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, JSON, graph, and table passages collapsed; line breaks, indentation, and fenced structure restored, with the architecture graph (§800), the provenance chain (§730), the execution/evidence relationship (§725), the tool invocation chain (§751), the write protocol (§686), the atomicity pipeline (§687), the canonicalization boundary (§737), the import pipeline (§764), the store-retrieval pipeline (§690/§739), the coverage matrix (§790), the evidence manifest (§723), and all JSON/YAML blocks reconstructed from their inline form. Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all twelve recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Heading level note.** Unlike v0.8–v1.1, which mark sections at `#`, v1.2 marks them at `##`, under a document-level `#` title. Preserved as supplied.
>
> **Standing.** Begins at **§656**, continuing [`v1.1`](rfl-ae-fixture-corpus-conformance-manifest-v1.1.md) (§556–§655). v1.1 ends at §655 and v1.2 opens at §656 — **the range is contiguous**. The effective specification is now **§0–§800 across twelve supplied documents**. **Citation rule: §1–§28 is occupied twice** (v0.1 §1–§28 and v0.2 §0–§40 share 28 numbers, zero identical headings) — citations in that range MUST name the document. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Status:** NORMATIVE
> **Predecessor:** RFL-AE v1.1 — Fixture Corpus & Conformance Manifest
> **Purpose:** Define the machine-readable execution and evidence layer that binds every conformance result to an immutable subject, exact executable artifact, verifier identity, environment, execution event chain, observations, expected/actual comparison, and evidence digest.

---

## §656 — Purpose

RFL-AE v1.2 defines the normative representation and lifecycle of:

1. `ExecutionRecord`
2. `ExecutionEvent`
3. `ObservationRecord`
4. `CheckExecution`
5. `EvidenceRecord`
6. `EvidenceReference`
7. `EvidenceBundle`
8. `EvidenceStore`
9. `VerifierIdentity`
10. `EnvironmentIdentity`
11. `ArtifactIdentity`
12. `ExecutionClosure`

The purpose is not to record that a test command printed `PASS`.

The purpose is to establish:

```text
WHAT   was executed
ON WHAT   immutable subject
BY WHAT   executable artifact
WITH WHAT   verifier/checker
WHERE/HOW   execution environment
UNDER WHICH   protocol/fixture/predicate
WHAT   was observed
HOW   observation was evaluated
WHAT   evidence was produced
WHICH   claims that evidence actually supports
```

---

## §657 — Fundamental Distinction

The following objects are distinct:

```text
Fixture
    ↓
ExecutionRequest
    ↓
ExecutionRecord
    ↓
ExecutionEvents
    ↓
Observations
    ↓
CheckExecution
    ↓
EvidenceRecord
    ↓
CoverageRecord
    ↓
ConformanceClaim
```

No object may be silently substituted for another.

In particular:

```text
TEST OUTPUT ≠ EXECUTION EVIDENCE
EXECUTION ≠ OBSERVATION
OBSERVATION ≠ EVIDENCE
EVIDENCE ≠ COVERAGE
COVERAGE ≠ CONFORMANCE
CONFORMANCE ≠ RELEASE
```

---

## §658 — Evidence Closure

An execution is evidentially closed only when all identity-bearing components required by the applicable contract are bound.

Define:

```text
Closure(E) =
  SubjectIdentity
  ∧ ArtifactIdentity
  ∧ VerifierIdentity
  ∧ EnvironmentIdentity
  ∧ ProtocolIdentity
  ∧ FixtureIdentity
  ∧ PredicateIdentity
  ∧ ExecutionIdentity
  ∧ ObservationIdentity
  ∧ ResultIdentity
  ∧ EvidenceDigest
```

If a required component is absent:

```text
Closure(E) = false
```

The result MUST NOT be represented as independently verified evidence.

---

## §659 — Evidence Identity

Every evidence record MUST have a stable identifier:

```text
evidence_id = algorithm ":" digest
```

Initial required algorithm:

```text
sha256
```

Example:

```text
sha256:8d3c...
```

The digest MUST be computed from canonical evidence content.

The digest MUST NOT include transport metadata that is explicitly declared non-semantic.

---

## §660 — Execution Identity

Every execution MUST have a unique execution identifier:

```text
execution_id = "execution:" + stable-id
```

The identifier identifies the execution event chain.

It MUST NOT be generated solely from wall-clock time.

Where deterministic replay is required, the execution identity SHOULD additionally bind:

```text
subject
fixture
tool
verifier
input
seed
protocol
```

---

## §661 — Subject Identity

An execution MUST identify the exact subject under test.

```json
{
  "subject_id": "subject:rfl-ae-kernel",
  "subject_version": "1.2.0",
  "artifact_digest": "sha256:...",
  "source_revision": "git:...",
  "source_digest": "sha256:..."
}
```

A repository name alone is insufficient.

A branch name alone is insufficient.

A mutable tag alone is insufficient.

The minimum immutable identity MUST include a content-addressed artifact or source identity appropriate to the subject.

---

## §662 — Artifact Identity

The executable implementation used by an execution MUST be separately identified.

```json
{
  "artifact_id": "artifact:rfl-conformance",
  "artifact_version": "1.2.0",
  "artifact_digest": "sha256:...",
  "build_identity": "build:...",
  "source_revision": "git:..."
}
```

This establishes:

```text
SOURCE ≠ BUILD ≠ EXECUTABLE ARTIFACT
```

A source commit does not by itself prove that the executed binary was produced from that commit.

---

## §663 — Verifier Identity

The verifier MUST be independently identified.

```json
{
  "verifier_id": "verifier:transition-check-v1",
  "verifier_version": "1.0.0",
  "source_revision": "git:...",
  "artifact_digest": "sha256:...",
  "predicate_id": "predicate:transition-valid-v1"
}
```

The verifier identity MUST be sufficient to distinguish materially different verification semantics.

---

## §664 — Predicate Identity

A check MUST identify the semantic predicate being evaluated.

```json
{
  "predicate_id": "predicate:evidence-closure-v1",
  "predicate_version": "1.0",
  "definition_digest": "sha256:..."
}
```

The following are invalid substitutions:

```text
"passed": true
```

for

```text
"predicate_id": "predicate:..."
"actual": ...
"expected": ...
"comparison": ...
```

A Boolean result without predicate identity is not stable verification semantics.

---

## §665 — Environment Identity

Execution evidence MUST identify the environment sufficiently for the applicable reproducibility requirement.

```json
{
  "os": "...",
  "architecture": "...",
  "runtime": "...",
  "runtime_version": "...",
  "compiler": "...",
  "compiler_version": "...",
  "dependencies": [],
  "environment_digest": "sha256:..."
}
```

Environment identity MUST distinguish:

```text
HOST
ENVIRONMENT
TOOLCHAIN
DEPENDENCIES
RUNTIME CONFIGURATION
```

Where a field is not observable, it MUST be represented as unknown rather than invented.

---

## §666 — Dependency Identity

Required dependencies MUST be individually identifiable where their behavior can affect verification.

```json
{
  "dependency_id": "dep:serde",
  "version": "...",
  "source": "...",
  "digest": "sha256:..."
}
```

A dependency listed without sufficient identity is not equivalent to an immutable dependency lock.

---

## §667 — Execution Request

An execution request describes what the runner was instructed to execute.

```json
{
  "execution_request_id": "request:...",
  "fixture_id": "fixture:...",
  "subject_id": "subject:...",
  "tool_id": "tool:...",
  "verifier_id": "verifier:...",
  "predicate_id": "predicate:...",
  "scope": {},
  "input_digest": "sha256:..."
}
```

The request MUST NOT be treated as evidence that execution occurred.

---

## §668 — Execution Record

The canonical execution record is:

```json
{
  "execution_id": "execution:...",
  "execution_request_id": "request:...",
  "protocol_version": "1.2",
  "fixture_id": "fixture:...",
  "subject": {},
  "artifact": {},
  "verifier": {},
  "environment": {},
  "start_time": "...",
  "end_time": "...",
  "status": "COMPLETED",
  "exit_code": 0,
  "events": [],
  "observations": [],
  "checks": [],
  "evidence_id": "sha256:..."
}
```

The record MUST preserve both successful and unsuccessful executions.

---

## §669 — Execution Status

Execution status is distinct from verification status.

Required execution states:

```text
REQUESTED
AUTHORIZED
STARTED
RUNNING
COMPLETED
FAILED_TO_START
INTERRUPTED
TIMEOUT
CANCELLED
CRASHED
UNKNOWN
```

Required rule:

```text
execution_status = COMPLETED
```

does NOT imply:

```text
verification_status = PASS
```

---

## §670 — Execution Event

Every material execution transition SHOULD produce an event.

```json
{
  "event_id": "event:...",
  "execution_id": "execution:...",
  "sequence": 4,
  "event_type": "OBSERVATION_CAPTURED",
  "timestamp": "...",
  "payload_digest": "sha256:...",
  "previous_event_digest": "sha256:..."
}
```

Events form an ordered chain.

---

## §671 — Event Chain Integrity

For events:

```text
E1, E2, ... En
```

the chain MUST satisfy:

```text
digest(E1) = H(canonical(E1))
digest(E2) = H(canonical(E2) || digest(E1))
...
digest(En) = H(canonical(En) || digest(E[n-1]))
```

The first event has:

```text
previous_event_digest = null
```

Only the first event may contain a null predecessor.

---

## §672 — Sequence Integrity

Event sequence numbers MUST satisfy:

```text
sequence(E1) = 0
sequence(E[n+1]) = sequence(En) + 1
```

Duplicate sequence numbers are invalid.

Missing sequence numbers are invalid for a closed execution chain.

---

## §673 — Observation Record

An observation records an externally observed fact.

```json
{
  "observation_id": "observation:...",
  "execution_id": "execution:...",
  "source": "stdout",
  "locator": "...",
  "value": {},
  "value_digest": "sha256:...",
  "observed_at": "...",
  "status": "OBSERVED"
}
```

The observation layer MUST NOT silently convert interpretation into fact.

---

## §674 — Observation Source

Observation sources SHOULD identify their origin:

```text
STDOUT
STDERR
EXIT_CODE
FILE
NETWORK_RESPONSE
PROCESS_STATUS
SYSTEM_CALL
ARTIFACT
CHECKER
USER_INPUT
TOOL_OUTPUT
```

The source is part of observation provenance.

---

## §675 — Observation Integrity

An observation is integrity-bound when:

```text
ObservationIntegrity =
  execution_id
  ∧ source
  ∧ value_digest
  ∧ observation identity
```

For file or artifact observations, the content digest SHOULD be computed from the exact bytes observed.

---

## §676 — Check Execution

A check execution binds a verifier to observations.

```json
{
  "check_execution_id": "check-execution:...",
  "execution_id": "execution:...",
  "verifier_id": "verifier:...",
  "predicate_id": "predicate:...",
  "inputs": [
    "observation:..."
  ],
  "expected": {},
  "actual": {},
  "status": "PASS",
  "reason_code": null
}
```

---

## §677 — Check Status

Required check statuses:

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

Semantics:

```text
PASS    = predicate evaluated true
FAIL    = predicate evaluated false
ERROR   = evaluation could not complete normally
UNKNOWN = required information was unavailable
SKIPPED = execution intentionally omitted the check
BLOCKED = prerequisite prevented evaluation
```

No non-PASS state may be silently coerced into PASS.

---

## §678 — Negative Fixture Verification

For an invalid or adversarial fixture, successful verification requires the expected semantic failure.

Example:

```json
{
  "status": "PASS",
  "expected": {
    "error_code": "E_DUPLICATE_ID"
  },
  "actual": {
    "error_code": "E_DUPLICATE_ID"
  }
}
```

A generic nonzero exit code is insufficient unless the fixture contract explicitly defines it as the required semantic result.

---

## §679 — Error Identity

Errors used for conformance MUST have stable semantic identifiers.

Example:

```text
E_SCHEMA_INVALID
E_DUPLICATE_ID
E_DIGEST_MISMATCH
E_SCOPE_VIOLATION
E_AUTHORIZATION_DENIED
E_STALE_SUBJECT
E_STALE_VERIFIER
E_PREDICATE_MISMATCH
E_CANONICALIZATION_MISMATCH
```

Human-readable error strings are diagnostic data, not stable semantic identities.

---

## §680 — Evidence Record

The canonical evidence record is:

```json
{
  "evidence_id": "sha256:...",
  "protocol_version": "1.2",

  "subject": {},
  "artifact": {},
  "fixture": {},
  "verifier": {},
  "predicate": {},
  "environment": {},

  "execution_id": "execution:...",
  "execution_events_digest": "sha256:...",

  "observations": [],
  "checks": [],

  "result": {
    "status": "PASS"
  },

  "coverage_ref": null,

  "created_at": "...",
  "evidence_schema_version": "1.0"
}
```

---

## §681 — Evidence Is Derived From Facts

Evidence MUST be derivable from recorded observations and execution metadata.

The system MUST NOT accept:

```json
{
  "verified": true
}
```

as authoritative evidence without the underlying evidence closure.

The authoritative relation is:

```text
Observations
    + Check semantics
    + Identity bindings
    + Execution provenance
    ↓
Evidence
```

---

## §682 — Evidence Digest

Evidence digest calculation MUST use canonical serialization.

Conceptually:

```text
evidence_id =
  sha256(
    canonical(
      evidence_without_evidence_id
    )
  )
```

The exact canonicalization algorithm MUST be declared by the protocol version.

---

## §683 — Evidence Reference

Large evidence objects MAY be stored externally.

The protocol MUST preserve:

```json
{
  "evidence_ref": "evidence:...",
  "content_digest": "sha256:...",
  "media_type": "application/json",
  "size_bytes": 1234,
  "store_id": "store:..."
}
```

A storage URI alone is not evidence identity.

---

## §684 — Evidence Store

An evidence store MUST provide:

```text
PUT
GET
EXISTS
VERIFY
```

Minimum semantic interface:

```text
put(evidence) -> evidence_id
get(evidence_id) -> evidence
exists(evidence_id) -> bool
verify(evidence_id) -> VerificationResult
```

Storage implementation is non-normative.

Evidence identity is normative.

---

## §685 — Evidence Store Immutability

Once an evidence identifier has been successfully stored:

```text
evidence_id → content
```

MUST remain immutable.

A second content value under the same evidence ID is a store integrity violation.

---

## §686 — Write Protocol

Evidence persistence SHOULD follow:

```text
BUILD
  ↓
CANONICALIZE
  ↓
DIGEST
  ↓
WRITE
  ↓
READ BACK
  ↓
RE-DIGEST
  ↓
COMPARE
  ↓
COMMIT STORE INDEX
```

A successful write without successful read-back does not establish durable evidence.

---

## §687 — Atomicity

Where the underlying storage supports atomic replacement, evidence writes SHOULD use:

```text
temporary object
     ↓
fsync / durability barrier
     ↓
atomic rename or equivalent
     ↓
index update
```

A crash during persistence MUST NOT produce a valid-looking evidence record whose content is incomplete.

---

## §688 — Evidence Validation

Evidence validation has at least four layers:

```text
1. STRUCTURAL
2. IDENTITY
3. INTEGRITY
4. SEMANTIC
```

Structural validation checks schema.

Identity validation checks referenced objects.

Integrity validation checks digests.

Semantic validation checks whether the evidence actually supports its stated result.

---

## §689 — Structural Validation

Structural validation MUST reject:

- missing required fields
- invalid types
- invalid enum values
- malformed identifiers
- malformed digests
- invalid timestamps
- unknown mandatory protocol variants

Structural validity does not establish semantic validity.

---

## §690 — Identity Validation

Identity validation MUST verify that:

```text
fixture_id
subject_id
artifact_id
verifier_id
predicate_id
execution_id
```

resolve to the objects claimed by the evidence record.

An unresolved required identity produces:

```text
BLOCKED
```

or:

```text
UNKNOWN
```

according to the applicable failure policy.

It MUST NOT produce PASS.

---

## §691 — Digest Validation

For every digest-bound object:

```text
declared_digest == digest(actual_content)
```

must hold.

Otherwise:

```text
E_DIGEST_MISMATCH
```

is required.

---

## §692 — Subject Freshness

Evidence MUST identify the subject version against which the execution occurred.

If current subject identity differs from evidence-bound subject identity:

```text
STALE_SUBJECT
```

MUST be reported.

Historical evidence remains valid as historical evidence but MUST NOT be presented as evidence for the new subject.

---

## §693 — Verifier Freshness

Verifier changes MAY change verification semantics.

Therefore:

```text
verifier identity + verifier artifact digest + predicate identity
```

MUST be retained.

Evidence from an older verifier MUST NOT automatically be promoted to evidence for a newer verifier.

---

## §694 — Predicate Freshness

Predicate definitions are immutable evidence dependencies.

If:

```text
predicate_definition_digest_old != predicate_definition_digest_current
```

then previous evidence is historical evidence only.

It MUST NOT silently become evidence for the changed predicate.

---

## §695 — Environment Binding

Environment-sensitive checks MUST explicitly declare whether environment identity is:

```text
REQUIRED
OPTIONAL
IRRELEVANT
```

A missing REQUIRED environment field invalidates the corresponding evidence closure.

---

## §696 — Tool Identity

The execution tool MUST be identifiable.

```json
{
  "tool_id": "tool:conformance-runner",
  "version": "1.2.0",
  "artifact_digest": "sha256:..."
}
```

The tool identity is distinct from the verifier identity.

```text
RUNNER ≠ VERIFIER
```

---

## §697 — Runner/Verifier Independence

The runner MAY invoke the verifier.

However:

```text
runner self-report
```

MUST NOT be treated as independent verification of:

```text
runner correctness
```

Where independence is required, an independent verifier MUST validate the produced evidence.

---

## §698 — Evidence Self-Certification

The following pattern is prohibited as sole evidence:

```text
program
   ↓
program says PASS
   ↓
program says evidence valid
```

This is self-certification.

At least one independently executable validation path MUST exist for release-grade evidence.

---

## §699 — Independent Evidence Validation

An independent validator SHOULD accept:

```text
EvidenceRecord + referenced immutable artifacts
```

and independently determine:

```text
STRUCTURALLY_VALID
IDENTITY_VALID
DIGEST_VALID
SEMANTICALLY_VALID
```

without trusting the producer's Boolean result.

---

## §700 — Execution Replay

A replay request MUST identify:

```text
fixture
subject
artifact
verifier
predicate
environment requirements
input
seed
```

where applicable.

Replay MUST produce a new execution identity.

It MUST NOT overwrite the original evidence.

---

## §701 — Replay Comparison

Replay comparison MUST distinguish:

```text
IDENTICAL
SEMANTICALLY_EQUIVALENT
DIFFERENT
NON_REPRODUCIBLE
BLOCKED
UNKNOWN
```

Byte equality is not automatically required for semantically deterministic executions unless the contract specifies it.

---

## §702 — Deterministic Execution

Where an execution contract declares deterministic behavior:

```text
same immutable inputs + same declared environment + same seed
```

MUST produce the same canonical semantic result.

Failure to reproduce MUST be recorded.

It MUST NOT be hidden by selecting the successful run.

---

## §703 — Randomness

Any verification relying on randomness MUST bind:

```text
randomness_source
seed
generator_identity
generator_version
```

If randomness is intentionally uncontrolled, the execution MUST declare that fact.

---

## §704 — Time

Execution timestamps are provenance data.

They MUST NOT be used as semantic evidence unless time is explicitly part of the tested contract.

For time-sensitive fixtures, the effective test time MUST be explicitly bound.

---

## §705 — Clock Identity

Where timestamp semantics are tested, evidence SHOULD distinguish:

```text
requested_time
observed_time
clock_source
clock_resolution
timezone/offset
```

A host clock timestamp alone is not proof of a semantic timestamp contract.

---

## §706 — Output Capture

Execution output MAY include:

```text
stdout
stderr
exit_code
files
structured_result
logs
metrics
```

Raw output is diagnostic evidence only until bound to a semantic observation.

---

## §707 — Output Truncation

If output is truncated:

```json
{
  "truncated": true,
  "original_size": 100000,
  "captured_size": 8192
}
```

MUST be recorded.

A truncated observation MUST NOT be treated as complete evidence unless the relevant predicate explicitly permits truncation.

---

## §708 — Missing Observation

Missing required observation MUST be represented as:

```text
UNKNOWN
```

or:

```text
BLOCKED
```

according to the applicable contract.

It MUST NOT be converted to an empty value.

---

## §709 — Evidence Completeness

Evidence completeness is a predicate:

```text
Complete(E) =
  all_required_bindings_present
  ∧ all_required_observations_present
  ∧ all_required_checks_evaluated
  ∧ all_required_digests_valid
```

Completeness MUST be computed, not asserted.

---

## §710 — Partial Evidence

Partial evidence MAY be stored.

It MUST be explicitly marked:

```text
PARTIAL
```

or represented through missing required closure components.

Partial evidence MUST NOT support a complete verification claim.

---

## §711 — Evidence Claims

Evidence MAY support claims only within its declared scope.

Example:

```text
Evidence:
  fixture:transition-17
  subject:commit-A
  predicate:transition-valid-v1

supports:
  fixture-level predicate result

does not automatically support:
  full protocol conformance
  repository correctness
  release eligibility
```

---

## §712 — Claim Derivation

A claim MUST be derived from:

```text
Evidence + Coverage + Applicable rules
```

not directly from:

```text
ExecutionRecord.status
```

or:

```text
CheckResult.status
```

alone.

---

## §713 — Evidence-to-Coverage Binding

Coverage records MUST reference the evidence records from which coverage was derived.

```json
{
  "coverage_id": "coverage:...",
  "population_digest": "sha256:...",
  "evidence": [
    "sha256:...",
    "sha256:..."
  ]
}
```

Evidence not referenced by coverage does not silently contribute to coverage.

---

## §714 — Duplicate Evidence

Multiple executions MAY produce evidence for the same fixture.

They MUST remain separately identifiable.

Example:

```text
fixture:X
 ├── execution:A → evidence:A
 ├── execution:B → evidence:B
 └── execution:C → evidence:C
```

The coverage evaluator determines whether these executions satisfy the applicable requirement.

---

## §715 — Supersession

Evidence MUST NOT be deleted merely because newer evidence exists.

A supersession relation MAY be recorded:

```json
{
  "supersedes": "sha256:old..."
}
```

Historical evidence remains distinguishable from current evidence.

---

## §716 — Evidence Revocation

Evidence MAY become invalid due to:

```text
subject replacement
verifier compromise
predicate change
artifact corruption
dependency invalidation
store corruption
specification withdrawal
```

Revocation MUST preserve the original evidence record and add an explicit invalidation event.

---

## §717 — Evidence Status

Evidence status MUST be distinct from check status.

Required values:

```text
VALID
INVALID
PARTIAL
STALE
REVOKED
UNKNOWN
```

Example:

```text
check = PASS
evidence = STALE
```

is valid and meaningful.

It means the historical check passed, but the evidence cannot establish a current-subject claim.

---

## §718 — Evidence Validation Result

A validator SHOULD produce:

```json
{
  "evidence_id": "sha256:...",
  "status": "VALID",
  "structural": "PASS",
  "identity": "PASS",
  "integrity": "PASS",
  "semantic": "PASS",
  "errors": [],
  "warnings": []
}
```

The validator's own result MUST remain distinct from the evidence being validated.

---

## §719 — Evidence Validation Error Taxonomy

Minimum required evidence errors:

```text
EVIDENCE_MALFORMED
EVIDENCE_ID_MISMATCH
SUBJECT_UNRESOLVED
SUBJECT_DIGEST_MISMATCH
ARTIFACT_UNRESOLVED
ARTIFACT_DIGEST_MISMATCH
VERIFIER_UNRESOLVED
VERIFIER_DIGEST_MISMATCH
PREDICATE_UNRESOLVED
PREDICATE_DIGEST_MISMATCH
FIXTURE_UNRESOLVED
EXECUTION_UNRESOLVED
EVENT_CHAIN_INVALID
OBSERVATION_DIGEST_MISMATCH
CHECK_UNRESOLVED
COVERAGE_UNRESOLVED
STALE_SUBJECT
STALE_VERIFIER
STALE_PREDICATE
INCOMPLETE_EVIDENCE
STORE_CORRUPTION
```

---

## §720 — Evidence Bundle

An evidence bundle groups related immutable records.

```json
{
  "bundle_id": "bundle:...",
  "protocol_version": "1.2",
  "subject": {},
  "executions": [],
  "observations": [],
  "checks": [],
  "evidence": [],
  "coverage": [],
  "bundle_digest": "sha256:..."
}
```

The bundle digest MUST bind the complete declared membership.

---

## §721 — Bundle Completeness

A bundle MUST distinguish:

```text
DECLARED MEMBERS
AVAILABLE MEMBERS
VALID MEMBERS
EXECUTED MEMBERS
```

These populations are not interchangeable.

---

## §722 — Bundle Closure

A bundle is closed only when every declared required reference resolves to immutable content.

Otherwise:

```text
OPEN
PARTIAL
BLOCKED
```

MUST be represented as appropriate.

---

## §723 — Evidence Manifest

A release-grade evidence manifest SHOULD contain:

```yaml
protocol_version: "1.2"
subject_digest: "sha256:..."
artifact_digest: "sha256:..."
fixture_corpus_digest: "sha256:..."
verifier_digest: "sha256:..."
predicate_digest: "sha256:..."
environment_digest: "sha256:..."
executions:
  - execution:...
evidence:
  - sha256:...
coverage:
  - coverage:...
```

---

## §724 — Evidence Manifest Digest

The manifest MUST have a canonical digest.

The digest MUST cover the identity-bearing membership of the evidence set.

Changing any identity-bearing member MUST change the manifest digest.

---

## §725 — Execution/Evidence Relationship

The normative relationship is:

```text
ExecutionRecord
       │
       ├── Events
       │
       ├── Observations
       │
       └── Checks
              │
              ▼
        EvidenceRecord
```

Evidence MUST reference the execution from which it was derived.

---

## §726 — No Orphan Evidence

Evidence claiming execution-derived facts MUST NOT exist without a resolvable execution record.

Exception:

Historical evidence imported from an external system MAY be represented as an external evidence source, but its external provenance MUST be explicitly declared.

---

## §727 — External Evidence

External evidence MUST identify:

```text
external source
external evidence identity
import timestamp
importer identity
original digest if available
trust level
```

Importing evidence does not upgrade its trust level.

---

## §728 — Trust Levels

Trust classification MAY use:

```text
UNTRUSTED
OBSERVED
IMPORTED
VALIDATED
INDEPENDENTLY_VALIDATED
RELEASE_BOUND
```

Trust labels are descriptive state, not proof by themselves.

---

## §729 — Evidence Provenance

Every evidence record MUST answer:

```text
Who/what produced it?
What did it execute?
Against what?
With which verifier?
Under which predicate?
In which environment?
From which observations?
When?
With which digest?
```

If a required answer is unavailable, the evidence MUST preserve that uncertainty.

---

## §730 — Provenance Chain

The minimum provenance chain is:

```text
Specification
  ↓
Protocol Version
  ↓
Fixture
  ↓
Subject
  ↓
Artifact
  ↓
Runner
  ↓
Verifier
  ↓
Predicate
  ↓
Environment
  ↓
Execution
  ↓
Observation
  ↓
Check
  ↓
Evidence
  ↓
Coverage
```

Any broken required edge prevents a complete claim.

---

## §731 — Authority Boundary

Evidence records are data.

They do not grant authority.

Therefore:

```text
EVIDENCE ≠ AUTHORIZATION
EVIDENCE ≠ CAPABILITY
EVIDENCE ≠ PERMISSION
```

An evidence record MUST NOT authorize execution merely because it claims that execution was previously authorized.

---

## §732 — Prompt Injection Boundary

Repository-controlled or fixture-controlled text MUST be treated as data unless an authorized protocol layer explicitly promotes it to instruction.

Therefore:

```text
fixture.input
repository.text
stdout
stderr
logs
```

MUST NOT alter:

```text
authority
scope
policy
verifier identity
evidence rules
```

merely by containing imperative language.

---

## §733 — Evidence Input Sanitization

Evidence parsers MUST NOT execute arbitrary content merely to validate evidence.

Parsing:

```text
JSON
YAML
Markdown
logs
tool output
```

MUST remain data processing unless an explicit executable contract exists.

---

## §734 — Serialization

Canonical evidence serialization MUST be deterministic.

Equivalent semantic records MUST serialize identically under the same protocol canonicalization rules.

Map/object member ordering MUST NOT affect identity.

---

## §735 — Unknown Preservation

Unknown information MUST survive:

```text
parse
validate
store
load
revalidate
export
```

unless the protocol explicitly defines a normalization that removes it.

The system MUST NOT silently convert:

```text
UNKNOWN
```

into:

```text
false
empty
zero
PASS
```

---

## §736 — Null Semantics

Nullability MUST be explicitly defined by schema.

The core evidence model SHOULD avoid ambiguous null values.

Where absence has semantic meaning, an explicit status SHOULD be preferred.

Example:

```text
NOT_OBSERVABLE
NOT_PRESENT
NOT_REACHABLE
UNKNOWN
```

rather than:

```text
"value": null
```

---

## §737 — Canonicalization Boundary

Canonicalization MUST occur before digest calculation.

Therefore:

```text
raw object
  ↓
canonical object
  ↓
canonical bytes
  ↓
digest
```

is normative.

Digesting non-canonical serialization is non-conformant where canonical identity is required.

---

## §738 — Evidence Mutation

Any mutation to identity-bearing evidence content MUST produce a different digest.

If:

```text
digest(E1) == digest(E2)
```

then:

```text
canonical(E1) == canonical(E2)
```

must hold under the protocol's collision-resistance assumption.

---

## §739 — Store Retrieval Verification

A store MUST verify retrieved evidence before returning it as valid.

Minimum:

```text
retrieve
  ↓
canonicalize
  ↓
digest
  ↓
compare evidence_id
```

Mismatch MUST produce:

```text
STORE_CORRUPTION
```

or equivalent integrity failure.

---

## §740 — Store Index Integrity

An index mapping:

```text
evidence_id → location
```

is auxiliary metadata.

The content digest remains authoritative.

A corrupt index MUST NOT alter evidence identity.

---

## §741 — Evidence Garbage Collection

Deletion of evidence is a lifecycle operation, not an identity operation.

Release-bound evidence MUST NOT be garbage-collected unless the release policy explicitly permits archival relocation or deletion.

Deletion MUST be observable.

---

## §742 — Retention

Evidence retention policy MUST distinguish:

```text
ACTIVE
ARCHIVED
EXPIRED
DELETED
```

Expired evidence may remain historically valid while no longer satisfying current retention requirements.

---

## §743 — Execution Failure Evidence

Failed executions MUST produce evidence where the protocol requires failure provenance.

Example:

```text
FAILED_TO_START
CRASHED
TIMEOUT
```

is valuable evidence about execution behavior even though it is not a successful conformance result.

---

## §744 — Check Error Evidence

Checker errors MUST preserve:

```text
error_code
error_context
verifier_identity
predicate_identity
observations_consumed
```

A checker crash MUST NOT become a fixture FAIL unless the fixture explicitly tests checker failure.

---

## §745 — Timeout Evidence

Timeout records MUST identify:

```text
timeout_limit
observed_duration
execution_id
termination mechanism
partial observations
```

A timeout is not equivalent to a semantic FAIL.

---

## §746 — Interrupted Execution

An interrupted execution MUST preserve all safely captured observations.

Partial observations MUST remain marked partial.

---

## §747 — Crash Evidence

Crash evidence SHOULD include:

```text
exit status
signal if observable
stderr
captured logs
partial outputs
artifact identity
environment identity
```

Crash evidence MUST NOT claim the semantic predicate result unless the predicate explicitly evaluates crashes.

---

## §748 — Authorization Binding

Execution evidence SHOULD reference the authorization decision that permitted execution.

```json
{
  "authorization_id": "authorization:..."
}
```

Authorization MUST precede execution.

Evidence MUST NOT retroactively authorize an execution.

---

## §749 — Scope Binding

Execution evidence MUST bind the executed scope.

The relation is:

```text
CLAIMED_SCOPE ⊇ EXECUTED_SCOPE
```

A claim exceeding executed scope is invalid.

---

## §750 — Execution Scope Violation

If execution exceeds authorized scope:

```text
E_SCOPE_VIOLATION
```

MUST be recorded.

The resulting evidence MUST NOT be treated as valid authorization-compliant evidence.

---

## §751 — Tool Invocation Chain

Where tools invoke other tools:

```text
Tool A
  ↓
Tool B
  ↓
Tool C
```

the execution chain MUST preserve the nested invocation relationship.

A parent tool MUST NOT erase child execution identity.

---

## §752 — Tool Result Binding

Every tool result used as evidence SHOULD bind:

```text
tool_id
invocation_id
input_digest
output_digest
execution_id
```

This prevents output reuse across unrelated invocations.

---

## §753 — Replay Protection

An observation from execution A MUST NOT be silently reused as execution B's observation.

Reuse requires an explicit immutable reference.

---

## §754 — Cross-Execution Contamination

Evidence validators MUST reject unexplained references where:

```text
observation.execution_id != evidence.execution_id
```

unless the protocol explicitly permits imported or shared observations.

---

## §755 — Cross-Language Evidence

Rust, TypeScript, Python, or other implementations MAY produce evidence.

All implementations MUST conform to the same semantic evidence contract.

Language-specific representation MUST NOT change evidence semantics.

---

## §756 — Golden Evidence

A golden evidence fixture MUST contain:

```text
canonical input
expected canonical representation
expected digest
expected validation result
```

Golden evidence MUST NOT merely contain:

```text
"valid": true
```

---

## §757 — Invalid Evidence Fixtures

Required invalid evidence fixtures SHOULD include:

```text
wrong evidence digest
wrong subject digest
wrong artifact digest
wrong verifier digest
wrong predicate digest
broken event chain
missing event
duplicate event
sequence missing observation
wrong observation digest
unknown fixture
unknown execution
stale subject
stale verifier
stale predicate
scope mismatch
```

---

## §758 — Mutation Testing

Evidence validation MUST be mutation-tested.

At minimum, mutations SHOULD target:

```text
subject digest
artifact digest
verifier digest
predicate digest
event predecessor
observation digest
expected result
actual result
execution identity
fixture identity
coverage membership
```

Critical mutations MUST be detected.

---

## §759 — Evidence Mutation Law

If a critical evidence mutation is introduced and the validator still returns valid:

```text
RELEASE BLOCKED
```

The surviving mutation demonstrates a verification gap.

---

## §760 — Metamorphic Evidence Tests

Metamorphic tests SHOULD verify invariants such as:

```text
reordering non-semantic JSON keys → same evidence digest
changing identity-bearing content → different evidence digest
changing subject revision → stale/invalid evidence
changing predicate definition → stale/invalid evidence
changing observation content → evidence digest changes
```

---

## §761 — Independent Bootstrap

An independent evidence validator SHOULD be capable of validating release evidence without importing the producer's internal validation logic.

This provides:

```text
producer ≠ validator
```

as a meaningful trust boundary.

---

## §762 — Minimal Validator

The minimal independent validator SHOULD require only:

```text
canonicalization
hashing
schema validation
identity resolution
digest validation
basic semantic predicates
```

It SHOULD avoid dependence on the complete execution runtime.

---

## §763 — Evidence Portability

Evidence SHOULD be portable across environments.

At minimum, a portable evidence bundle MUST contain or reference:

```text
protocol identity
fixture identity
subject identity
artifact identity
verifier identity
predicate identity
execution identity
observations
checks
digests
```

---

## §764 — Evidence Import

Importing an evidence bundle MUST perform:

```text
parse
  ↓
schema validation
  ↓
digest validation
  ↓
identity resolution
  ↓
provenance validation
  ↓
semantic validation
  ↓
store
```

Import MUST NOT mean trust.

---

## §765 — Evidence Export

Export MUST preserve semantic identity.

Therefore:

```text
import(export(E)) = E
```

up to explicitly non-semantic transport metadata.

---

## §766 — Evidence Compression

Compression MAY alter transport representation.

It MUST NOT alter canonical evidence bytes.

Therefore:

```text
decompress(compress(E))
```

MUST reproduce the original canonical evidence content.

---

## §767 — Evidence Encryption

Encryption MAY protect evidence at rest or in transit.

Cryptographic protection is orthogonal to semantic evidence validity.

```text
ENCRYPTED ≠ VERIFIED
```

---

## §768 — Evidence Authentication

Digital signatures MAY authenticate the producer.

A signature proves control of a signing key over the signed content.

It does not independently prove that the underlying execution or predicate was correct.

```text
SIGNATURE ≠ SEMANTIC PROOF
```

---

## §769 — Signed Evidence

Where signatures are used:

```json
{
  "content_digest": "sha256:...",
  "signature_algorithm": "...",
  "key_id": "...",
  "signature": "..."
}
```

The signed content MUST be canonical.

---

## §770 — Key Identity

Signing evidence MUST identify the verification key or stable key identity.

Key rotation MUST NOT rewrite historical evidence.

---

## §771 — Trust Root

Trust in signatures MUST be rooted in an explicitly declared trust configuration.

The presence of a valid cryptographic signature alone MUST NOT imply that the key is trusted.

---

## §772 — Evidence Time

Evidence creation time is provenance.

It MUST NOT automatically establish:

```text
execution time
artifact build time
subject commit time
```

These must be separately represented where required.

---

## §773 — Time Ordering

Where causality is required:

```text
authorization ≤ execution start ≤ observation ≤ check ≤ evidence creation
```

must hold according to the applicable clock model.

If ordering cannot be established, the relation MUST remain unknown.

---

## §774 — Monotonic Execution Time

Execution duration SHOULD use a monotonic clock.

Wall-clock timestamps MAY be retained for provenance.

Mixing wall-clock and monotonic measurements without declaration is non-conformant.

---

## §775 — Environment Capture Failure

If required environment information cannot be captured:

```text
environment_status = INCOMPLETE
```

The evidence MAY remain historical/diagnostic but cannot satisfy a release requirement requiring complete environment binding.

---

## §776 — Dependency Resolution Failure

If a required dependency cannot be resolved immutably:

```text
BLOCKED
```

MUST propagate to the corresponding verification claim.

---

## §777 — Evidence Dependency Closure

Evidence dependency closure requires:

```text
Evidence
  ↓
Execution
  ↓
Artifact
  ↓
Source
  ↓
Dependencies
  ↓
Environment
```

for all dependencies declared required by the applicable contract.

---

## §778 — Dependency Digest

A dependency graph digest SHOULD be computed from canonical dependency identities.

Example:

```text
dependency_set_digest = sha256(canonical(sorted(dependency identities)))
```

Dependency ordering MUST NOT alter the digest.

---

## §779 — Build Reproducibility

Where reproducible builds are required:

```text
source digest
+ dependency digest
+ toolchain identity
+ build configuration
```

MUST be bound to the artifact.

---

## §780 — Artifact Provenance

An artifact SHOULD be accompanied by:

```text
source revision
source digest
build tool
toolchain
dependency lock
build configuration
artifact digest
```

The exact requirements depend on the release class.

---

## §781 — Artifact Mismatch

If:

```text
executed_artifact_digest != declared_artifact_digest
```

the execution evidence is invalid.

---

## §782 — Source/Artifact Mismatch

If the release contract requires source-to-artifact provenance and that relation cannot be established:

```text
IMPLEMENTATION_IDENTITY = UNVERIFIED
```

The artifact may still be executable, but the stronger implementation claim is unavailable.

---

## §783 — Evidence Scope

Every evidence record MUST have an implicit or explicit scope.

Minimum scope dimensions:

```text
subject
fixture
predicate
execution
```

Higher-level scopes require additional coverage evidence.

---

## §784 — Fixture-Level Evidence

Fixture-level evidence establishes:

```text
fixture X was executed against subject Y under predicate Z and produced result R
```

It does not establish full corpus conformance.

---

## §785 — Corpus-Level Evidence

Corpus-level evidence requires:

```text
fixture corpus identity
+ required fixture execution coverage
+ valid evidence for required fixtures
```

A collection of arbitrary passing tests is not corpus-level evidence.

---

## §786 — Release-Level Evidence

Release-level evidence additionally requires:

```text
release subject identity
release artifact identity
frozen protocol identity
fixture corpus identity
verifier identity
coverage closure
mutation requirements
release gate
```

---

## §787 — Evidence Aggregation

Evidence aggregation MUST be deterministic.

Given identical:

```text
evidence set
rules
population
```

the aggregate result MUST be identical.

---

## §788 — Aggregation Must Not Hide Failures

Aggregation MUST preserve:

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

A summary such as:

```text
997 / 1000 passed
```

MUST NOT conceal the three non-PASS states.

---

## §789 — Aggregate Status

Aggregate status MUST be derived from the applicable policy.

Example:

```text
all required = PASS → eligible for PASS
required FAIL → FAIL
required ERROR → ERROR/BLOCKED according to policy
required UNKNOWN → UNKNOWN/BLOCKED
required SKIPPED → incomplete
required BLOCKED → BLOCKED
```

The policy itself MUST be versioned.

---

## §790 — Evidence Coverage Matrix

A release evidence report SHOULD expose:

| Fixture | Executed | Evidence | Predicate | Result | Digest Valid | Covered |
|---|---|---|---|---|---|---|
| F1 | yes | yes | P1 | PASS | yes | yes |
| F2 | yes | yes | P2 | FAIL | yes | no |
| F3 | no | no | P3 | — | — | no |

This matrix is a projection.

The underlying evidence records remain authoritative.

---

## §791 — No Percentage Conformance

A percentage such as:

```text
99.7% passed
```

MUST NOT substitute for required-fixture coverage.

A single missing critical fixture MAY prevent conformance regardless of aggregate percentage.

---

## §792 — Critical Evidence

Fixtures or predicates marked critical MUST have explicit evidence closure.

Critical evidence gaps MUST block the applicable gate.

---

## §793 — Evidence Classification

Evidence SHOULD be classified:

```text
DIAGNOSTIC
CONFORMANCE
RELEASE
HISTORICAL
IMPORTED
```

Classification MUST NOT change underlying evidence semantics.

---

## §794 — Diagnostic Evidence

Diagnostic evidence MAY be incomplete.

It MUST NOT be silently promoted to release evidence.

---

## §795 — Historical Evidence

Historical evidence remains bound to its original:

```text
subject
artifact
verifier
predicate
environment
execution
```

Historical evidence MUST NOT be rewritten to appear current.

---

## §796 — Release Evidence Freeze

Before release evaluation, the evidence population MUST be frozen.

After freeze:

```text
new evidence
changed evidence
deleted evidence
changed verifier
changed predicate
changed fixture
```

MUST trigger re-evaluation.

---

## §797 — Evidence Freeze Digest

The frozen evidence population MUST have a digest.

```text
evidence_population_digest = H(canonical(sorted(evidence identities)))
```

---

## §798 — Gate Input

The release gate MUST consume:

```text
frozen protocol
+ subject identity
+ artifact identity
+ fixture corpus
+ evidence population
+ coverage
+ mutation results
+ required policies
```

The gate MUST NOT consume an informal test summary as authoritative input.

---

## §799 — Gate/Evidence Separation

The gate evaluates evidence.

It does not create evidence.

```text
EXECUTOR → EVIDENCE
EVIDENCE → GATE
GATE → RELEASE DECISION
```

---

## §800 — Final v1.2 Invariant

The complete evidence architecture is:

```text
                 FROZEN SPECIFICATION
                          │
                          ▼
                   FIXTURE CORPUS
                          │
                          ▼
                     EXECUTION
                          │
               ┌──────────┴──────────┐
               ▼                     ▼
         EXECUTION EVENTS       ENVIRONMENT
               │                     │
               ▼                     │
          OBSERVATIONS               │
               │                     │
               ▼                     │
             CHECKS                 │
               │                     │
               └──────────┬──────────┘
                          ▼
                    EVIDENCE RECORD
                          │
                          ▼
                   EVIDENCE STORE
                          │
                          ▼
                   COVERAGE RECORD
                          │
                          ▼
                   CONFORMANCE CLAIM
                          │
                          ▼
                    RELEASE GATE
```

The governing laws are:

```text
NO EXECUTION RECORD → NO EXECUTION CLAIM
NO SUBJECT IDENTITY → NO SUBJECT-SPECIFIC EVIDENCE
NO ARTIFACT IDENTITY → NO EXECUTABLE-ARTIFACT CLAIM
NO VERIFIER IDENTITY → NO VERIFIER-SPECIFIC EVIDENCE
NO PREDICATE IDENTITY → NO STABLE VERIFICATION SEMANTICS
NO OBSERVATION → NO OBSERVATION-BASED CLAIM
NO DIGEST → NO IMMUTABLE CONTENT IDENTITY
NO EVIDENCE CLOSURE → NO VERIFIED CLAIM
NO COVERAGE → NO COMPLETE-SCOPE CLAIM
NO INDEPENDENT VALIDATION → NO RELEASE-GRADE EVIDENCE ASSURANCE
NO CRITICAL MUTATION DETECTION → NO RELEASE
NO FROZEN EVIDENCE POPULATION → NO IMMUTABLE RELEASE CLAIM
```

The central invariant is:

```text
EXECUTION PRODUCES FACTS.
CHECKS INTERPRET FACTS.
EVIDENCE BINDS FACTS TO IDENTITY.
COVERAGE BINDS EVIDENCE TO POPULATION.
GATES BIND COVERAGE TO POLICY.
RELEASE BINDS THE GATE TO AN IMMUTABLE ARTIFACT.

NO LAYER MAY PRETEND TO BE THE NEXT LAYER.
```

# End of RFL-AE Evidence & Execution Record Specification v1.2
