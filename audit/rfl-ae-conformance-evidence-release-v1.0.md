# RFL-AE Protocol — Conformance, Evidence & Release Specification v1.0

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, YAML, tree, and graph passages collapsed; line breaks, indentation, and fenced structure restored, with the fixture corpus tree (§469), the evidence store tree (§526), the evidence dependency graph (§482), the evidence chain (§529), the release chain (§555), and all YAML/graph blocks reconstructed from their inline form. Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all ten recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Standing.** Begins at **§456**, continuing [`v0.9`](rfl-ae-executable-protocol-kernel-v0.9.md) (§362–§455). v0.9 ends at §455 and v1.0 opens at §456 — **the range is contiguous**, with no unassigned section. The effective specification is now **§0–§555 across ten supplied documents**, nine of which form a single numbered progression (v0.2 §0–§40 → v1.0 §456–§555).
>
> **Citation rule for §1–§28 — corrected 2026-09-28.** **§1–§28 is occupied twice**: [`v0.1`](rfl-ae-master-agent-instructions-v0.1.md) is §1–§28 and [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md) is §0–§40, so the two share **28 section numbers and zero identical headings** (v0.1 §4 = *AUTHORITY*; v0.2 §2 = *AUTHORITY*). Every citation in this corpus from §1 to §28 **MUST name its document**. §29–§555 is unambiguous. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Status:** NORMATIVE RELEASE/CONFORMANCE SPECIFICATION
> **Predecessor:** v0.9 Executable Protocol Kernel Contract
> **Range:** §456–§555
> **Purpose:** Define the frozen conformance model, fixture corpus, execution evidence, independent verification, CI authority, artifact binding, release gates, and claim-generation rules for RFL-AE v1.0.

---

# §456 — v1.0 Objective

v1.0 SHALL establish the boundary between:

```text
IMPLEMENTED
CONFORMANCE-TESTED
EXECUTION-VERIFIED
RELEASE-GATED
RELEASED
```

These states SHALL remain distinct.

```text
implementation exists
        ≠ implementation conforms
        ≠ execution was observed
        ≠ evidence was verified
        ≠ release gate passed
        ≠ release was published
```

---

# §457 — v1.0 Semantic Freeze

The following SHALL be frozen before v1.0 conformance execution:

```text
protocol semantics
schemas
canonicalization
identifier grammar
status semantics
error taxonomy
transition relation
authorization semantics
evidence semantics
coverage semantics
gate semantics
fixture identities
CLI exit codes
evidence schema
release manifest schema
```

Any semantic modification after freeze SHALL invalidate the affected conformance evidence.

---

# §458 — Freeze Manifest

The release candidate SHALL contain:

```yaml
freeze:
  protocol_version: "1.0"
  specification_digest: sha256:...
  schema_digest: sha256:...
  transition_digest: sha256:...
  fixture_manifest_digest: sha256:...
  cli_contract_digest: sha256:...
```

The freeze manifest SHALL itself be canonicalized and content-addressed.

---

# §459 — Subject Definition

A conformance claim SHALL identify exactly what implementation is being tested.

Minimum:

```yaml
subject:
  repository: ...
  commit: ...
  implementation_digest: sha256:...
```

A branch name SHALL NOT substitute for an immutable subject identity.

---

# §460 — Commit Identity

Where Git is the implementation source:

```text
subject.commit
```

SHALL identify the exact commit under test.

A mutable reference such as:

```text
main
master
latest
HEAD
```

SHALL NOT constitute sufficient implementation identity.

---

# §461 — Working Tree State

A commit SHA alone SHALL NOT prove that the executed artifact corresponded exactly to that commit.

The execution manifest SHOULD record:

```yaml
working_tree:
  clean: true
  status_digest: sha256:...
```

If uncommitted changes exist:

```text
clean = false
```

and the execution SHALL be classified accordingly.

---

# §462 — Source Artifact Digest

The implementation artifact SHALL have a deterministic digest.

Conceptually:

```text
implementation_digest =
    H(
      canonical_source_manifest
    )
```

The manifest SHOULD contain:

```text
path
file type
content digest
mode where relevant
```

Generated files SHALL be explicitly classified.

---

# §463 — Generated Artifact Rule

Generated code SHALL identify:

```text
source schema/specification
generator identity
generator version
generator digest
generated artifact digest
```

Generated artifacts SHALL NOT silently become independent semantic authorities.

---

# §464 — Dependency Lock

The release candidate SHALL use a frozen dependency resolution.

For Rust:

```text
Cargo.lock
```

SHALL be included where applicable.

The evidence SHALL bind the lockfile digest.

---

# §465 — Dependency Identity

Dependency identity SHALL include at least:

```text
name
version
source
resolved identity
```

Where available, content/package hashes SHOULD also be recorded.

---

# §466 — Tool Identity

Every verification tool SHALL expose:

```yaml
tool:
  name: ...
  version: ...
  implementation_digest: sha256:...
```

A tool's display version alone SHALL NOT establish implementation identity.

---

# §467 — Verifier Identity

The verifier SHALL be treated as a first-class subject.

```yaml
verifier:
  name: ...
  version: ...
  implementation_digest: sha256:...
  predicate_digest: sha256:...
```

Thus:

```text
subject identity + verifier identity + predicate identity
```

form the minimum verification identity.

---

# §468 — Predicate Versioning

Every predicate SHALL have:

```text
name
version
digest
```

Changing predicate behavior SHALL require a changed predicate identity.

A test result generated under predicate `P1` SHALL NOT automatically validate predicate `P2`.

---

# §469 — Fixture Corpus

The v1.0 fixture corpus SHALL be explicit.

```text
fixtures/
├── valid/
├── invalid/
├── canonicalization/
├── transitions/
├── authorization/
├── evidence/
├── coverage/
├── gates/
├── replay/
├── persistence/
├── injection/
├── mutation/
└── cross-language/
```

The manifest SHALL enumerate every required fixture.

---

# §470 — Fixture Manifest

```yaml
fixture:
  id: fixture:...
  version: "1.0"
  kind: ...
  content_digest: sha256:...
  expected:
    outcome: PASS
```

A fixture not present in the manifest SHALL not count toward required conformance coverage.

---

# §471 — Fixture Immutability

Once a fixture has been used for a release claim:

```text
fixture content + expected result
```

SHALL be immutable.

Modification SHALL produce a new fixture identity or invalidate prior evidence.

---

# §472 — Positive Fixtures

Positive fixtures SHALL demonstrate valid behavior.

Minimum classes:

```text
valid identifiers
valid digests
valid timestamps
valid protocol objects
valid transitions
valid authorization
valid observations
valid evidence
complete coverage
passing gates
valid replay
valid persistence
```

---

# §473 — Negative Fixtures

Negative fixtures SHALL demonstrate rejection behavior.

Minimum classes:

```text
invalid identifier
wrong identifier kind
malformed digest
schema violation
semantic violation
unauthorized request
out-of-scope request
invalid transition
stale evidence
wrong subject
wrong verifier
wrong predicate
incomplete coverage
forged PASS
invalid replay
tampered persistence
prompt injection
```

---

# §474 — Expected Negative Outcome

Every negative fixture SHALL specify the expected semantic failure.

Example:

```yaml
expected:
  outcome: REJECT
  error_code: EVIDENCE_STALE
```

The conformance runner SHALL compare structured error identity.

This is invalid:

```text
expected: nonzero
```

because unrelated failures may also return nonzero.

---

# §475 — Canonicalization Vectors

Every canonicalization vector SHALL contain:

```yaml
input: ...
canonical_bytes: ...
digest: ...
```

The expected bytes SHALL be authoritative.

A matching digest with different bytes SHALL fail.

---

# §476 — Canonicalization Verification

The runner SHALL verify:

```text
canonicalize(input) == expected_bytes
digest(expected_bytes) == expected_digest
```

Both comparisons are required.

---

# §477 — Transition Vectors

Every transition vector SHALL specify:

```yaml
current_state: CREATED
requested_state: AUTHORIZED
context: ...
expected:
  outcome: PASS
  resulting_state: AUTHORIZED
```

Negative transitions SHALL specify the exact error class.

---

# §478 — Transition Completeness

The transition corpus SHALL cover:

```text
every permitted transition
every terminal state
every required rejection boundary
```

An untested transition SHALL be reported as uncovered.

---

# §479 — Authorization Vectors

Authorization fixtures SHALL vary independently:

```text
authority
capability
scope
tool
policy
request
```

The suite SHALL contain both:

```text
authorized
unauthorized
```

cases.

---

# §480 — Authorization Non-Bypass Test

At least one integration test SHALL demonstrate:

```text
direct executor invocation
```

cannot obtain execution without the required authorization boundary.

If the architecture intentionally exposes a lower-level primitive, it SHALL be inaccessible to the untrusted agent path.

---

# §481 — Evidence Vectors

Evidence fixtures SHALL test:

```text
valid binding
wrong subject
wrong execution
wrong observation
wrong checker
wrong verifier
wrong predicate
wrong policy
wrong digest
missing dependency
stale dependency
```

---

# §482 — Evidence Dependency Closure

Valid evidence SHALL satisfy:

```text
Evidence
  │
  ├── Subject
  ├── Execution
  ├── Observation(s)
  ├── CheckResult
  ├── Verifier
  ├── Predicate
  └── Policy
```

Every required dependency SHALL be resolvable.

---

# §483 — Evidence Staleness

Evidence SHALL become stale when a required identity changes.

Minimum:

```text
subject
verifier
predicate
policy
```

The compatibility policy MAY permit specific substitutions.

Such substitutions SHALL themselves be evidence-bearing.

---

# §484 — Coverage Vectors

Coverage fixtures SHALL include:

```text
complete
partial
empty
duplicate declared
duplicate checked
unexpected member
unresolved member
unobservable member
skipped member
```

---

# §485 — Coverage Population Identity

The same logical member SHALL have one canonical identity.

Example:

```text
file:A
```

and:

```text
./file:A
```

must not be treated as distinct if the protocol defines them as the same member.

Canonical identity rules SHALL be explicit.

---

# §486 — Coverage Completeness

The conformance suite SHALL prove that:

```text
partial coverage
```

cannot produce:

```text
complete coverage
```

merely because a caller asserts:

```text
complete = true
```

---

# §487 — Gate Vectors

Gate fixtures SHALL cover:

```text
all checks pass
one check fails
one check errors
one check unknown
one check blocked
missing evidence
stale evidence
partial coverage
unknown coverage
invalid policy
```

---

# §488 — Gate Independence

Gate evaluation SHALL derive its result from its supplied protocol inputs.

It SHALL NOT trust:

```text
caller-provided gate status
stored textual claim
model-generated verdict
previous gate assertion
```

as authoritative.

---

# §489 — Replay Fixtures

Replay fixtures SHALL include:

```text
valid history
missing event
duplicate event
reordered event
tampered event
invalid transition
wrong previous digest
invalid terminal state
```

---

# §490 — Replay Determinism

Given:

```text
same initial state
same event sequence
same protocol version
```

replay SHALL produce the same result.

Environment-independent replay SHOULD be preferred.

---

# §491 — Persistence Fixtures

Persistence tests SHALL demonstrate:

```text
write
read
parse
validate
canonicalize
digest
compare
```

A write returning success SHALL not by itself satisfy the persistence test.

---

# §492 — Atomicity vs Durability

The test suite SHALL distinguish:

```text
atomic commit
```

from:

```text
durable persistence
```

The implementation SHALL document which guarantee is established by each persistence backend.

---

# §493 — Conformance Execution

A conformance execution SHALL have a unique:

```text
execution_id
```

and bind:

```text
subject
protocol
schemas
fixtures
tools
environment
results
```

---

# §494 — Execution Manifest

Minimum:

```yaml
execution:
  id: execution:...
  protocol_version: "1.0"
  subject_digest: sha256:...
  schema_digest: sha256:...
  fixture_manifest_digest: sha256:...
  verifier_digest: sha256:...
  started_at: ...
  completed_at: ...
```

---

# §495 — Environment Identity

The execution manifest SHOULD record:

```yaml
environment:
  os: ...
  architecture: ...
  kernel: ...
  compiler: ...
  runtime: ...
  container: ...
```

Only environment properties material to the predicate need to be normative.

---

# §496 — Environment Normalization

Environment identity SHALL distinguish:

```text
required
observed
irrelevant
unknown
```

An unspecified environment property SHALL not be silently assumed.

---

# §497 — Execution Step Record

Each test execution SHALL produce:

```yaml
step:
  id: ...
  fixture_id: ...
  started_at: ...
  completed_at: ...
  tool: ...
  input_digest: ...
  output_digest: ...
  status: PASS
  error_code: null
```

---

# §498 — Step Result Semantics

A step SHALL use:

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

These statuses SHALL not be collapsed into Boolean success/failure.

---

# §499 — Execution Error

An infrastructure/tool crash SHALL produce:

```text
ERROR
```

unless the fixture's declared contract specifically defines the failure as the expected semantic result.

---

# §500 — Skipped Test

A skipped test SHALL contain a reason.

```yaml
status: SKIPPED
reason: ...
```

A skipped required test SHALL prevent complete conformance.

---

# §501 — Blocked Test

A blocked test SHALL identify the blocking dependency.

```yaml
status: BLOCKED
blocked_by:
  - ...
```

Blocked SHALL not count as PASS.

---

# §502 — Unknown Result

Unknown SHALL be retained when the available evidence cannot determine the predicate result.

Unknown SHALL not be converted to PASS for summary convenience.

---

# §503 — Conformance Summary

The summary SHALL expose:

```text
required
executed
passed
failed
errored
unknown
skipped
blocked
missing
```

It SHALL NOT report only:

```text
passed / total
```

as the verification state.

---

# §504 — Coverage Record

Conformance coverage SHALL be represented explicitly:

```yaml
coverage:
  declared: ...
  executed: ...
  missing: ...
  skipped: ...
  failed: ...
  status: COMPLETE
```

`COMPLETE` SHALL be derived, not supplied by the caller.

---

# §505 — Required Coverage

A release policy SHALL distinguish:

```text
required fixtures
optional fixtures
informational fixtures
```

Only required fixtures determine required conformance coverage.

---

# §506 — Conformance Verdict

The conformance engine MAY derive:

```text
CONFORMANT
NON_CONFORMANT
INCOMPLETE
BLOCKED
ERROR
```

These are protocol states, not subjective quality rankings.

---

# §507 — Conformant Definition

`CONFORMANT` SHALL require:

```text
all required fixtures executed
all required fixtures passed
no unresolved required error
no unresolved required unknown
no required fixture skipped
coverage complete
subject identity bound
verifier identity bound
protocol identity bound
```

---

# §508 — Incomplete Definition

`INCOMPLETE` SHALL apply when required evidence is missing without sufficient information to establish non-conformance.

Examples:

```text
required test skipped
required CI run unavailable
required fixture missing
required artifact unavailable
```

---

# §509 — Non-Conformant Definition

`NON_CONFORMANT` SHALL require observed evidence of at least one required contract violation.

Example:

```text
required negative fixture accepted
```

or:

```text
required valid fixture rejected
```

---

# §510 — Blocked Definition

`BLOCKED` SHALL mean that execution could not establish the required predicate because a prerequisite authority/dependency was unavailable or prohibited.

It SHALL not be silently converted into:

```text
NON_CONFORMANT
```

or:

```text
CONFORMANT
```

---

# §511 — CI Execution Authority

For claims requiring CI:

```text
CI execution
```

SHALL be the authoritative execution source.

Local execution SHALL remain separately identified.

---

# §512 — Local Evidence

Local execution MAY establish:

```text
local test passed
```

It SHALL NOT establish:

```text
CI test passed
```

without corresponding CI evidence.

---

# §513 — CI Run Identity

A CI-backed claim SHALL bind:

```text
workflow identity
run identity
commit
job identity
environment
artifact
result
```

A screenshot or textual report alone SHOULD NOT be considered sufficient machine evidence.

---

# §514 — CI Artifact Binding

CI artifacts SHALL identify:

```text
run_id
commit
artifact_digest
test_manifest_digest
result_digest
```

This prevents unrelated artifacts from being attached to a successful run.

---

# §515 — Remote Verification

A remote CI claim SHALL be verified against the remote execution record.

The verifier SHALL establish:

```text
requested commit = executed commit
```

and:

```text
reported artifact = artifact under verification
```

---

# §516 — CI Failure Interpretation

A CI job that fails because of infrastructure SHALL be classified separately from a test assertion failure.

Minimum:

```text
TEST_FAIL
INFRA_ERROR
CANCELLED
TIMEOUT
BLOCKED
```

---

# §517 — CI Matrix Coverage

Where a release requires multiple environments:

```text
environment matrix
```

SHALL be declared before execution.

A missing matrix member SHALL be:

```text
MISSING
```

not PASS.

---

# §518 — Toolchain Binding

The execution record SHALL bind the actual toolchain used.

Example:

```yaml
toolchain:
  rustc: ...
  cargo: ...
  target: ...
```

Reported configuration SHALL be distinguished from observed configuration.

---

# §519 — Observed Toolchain

Where possible, toolchain identity SHALL be obtained from execution output rather than configuration text.

Example:

```text
rustc --version
cargo --version
```

The resulting observation SHALL be preserved.

---

# §520 — Build Reproducibility

A reproducibility claim SHALL specify:

```text
source
dependencies
toolchain
target
build procedure
artifact digest
```

"Build succeeds twice" is not sufficient unless the compared artifacts are defined.

---

# §521 — Artifact Reproducibility

Where deterministic builds are required:

```text
artifact_digest(run1) = artifact_digest(run2)
```

SHALL be tested.

If byte-identical output is not required, the permitted equivalence relation SHALL be defined.

---

# §522 — Release Manifest

The release SHALL contain:

```yaml
release:
  name: rfl-ae
  version: "1.0"

protocol:
  specification_digest: sha256:...
  schema_digest: sha256:...

subject:
  commit: ...
  implementation_digest: sha256:...

fixtures:
  manifest_digest: sha256:...

verification:
  execution_id: execution:...
  conformance_id: conformance:...
  coverage_id: coverage:...
  gate_id: gate:...
```

---

# §523 — Release Manifest Identity

The release manifest SHALL itself be canonicalized and hashed.

```text
release_manifest_digest
```

SHALL identify the exact release metadata.

---

# §524 — Release Artifact Set

The release artifact set SHALL include, where applicable:

```text
protocol specification
schemas
Rust source
TypeScript bindings
fixture corpus
fixture manifest
conformance runner
execution manifest
verification evidence
coverage record
gate result
release manifest
```

---

# §525 — Artifact Set Completeness

The release gate SHALL verify that every artifact required by the release manifest exists.

Missing artifacts SHALL block release.

---

# §526 — Evidence Store

Evidence SHALL be persisted independently from human-readable reports.

Recommended:

```text
evidence/
├── executions/
├── observations/
├── checks/
├── coverage/
├── gates/
├── claims/
└── releases/
```

Reports SHALL be projections over evidence.

---

# §527 — Persist Facts, Derive Views

The implementation SHALL prefer:

```text
persist facts
       ↓
derive summaries
       ↓
derive claims
```

rather than:

```text
persist final claim
       ↓
treat claim as fact
```

---

# §528 — Evidence Immutability

Once evidence has been committed:

```text
Evidence E
```

SHALL NOT be mutated in place.

A change SHALL create:

```text
Evidence E2
```

with a new identity.

---

# §529 — Evidence Chain

A verification result SHALL be traceable:

```text
Claim
  ↓
Gate
  ↓
Coverage
  ↓
Evidence
  ↓
Check
  ↓
Observation
  ↓
Execution
  ↓
Authorization
  ↓
ToolRequest
  ↓
Task
  ↓
Subject
```

Broken links SHALL invalidate the corresponding claim.

---

# §530 — Evidence Closure

A release claim SHALL have dependency closure.

For every referenced object:

```text
identity resolvable
digest valid
version compatible
required evidence available
```

No unresolved mandatory dependency is permitted.

---

# §531 — Independent Verification

At least one release-critical predicate SHOULD be verified by a path independent from the implementation under test.

Examples:

```text
Rust implementation
        ↓
JSON fixture
        ↓
independent verifier
```

or:

```text
primary implementation
        ↓
canonical artifact
        ↓
independent digest/schema verifier
```

---

# §532 — Independence Definition

Two verifiers SHALL NOT be considered independent merely because they are separate functions if they share the same defect source.

Independence SHALL consider:

```text
source code
algorithm
generated code
shared dependencies
fixture generation
semantic assumptions
```

---

# §533 — Independent Bootstrap

The conformance suite SHOULD provide a minimal bootstrap verifier requiring fewer dependencies than the full implementation.

Purpose:

```text
verify the verifier
```

not:

```text
prove the entire protocol correct
```

---

# §534 — Bootstrap Trust Boundary

The bootstrap verifier SHALL itself be treated as an implementation.

Therefore:

```text
bootstrap verifier → implementation identity → execution evidence → verifier evidence
```

It SHALL not receive unconditional trust merely because it is small.

---

# §535 — Mutation Evidence

Mutation testing SHALL generate evidence:

```yaml
mutation:
  id: mutation:...
  target: ...
  original_digest: sha256:...
  mutated_digest: sha256:...
  expected: REJECT
  actual: REJECT
  detected: true
```

---

# §536 — Required Mutation Set

The release policy SHALL identify critical mutations.

Examples:

```text
remove scope check
remove subject binding
accept stale verifier
force PASS
ignore missing coverage
skip authorization
ignore digest mismatch
```

Each critical mutation SHALL be detected.

---

# §537 — Mutation Survival

If a critical mutation survives:

```text
detected = false
```

the corresponding release gate SHALL fail.

No aggregate percentage may conceal a required mutation failure.

---

# §538 — Injection Boundary

Repository/tool/external content SHALL have the semantic class:

```text
DATA
```

by default.

Instruction-like text inside data SHALL not acquire authority automatically.

---

# §539 — Injection Test

A release-critical injection fixture SHALL verify:

```text
hostile data + valid protocol context
```

does not alter:

```text
authority
scope
authorization
check result
evidence status
gate status
```

except where the actual predicate explicitly evaluates that data.

---

# §540 — Model Output Boundary

Model output SHALL be represented as:

```text
proposal
```

unless explicitly transformed through an authorized protocol operation.

Therefore:

```text
model_output = "PASS"
```

does not establish:

```text
check_status = PASS
```

---

# §541 — Prompt Pack Binding

When a prompt/instruction pack materially affects execution, evidence SHALL bind:

```text
pack identity
pack version
pack digest
compiled representation digest
```

A textual prompt version alone is insufficient.

---

# §542 — Pack Compilation

The compilation pipeline SHALL be:

```text
source pack
    ↓
parse
    ↓
Pack IR
    ↓
resolve inheritance
    ↓
apply precedence
    ↓
canonicalize
    ↓
digest
    ↓
compiled pack
```

Compilation SHALL be deterministic.

---

# §543 — Pack Determinism

For identical:

```text
source
dependencies
compiler version
compiler policy
```

the compiler SHALL produce equivalent canonical output.

---

# §544 — Pack Authority

The effective authority SHALL satisfy:

```text
pack authority ∩ granted authority
```

A prompt pack SHALL not grant itself capabilities unavailable to the runtime.

---

# §545 — Scope Binding

Execution SHALL satisfy:

```text
executed_scope ⊆ authorized_scope
```

and verification claims SHALL satisfy:

```text
claimed_scope ⊆ executed_scope
```

Therefore:

```text
CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED
```

shall be a release-critical invariant.

---

# §546 — Evidence Completeness

Evidence SHALL explicitly identify missing information.

Forbidden transformation:

```text
missing → empty → successful
```

Required:

```text
missing → explicit completeness state → downstream policy decision
```

---

# §547 — Unknown Preservation

The release implementation SHALL preserve:

```text
UNKNOWN
```

until a later operation supplies sufficient evidence to resolve it.

A summary layer SHALL not erase uncertainty.

---

# §548 — Report Generation

Human-readable reports SHALL be projections.

```text
Evidence
    ↓
Report Generator
    ↓
Markdown/Text/HTML
```

Editing a report SHALL not modify protocol evidence.

---

# §549 — Report Trust

A report SHALL never be the sole source of a release decision when machine-readable evidence exists.

The gate SHALL consume structured evidence.

---

# §550 — Claim Generator

The claim generator SHALL produce a scoped statement.

Minimum conceptual structure:

```yaml
claim:
  protocol: ...
  subject: ...
  scope: ...
  predicate: ...
  evidence: ...
  coverage: ...
  gate: ...
  status: ...
```

---

# §551 — Claim Scope Law

The claim SHALL NOT exceed:

```text
protocol scope
+ subject scope
+ authorized scope
+ executed scope
+ verified coverage
```

A broader prose statement SHALL be rejected or explicitly marked unsupported.

---

# §552 — Release Gate

The final release gate SHALL evaluate:

```text
protocol freeze
subject identity
schema integrity
canonicalization
required fixtures
required coverage
negative detection
mutation requirements
cross-language conformance
evidence closure
CI requirements
artifact integrity
release manifest integrity
```

---

# §553 — Release Gate Result

The release gate SHALL return:

```text
PASS
FAIL
BLOCKED
UNKNOWN
```

It SHALL include the reasons and referenced evidence.

A human-readable:

```text
READY
```

flag SHALL not substitute for the structured gate result.

---

# §554 — Release Decision Boundary

Only the release gate may establish:

```text
release eligible
```

The following SHALL NOT establish release eligibility by themselves:

```text
README statement
CI green badge
developer assertion
model output
test count
percentage passed
local build
successful compilation
```

These may be inputs/evidence, but not the release decision itself.

---

# §555 — Absolute v1.0 Law

```text
NO IMMUTABLE SUBJECT     → NO IMPLEMENTATION IDENTITY
NO FROZEN SPECIFICATION     → NO STABLE CONFORMANCE TARGET
NO FIXTURE MANIFEST     → NO DEFINED TEST POPULATION
NO EXPECTED NEGATIVE RESULT     → NO REJECTION CONFORMANCE
NO EXECUTION RECORD     → NO EXECUTION CLAIM
NO CI RECORD     → NO CI CLAIM
NO SUBJECT BINDING     → NO SUBJECT-SPECIFIC VERIFICATION
NO VERIFIER BINDING     → NO VERIFIER-SPECIFIC EVIDENCE
NO PREDICATE IDENTITY     → NO STABLE VERIFICATION SEMANTICS
NO EVIDENCE CLOSURE     → NO VERIFIED CLAIM
NO COVERAGE     → NO COMPLETE-SCOPE CLAIM
NO CRITICAL MUTATION DETECTION     → NO RELEASE
NO REQUIRED FIXTURE COVERAGE     → NO COMPLETE CONFORMANCE
NO RELEASE GATE     → NO RELEASE ELIGIBILITY
NO RELEASE MANIFEST     → NO IMMUTABLE RELEASE IDENTITY
NO EXECUTION EVIDENCE     → NO VERIFIED IMPLEMENTATION CLAIM
```

v1.0 therefore establishes the complete evidence chain:

```text
SPECIFICATION
      │
      ▼
FROZEN CONTRACT
      │
      ▼
IMMUTABLE SUBJECT
      │
      ▼
EXECUTION
      │
      ▼
OBSERVATIONS
      │
      ▼
CHECKS
      │
      ▼
EVIDENCE
      │
      ▼
COVERAGE
      │
      ▼
CONFORMANCE
      │
      ▼
RELEASE GATE
      │
      ▼
RELEASE MANIFEST
      │
      ▼
SCOPED CLAIM
```

The central v1.0 invariant is:

```text
NO EVIDENCE
    → NO VERIFIED CLAIM.
```

And more strongly:

```text
NO EXECUTION EVIDENCE
    → NO CLAIM THAT THE IMPLEMENTATION EXECUTED AS DESCRIBED.
```
