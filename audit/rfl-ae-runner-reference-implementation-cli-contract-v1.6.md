# RFL-AE — Runner Reference Implementation & CLI Contract v1.6

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with type declarations, trait declarations, enumerations, command lists, pipelines, trees, diagrams and law lists collapsed into inline runs; line breaks, indentation, and fenced structure restored. Restored from inline form: the two implementation-direction chains (§1402), the reference-implementation inequality (§1403), the repository tree (§1404), the dependency-direction chain (§1405), the kernel/adapter separation diagram (§1406), the pure-input rule (§1407), the side-effect boundary diagram (§1408), the eight strong identifier types (§1409), the non-interchangeability inequalities (§1410), the digest, timestamp, status, task, subject, scope, tool, request, command, handle, result, checker, evidence and coverage type and trait declarations (§1411, §1414, §1416–§1436, §1438–§1439), the execution, observation, check, predicate, evidence and gate interface bodies (§1420–§1435, §1438), the shell-mode and evidence-validation enumerations (§1423, §1435), the transition function and error enumeration (§1450, §1452), the runner composition, run request/result and run-status declarations (§1453, §1455–§1457), the runner state machine (§1460), the authorization-record fields (§1464), the environment, clock, random, network, process, fixture, manifest, planner, scheduler, worker, retry, cache and event interfaces (§1468–§1503), the schedule and event declarations (§1483, §1484, §1502), the worker result binding (§1490), the cache-safety identity set (§1497), the cache-versus-execution states (§1499), the CLI root, the seven command groups and the diagnostic command pair (§1505–§1515), the output-mode flags (§1516, §1518, §1519), the exit-code table (§1520), the run invocation form and its precondition fields (§1522, §1524), the resume, replay, dry-run, plan-output, authorization-preview, offline, deterministic, seed, concurrency, retry, cache and CI modes (§1526–§1541), the configuration precedence chain (§1542), the filesystem store layout (§1443), the store identity, timeout, atomic-rename and transaction sequences (§1446, §1585, §1591), the artifact, truncation and redaction fields (§1559, §1562), the error taxonomy (§1563), the error-context fields (§1565), the verification-methodology sets (§1570–§1573, §1575, §1578, §1580–§1583, §1595, §1599), the absolute implementation invariant, its law list, its architecture diagram and the central implementation law (§1600). Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Verbatim anomaly recorded, not corrected.** §1410's two inequalities name `FixtureId` and `ExecutionId` (twice) and `EvidenceId`; §1409 declares eight strong identifier types, of which `AttemptId`, `CheckId`, `CoverageId`, `GateId` and `PlanDigest` are not used in any inequality. Recorded as supplied; analysed in Part XXVII.
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all sixteen recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Heading level note.** As with v1.2–v1.5, v1.6 marks sections at `##` under a document-level `#` title. Preserved as supplied.
>
> **Standing.** Begins at **§1401**, continuing [`v1.5`](rfl-ae-conformance-runner-orchestration-v1.5.md) (§1201–§1400). v1.5 ends at §1400 and v1.6 opens at §1401 — **the range is contiguous**. The effective specification is now **§0–§1600 across sixteen supplied documents**. **Citation rule: §1–§28 is occupied twice** (v0.1 §1–§28 and v0.2 §0–§40 share 28 numbers, zero identical headings) — citations in that range MUST name the document. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Declared dependencies.** v1.6 states its own predecessor set: *"RFL-AE v0.7, v0.8, v1.0, v1.1, v1.4, v1.5"* — six documents. As with v1.5, the set **omits v1.2 and v1.3**, whose evidence-record, coverage and conformance vocabularies §1435, §1436 and §1499 restate. Recorded verbatim; analysed in Part XXVII.
>
> **Status:** Normative implementation contract
> **Layer:** Runner / CLI / Runtime Kernel
> **Range:** §1401–§1600
> **Purpose:** Define the reference implementation boundary for the RFL-AE Conformance Runner: crate boundaries, typed interfaces, CLI contracts, persistence interfaces, execution adapters, checker interfaces, evidence construction, coverage aggregation, gate invocation, replay, and conformance reporting.

---

## §1401 — Purpose

This specification defines the reference implementation boundary for the RFL-AE Conformance Runner.

It converts the orchestration semantics of v1.5 into:

- Rust crate boundaries;
- typed interfaces;
- command-line contracts;
- persistence interfaces;
- execution adapters;
- checker interfaces;
- evidence construction;
- coverage aggregation;
- gate invocation;
- replay;
- conformance reporting.

The reference implementation MUST implement the semantics defined by the normative protocol.

---

## §1402 — Implementation Principle

The implementation MUST follow:

```text
Specification
    ↓
Semantic Model
    ↓
Typed Interface
    ↓
Implementation
    ↓
Executable Test
    ↓
Evidence
```

The reverse direction is prohibited:

```text
Implementation
    ↓
retroactive specification
```

---

## §1403 — Reference Implementation

The reference implementation is:

an executable implementation conforming to the protocol, not the protocol itself.

Therefore:

```text
reference implementation ≠ semantic authority
```

---

## §1404 — Repository Boundary

Recommended repository structure:

```text
rfl-ae/
├── protocol/
│   ├── schema/
│   ├── canonical/
│   ├── manifests/
│   ├── transitions/
│   └── policies/
│
├── crates/
│   ├── rfl-protocol/
│   ├── rfl-types/
│   ├── rfl-schema/
│   ├── rfl-canonical/
│   ├── rfl-authorization/
│   ├── rfl-executor/
│   ├── rfl-observation/
│   ├── rfl-check/
│   ├── rfl-evidence/
│   ├── rfl-coverage/
│   ├── rfl-conformance/
│   ├── rfl-gate/
│   ├── rfl-runner/
│   ├── rfl-persistence/
│   └── rfl-cli/
│
├── fixtures/
├── tools/
├── tests/
└── docs/
```

---

## §1405 — Dependency Direction

The dependency graph MUST remain acyclic.

Recommended:

```text
rfl-types
    ↓
rfl-protocol
    ↓
rfl-canonical
    ↓
rfl-validation
    ↓
rfl-transition
    ↓
domain engines
    ↓
rfl-runner
    ↓
rfl-cli
```

Infrastructure adapters MUST NOT redefine domain semantics.

---

## §1406 — Kernel / Adapter Separation

The implementation SHOULD separate:

```text
PURE KERNEL
───────────
types
canonicalization
validation
transitions
predicates
coverage
gate evaluation

ADAPTERS
────────
filesystem
process execution
CI
network
workers
storage
terminal
UI
```

---

## §1407 — Pure Kernel

Pure semantic functions SHOULD have:

```text
input → deterministic output
```

with no:

- filesystem access;
- network access;
- environment mutation;
- global mutable state.

---

## §1408 — Side-Effect Boundary

Side effects MUST cross explicit interfaces.

Conceptually:

```text
Pure Domain
     │
     ▼
Port / Trait
     │
     ▼
Adapter
     │
     ▼
Operating System
```

---

## §1409 — Strong Identifier Types

Stringly typed identifiers SHOULD NOT cross core APIs.

Recommended Rust pattern:

```rust
struct FixtureId(String);
struct ExecutionId(String);
struct AttemptId(String);
struct CheckId(String);
struct EvidenceId(String);
struct CoverageId(String);
struct GateId(String);
struct PlanDigest(Digest);
```

---

## §1410 — Identifier Non-Interchangeability

The compiler SHOULD make accidental substitution difficult:

```text
FixtureId ≠ ExecutionId
ExecutionId ≠ EvidenceId
```

---

## §1411 — Digest Type

All protocol digests SHOULD use a dedicated type.

```rust
struct Digest {
    algorithm: DigestAlgorithm,
    bytes: Vec<u8>,
}
```

Raw hexadecimal strings SHOULD NOT be the core semantic type.

---

## §1412 — Digest Algorithm

The initial required algorithm is:

```text
SHA-256
```

Additional algorithms MAY be supported.

Algorithm identity MUST be preserved.

---

## §1413 — Timestamp Type

Semantic timestamps SHOULD use an explicit UTC representation.

Execution durations SHOULD use monotonic measurements where available.

---

## §1414 — Closed Status Types

Protocol status enums SHOULD be closed.

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
```

Unknown future values MUST NOT silently map to `Pass`.

---

## §1415 — Forward Compatibility

Serialization MAY support unknown enum values for forward compatibility.

The semantic layer MUST represent them as an explicit unknown/unsupported state.

---

## §1416 — Task Type

The core task type SHOULD contain:

```rust
struct Task {
    task_id: TaskId,
    protocol: ProtocolIdentity,
    subject: SubjectIdentity,
    scope: Scope,
    authority: Authority,
    plan: PlanIdentity,
}
```

---

## §1417 — Subject Type

A subject MUST have immutable identity.

```rust
struct SubjectIdentity {
    subject_id: SubjectId,
    kind: SubjectKind,
    digest: Digest,
    metadata: SubjectMetadata,
}
```

---

## §1418 — Scope Type

Scope MUST be explicit.

```rust
struct Scope {
    subject: SubjectId,
    resources: ResourceScope,
    tools: ToolScope,
    network: NetworkScope,
    time: TimeScope,
}
```

---

## §1419 — Tool Type

```rust
struct ToolIdentity {
    name: String,
    version: String,
    binary_digest: Option<Digest>,
    source_identity: Option<SourceIdentity>,
    configuration_digest: Option<Digest>,
}
```

---

## §1420 — Executor Trait

The executor SHOULD expose a narrow interface:

```rust
trait Executor {
    fn execute(
        &self,
        request: ExecutionRequest,
    ) -> Result<ExecutionHandle, ExecutionError>;
}
```

The executor MUST NOT decide protocol conformance.

---

## §1421 — Execution Request

```rust
struct ExecutionRequest {
    execution_id: ExecutionId,
    attempt_id: AttemptId,
    fixture: FixtureIdentity,
    command: CommandSpec,
    scope: Scope,
    environment: EnvironmentSpec,
    resources: ResourceLimits,
}
```

---

## §1422 — Command Specification

A command SHOULD be represented structurally:

```rust
struct CommandSpec {
    program: String,
    args: Vec<String>,
    working_directory: PathBuf,
}
```

Shell interpolation MUST NOT be implicit.

---

## §1423 — Shell Execution

Shell execution MAY be supported as an adapter.

It MUST be explicit:

```text
execution_mode = DIRECT
execution_mode = SHELL
```

---

## §1424 — Shell Safety

The runner MUST NOT construct shell commands by concatenating untrusted fixture strings into a command line.

---

## §1425 — Execution Handle

An execution handle SHOULD expose:

```rust
trait ExecutionHandle {
    fn wait(&mut self) -> Result<ExecutionResult, ExecutionError>;
    fn cancel(&mut self) -> Result<(), ExecutionError>;
}
```

---

## §1426 — Execution Result

```rust
struct ExecutionResult {
    status: ExecutionStatus,
    exit: ExitStatus,
    stdout: ArtifactRef,
    stderr: ArtifactRef,
    outputs: Vec<ArtifactRef>,
    resources: ResourceUsage,
}
```

---

## §1427 — Observer Trait

Observation MUST be a separate interface.

```rust
trait Observer {
    fn observe(
        &self,
        execution: &ExecutionResult,
    ) -> Result<Observation, ObservationError>;
}
```

---

## §1428 — Checker Trait

```rust
trait Checker {
    fn check(
        &self,
        input: CheckInput,
    ) -> Result<CheckResult, CheckerError>;
}
```

The checker MUST evaluate the declared predicate.

---

## §1429 — Check Input

```rust
struct CheckInput {
    fixture: Fixture,
    expected: ExpectedResult,
    observation: Observation,
    predicate: PredicateIdentity,
}
```

---

## §1430 — Checker Independence

A checker MUST NOT receive a runner-produced Boolean as its semantic answer.

It receives evidence from which it derives the result.

---

## §1431 — Predicate Identity

```rust
struct PredicateIdentity {
    predicate_id: PredicateId,
    version: String,
    digest: Digest,
}
```

Predicate mutation changes predicate identity.

---

## §1432 — Evidence Builder

```rust
trait EvidenceBuilder {
    fn build(
        &self,
        input: EvidenceInput,
    ) -> Result<EvidenceRecord, EvidenceError>;
}
```

---

## §1433 — Evidence Input

Evidence input MUST include enough identity to establish closure:

```rust
struct EvidenceInput {
    subject: SubjectIdentity,
    fixture: FixtureIdentity,
    execution: ExecutionRecord,
    observation: Observation,
    check: CheckResult,
}
```

---

## §1434 — Evidence Validator

```rust
trait EvidenceValidator {
    fn validate(
        &self,
        evidence: &EvidenceRecord,
    ) -> EvidenceValidation;
}
```

---

## §1435 — Evidence Validation

Validation MUST distinguish:

```text
VALID
INVALID
STALE
INCOMPLETE
UNKNOWN
```

---

## §1436 — Coverage Evaluator

```rust
trait CoverageEvaluator {
    fn evaluate(
        &self,
        population: &FixturePopulation,
        results: &[CheckResult],
    ) -> CoverageRecord;
}
```

---

## §1437 — Coverage Authority

Coverage MUST use the declared fixture population.

The evaluator MUST NOT silently replace it with filesystem discovery.

---

## §1438 — Gate Evaluator

```rust
trait GateEvaluator {
    fn evaluate(
        &self,
        input: GateInput,
    ) -> GateResult;
}
```

The gate evaluator SHOULD be pure.

---

## §1439 — Persistence Trait

```rust
trait EvidenceStore {
    fn append(
        &mut self,
        record: EvidenceRecord,
    ) -> Result<PersistedEvidence, StoreError>;

    fn get(
        &self,
        id: &EvidenceId,
    ) -> Result<EvidenceRecord, StoreError>;
}
```

---

## §1440 — Append Semantics

`append()` MUST NOT overwrite an existing record with the same immutable identity.

Duplicate identity with different content MUST be an error.

---

## §1441 — Read-Back Contract

Successful persistence MUST permit subsequent retrieval and validation.

---

## §1442 — Persistence Atomicity

A store MUST provide a transactional or equivalent atomic commit boundary for individual authoritative records.

---

## §1443 — Filesystem Store

A reference filesystem implementation MAY use:

```text
runs/
  <run_id>/
    plan.json
    executions/
    observations/
    checks/
    evidence/
    coverage.json
    conformance.json
    gate.json
    manifest.json
    events.jsonl
```

---

## §1444 — File Identity

Filenames MUST NOT be the sole source of identity.

Each file MUST contain its own typed identity.

---

## §1445 — Canonical Serialization

Authoritative JSON objects MUST be canonicalizable.

Recommended:

```text
RFC 8785 JSON Canonicalization Scheme
```

The exact canonicalization policy MUST be versioned.

---

## §1446 — Canonical Digest

```text
canonical_bytes
     ↓
SHA-256
     ↓
content_digest
```

The digest MUST refer to canonical bytes, not pretty-printed output.

---

## §1447 — Serialization Boundary

Serialization MUST be treated as representation.

Semantic meaning belongs to the protocol model.

---

## §1448 — Schema Validation

Every persisted authoritative object MUST be structurally schema-valid.

---

## §1449 — Semantic Validation

Schema validity MUST NOT be treated as semantic conformance.

Both checks are required where applicable.

---

## §1450 — Transition Engine

State transitions SHOULD be represented as an explicit function:

```rust
fn transition(
    state: State,
    event: Event,
) -> Result<State, TransitionError>;
```

---

## §1451 — Closed Transition Relation

Illegal transitions MUST return a typed error.

They MUST NOT be silently ignored.

---

## §1452 — Transition Error

Recommended:

```rust
enum TransitionError {
    InvalidState,
    InvalidEvent,
    PreconditionsNotMet,
    Unauthorized,
    ScopeViolation,
    StaleIdentity,
}
```

---

## §1453 — Runner Object

The runner SHOULD compose the required ports:

```rust
struct Runner<E, O, C, S, G> {
    executor: E,
    observer: O,
    checker: C,
    store: S,
    gate: G,
}
```

---

## §1454 — Runner Does Not Own Semantics

The runner orchestrates.

It MUST NOT embed hidden versions of:

- fixture predicates;
- coverage policy;
- release policy;
- canonicalization semantics.

---

## §1455 — Run Request

```rust
struct RunRequest {
    plan: ExecutionPlan,
    mode: RunMode,
    concurrency: ConcurrencyPolicy,
    retry: RetryPolicy,
    persistence: PersistencePolicy,
}
```

---

## §1456 — Run Result

```rust
struct RunResult {
    run_id: RunId,
    status: RunStatus,
    coverage: CoverageRecord,
    conformance: ConformanceRecord,
    gate: Option<GateResult>,
    manifest: Option<ReleaseManifest>,
}
```

---

## §1457 — Run Status

```rust
enum RunStatus {
    Completed,
    Partial,
    Blocked,
    Error,
    Cancelled,
}
```

---

## §1458 — Completed Run

`Completed` means orchestration completed.

It does not imply conformance.

---

## §1459 — Partial Run

A run is partial when required work remains unresolved.

---

## §1460 — Runner State Machine

The implementation MUST enforce:

```text
DECLARED
    ↓
PREFLIGHT
    ↓
READY
    ↓
SCHEDULED
    ↓
DISPATCHED
    ↓
RUNNING
    ↓
OBSERVED
    ↓
EVALUATED
    ↓
EVIDENCE_BOUND
    ↓
COVERAGE_BOUND
    ↓
GATED
    ↓
COMPLETED
```

---

## §1461 — No State Skipping

A state MAY be internally optimized away only if the implementation still produces all required semantic records.

The optimization MUST NOT change observable protocol semantics.

---

## §1462 — Authorization Port

```rust
trait Authorizer {
    fn authorize(
        &self,
        request: &ToolRequest,
    ) -> AuthorizationDecision;
}
```

---

## §1463 — Authorization Before Execution

The runner MUST invoke authorization before executor dispatch.

---

## §1464 — Authorization Record

Every authorized execution SHOULD persist:

```text
authorization_id
request_id
policy_id
decision
scope
reason
timestamp
```

---

## §1465 — Authorization Denial

Denial MUST prevent dispatch.

A denied request MUST NOT generate an execution record claiming execution occurred.

A decision record MAY exist.

---

## §1466 — Scope Checker

```rust
trait ScopeChecker {
    fn permits(
        &self,
        scope: &Scope,
        request: &ToolRequest,
    ) -> ScopeDecision;
}
```

---

## §1467 — Scope Violation

A scope violation MUST stop dispatch.

---

## §1468 — Environment Provider

```rust
trait EnvironmentProvider {
    fn describe(&self) -> EnvironmentDescriptor;
}
```

---

## §1469 — Environment Snapshot

The runner SHOULD capture the environment before execution.

---

## §1470 — Environment Drift

If the environment changes materially during a run, the affected evidence MUST identify the change.

---

## §1471 — Clock Provider

Time-dependent logic SHOULD use an injected clock abstraction in deterministic tests.

---

## §1472 — Random Provider

Randomness SHOULD use an injected seedable source.

---

## §1473 — Network Provider

Network access SHOULD cross an explicit policy-controlled adapter.

---

## §1474 — Process Provider

OS process execution MUST cross the executor interface.

This makes the pure runner testable without launching real processes.

---

## §1475 — Fixture Loader

```rust
trait FixtureLoader {
    fn load(
        &self,
        id: &FixtureId,
    ) -> Result<Fixture, FixtureLoadError>;
}
```

---

## §1476 — Fixture Digest Verification

Loaded fixture bytes MUST be verified against the manifest digest before execution.

---

## §1477 — Fixture Mismatch

Digest mismatch MUST produce:

```text
INVALID
```

or:

```text
STALE
```

according to policy.

Execution MUST NOT proceed as though the fixture were unchanged.

---

## §1478 — Manifest Loader

```rust
trait ManifestLoader {
    fn load(
        &self,
    ) -> Result<FixtureManifest, ManifestError>;
}
```

---

## §1479 — Manifest Digest

The loaded manifest MUST be canonicalized and compared with the expected identity where one is required.

---

## §1480 — Execution Planner

```rust
trait Planner {
    fn plan(
        &self,
        request: RunRequest,
    ) -> Result<ExecutionPlan, PlanningError>;
}
```

---

## §1481 — Planner Determinism

For deterministic mode:

```text
same inputs → same plan
```

MUST hold.

---

## §1482 — Scheduler

```rust
trait Scheduler {
    fn schedule(
        &self,
        plan: &ExecutionPlan,
    ) -> Result<Schedule, SchedulingError>;
}
```

---

## §1483 — Schedule Representation

```rust
struct Schedule {
    plan_digest: Digest,
    batches: Vec<ScheduleBatch>,
}
```

---

## §1484 — Schedule Batch

```rust
struct ScheduleBatch {
    batch_id: BatchId,
    fixtures: Vec<FixtureId>,
}
```

Fixtures in the same batch MUST be mutually runnable under the declared policy.

---

## §1485 — Stable Scheduling

Fixture ordering within deterministic batches MUST be stable.

---

## §1486 — Concurrency Limit

The scheduler MUST honor the configured concurrency limit.

---

## §1487 — Resource-Aware Scheduling

The scheduler MAY account for:

- CPU;
- memory;
- process count;
- network;
- storage.

The scheduling decision MUST remain reproducible where deterministic scheduling is required.

---

## §1488 — Worker Interface

Remote execution SHOULD use:

```rust
trait Worker {
    fn execute(
        &self,
        request: WorkerRequest,
    ) -> Result<WorkerResult, WorkerError>;
}
```

---

## §1489 — Worker Trust

Worker output is untrusted until validated by the controlling runner.

---

## §1490 — Worker Result Binding

Worker results MUST bind:

```text
worker_id
plan_digest
execution_id
attempt_id
fixture_digest
```

---

## §1491 — Worker Replay

A worker result MUST be replayable or independently inspectable according to the declared reproducibility policy.

---

## §1492 — Retry Controller

```rust
trait RetryController {
    fn should_retry(
        &self,
        attempt: &ExecutionRecord,
    ) -> RetryDecision;
}
```

---

## §1493 — Retry Non-Overwrite

Retry execution MUST create a new attempt record.

---

## §1494 — Retry Evidence

Each materially relevant attempt SHOULD have independently addressable evidence.

---

## §1495 — Cache Interface

```rust
trait ExecutionCache {
    fn lookup(
        &self,
        key: &CacheKey,
    ) -> CacheResult;

    fn insert(
        &mut self,
        key: CacheKey,
        value: CachedExecution,
    ) -> Result<(), CacheError>;
}
```

---

## §1496 — Cache Key

The cache key MUST include all execution-affecting identity fields.

---

## §1497 — Cache Safety

A cache MUST NOT return an old result when:

```text
fixture_digest
tool_digest
environment_digest
predicate_digest
```

or another execution-affecting identity has changed.

---

## §1498 — Cache Provenance

A cache hit MUST identify the original execution record.

---

## §1499 — Cache vs Execution

The runner MUST preserve:

```text
EXECUTED
```

and:

```text
REUSED
```

as distinct provenance states.

---

## §1500 — Event Sink

```rust
trait EventSink {
    fn append(
        &mut self,
        event: RunnerEvent,
    ) -> Result<(), EventError>;
}
```

---

## §1501 — Event Persistence

Event persistence SHOULD be append-only.

---

## §1502 — Event Identity

```rust
struct RunnerEvent {
    event_id: EventId,
    run_id: RunId,
    parent: Option<EventId>,
    event_type: EventType,
    payload_digest: Digest,
    timestamp: Timestamp,
}
```

---

## §1503 — Event Replay

A run SHOULD be reconstructible from its durable event and result records.

---

## §1504 — CLI Boundary

The CLI is an adapter over the runner API.

The CLI MUST NOT implement independent protocol semantics.

---

## §1505 — CLI Root

Recommended executable:

```text
rfl
```

---

## §1506 — Manifest Commands

Required:

```text
rfl manifest validate
rfl manifest digest
```

---

## §1507 — Fixture Commands

Required:

```text
rfl fixture list
rfl fixture validate
rfl fixture digest
```

---

## §1508 — Plan Commands

Required:

```text
rfl plan build
rfl plan validate
rfl plan digest
```

---

## §1509 — Run Commands

Required:

```text
rfl run
rfl run resume
rfl run replay
rfl run inspect
```

---

## §1510 — Execution Commands

Required diagnostic commands:

```text
rfl execution inspect <execution-id>
rfl execution replay <execution-id>
```

---

## §1511 — Evidence Commands

Required:

```text
rfl evidence validate
rfl evidence inspect
rfl evidence digest
```

---

## §1512 — Coverage Commands

Required:

```text
rfl coverage evaluate
rfl coverage inspect
```

---

## §1513 — Conformance Commands

Required:

```text
rfl conformance evaluate
rfl conformance inspect
```

---

## §1514 — Gate Commands

Required:

```text
rfl gate evaluate
rfl gate inspect
```

---

## §1515 — Release Commands

Required:

```text
rfl release candidate
rfl release verify
rfl release manifest
```

Publishing MUST remain separate.

---

## §1516 — JSON Output

Every command MUST support a machine-readable output mode.

Recommended:

```text
--format json
```

---

## §1517 — Human Output

Human output MAY provide:

```text
summary
diagnostics
counts
failure explanations
```

It MUST be derived from authoritative records.

---

## §1518 — Quiet Mode

```text
--quiet
```

MAY suppress non-authoritative presentation.

It MUST NOT suppress required machine records.

---

## §1519 — Verbose Mode

```text
--verbose
```

MAY expose diagnostic information.

Verbose output MUST NOT alter semantic execution.

---

## §1520 — Exit Code Contract

Exit codes MUST represent CLI invocation state, not replace protocol result state.

Recommended:

```text
0  command completed with requested semantic operation successful
1  semantic operation produced failure/non-conformance
2  invalid CLI usage
3  blocked/precondition unavailable
4  internal execution error
5  unknown/incomplete result
6  stale/identity mismatch
7  cancelled
```

Exact numeric values MUST be frozen before release.

---

## §1521 — Exit Code Law

The exit code MUST NOT be interpreted as the complete evidence record.

---

## §1522 — `run` Command

Conceptually:

```text
rfl run \
  --manifest fixtures/manifest.yaml \
  --subject subject.json \
  --policy conformance.yaml
```

---

## §1523 — Run Preconditions

`rfl run` MUST fail before execution when required manifests or identities cannot be established.

---

## §1524 — Run Directory

The run directory SHOULD be content-addressable or bound to:

```text
run_id
plan_digest
```

---

## §1525 — Run Lock

Concurrent writers to the same authoritative run MUST be prevented.

---

## §1526 — Resume Command

```text
rfl run resume <run-id>
```

MUST load persisted state and continue only unfinished work.

---

## §1527 — Resume Safety

Resume MUST verify plan identity before continuing.

---

## §1528 — Replay Command

```text
rfl run replay <run-id>
```

MUST create a new replay identity.

---

## §1529 — Inspect Command

Inspection MUST be read-only.

It MUST NOT mutate authoritative evidence.

---

## §1530 — Dry Run

The CLI SHOULD support:

```text
rfl run --dry-run
```

Dry run MUST construct and validate the execution plan without claiming that execution occurred.

---

## §1531 — Plan Output

Dry-run output SHOULD include:

```text
plan_digest
fixture_count
schedule
authorization decisions
resource requirements
blocked prerequisites
```

---

## §1532 — No Execution Claim

A dry run MUST NOT create `EXECUTED` records.

---

## §1533 — Authorization Preview

A dry run MAY show authorization decisions.

It MUST distinguish:

```text
would_authorize
```

from:

```text
authorized_and_executed
```

---

## §1534 — Offline Mode

Recommended:

```text
rfl run --offline
```

The runner MUST enforce network denial rather than merely documenting it.

---

## §1535 — Deterministic Mode

Recommended:

```text
rfl run --deterministic
```

This mode MUST freeze:

- schedule policy;
- randomness;
- canonicalization;
- relevant environment inputs.

---

## §1536 — Seed

Recommended:

```text
--seed <value>
```

The seed MUST become part of execution identity when it affects results.

---

## §1537 — Concurrency CLI

Recommended:

```text
--jobs <N>
```

The value MUST be captured in execution metadata.

---

## §1538 — Retry CLI

Recommended:

```text
--retry <policy>
```

CLI retry options MUST NOT bypass protocol retry policy.

---

## §1539 — Cache CLI

Recommended:

```text
--cache --no-cache
```

The selected mode MUST be recorded.

---

## §1540 — CI Mode

The runner SHOULD expose an explicit CI mode:

```text
rfl run --ci
```

CI identity MUST be captured from the environment or supplied through trusted metadata.

---

## §1541 — Local vs CI

The same executable MAY run locally and in CI.

The execution context MUST remain distinguishable.

---

## §1542 — Configuration Precedence

Recommended:

```text
immutable protocol
    >
release policy
    >
manifest
    >
explicit authorized CLI
    >
configuration file
    >
environment defaults
```

Lower-precedence configuration MUST NOT override higher-precedence constraints.

---

## §1543 — Unknown Configuration

Unknown configuration keys SHOULD cause an error in strict mode.

Silently ignoring a typo in a security-relevant option is prohibited.

---

## §1544 — Configuration Digest

Execution-affecting configuration MUST have a digest.

---

## §1545 — Environment Variables

Only declared environment variables SHOULD affect semantic execution.

Uncontrolled environment inheritance SHOULD be disabled for reproducible mode.

---

## §1546 — Working Directory

Working directory MUST be explicit.

---

## §1547 — Path Canonicalization

Paths MUST be canonicalized according to the platform policy before scope checks.

---

## §1548 — Temporary Directory

Each isolated execution SHOULD receive a unique temporary directory.

---

## §1549 — Cleanup

Cleanup MUST be attempted after execution.

Cleanup failure MUST be recorded.

---

## §1550 — Cleanup Semantics

Cleanup failure MUST NOT erase the execution result.

It creates additional execution evidence.

---

## §1551 — Artifact Collector

```rust
trait ArtifactCollector {
    fn collect(
        &self,
        context: ArtifactContext,
    ) -> Result<Vec<ArtifactRef>, ArtifactError>;
}
```

---

## §1552 — Artifact Digest

Every authoritative artifact MUST have a content digest.

---

## §1553 — Artifact Size

Artifact size MUST be recorded where persistence permits.

---

## §1554 — Artifact Type

Artifact media/type metadata SHOULD be recorded.

---

## §1555 — Artifact Provenance

Every produced artifact MUST identify its producer execution.

---

## §1556 — Artifact Collision

Two different contents MUST NOT occupy the same immutable artifact identity.

---

## §1557 — Digest Collision Handling

A detected digest/content mismatch MUST produce an integrity error.

---

## §1558 — Standard Streams

stdout and stderr SHOULD be represented as artifacts rather than embedded unboundedly inside execution JSON.

---

## §1559 — Truncation

If streams are truncated, the execution record MUST identify:

```text
truncated
original_size if known
retained_size
```

---

## §1560 — Large Output

Large output SHOULD be stored externally and referenced by digest.

---

## §1561 — Secret Redaction

Redaction MUST be explicit and observable.

A redacted artifact MUST NOT be presented as the original artifact.

---

## §1562 — Redaction Identity

The runner SHOULD preserve:

```text
original_digest where safely computable
redacted_digest
redaction_policy_digest
```

without exposing secret content.

---

## §1563 — Error Taxonomy

Implementation errors SHOULD be classified:

```text
ConfigurationError
ManifestError
IdentityError
AuthorizationError
ScopeError
PlanningError
SchedulingError
ExecutionError
ObservationError
CheckerError
EvidenceError
CoverageError
GateError
PersistenceError
IntegrityError
ReplayError
InternalError
```

---

## §1564 — Error Serialization

Errors MUST contain stable machine-readable codes.

Human messages are non-authoritative projections.

---

## §1565 — Error Context

Errors SHOULD include:

```text
run_id
fixture_id
execution_id
attempt_id
component
```

where known.

---

## §1566 — Panic Boundary

A panic or unhandled exception MUST NOT be interpreted as a semantic fixture failure.

It is an implementation error unless explicitly converted by a declared boundary.

---

## §1567 — Rust Safety

The reference Rust implementation SHOULD use:

```text
#![forbid(unsafe_code)]
```

where practical.

Any required `unsafe` MUST be isolated behind a documented trust boundary.

---

## §1568 — FFI

FFI MUST be treated as a trust boundary.

Inputs crossing FFI MUST undergo explicit validation.

---

## §1569 — Miri

Unsafe-sensitive Rust components SHOULD be tested with Miri where applicable.

---

## §1570 — Property Tests

Core pure components SHOULD have property tests for:

- canonicalization;
- digest stability;
- transition closure;
- coverage;
- gate evaluation;
- identifier parsing.

---

## §1571 — Fuzzing

Parsers and untrusted-input boundaries SHOULD be fuzzed.

Priority targets:

```text
manifest parser
fixture parser
canonicalizer
schema decoder
CLI parser
event decoder
```

---

## §1572 — Negative Tests

Every core validator SHOULD have negative fixtures.

At minimum:

```text
valid input
near-invalid input
malformed input
stale input
identity mismatch
```

---

## §1573 — Mutation Tests

The runner itself MUST be subjected to mutation testing.

Critical mutations include:

```text
PASS → FAIL
FAIL → PASS
BLOCKED → PASS
UNKNOWN → PASS
SKIPPED → PASS
stale → valid
missing fixture → ignored
authorization → bypassed
scope → widened
digest → unchecked
```

---

## §1574 — Mutation Detection

The conformance corpus MUST detect critical semantic mutations.

---

## §1575 — Golden Fixtures

Canonical protocol objects SHOULD have golden fixtures.

The golden fixture MUST include:

```text
input
canonical output
digest
```

---

## §1576 — Cross-Language Golden Contract

Any alternate implementation MUST consume the same semantic golden fixtures.

---

## §1577 — Schema/Code Synchronization

Generated Rust/TypeScript types MUST be checked against schema identity.

Generated code MUST NOT silently drift from the frozen schema.

---

## §1578 — Code Generation

Generated artifacts SHOULD contain:

```text
source_schema_digest
generator_identity
generator_version
```

---

## §1579 — Generator Verification

A generator MUST be tested independently from generated output.

---

## §1580 — CLI Conformance

The CLI MUST itself be tested as a protocol adapter.

Tests MUST verify:

```text
arguments
authorization
execution records
exit code
stdout/stderr
```

---

## §1581 — CLI Injection

Arguments containing:

```text
spaces
quotes
shell metacharacters
Unicode
path separators
```

MUST be tested.

---

## §1582 — Signal Handling

The runner MUST handle process interruption explicitly.

Examples:

```text
SIGINT
SIGTERM
worker loss
parent process death
```

---

## §1583 — Signal Evidence

Interruption MUST leave enough durable state for recovery to distinguish:

```text
never started
started
completed
cancelled
unknown
```

---

## §1584 — Crash Consistency

A crash during persistence MUST NOT produce a record that validates as complete evidence unless all required bytes were durably committed.

---

## §1585 — Atomic Rename

Filesystem stores SHOULD use:

```text
temporary file → flush → fsync where required → atomic rename
```

for authoritative records.

---

## §1586 — Directory Durability

Where durable filesystem semantics are required, directory metadata SHOULD also be synchronized after atomic replacement.

---

## §1587 — Partial File Detection

Malformed or incomplete authoritative files MUST be rejected rather than repaired silently.

---

## §1588 — Recovery Journal

A filesystem implementation MAY maintain a recovery journal.

Journal replay MUST be deterministic.

---

## §1589 — Locking

The reference filesystem implementation MUST prevent simultaneous mutation of the same run.

---

## §1590 — Concurrent Readers

Read-only inspection SHOULD remain available during execution without exposing partially committed authoritative records.

---

## §1591 — Transaction Boundary

A transaction SHOULD cover:

```text
execution completion → observation → check → evidence commit
```

or explicitly preserve each intermediate state if atomic grouping is impossible.

---

## §1592 — Intermediate Durability

If intermediate states are persisted, each MUST have a valid schema and explicit lifecycle state.

---

## §1593 — No Fake Completion

Presence of a file named:

```text
completed.json
```

MUST NOT establish completion by itself.

Completion is derived from validated state.

---

## §1594 — Release Manifest Command

```text
rfl release manifest
```

MUST refuse to generate a release manifest unless release-gate prerequisites are satisfied.

---

## §1595 — Release Verification

```text
rfl release verify <manifest>
```

MUST independently verify:

```text
manifest digest
subject
artifact
fixtures
evidence
coverage
conformance
gate
policy
```

where required.

---

## §1596 — Independent Verification

The verification command SHOULD be capable of operating without trusting the original runner's final Boolean summary.

---

## §1597 — Self-Test

The runner SHOULD provide:

```text
rfl self-test
```

covering its internal protocol adapters and invariants.

---

## §1598 — Self-Test Limitation

Self-test establishes implementation behavior against its test corpus.

It does not independently prove that the specification itself is correct.

---

## §1599 — Implementation Release Gate

The reference implementation MUST NOT be released as conformance-grade until:

```text
schema tests
transition tests
negative tests
evidence tests
coverage tests
gate tests
replay tests
persistence tests
mutation tests
CLI tests
```

have satisfied the applicable release policy.

---

## §1600 — Absolute Implementation Invariant

The reference implementation MUST enforce:

```text
NO STRONG IDENTITY → NO SAFE CORE API
NO PLAN → NO RUN
NO AUTHORIZATION → NO DISPATCH
NO SCOPE → NO EXECUTION
NO FIXTURE DIGEST MATCH → NO VALID FIXTURE EXECUTION
NO EXECUTION RECORD → NO EXECUTION CLAIM
NO OBSERVATION → NO OBSERVATION CLAIM
NO SEMANTIC CHECK → NO VERIFICATION CLAIM
NO EVIDENCE CLOSURE → NO VERIFIED CLAIM
NO DECLARED POPULATION → NO COMPLETE COVERAGE
NO REQUIRED COVERAGE → NO COMPLETE CONFORMANCE
NO CRITICAL MUTATION DETECTION → NO RELEASE
NO FROZEN IDENTITY → NO REPRODUCIBLE CLAIM
NO VALID GATE → NO RELEASE ELIGIBILITY
NO RELEASE MANIFEST → NO IMMUTABLE RELEASE IDENTITY
NO BOOLEAN SUMMARY → NO AUTHORITATIVE CONFORMANCE
NO RUNNER SELF-ASSERTION → NO INDEPENDENT ASSURANCE
```

The implementation architecture is therefore:

```text
                 ┌─────────────────────┐
                 │  NORMATIVE PROTOCOL │
                 └──────────┬──────────┘
                            │
                     semantic model
                            │
                            ▼
                 ┌─────────────────────┐
                 │     PURE KERNEL     │
                 │                     │
                 │ types               │
                 │ validation          │
                 │ canonicalization    │
                 │ transitions         │
                 │ predicates          │
                 │ coverage            │
                 │ gates               │
                 └──────────┬──────────┘
                            │
                         ports
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
      EXECUTION         PERSISTENCE        EXTERNAL
      ADAPTERS           ADAPTERS          ADAPTERS
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                       ORCHESTRATOR
                            │
                            ▼
                         RUNNER
                            │
                            ▼
                           CLI
                            │
                            ▼
                    EXECUTION EVIDENCE
                            │
                            ▼
                       CONFORMANCE
                            │
                            ▼
                       RELEASE GATE
```

The central implementation law remains:

```text
The runner orchestrates.
The kernel defines semantics.
The executor performs actions.
The observer records facts.
The checker evaluates predicates.
The evidence layer binds claims.
The coverage layer establishes population scope.
The gate determines eligibility.
The release manifest establishes immutable release identity.
```

No implementation component may silently assume the authority of another layer.

# End of RFL-AE Runner Reference Implementation & CLI Contract v1.6
