# RFL-AE Prompt Instructions — Executable Protocol Types & Schemas v0.7

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, JSON-shaped, Rust/TypeScript, and tree passages collapsed; line breaks, indentation, and fenced structure restored, with JSON objects and the implementation tree reconstructed from their inline form. Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Standing.** Begins at **§201**, continuing [`v0.6`](rfl-ae-protocol-schemas-v0.6.md) (§150–§199). **Note the gap: v0.6 ends at §199, and v0.7 begins at §201. §200 is unassigned** — flagged in [`rfl-ae-prompt-packs-part18.md`](rfl-ae-prompt-packs-part18.md) §1.
>
> **Status:** NORMATIVE IMPLEMENTATION CONTRACT
> **Predecessor:** v0.6 Protocol Schemas & State Machines
> **Purpose:** Convert the protocol model into machine-validatable, typed, deterministic artifacts.

---

## §201 — Implementation Boundary

v0.7 SHALL define executable representations for the protocol objects introduced in v0.6.

The implementation SHALL have three representations:

```text
SOURCE / AUTHORING
        │
        ▼
CANONICAL PROTOCOL OBJECT
        │
        ├── JSON Schema validation
        ├── Rust type validation
        └── TypeScript type validation
        │
        ▼
CANONICAL BYTES
        │
        ▼
DIGEST
```

The three representations MUST preserve the same protocol semantics.

```text
Rust type ≠ JSON Schema ≠ TypeScript type
```

but all three SHALL implement the same contract.

---

## §202 — Source of Truth

The project SHALL designate exactly one normative semantic specification.

Recommended hierarchy:

```text
SPECIFICATION
      │
      ▼
SCHEMA
      │
      ├── Rust implementation
      └── TypeScript implementation
```

Generated types SHALL NOT silently redefine semantics.

If generated code disagrees with the schema:

```text
CONFORMANCE = FAIL
```

---

## §203 — Primitive Types

The protocol SHALL define explicit primitive aliases.

```rust
type TaskId = String;
type SubjectId = String;
type ScopeId = String;
type AuthorityId = String;
type ToolId = String;
type ToolRequestId = String;
type ExecutionId = String;
type ObservationId = String;
type CheckId = String;
type CheckResultId = String;
type EvidenceId = String;
type CoverageId = String;
type GateId = String;
type EventId = String;
```

The implementation SHOULD use stronger nominal types where practical.

For example:

```rust
struct TaskId(String);
struct ExecutionId(String);
struct EvidenceId(String);
```

A raw `String` SHOULD NOT be accepted where a typed identifier is required.

---

## §204 — Timestamp Contract

Timestamps SHALL use a single canonical representation.

Required properties:

```text
UTC
ISO-8601 / RFC3339 compatible
explicit timezone
canonical serialization
```

Example:

```text
2026-09-28T12:00:00Z
```

The implementation SHALL reject ambiguous local timestamps.

---

## §205 — Digest Contract

Digest values SHALL identify both algorithm and digest.

Canonical form:

```text
<algorithm>:<hexadecimal digest>
```

Example:

```text
sha256:0123456789abcdef...
```

The implementation SHALL reject:

```text
bare hexadecimal digest
unknown algorithm
wrong digest length
invalid hexadecimal
```

unless the schema explicitly permits them.

---

## §206 — Identifier Contract

Identifiers SHALL conform to:

```text
<type>:<opaque-id>
```

Examples:

```text
task:01J...
subject:01J...
execution:01J...
evidence:sha256:...
```

Identifier parsing SHALL validate the declared type.

Therefore:

```text
execution:abc
```

MUST NOT be accepted where:

```text
task:abc
```

is required.

---

## §207 — Status Types

Statuses SHALL be closed enums.

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

The implementation MUST NOT silently map unknown statuses to `UNKNOWN`.

Unknown protocol values SHALL produce a schema/version error.

---

## §208 — Execution States

Execution state SHALL be a separate enum:

```text
CREATED
AUTHORIZED
RUNNING
SUCCEEDED
FAILED
ERROR
CANCELLED
TIMED_OUT
BLOCKED
```

Execution states SHALL NOT share the same type as check statuses.

This prevents:

```text
ExecutionState::PASS
```

from existing accidentally.

---

## §209 — Authorization States

```text
ALLOW
DENY
UNKNOWN
```

Authorization SHALL be independent of execution.

```text
ALLOW ≠ SUCCEEDED
DENY  ≠ FAILED
```

---

## §210 — Coverage States

```text
COMPLETE
PARTIAL
UNKNOWN
```

Coverage SHALL be represented explicitly.

No Boolean field such as:

```json
{"complete": true}
```

may replace the structured coverage model.

---

## §211 — Schema Object

Every protocol schema SHALL contain:

```json
{
  "$schema": "...",
  "$id": "rfl-ae/<object>/v1",
  "title": "...",
  "type": "object"
}
```

The schema ID SHALL be stable.

The implementation SHALL NOT depend on a mutable documentation URL for schema identity.

---

## §212 — Required Fields

Required protocol objects SHALL reject omission of identity-critical fields.

At minimum:

```text
id
schema
subject/reference
timestamp where applicable
```

Optional fields SHALL be explicitly represented as optional.

Missing and null SHALL NOT automatically mean the same thing.

---

## §213 — Unknown Fields

For evidence-critical protocol objects, schemas SHOULD reject undeclared fields.

Recommended:

```json
{
  "additionalProperties": false
}
```

This prevents accidental acceptance of misspelled fields such as:

```text
verifer_digest
```

instead of:

```text
verifier_digest
```

---

## §214 — Task Type

Canonical structure:

```json
{
  "task_id": "task:...",
  "schema": "rfl-ae/task/v1",
  "created_at": "...",
  "source": {
    "kind": "user",
    "id": "..."
  },
  "objective": {},
  "subject_ref": "subject:...",
  "scope_ref": "scope:...",
  "authority_ref": "authority:...",
  "constraints": [],
  "requested_outputs": [],
  "required_checks": [],
  "stop_conditions": []
}
```

The schema SHALL validate references syntactically.

Semantic reference existence SHALL be checked separately.

---

## §215 — Subject Type

```json
{
  "subject_id": "subject:...",
  "schema": "rfl-ae/subject/v1",
  "kind": "repository",
  "locator": "...",
  "identity": {
    "digest_algorithm": "sha256",
    "digest": "..."
  },
  "observed_at": "..."
}
```

The locator SHALL NOT replace the identity digest.

---

## §216 — Scope Type

```json
{
  "scope_id": "scope:...",
  "schema": "rfl-ae/scope/v1",
  "subject_refs": [],
  "include": [],
  "exclude": [],
  "dimensions": {},
  "declared_at": "..."
}
```

The scope evaluator SHALL produce an explicit resolved population.

---

## §217 — Tool Type

```json
{
  "tool_id": "tool:...",
  "schema": "rfl-ae/tool/v1",
  "name": "...",
  "version": "...",
  "implementation_digest": "sha256:...",
  "interface_version": "...",
  "capabilities": []
}
```

Tool identity SHALL be evidence-addressable.

---

## §218 — ToolRequest Type

```json
{
  "request_id": "tool-request:...",
  "schema": "rfl-ae/tool-request/v1",
  "task_ref": "task:...",
  "tool_ref": "tool:...",
  "actor_ref": "actor:...",
  "operation": "...",
  "arguments": {},
  "requested_capabilities": [],
  "scope_ref": "scope:...",
  "requested_at": "..."
}
```

Arguments SHALL be canonicalized before digesting.

---

## §219 — AuthorizationDecision Type

```json
{
  "decision_id": "authz:...",
  "schema": "rfl-ae/authorization/v1",
  "request_ref": "tool-request:...",
  "authority_ref": "authority:...",
  "capability_results": [],
  "scope_result": "PASS",
  "policy_result": "PASS",
  "decision": "ALLOW",
  "decided_at": "...",
  "policy_digest": "sha256:..."
}
```

The decision SHALL be generated by the authorization subsystem.

The model SHALL not supply it as authoritative state.

---

## §220 — ExecutionRecord Type

```json
{
  "execution_id": "execution:...",
  "schema": "rfl-ae/execution/v1",
  "request_ref": "tool-request:...",
  "authorization_ref": "authz:...",
  "tool_ref": "tool:...",
  "state": "SUCCEEDED",
  "started_at": "...",
  "ended_at": "...",
  "input_digest": "sha256:...",
  "output_digest": "sha256:...",
  "exit_status": 0,
  "error_class": null,
  "environment_ref": "environment:..."
}
```

A successful exit code SHALL NOT by itself establish semantic success.

---

## §221 — Observation Type

```json
{
  "observation_id": "observation:...",
  "schema": "rfl-ae/observation/v1",
  "execution_ref": "execution:...",
  "subject_ref": "subject:...",
  "kind": "filesystem_state",
  "content_digest": "sha256:...",
  "content_ref": "...",
  "observed_at": "...",
  "completeness": "COMPLETE"
}
```

---

## §222 — CheckResult Type

```json
{
  "check_result_id": "check-result:...",
  "schema": "rfl-ae/check-result/v1",
  "check_id": "check:...",
  "observation_refs": [],
  "predicate_digest": "sha256:...",
  "status": "PASS",
  "findings": [],
  "evaluated_at": "...",
  "verifier_ref": "verifier:...",
  "verifier_version": "...",
  "verifier_digest": "sha256:..."
}
```

`PASS` SHALL mean only:

```text
the declared predicate evaluated true
```

It SHALL NOT mean:

```text
the repository is correct
```

unless the predicate explicitly defines that claim and coverage supports it.

---

## §223 — Evidence Type

```json
{
  "evidence_id": "evidence:sha256:...",
  "schema": "rfl-ae/evidence/v1",
  "subject_ref": "subject:...",
  "task_ref": "task:...",
  "execution_refs": [],
  "observation_refs": [],
  "check_result_refs": [],
  "subject_digest": "sha256:...",
  "verifier_digest": "sha256:...",
  "policy_digest": "sha256:...",
  "status": "PASS",
  "completeness": "COMPLETE",
  "produced_at": "..."
}
```

Evidence identity SHALL be content-derived.

The evidence ID SHALL NOT be a random UUID alone.

---

## §224 — Coverage Type

```json
{
  "coverage_id": "coverage:...",
  "schema": "rfl-ae/coverage/v1",
  "declared_scope_ref": "scope:...",
  "executed_scope": [],
  "observed_scope": [],
  "checked_scope": [],
  "excluded_scope": [],
  "coverage_status": "COMPLETE",
  "missing": [],
  "unsupported": [],
  "skipped": [],
  "calculated_at": "..."
}
```

Coverage SHALL be independently derivable from scope and execution records where possible.

---

## §225 — VerificationGate Type

```json
{
  "gate_id": "gate:...",
  "schema": "rfl-ae/gate/v1",
  "task_ref": "task:...",
  "required_checks": [],
  "evidence_refs": [],
  "coverage_refs": [],
  "predicates": [],
  "result": "PASS",
  "evaluated_at": "...",
  "gate_policy_digest": "sha256:..."
}
```

The `result` field is an output of evaluation.

It is not an input to evaluation.

---

# Typed Implementation

## §226 — Rust Representation

The Rust implementation SHOULD use enums and newtypes to prevent category confusion.

Example:

```rust
enum CheckStatus {
    Pass,
    Fail,
    Error,
    Unknown,
    Skipped,
    Blocked,
}

enum ExecutionState {
    Created,
    Authorized,
    Running,
    Succeeded,
    Failed,
    Error,
    Cancelled,
    TimedOut,
    Blocked,
}

struct EvidenceId(String);
struct SubjectId(String);
struct ExecutionId(String);
```

The implementation SHOULD reject invalid construction rather than relying entirely on downstream validation.

---

## §227 — TypeScript Representation

TypeScript SHALL mirror semantic distinctions.

```typescript
type CheckStatus =
  | "PASS"
  | "FAIL"
  | "ERROR"
  | "UNKNOWN"
  | "SKIPPED"
  | "BLOCKED";

type ExecutionState =
  | "CREATED"
  | "AUTHORIZED"
  | "RUNNING"
  | "SUCCEEDED"
  | "FAILED"
  | "ERROR"
  | "CANCELLED"
  | "TIMED_OUT"
  | "BLOCKED";
```

The TypeScript layer MUST NOT collapse these into:

```typescript
type Status = string;
```

for protocol-critical operations.

---

## §228 — Runtime Validation

Validation SHALL occur at boundaries.

Minimum boundaries:

```text
input
  ↓
parse
  ↓
schema validation
  ↓
semantic validation
  ↓
authorization
  ↓
execution
  ↓
observation
  ↓
verification
  ↓
evidence
  ↓
coverage
  ↓
gate
```

A successful parse SHALL NOT imply semantic validity.

---

## §229 — Semantic Validation

Schema validation answers:

```text
"Is this object structurally valid?"
```

Semantic validation answers:

```text
"Does this object make sense in this protocol state?"
```

Example:

```json
{
  "state": "SUCCEEDED",
  "ended_at": null
}
```

may be structurally valid but semantically invalid.

---

## §230 — Referential Integrity

Every reference SHALL resolve to an object compatible with the referenced type.

Examples:

```text
execution.request_ref  → ToolRequest
evidence.subject_ref   → Subject
coverage.scope_ref     → Scope
gate.evidence_refs     → EvidenceRecord
```

Broken references SHALL be explicit errors.

---

## §231 — State Transition Engine

State transitions SHALL be implemented as a closed transition relation.

Conceptually:

```text
allowed(current, requested_next) → true|false
```

Example:

```text
allowed(CREATED, AUTHORIZED) = true
allowed(CREATED, RUNNING)    = false
allowed(CREATED, SUCCEEDED)  = false
```

The transition engine SHALL be independently testable.

---

## §232 — Transition Authority

Only the runtime state machine may commit execution-state transitions.

The model may propose:

```text
"request transition to RUNNING"
```

but cannot commit it.

The runtime decides:

```text
requested transition + current state + authorization + preconditions
    → committed transition
```

---

## §233 — Preconditions

Every transition SHALL have explicit preconditions.

Example:

```text
AUTHORIZED → RUNNING

requires:
  authorization.decision == ALLOW
  request exists
  tool exists
  scope valid
  runtime available
```

Failure of a precondition SHALL produce a defined state.

---

## §234 — Postconditions

A transition SHALL define postconditions.

For:

```text
RUNNING → SUCCEEDED
```

minimum requirements SHOULD include:

```text
execution completed
output recorded
exit status recorded
observation generated where applicable
```

The postcondition SHALL NOT claim semantic correctness.

---

## §235 — Transition Events

Every committed transition SHOULD emit an event:

```text
(previous_state, next_state, actor, timestamp, reason)
```

The event SHALL be immutable after commitment.

---

## §236 — Illegal Transition Test

At least one test SHALL exist for every forbidden transition class.

Examples:

```text
CREATED → SUCCEEDED
DENIED → RUNNING
RUNNING → CREATED
SUCCEEDED → RUNNING
CANCELLED → RUNNING
```

---

# Canonicalization

## §237 — Canonical Serialization

Canonical serialization SHALL define:

```text
UTF-8
deterministic object ordering
deterministic array semantics
canonical number representation
canonical escaping
canonical Unicode handling
```

The implementation SHALL use one normative canonicalization algorithm.

---

## §238 — Semantic vs Byte Equality

The protocol SHALL distinguish:

```text
semantic equality
byte equality
digest equality
```

These are related but not interchangeable.

```text
byte equality → digest equality
```

under a collision-resistant digest.

The reverse SHALL NOT be treated as a mathematical identity guarantee.

---

## §239 — Digest Construction

For an evidence object:

```text
EvidenceDigest =
    Digest(
        Canonicalize(
            EvidenceWithoutSelfDigest
        )
    )
```

The digest field SHALL NOT recursively contain itself.

---

## §240 — Determinism Test

For every canonical object fixture:

```text
parse(x)
canonicalize(x)
digest(x)
```

SHALL produce the same result across repeated executions under the same implementation/version contract.

At minimum:

```text
100 repeated compilations
```

SHOULD produce identical canonical output and digest.

---

# Evidence Engine

## §241 — Evidence Construction

Evidence SHALL be constructed from observed protocol state.

Conceptually:

```text
Execution + Observation + CheckResult + Subject
+ Verifier + Policy + Coverage
    → Evidence
```

The model SHALL NOT be allowed to directly construct authoritative evidence.

---

## §242 — Evidence Verification

Evidence validation SHALL verify:

```text
subject exists
subject digest matches
execution exists
observation exists
check result exists
verifier identity matches
policy identity matches
references resolve
status is coherent
timestamps are coherent
```

---

## §243 — Stale Evidence

Evidence SHALL be considered stale when any identity-critical dependency changes.

Example:

```text
Evidence E
     │
     ├── subject digest A
     └── verifier digest B

subject changes
     ↓
subject digest C
     ↓
E is stale
```

Stale evidence SHALL NOT silently remain `VERIFIED`.

---

## §244 — Evidence Status

Evidence status SHALL be independently evaluated.

It SHALL NOT be copied blindly from `CheckResult.status`.

For example:

```text
CheckResult = PASS
Coverage    = PARTIAL
```

does not imply:

```text
Evidence = VERIFIED
```

The evidence layer must evaluate completeness and binding.

---

# Coverage Engine

## §245 — Scope Resolution

The coverage engine SHALL resolve declared scope into a concrete population where possible.

Example:

```text
declared:   repository/*.rs
resolved:   142 files
checked:    142 files
missing:    0
```

Only then can:

```text
coverage = COMPLETE
```

be considered.

---

## §246 — Coverage Accounting

The engine SHALL account explicitly for:

```text
checked
missing
excluded
unsupported
skipped
failed
unknown
```

These populations MUST NOT disappear from the report.

---

## §247 — Coverage Monotonicity

Adding successfully checked members MAY increase coverage.

However, discovering previously unrecognized scope MAY decrease the effective coverage state.

Therefore:

```text
coverage is derived from current scope + current observations
```

not from historical optimism.

---

# Gate Engine

## §248 — Gate Inputs

A gate evaluator SHALL consume:

```text
Task
Scope
Required Checks
Check Results
Evidence
Coverage
Gate Policy
```

It SHALL NOT consume a precomputed model verdict as an authoritative input.

---

## §249 — Gate Evaluation Function

Conceptually:

```text
GateResult =
    Evaluate(
        Policy,
        Checks,
        Evidence,
        Coverage,
        Scope
    )
```

The function SHALL be deterministic for equivalent inputs.

---

## §250 — Gate Output

The gate SHALL produce:

```text
PASS
FAIL
UNKNOWN
BLOCKED
```

`ERROR` may be represented internally by the evaluator but SHOULD be normalized according to the gate policy.

The normalization rule MUST be explicit.

---

## §251 — Gate Evidence

A gate result SHALL itself be evidence-bearing.

Minimum:

```text
gate policy digest
input evidence IDs
coverage IDs
evaluation timestamp
gate evaluator digest
result
```

Thus:

```text
Gate PASS
```

is reproducible as an evaluation artifact rather than merely a log message.

---

## §252 — Gate Tampering Detection

Changing any of the following SHALL change the gate evidence identity:

```text
gate policy
required checks
evidence
coverage
predicate definitions
gate evaluator
```

unless the protocol explicitly declares the dependency non-semantic.

---

# Conformance

## §253 — Schema Conformance

The test suite SHALL validate:

```text
valid object → accepted
missing required field → rejected
wrong type → rejected
wrong enum → rejected
unknown critical field → rejected
invalid identifier → rejected
invalid digest → rejected
invalid timestamp → rejected
```

---

## §254 — Semantic Conformance

Tests SHALL include structurally valid but semantically invalid objects.

Examples:

```text
SUCCEEDED without completed execution
ALLOW without authority
PASS with missing observation
COMPLETE coverage with missing members
evidence referencing wrong subject
gate referencing stale evidence
```

These MUST fail semantic validation.

---

## §255 — Mutation Testing

The verifier SHALL be tested against planted mutations.

Minimum mutations:

```text
remove check
invert predicate
change subject digest
change verifier digest
remove coverage member
alter authorization result
alter execution state
replace evidence ID
modify policy digest
```

Expected outcome:

```text
mutation detected
```

A mutation surviving the verifier is a conformance defect.

---

## §256 — Cross-Implementation Conformance

Rust and TypeScript implementations SHALL be tested against the same fixtures.

```text
fixture
  ├── JSON Schema
  ├── Rust
  └── TypeScript
```

All three SHALL agree on:

```text
accepted
rejected
canonical representation
digest
state transition
```

Where intentional implementation differences exist, they SHALL be documented.

---

## §257 — Golden Fixtures

The repository SHALL contain immutable golden fixtures.

```text
fixtures/
├── valid/
├── invalid/
├── transitions/
├── evidence/
├── coverage/
├── gates/
├── canonicalization/
└── injection/
```

Each fixture SHOULD contain:

```text
fixture_id:
input:
expected_status:
expected_digest:
expected_error:
```

---

## §258 — Negative-Test Integrity

Negative tests SHALL assert the expected defect.

Forbidden:

```text
assert subprocess.run(...).returncode != 0
```

without establishing why the command failed.

Required pattern:

```text
execute
   ↓
observe result
   ↓
classify failure
   ↓
assert expected failure class
   ↓
assert expected finding
```

---

## §259 — No Silent Skip

A test that cannot execute SHALL produce:

```text
SKIPPED
```

or:

```text
BLOCKED
```

with an explicit reason.

A skipped test SHALL NOT count as passing coverage.

---

## §260 — Release Evidence

A v0.7 release SHALL contain:

```text
schema manifest
schema digests
typed implementation digest
fixture manifest
test execution record
negative-test execution record
canonicalization results
transition-test results
cross-language results
gate result
```

The release claim SHALL reference the actual execution evidence.

---

## §261 — Implementation Tree

Recommended repository structure:

```text
rfl-ae/
├── protocol/
│   ├── schema/
│   │   ├── task.schema.json
│   │   ├── subject.schema.json
│   │   ├── scope.schema.json
│   │   ├── authority.schema.json
│   │   ├── tool.schema.json
│   │   ├── tool-request.schema.json
│   │   ├── authorization.schema.json
│   │   ├── execution.schema.json
│   │   ├── observation.schema.json
│   │   ├── check-result.schema.json
│   │   ├── evidence.schema.json
│   │   ├── coverage.schema.json
│   │   ├── claim.schema.json
│   │   └── gate.schema.json
│   │
│   ├── canonical/
│   ├── transitions/
│   └── manifests/
│
├── crates/
│   └── protocol/
│
├── packages/
│   └── protocol/
│
├── fixtures/
│   ├── valid/
│   ├── invalid/
│   ├── transitions/
│   ├── evidence/
│   ├── coverage/
│   ├── gates/
│   ├── canonicalization/
│   └── injection/
│
├── tools/
│   ├── validate-schema
│   ├── canonicalize
│   ├── digest
│   ├── replay
│   └── conformance
│
└── tests/
    ├── schema/
    ├── semantic/
    ├── transitions/
    ├── evidence/
    ├── coverage/
    ├── gates/
    └── cross-language/
```

---

## §262 — Build Order

Implementation SHALL proceed in this order:

```text
1.  Primitive types
2.  Identifier types
3.  Digest type
4.  Timestamp type
5.  Canonicalization
6.  JSON Schemas
7.  Rust types
8.  TypeScript types
9.  Semantic validators
10. Transition engine
11. Evidence engine
12. Coverage engine
13. Gate engine
14. Golden fixtures
15. Negative fixtures
16. Mutation tests
17. Cross-language tests
18. Replay
19. Release gate
```

---

## §263 — Bootstrap Constraint

The implementation SHALL avoid circular trust.

The component under verification SHALL NOT be the sole source of evidence proving its own correctness.

At minimum:

```text
implementation
     ↓
test
     ↓
independent observation
     ↓
evidence
```

For high-assurance components:

```text
implementation verifier ≠ implementation under test
```

SHOULD hold.

---

## §264 — Generated Code Constraint

Generated Rust/TypeScript types SHALL record:

```text
source schema digest
generator identity
generator version
generation timestamp
```

If generated artifacts are committed, the repository SHALL be able to verify that they correspond to the declared source schema.

---

## §265 — Schema Drift Detection

The build SHALL detect:

```text
schema changed but generated types unchanged
```

and:

```text
generated types changed but schema unchanged
```

where generated artifacts are expected to be deterministic.

---

## §266 — Protocol Manifest

A manifest SHALL bind the implementation components:

```yaml
protocol: rfl-ae
version: "0.7"

schemas:
  - id: rfl-ae/task/v1
    digest: sha256:...

implementations:
  rust:
    digest: sha256:...
  typescript:
    digest: sha256:...

canonicalization:
  algorithm: <identifier>
  digest: sha256:...

fixtures:
  digest: sha256:...

conformance:
  execution_ref: execution:...
```

---

## §267 — Conformance Claim

A conformant implementation MAY claim:

```text
RFL-AE v0.7 protocol conformance
```

only if the release gate establishes the required conditions.

It SHALL NOT claim:

```text
fully verified
production safe
correct
secure
```

unless separate predicates and evidence establish those claims.

---

## §268 — Evidence Claim Hierarchy

The implementation SHALL distinguish:

```text
SCHEMA_VALID
TYPE_VALID
SEMANTICALLY_VALID
CONFORMANCE_VERIFIED
RELEASE_GATED
```

These states SHALL NOT be collapsed.

Example:

```text
SchemaValid = true
```

does not imply:

```text
ConformanceVerified = true
```

---

## §269 — Required Artifact Set

The minimum v0.7 artifact set is:

```text
A. normative schemas
B. canonicalization implementation
C. typed protocol implementation
D. semantic validator
E. transition engine
F. evidence validator
G. coverage evaluator
H. gate evaluator
I. positive fixtures
J. negative fixtures
K. execution evidence
L. conformance gate
```

---

## §270 — Final v0.7 Invariant

```text
THE SCHEMA DEFINES STRUCTURE.
THE TYPE SYSTEM DEFINES IMPLEMENTATION REPRESENTATION.
THE VALIDATOR DEFINES SEMANTIC ACCEPTANCE.
THE TRANSITION ENGINE DEFINES LEGAL STATE CHANGE.
THE RUNTIME DEFINES EXECUTION AUTHORITY.
THE OBSERVATION LAYER RECORDS WHAT OCCURRED.
THE CHECKER EVALUATES DECLARED PREDICATES.
THE EVIDENCE LAYER BINDS RESULTS TO THEIR DEPENDENCIES.
THE COVERAGE LAYER BOUNDS THE POPULATION.
THE GATE DERIVES THE RELEASE STATE.
NO GENERATED ARTIFACT MAY OVERRIDE THE NORMATIVE CONTRACT.
NO MODEL OUTPUT MAY SUBSTITUTE FOR RUNTIME STATE.
NO SELF-ASSERTION MAY SUBSTITUTE FOR EXECUTION EVIDENCE.
NO PARTIAL RESULT MAY MASQUERADE AS COMPLETE COVERAGE.
NO STRUCTURAL VALIDITY CLAIM MAY MASQUERADE AS SEMANTIC CONFORMANCE.
```

---

## §271 — Absolute Implementation Law

```text
NO SCHEMA   → NO MACHINE CONTRACT
NO TYPE     → NO SAFE IMPLEMENTATION BOUNDARY
NO SEMANTIC VALIDATOR → NO SEMANTIC CONFORMANCE
NO TRANSITION ENGINE  → NO TRUSTWORTHY STATE MACHINE
NO EXECUTION EVIDENCE → NO VERIFIED IMPLEMENTATION CLAIM
NO NEGATIVE TESTS     → NO EVIDENCE THAT THE VERIFIER REJECTS DEFECTS
NO CROSS-IMPLEMENTATION CONFORMANCE → NO CLAIM OF SHARED PROTOCOL SEMANTICS
NO RELEASE GATE       → NO RELEASE CLAIM
```

**v0.7 therefore converts the RFL-AE instruction architecture into an executable protocol contract.**

The next layer is v0.8: **the actual implementation blueprint — concrete JSON Schema files, Rust `struct`/`enum` definitions, TypeScript types, canonicalization algorithm, transition-table implementation, evidence validator, and conformance-test harness.**
