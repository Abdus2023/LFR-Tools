# RFL-AE — Conformance Runner & Orchestration Specification v1.5

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, JSON, graph, pipeline, tree, numbered-list and inequality passages collapsed into inline runs; line breaks, indentation, and fenced structure restored. Restored from their inline form: the runner boundary diagram and stage pipeline (§1202), the non-equivalence law (§1203), the ten numbered authoritative inputs (§1204), the execution-plan composition and plan-digest formula (§1205, §1206), the runner lifecycle and terminal states (§1208), the transition tuple (§1209), the preflight checklist and its failure mapping (§1210, §1211), the fixture-population set definitions and inequalities (§1213), the dependency graph (§1215), the scheduling and identity rules (§1218, §1219), the isolation and resource enumerations (§1221, §1222), the timeout representation (§1223), the cancellation record (§1224), the authorization boundary (§1225), the scope enumeration and escalation rule (§1227, §1228), the tool, environment, network and model identity sets (§1229, §1231, §1232, §1233, §1235), the injection boundary (§1234), the attempt identity (§1237), the execution, observation and check-result JSON objects (§1238, §1245, §1249), the execution-status and check-status enumerations (§1239, §1250), the positive/negative fixture laws and the negative-test law (§1251–§1253), the evidence construction chain, binding set and closure tree (§1257–§1259), the evidence-validity conjunction (§1260), the read-back sequence (§1263), the coverage observable list and formulas (§1265–§1268), the conformance-state enumeration (§1273), the retry and flakiness enumerations (§1275, §1278), the cache identity set (§1279), the delivery-semantics set (§1284), the event ledger and event identity (§1286, §1287), the worker identity and controller verification sets (§1290, §1292), the CI identity and commit/artifact/build binding sets (§1296–§1300), the mutation record and metamorphic/property sets (§1301, §1304, §1305), the configuration separation and projection direction (§1312, §1313), the gate result JSON and input closure (§1318, §1320), the release-manifest reference set and freeze check (§1323, §1326), the replay modes and equality rule (§1330, §1334), the run and runner identity sets (§1338, §1339), the bootstrap validation set (§1341), the adversarial-input set (§1344), the output-limit and truncation rules (§1348, §1349), the timestamp distinctions (§1350), the external-dependency and mode sets (§1361, §1364), the conformance-mode requirements (§1366), the release evidence closure (§1388), the claim-scope relation (§1389), the summary-counter list and the coverage reconciliation identity (§1391, §1392), the runner output set (§1398), and the absolute runner invariant with its laws, stage chain and non-equivalence list (§1400). Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Verbatim anomaly recorded, not corrected.** §1400's law list contains **seventeen lines, of which the final law `NO EVIDENCE → NO VERIFIED CLAIM` appears three times consecutively**, giving fifteen distinct laws. Recorded exactly as supplied. Whether the triplication is emphasis or duplication is a question for Part XXVI, not for the recording.
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all fifteen recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Heading level note.** As with v1.2–v1.4, v1.5 marks sections at `##` under a document-level `#` title. Preserved as supplied.
>
> **Standing.** Begins at **§1201**, continuing [`v1.4`](rfl-ae-release-gate-manifest-v1.4.md) (§998–§1200). v1.4 ends at §1200 and v1.5 opens at §1201 — **the range is contiguous**. The effective specification is now **§0–§1400 across fifteen supplied documents**. **Citation rule: §1–§28 is occupied twice** (v0.1 §1–§28 and v0.2 §0–§40 share 28 numbers, zero identical headings) — citations in that range MUST name the document. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Declared dependencies.** v1.5 states its own dependency set: *"Protocol v0.7+, Reference Implementation Blueprint v0.8, Conformance/Evidence/Release Specification v1.0, Fixture Corpus Specification v1.1, Release Gate Specification v1.4"* — five documents, named by number. This is the first self-declared dependency set in the lineage, and it **omits v1.2 and v1.3**, whose execution-record and coverage vocabularies §1239 and §1265–§1268 restate. Recorded verbatim; analysed in Part XXVI.
>
> **Status:** Normative
> **Layer:** Conformance Execution / Orchestration
> **Range:** §1201–§1400
> **Purpose:** Define the executable runner that transforms a declared fixture population and a frozen execution plan into execution records, observations, checks, evidence, coverage, conformance state, and a release-gate input.

---

## §1201 — Purpose

The Conformance Runner is the executable authority for transforming a declared fixture population and frozen execution plan into structured execution records, observations, verification results, evidence, coverage, conformance state, and—when all requirements are satisfied—a release-gate input.

The runner MUST NOT redefine protocol semantics.

The runner executes semantics already defined by the normative protocol.

---

## §1202 — Runner Boundary

The runner consists conceptually of:

```text
                 FROZEN PROTOCOL
                        │
                        ▼
                 ┌──────────────┐
                 │ RUNNER PLAN  │
                 └──────┬───────┘
                        │
              ┌─────────▼─────────┐
              │  ORCHESTRATOR     │
              └─────────┬─────────┘
                        │
           ┌────────────┼────────────┐
           ▼            ▼            ▼
       AUTHORIZER    EXECUTOR     OBSERVER
           │            │            │
           └────────────┼────────────┘
                        ▼
                   CHECKER/VERIFIER
                        │
                        ▼
                   EVIDENCE STORE
                        │
                        ▼
                  COVERAGE ENGINE
                        │
                        ▼
                  CONFORMANCE
                        │
                        ▼
                   RELEASE GATE
```

The runner MUST preserve these boundaries.

---

## §1203 — Non-Equivalence Law

The following objects MUST remain distinct:

```text
Runner ≠ Orchestrator ≠ Executor ≠ Observer ≠ Checker ≠ Evidence Builder ≠ Coverage Evaluator ≠ Gate Evaluator
```

Combining implementations is permitted.

Combining semantics is not.

---

## §1204 — Authoritative Inputs

A conforming runner MUST obtain its execution authority from explicit immutable or versioned inputs.

Minimum authoritative inputs:

1. protocol identity;

2. subject identity;

3. fixture corpus manifest;

4. execution policy;

5. authorization policy;

6. verifier/check manifest;

7. environment policy;

8. dependency manifest;

9. runner configuration;

10. release/conformance policy where applicable.

Filesystem discovery MUST NOT silently replace a declared manifest.

---

## §1205 — Execution Plan

The runner MUST construct an explicit execution plan.

Conceptually:

```text
ExecutionPlan =
    Protocol
  + Subject
  + FixturePopulation
  + Checks
  + Dependencies
  + Authorization
  + Scope
  + EnvironmentPolicy
  + ResourcePolicy
  + RetryPolicy
  + PersistencePolicy
```

The plan MUST have a stable identity.

---

## §1206 — Plan Identity

The plan identity MUST be derived from the canonical representation of all execution-affecting inputs.

At minimum:

```text
plan_digest =
    Digest(
        Canonicalize(
            protocol
            + subject
            + fixture_manifest
            + check_manifest
            + dependency_manifest
            + authorization_policy
            + execution_policy
        )
    )
```

Runtime timestamps MUST NOT affect the semantic plan digest unless explicitly declared execution-affecting.

---

## §1207 — Plan Freeze

Once execution begins, the execution plan MUST be immutable.

A mutation to an execution-affecting input MUST invalidate the current plan.

The runner MUST NOT continue under a stale plan.

---

## §1208 — Runner Lifecycle

The normative lifecycle is:

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

Terminal exceptional states:

```text
BLOCKED
ERROR
CANCELLED
```

---

## §1209 — Lifecycle Semantics

A state transition MUST have:

```text
current_state
event
preconditions
postconditions
transition_result
```

A runner MUST NOT infer a transition merely because a subprocess returned successfully.

---

## §1210 — Preflight

Preflight MUST verify, as applicable:

- protocol identity;

- subject identity;

- corpus identity;

- fixture manifest;

- check manifest;

- dependency availability;

- tool availability;

- executor availability;

- authorization;

- scope;

- resource limits;

- environment requirements;

- persistence availability;

- runner compatibility.

---

## §1211 — Preflight Failure

A failed precondition MUST produce an explicit state.

Examples:

```text
missing dependency      → BLOCKED
invalid manifest        → ERROR
unauthorized operation  → BLOCKED
unsupported environment → BLOCKED
corrupt fixture         → ERROR
cancelled before run    → CANCELLED
```

These MUST NOT be represented as ordinary fixture failures.

---

## §1212 — Ready State

`READY` means:

all required preconditions for scheduling have been observed as satisfied.

It does NOT mean:

- fixtures passed;

- implementation is conformant;

- release is eligible.

---

## §1213 — Fixture Population

The authoritative fixture population is the set declared by the fixture manifest.

Define:

```text
D = declared fixtures
X = discovered fixtures
E = executed fixtures
V = evaluated fixtures
P = passed fixtures
```

The runner MUST preserve:

```text
D ≠ X
D ≠ E
E ≠ V
V ≠ P
```

---

## §1214 — Discovery Reconciliation

If filesystem discovery is performed:

```text
Discovery → Diagnostic Reconciliation
```

not:

```text
Discovery → Replacement Manifest
```

Unexpected files MUST be reported.

Missing declared fixtures MUST be reported.

Neither condition may silently alter the declared population.

---

## §1215 — Dependency Graph

Fixtures MAY declare dependencies.

The runner MUST construct:

```text
Fixture DAG
```

and MUST reject dependency cycles.

For fixtures:

```text
A → B → C
```

`A` MUST NOT execute before its required dependency semantics permit it.

---

## §1216 — Dependency Failure

If a required dependency is unavailable:

```text
DEPENDENCY_UNAVAILABLE → BLOCKED
```

The dependent fixture MUST NOT be converted into `FAIL`.

---

## §1217 — Dependency Failure Propagation

A dependent fixture MAY become:

```text
BLOCKED
```

when its prerequisite is blocked.

The runner MUST preserve the distinction between:

```text
fixture failed
fixture could not execute because prerequisite failed
```

---

## §1218 — Deterministic Scheduling

When multiple runnable fixtures exist, deterministic mode MUST select execution order according to a stable ordering rule.

Recommended ordering:

```text
dependency depth → fixture_id
```

The exact ordering MUST be specified by the execution policy.

---

## §1219 — Schedule Identity

The schedule MUST be reproducible from:

```text
plan_digest + dependency_graph + scheduling_policy
```

A scheduler MUST NOT depend on nondeterministic hash-map iteration when deterministic mode is enabled.

---

## §1220 — Concurrency

Fixtures MAY execute concurrently only when their declared dependencies and resource requirements permit it.

Concurrency MUST NOT alter semantic results.

If concurrency can affect a test's semantics, the fixture MUST declare that constraint.

---

## §1221 — Resource Isolation

The runner SHOULD isolate:

- filesystem;

- environment variables;

- network;

- process namespace;

- temporary storage;

- credentials;

- ports;

- CPU;

- memory;

- execution time.

The isolation level MUST be captured in the execution record.

---

## §1222 — Resource Limits

A fixture MUST have an applicable resource policy.

Possible limits include:

```text
CPU
memory
wall-clock time
process count
file size
output size
network
storage
```

Resource exhaustion MUST produce a structured execution status.

---

## §1223 — Timeout

A timeout MUST NOT be represented as an ordinary assertion failure.

Recommended representation:

```text
execution_status = TIMEOUT
check_status = UNKNOWN
```

unless the fixture's normative expected result explicitly defines timeout as the semantic expected behavior.

---

## §1224 — Cancellation

Cancellation MUST be observable.

The runner MUST preserve:

```text
cancel_requested_at
cancellation_source
attempt_state
cleanup_result
```

A cancelled fixture MUST NOT become `PASS`.

---

## §1225 — Authorization Boundary

Authorization MUST occur before dispatch.

```text
ToolRequest
     ↓
Authorization
     ↓
Dispatch
```

Never:

```text
Dispatch
     ↓
Authorization
```

---

## §1226 — Authorization Non-Bypass

No executor, worker, plugin, subprocess, or model-generated instruction may bypass the authorization layer.

An authorized plan does not authorize arbitrary subsequent operations.

---

## §1227 — Scope Enforcement

Every execution MUST be bound to an explicit scope.

Scope MUST cover, where applicable:

```text
subject
repository
files
commands
network
credentials
artifacts
time
resources
```

---

## §1228 — Scope Escalation

A request exceeding scope MUST produce:

```text
BLOCKED
```

unless an explicit authorized scope transition exists.

The runner MUST NOT silently widen scope.

---

## §1229 — Tool Identity

Every execution-affecting tool MUST have an identity containing, as applicable:

```text
tool_name
tool_version
binary_digest
source_identity
configuration_digest
```

---

## §1230 — Tool Identity Failure

If a required tool identity cannot be established:

```text
UNKNOWN
```

or

```text
BLOCKED
```

MUST be used according to policy.

The runner MUST NOT claim exact reproducibility without sufficient identity.

---

## §1231 — Environment Identity

The environment record SHOULD capture:

```text
OS
architecture
kernel
runtime
compiler
interpreter
container/image
environment variables selected by policy
locale
timezone
dependency lock identity
```

Sensitive values MUST NOT be persisted merely because they exist.

---

## §1232 — Environment Digest

The runner SHOULD produce a canonical environment descriptor and digest.

```text
environment_digest =
    Digest(Canonicalize(EnvironmentDescriptor))
```

Secrets MUST be represented by safe identity metadata rather than raw secret material.

---

## §1233 — Network Policy

Network behavior MUST be explicit.

Possible policies:

```text
DENY
ALLOWLIST
UNRESTRICTED
RECORDED
```

Offline conformance tests MUST fail preflight or execution if network access is required but forbidden.

---

## §1234 — Prompt Injection Boundary

Repository files, fixture contents, command output, generated text, and model output are DATA.

They are not authority.

```text
DATA ≠ INSTRUCTION
DATA ≠ AUTHORIZATION
DATA ≠ POLICY
```

unless an explicit trusted protocol transition promotes the data.

---

## §1235 — Model Execution

If an LLM/model participates in execution:

```text
model_identity
model_version
prompt_pack_identity
input_digest
output_digest
tool permissions
temperature/randomness policy
seed where applicable
```

SHOULD be captured.

Model output MUST NOT directly become authorization.

---

## §1236 — Dispatch

Dispatch creates an execution attempt.

Every attempt MUST receive a stable:

```text
attempt_id
```

---

## §1237 — Attempt Identity

Recommended:

```text
attempt_id =
    attempt:<fixture_id>:<attempt_ordinal>:<unique_run_context>
```

An attempt MUST never be silently overwritten by a retry.

---

## §1238 — Execution Record

Minimum execution record:

```json
{
  "execution_id": "execution:...",
  "attempt_id": "attempt:...",
  "plan_digest": "sha256:...",
  "fixture_id": "fixture:...",
  "executor": {},
  "tool": {},
  "environment": {},
  "authorization": {},
  "scope": {},
  "started_at": "...",
  "finished_at": "...",
  "status": "...",
  "exit": {},
  "inputs": {},
  "outputs": {},
  "resources": {},
  "seed": null
}
```

---

## §1239 — Execution Status

Execution status MUST be richer than Boolean success.

Minimum:

```text
COMPLETED
FAILED_TO_START
TIMEOUT
CANCELLED
CRASHED
RESOURCE_EXHAUSTED
BLOCKED
ERROR
UNKNOWN
```

---

## §1240 — Exit Status

Process exit status MUST be preserved independently of semantic check status.

For example:

```text
exit_code = 1
check_status = PASS
```

may be valid for a negative fixture whose expected behavior is a semantic rejection.

---

## §1241 — Standard Output

Raw stdout MAY be persisted.

If persisted, it SHOULD be content-addressed.

```text
stdout_digest
```

MUST identify the exact persisted content.

---

## §1242 — Standard Error

stderr MUST be treated independently of stdout.

A diagnostic message on stderr MUST NOT automatically mean `FAIL`.

---

## §1243 — Output Artifacts

Produced artifacts MUST have:

```text
artifact_id
content_digest
media/type metadata
size
producer identity
```

where applicable.

---

## §1244 — Observation Boundary

Execution produces observations.

Observation is not verification.

```text
Execution
    ↓
Observation
    ↓
Check
```

---

## §1245 — Observation Record

An observation SHOULD include:

```json
{
  "observation_id": "observation:...",
  "execution_id": "execution:...",
  "observed_at": "...",
  "kind": "...",
  "facts": {},
  "artifacts": [],
  "raw_evidence_refs": []
}
```

---

## §1246 — Observation Integrity

The runner MUST preserve sufficient information to establish that an observation originated from the corresponding execution attempt.

At minimum:

```text
execution_id → observation_id
```

MUST be traceable.

---

## §1247 — Checker Invocation

A checker receives:

```text
fixture
expected_result
observation
checker_identity
```

It MUST return a structured `CheckResult`.

---

## §1248 — Checker Independence

A checker MUST NOT derive its answer solely from a runner-produced Boolean such as:

```text
execution.success == true
```

The checker MUST evaluate the fixture's semantic predicate.

---

## §1249 — Check Result

Minimum structure:

```json
{
  "check_id": "check:...",
  "fixture_id": "fixture:...",
  "checker": {},
  "predicate": {},
  "status": "PASS",
  "expected": {},
  "actual": {},
  "reason": null
}
```

---

## §1250 — Check Status

Allowed statuses:

```text
PASS
FAIL
ERROR
UNKNOWN
SKIPPED
BLOCKED
```

---

## §1251 — Positive Fixture

A positive fixture MUST pass only when its expected semantic result is satisfied.

Process success alone is insufficient.

---

## §1252 — Negative Fixture

A negative fixture MUST specify the expected semantic rejection.

For example:

```text
expected:   error_code = INVALID_IDENTIFIER
```

Receiving any arbitrary nonzero exit code is insufficient evidence.

---

## §1253 — Negative Test Law

```text
EXPECTED SEMANTIC REJECTION
        ≠
ARBITRARY FAILURE
```

Therefore:

```text
nonzero exit
```

MUST NOT automatically satisfy a negative fixture.

---

## §1254 — Near-Miss Fixtures

Negative tests SHOULD be paired with nearby valid fixtures.

Purpose:

```text
detect over-broad rejection
```

A checker that rejects both invalid and valid neighbors is non-conformant.

---

## §1255 — Error Handling

Internal checker exceptions MUST become:

```text
ERROR
```

not:

```text
FAIL
```

unless the protocol explicitly defines the exception as the expected semantic outcome.

---

## §1256 — Unknown Handling

Insufficient evidence MUST produce:

```text
UNKNOWN
```

rather than an inferred result.

---

## §1257 — Evidence Construction

Evidence MUST be constructed only after observation and checking.

```text
Execution → Observation → Check → Evidence
```

---

## §1258 — Evidence Binding

Evidence MUST bind at minimum:

```text
subject
fixture
execution
observation
checker
predicate
expected
actual
result
tool/environment identity
```

as applicable.

---

## §1259 — Evidence Closure

A verification claim is closed only when all required evidence dependencies resolve.

Conceptually:

```text
Claim
  ├── Subject
  ├── Execution
  ├── Observation
  ├── Checker
  ├── Predicate
  └── Result
```

Missing required nodes prevent closure.

---

## §1260 — Evidence Validity

Evidence is valid only if:

```text
subject_identity_valid
AND execution_identity_valid
AND observation_binding_valid
AND checker_identity_valid
AND predicate_identity_valid
AND required_artifacts_available
AND digests_match
AND policy_version_valid
```

---

## §1261 — Evidence Persistence

Evidence persistence MUST be append-oriented.

Existing evidence MUST NOT be silently replaced.

Corrections MUST produce new records linked to the superseded record.

---

## §1262 — Atomic Persistence

A persisted evidence object MUST be either:

```text
fully committed
```

or:

```text
not committed
```

A partial object MUST NOT be presented as durable evidence.

---

## §1263 — Read-Back Verification

After persistence, the runner SHOULD:

```text
write → reopen → verify digest → verify schema → verify identity
```

before treating the evidence as durable.

---

## §1264 — Evidence Store Failure

Failure to persist required evidence MUST prevent an evidence-bound PASS claim.

The check result may remain:

```text
PASS
```

while the verification state becomes:

```text
EVIDENCE_ERROR
```

or equivalent structured state.

---

## §1265 — Coverage Population

Coverage MUST be computed against the declared corpus.

At minimum:

```text
declared
discovered
scheduled
executed
evaluated
passed
failed
blocked
unknown
skipped
errored
```

MUST be separately observable.

---

## §1266 — Coverage Formula

For required fixtures:

```text
required_total = |D_required|
```

A fixture counts as executed only when a valid execution record exists.

A fixture counts as evaluated only when a valid check result exists.

---

## §1267 — Complete Evaluation

Complete evaluation requires:

```text
evaluated_required = declared_required
```

subject to explicitly declared exemptions.

---

## §1268 — Complete Conformance

Complete conformance additionally requires all required evaluated fixtures to satisfy their semantic expectations.

```text
complete_coverage AND all_required_checks_pass
```

---

## §1269 — Blocked Fixtures

Blocked fixtures MUST remain visible in coverage.

They MUST NOT disappear from denominators.

---

## §1270 — Skipped Fixtures

Skipped fixtures MUST remain visible.

A skipped fixture MUST NOT count as passed.

---

## §1271 — Unknown Fixtures

Unknown fixtures MUST remain unknown.

The runner MUST NOT coerce:

```text
UNKNOWN → FAIL
```

or:

```text
UNKNOWN → PASS
```

without an explicit protocol rule.

---

## §1272 — Conformance Aggregation

Conformance is a derived result.

It MUST be computed from:

```text
declared population + execution records + check results + evidence validity + coverage + policy
```

not from an independently supplied `conformant=true`.

---

## §1273 — Conformance States

Recommended aggregate states:

```text
CONFORMANT
NON_CONFORMANT
INCOMPLETE
BLOCKED
ERROR
UNKNOWN
STALE
INVALID
```

---

## §1274 — Incomplete Conformance

A corpus with unresolved required fixtures MUST be:

```text
INCOMPLETE
```

or a more specific blocking state.

It MUST NOT be represented as complete conformance.

---

## §1275 — Retry Policy

Retries MUST be explicitly policy-controlled.

Every retry creates a new attempt.

```text
attempt:1
attempt:2
attempt:3
```

All remain historically observable.

---

## §1276 — Retry Eligibility

A retry MUST NOT occur merely because the runner wants a PASS.

Retries SHOULD be restricted to declared transient classes such as:

```text
worker loss
infrastructure interruption
network transient
resource provisioning failure
```

---

## §1277 — Retry Semantics

If attempts disagree:

```text
PASS + FAIL
```

the runner MUST preserve both.

It MUST NOT silently select the favorable result.

The aggregate state MUST follow the declared policy.

---

## §1278 — Flakiness

Repeated disagreement across equivalent attempts MUST be observable as instability.

Suggested:

```text
STABLE_PASS
STABLE_FAIL
INCONSISTENT
UNRESOLVED
```

The exact terminology MAY vary, but instability MUST NOT disappear into PASS.

---

## §1279 — Cache Identity

A cached execution result MAY be reused only when all execution-affecting identities match.

Minimum:

```text
plan_digest
fixture_digest
tool_identity
environment_identity
checker_identity
predicate_identity
dependency_identity
```

---

## §1280 — Cache Hit

A cache hit is not necessarily an execution.

Therefore:

```text
CACHE_REUSED
```

MUST remain distinct from:

```text
EXECUTED
```

---

## §1281 — Cached Evidence

Previously generated evidence MAY be reused only if its identity and validity remain established.

Stale evidence MUST be marked:

```text
STALE
```

or invalidated.

---

## §1282 — Crash Recovery

After runner interruption, recovery MUST reconstruct state from durable records.

The runner MUST NOT infer completion merely because a process previously existed.

---

## §1283 — Resume

Resume MUST use:

```text
plan_digest + persisted execution records + attempt identities + event ledger
```

to determine unfinished work.

---

## §1284 — Exactly-Once Claim

The runner MUST NOT claim exactly-once execution unless the underlying dispatch mechanism proves it.

Possible semantics:

```text
AT_MOST_ONCE
AT_LEAST_ONCE
EFFECTIVELY_ONCE
UNKNOWN
```

---

## §1285 — Idempotency

Where retries are possible, fixture execution SHOULD be idempotent.

Non-idempotent fixtures MUST declare that property and receive appropriate isolation.

---

## §1286 — Event Ledger

The runner SHOULD maintain an append-only event ledger.

Example:

```text
run_declared
preflight_started
preflight_completed
fixture_scheduled
authorization_granted
execution_dispatched
execution_started
execution_observed
check_completed
evidence_committed
coverage_updated
gate_evaluated
run_completed
```

---

## §1287 — Event Identity

Every event SHOULD contain:

```text
event_id
run_id
timestamp
actor
event_type
parent_event
payload_digest
```

---

## §1288 — Causality

Events MUST preserve enough information to reconstruct causal order.

Wall-clock timestamps alone are insufficient when distributed workers are involved.

---

## §1289 — Worker Boundary

Remote workers are untrusted execution boundaries unless explicitly trusted by policy.

Worker results MUST be authenticated or otherwise integrity-bound according to deployment requirements.

---

## §1290 — Worker Identity

A remote worker SHOULD provide:

```text
worker_id
worker_version
executor_identity
environment_identity
capability_identity
attestation where available
```

---

## §1291 — Worker Result

A worker MUST NOT directly write authoritative conformance state.

Worker output is an observation/result submitted to the controlling runner.

---

## §1292 — Remote Result Verification

The controller MUST verify:

```text
worker identity
run identity
plan identity
fixture identity
artifact identity
result integrity
```

before accepting worker evidence.

---

## §1293 — Distributed Scheduling

Distributed execution MAY reorder physically executed fixtures.

Semantic coverage MUST remain based on fixture identity, not completion order.

---

## §1294 — CI Integration

If CI is a required execution authority:

```text
CI execution record
```

MUST be distinguished from:

```text
local execution record
```

---

## §1295 — CI Evidence

A local execution MUST NOT substitute for a required CI execution.

Conversely, absence of an observed CI record MUST NOT be interpreted as proof that CI never executed.

Correct state:

```text
CI_RECORD_NOT_OBSERVED
```

when observability is incomplete.

---

## §1296 — CI Identity

A CI execution SHOULD bind:

```text
provider
workflow
run_id
job_id
commit_sha
workflow_definition_digest
runner_identity
environment_identity
artifacts
```

---

## §1297 — Commit Binding

A source-specific conformance claim MUST identify the exact source revision.

Recommended:

```text
source_commit_sha
```

or equivalent immutable identity.

---

## §1298 — Artifact Binding

An artifact-specific claim MUST identify exact artifact bytes.

```text
artifact_digest
```

is authoritative for byte identity.

Filename is not sufficient.

---

## §1299 — Source/Artifact Distinction

These remain distinct:

```text
source identity ≠ build identity ≠ artifact identity
```

A source commit does not prove which artifact was produced.

---

## §1300 — Build Identity

Build identity SHOULD include:

```text
source
toolchain
dependencies
build configuration
target environment
build command
```

and their relevant digests.

---

## §1301 — Mutation Testing

Mutation testing MUST execute against a declared mutation population.

Each mutation receives:

```text
mutation_id
base_subject
mutation_description
mutated_subject_digest
expected_detection
execution_record
detection_result
```

---

## §1302 — Mutation Detection

A mutation is detected only when the expected verification mechanism actually distinguishes it from the baseline.

A generic nonzero exit code is insufficient.

---

## §1303 — Critical Mutation Requirement

If the release policy requires detection of a critical mutation class:

```text
undetected critical mutation → RELEASE BLOCKED
```

---

## §1304 — Metamorphic Execution

Metamorphic fixtures MUST preserve the transformation identity:

```text
generator_id
generator_version
seed
input_identity
transformation
expected_relation
```

---

## §1305 — Property Testing

Property-generated cases MUST remain traceable to:

```text
generator identity
seed
input digest
property identity
execution identity
```

---

## §1306 — Randomness

Random execution MUST record its seed where reproducibility is required.

If no seed can reproduce the run:

```text
reproducibility = UNKNOWN
```

not `VERIFIED`.

---

## §1307 — Cross-Language Conformance

Cross-language execution MUST compare semantic canonical outputs.

Comparison MAY occur between:

```text
Rust
TypeScript
Python
other conforming implementation
```

but language-specific representation MUST NOT become the semantic contract.

---

## §1308 — Canonical Comparison

Cross-language results SHOULD compare:

```text
canonical semantic object
```

rather than:

```text
source-language memory representation
```

---

## §1309 — Schema Validation

Every protocol object entering the runner MUST undergo applicable structural validation.

Semantic validation remains separate.

---

## §1310 — Semantic Validation

Structural validity does not establish semantic validity.

Therefore:

```text
JSON Schema PASS ≠ Protocol CONFORMANCE
```

---

## §1311 — Schema Drift

If schema identity differs from the frozen protocol target:

```text
STALE
```

or:

```text
BLOCKED
```

MUST result.

---

## §1312 — Runner Configuration

Configuration MUST be separated into:

```text
semantic configuration
execution configuration
diagnostic configuration
```

Diagnostic changes MUST NOT silently change semantic execution.

---

## §1313 — Diagnostic Output

Human-readable summaries are projections.

They are not authoritative state.

```text
Machine records
     ↓
Human summary
```

not:

```text
Human summary
     ↓
Machine truth
```

---

## §1314 — Summary Generation

A summary MUST be derivable from persisted records.

The runner MUST NOT report:

```text
"all tests passed"
```

when the underlying records contain unresolved required fixtures.

---

## §1315 — Result Precedence

The runner MUST preserve the strongest applicable unresolved state.

For example:

```text
BLOCKED
ERROR
UNKNOWN
SKIPPED
FAIL
PASS
```

MUST NOT be arbitrarily flattened.

Exact precedence MUST be policy-defined because semantic contexts differ.

---

## §1316 — No Boolean Collapse

The runner MUST NOT expose only:

```text
success: true/false
```

as the authoritative result.

Boolean projections MAY exist for compatibility, but they MUST be derived.

---

## §1317 — Gate Invocation

The release gate MAY execute only after:

```text
evidence
coverage
conformance
```

have been computed for the same frozen identity set.

---

## §1318 — Gate Input Closure

Gate inputs MUST bind:

```text
protocol
subject
artifact
fixtures
evidence
coverage
conformance
policies
```

to the same candidate.

---

## §1319 — Stale Gate Input

If any required gate input belongs to a different identity:

```text
STALE
```

The runner MUST NOT silently substitute current data.

---

## §1320 — Gate Result

The gate evaluator MUST return a structured result:

```json
{
  "gate_id": "gate:...",
  "policy": {},
  "candidate": {},
  "predicates": [],
  "status": "PASS",
  "evidence": []
}
```

---

## §1321 — Gate PASS

`PASS` means every required gate predicate was evaluated and satisfied.

It does not mean:

```text
published
deployed
trusted
universally correct in all contexts
```

---

## §1322 — Gate BLOCKED

A required predicate that cannot be evaluated because necessary evidence is unavailable MUST produce `BLOCKED` or `UNKNOWN` according to policy.

It MUST NOT become PASS.

---

## §1323 — Release Freeze Check

Before release-manifest generation, the runner MUST verify that all frozen identities remain unchanged.

Minimum:

```text
protocol
subject
source
artifact
fixtures
evidence
coverage
policies
```

---

## §1324 — Freeze Mutation

Any mutation to a freeze-bound object MUST invalidate the release candidate.

A new candidate MUST be generated.

---

## §1325 — Release Manifest Generation

Only after a valid gate MAY the runner emit a release manifest.

Conceptually:

```text
Conformance Run
       ↓
Evidence
       ↓
Coverage
       ↓
Conformance
       ↓
Gate
       ↓
Release Manifest
```

---

## §1326 — Release Manifest Binding

The release manifest MUST reference exact:

```text
plan_digest
subject_digest
artifact_digest
fixture_population_digest
evidence_population_digest
coverage_digest
gate_digest
policy_digest
```

where applicable.

---

## §1327 — Manifest Digest

The manifest digest MUST be computed over canonical manifest content.

The digest MUST NOT include itself.

---

## §1328 — Immutable Output

After publication, a release manifest MUST be immutable.

Corrections require:

```text
new manifest
new digest
new release identity
```

---

## §1329 — Replay

Replay MUST begin from immutable inputs:

```text
protocol
subject
fixture population
execution plan
policy
seeds
```

---

## §1330 — Replay Modes

The runner SHOULD support:

```text
SEMANTIC_REPLAY
EXECUTION_REPLAY
EVIDENCE_REPLAY
```

---

## §1331 — Semantic Replay

Semantic replay recomputes protocol/check/gate semantics from existing observations without necessarily rerunning external commands.

---

## §1332 — Execution Replay

Execution replay reruns the actual fixture execution.

It SHOULD preserve the original plan identity where possible.

---

## §1333 — Evidence Replay

Evidence replay reconstructs evidence from durable execution and observation records.

Any discrepancy MUST be observable.

---

## §1334 — Replay Equality

Deterministic replay SHOULD satisfy:

```text
same inputs + same implementation + same dependencies + same environment + same seed = same semantic result
```

---

## §1335 — Replay Failure

A replay mismatch MUST NOT overwrite the original result.

It creates new evidence.

---

## §1336 — Evidence History

Original and replayed records MUST remain distinguishable.

```text
original_execution
replay_execution
```

---

## §1337 — Resume vs Replay

Resume continues an interrupted run.

Replay creates a new verification attempt.

These operations MUST NOT be conflated.

---

## §1338 — Run Identity

Every complete runner invocation MUST have:

```text
run_id
plan_digest
runner_identity
started_at
finished_at
```

---

## §1339 — Runner Identity

Runner identity SHOULD include:

```text
runner_name
runner_version
source_revision
binary_digest
configuration_digest
```

---

## §1340 — Self-Verification

The runner MUST NOT be the sole authority proving its own conformance.

At least one independent verification path SHOULD exist for release-grade assurance.

---

## §1341 — Bootstrap Verification

Bootstrap verification SHOULD be capable of validating:

```text
schema
canonicalization
fixtures
execution records
evidence
coverage
gate
release manifest
```

without depending entirely on the implementation being verified.

---

## §1342 — Independent Checker

At least critical predicates SHOULD have an independently implemented checker.

This reduces correlated implementation defects.

---

## §1343 — Correlated Failure

If runner and checker share the same defective semantic implementation, agreement does not establish correctness.

Therefore:

```text
agreement ≠ independence
```

---

## §1344 — Security Boundary

The runner MUST treat:

```text
fixture input
repository content
tool output
worker output
model output
network data
```

as potentially adversarial.

---

## §1345 — Command Injection

Fixture-controlled strings MUST NOT automatically become shell commands.

Command construction MUST pass through an explicit execution interface.

---

## §1346 — Path Safety

Fixture paths MUST be validated against declared scope.

Path traversal MUST be rejected.

---

## §1347 — Credential Isolation

Credentials MUST NOT be exposed to fixtures unless explicitly authorized.

Evidence MUST NOT contain raw secrets.

---

## §1348 — Output Limits

Unbounded stdout/stderr/artifact production MUST be prevented by resource policy.

Truncation MUST be explicitly recorded.

---

## §1349 — Artifact Truncation

If output is truncated:

```text
complete = false
```

MUST be recorded.

A check requiring the complete output MUST become `UNKNOWN`, `FAIL`, or another policy-defined state—not silently PASS.

---

## §1350 — Time Semantics

The runner MUST distinguish:

```text
semantic timestamp
execution timestamp
evidence creation timestamp
publication timestamp
```

These MUST NOT be conflated.

---

## §1351 — Clock Trust

Wall-clock timestamps are observations about the execution environment.

They do not independently establish causality or semantic truth.

---

## §1352 — Monotonic Timing

Duration measurements SHOULD use a monotonic clock where available.

Wall-clock timestamps MAY be retained for human correlation.

---

## §1353 — Environment Mutation

A fixture that mutates its environment MUST be isolated so that unrelated fixtures cannot inherit hidden state.

---

## §1354 — Fixture Isolation

Unless explicitly declared otherwise:

```text
fixture A
```

MUST NOT modify semantic inputs of:

```text
fixture B
```

---

## §1355 — Shared State

Shared state MUST be explicitly declared.

Undeclared shared state makes deterministic conformance potentially invalid.

---

## §1356 — Order Dependence

A fixture MUST NOT depend on execution order unless order is part of the declared protocol semantics.

---

## §1357 — Order Mutation Test

The runner SHOULD test selected fixture populations under alternative valid schedules.

Unexpected semantic divergence is evidence of hidden coupling.

---

## §1358 — Resource Fairness

Parallel execution MUST NOT cause a fixture to receive materially different resources unless that variation is part of the environment policy.

---

## §1359 — Environment Capture

Resource allocations SHOULD be captured when they materially affect reproducibility.

---

## §1360 — Network Observation

If network access is allowed, the runner SHOULD record sufficient metadata to establish:

```text
policy
destination class
request identity
response artifact identity
```

without unnecessarily storing sensitive content.

---

## §1361 — External Dependency

External dependencies MUST have explicit identity where reproducibility requires them.

Examples:

```text
package lock digest
container digest
remote API version
fixture dataset digest
```

---

## §1362 — Dependency Drift

Dependency drift invalidates exact replay unless the protocol explicitly permits floating dependencies.

---

## §1363 — Floating Dependencies

A floating dependency MAY be used for exploratory execution.

It MUST NOT silently produce an immutable reproducibility claim.

---

## §1364 — Exploratory Mode

The runner MAY support:

```text
EXPLORATORY
CONFORMANCE
RELEASE
```

execution modes.

Only explicitly frozen modes may produce release-grade claims.

---

## §1365 — Mode Policy

Mode MUST be recorded in every run.

Changing mode MUST change the run identity.

---

## §1366 — Conformance Mode

Conformance mode requires:

```text
frozen subject
frozen corpus
frozen checks
declared policies
traceable execution
```

---

## §1367 — Release Mode

Release mode additionally requires all v1.4 release predicates.

---

## §1368 — Diagnostic Mode

Diagnostic mode MAY relax evidence or isolation requirements.

Its results MUST NOT automatically be promoted to release evidence.

---

## §1369 — Promotion

Promotion from diagnostic to conformance MUST create a new execution record unless exact evidence equivalence has been formally established.

---

## §1370 — Fixture Mutation

A mutation to fixture content changes its identity.

The old execution MUST remain bound to the old fixture digest.

---

## §1371 — Checker Mutation

A checker implementation mutation changes checker identity.

Old evidence MUST NOT be silently relabeled as evidence from the new checker.

---

## §1372 — Predicate Mutation

A predicate semantic change requires a new predicate identity.

Existing results MUST NOT be retroactively interpreted as results under the new predicate.

---

## §1373 — Policy Mutation

A policy change creates a new policy identity.

Previous gate results remain historically valid only under their original policy.

---

## §1374 — Schema Mutation

A schema version change MUST be visible in the execution identity where it affects interpretation.

---

## §1375 — Canonicalization Mutation

A canonicalization algorithm/configuration change MUST change canonicalization identity.

Digest equality under different canonicalization rules MUST NOT be assumed meaningful.

---

## §1376 — Event Ordering

Event ordering MUST NOT be inferred solely from array position if events may originate from distributed workers.

---

## §1377 — Event Duplication

Duplicate event delivery MUST be tolerated where transport is at-least-once.

Deduplication MUST use stable event identity.

---

## §1378 — Event Loss

Missing required events MUST be observable.

A successful final summary cannot compensate for missing mandatory execution evidence.

---

## §1379 — Partial Run

A run interrupted after some fixtures execute is a partial run.

Its completed records remain valid if individually valid.

The aggregate run MUST NOT be reported complete.

---

## §1380 — Run Completion

`COMPLETED` means the runner completed the declared orchestration lifecycle.

It does not imply:

```text
all fixtures passed
```

---

## §1381 — Successful Run vs Conformance

These remain distinct:

```text
runner completed successfully ≠ subject conformed
```

---

## §1382 — Conformance vs Release

These remain distinct:

```text
conformant ≠ release-eligible
```

---

## §1383 — Gate vs Publication

These remain distinct:

```text
gate PASS ≠ publication
```

---

## §1384 — Publication

Publication is an external lifecycle event.

The runner SHOULD record:

```text
publication identity
destination
timestamp
published artifact digest
```

---

## §1385 — Deployment

Deployment is distinct from publication.

Deployment MUST NOT alter release identity.

---

## §1386 — Rollback

Rollback MUST reference an existing immutable release identity.

It MUST NOT mutate the historical release.

---

## §1387 — Revocation

Revocation creates a new state/event associated with the existing release.

Historical evidence MUST remain preserved.

---

## §1388 — Release Evidence Closure

A release-grade claim requires closure across:

```text
Protocol
Subject
Source
Artifact
Build
Fixtures
Execution
Observation
Checks
Evidence
Coverage
Conformance
Gate
Policy
```

---

## §1389 — Claim Scope

The runner MUST derive claim scope from evidence scope.

```text
CLAIMED_SCOPE ⊆ EVIDENCE_SCOPE
```

---

## §1390 — Overclaim Prevention

A run over 1,000 fixtures MUST NOT produce a claim about 10,000 fixtures unless the additional 9,000 are independently evidenced.

---

## §1391 — Summary Counters

The runner SHOULD expose:

```text
declared
discovered
scheduled
executed
evaluated
passed
failed
blocked
unknown
skipped
errored
```

as independent counters.

---

## §1392 — Coverage Reconciliation

The following identity SHOULD hold:

```text
declared =
    passed
  + failed
  + blocked
  + unknown
  + skipped
  + error
  + unevaluated
```

subject to explicitly documented multi-attempt semantics.

---

## §1393 — Attempt Counters

Attempt counts MUST NOT be confused with fixture counts.

One fixture may have:

```text
1 fixture
3 attempts
1 aggregate result
```

---

## §1394 — Aggregate Attempt Policy

Aggregation across attempts MUST be deterministic and policy-defined.

---

## §1395 — First-Pass Bias

The runner MUST NOT discard later contradictory evidence merely because an earlier attempt passed.

---

## §1396 — Last-Pass Bias

Likewise, the runner MUST NOT replace earlier failures merely because the latest attempt passed.

---

## §1397 — Evidence Selection

If an aggregate result selects representative evidence, the selection rule MUST be explicit.

All materially relevant attempts MUST remain traceable.

---

## §1398 — Runner Output Set

A release-grade run SHOULD produce:

```text
run.json
plan.json
schedule.json
execution/*.json
observation/*.json
check/*.json
evidence/*.json
coverage.json
conformance.json
gate.json
release-manifest.json
event-ledger.jsonl
```

---

## §1399 — Output Authority

Among runner outputs:

```text
raw execution records
observations
checks
evidence
coverage
gate
release manifest
```

are authoritative according to their respective schemas.

Human summaries are projections.

---

## §1400 — Absolute Runner Invariant

The RFL-AE Conformance Runner MUST enforce:

```text
NO PLAN → NO CONFORMANCE EXECUTION
NO AUTHORIZATION → NO DISPATCH
NO SCOPE → NO AUTHORIZED EXECUTION
NO EXECUTION RECORD → NO EXECUTION CLAIM
NO OBSERVATION → NO OBSERVATION CLAIM
NO SEMANTIC CHECK → NO VERIFICATION RESULT
NO EVIDENCE BINDING → NO VERIFIED CLAIM
NO DECLARED POPULATION → NO COMPLETE COVERAGE
NO REQUIRED FIXTURE COVERAGE → NO COMPLETE CONFORMANCE
NO REQUIRED CI EVIDENCE → NO CI CLAIM
NO CRITICAL MUTATION DETECTION → NO RELEASE
NO FROZEN INPUTS → NO REPRODUCIBLE RELEASE CLAIM
NO VALID GATE → NO RELEASE ELIGIBILITY
NO RELEASE MANIFEST → NO IMMUTABLE RELEASE IDENTITY
NO EVIDENCE → NO VERIFIED CLAIM
NO EVIDENCE → NO VERIFIED CLAIM
NO EVIDENCE → NO VERIFIED CLAIM
```

The runner's fundamental rule is therefore:

```text
                 DECLARE
                     │
                     ▼
                   FREEZE
                     │
                     ▼
                  AUTHORIZE
                     │
                     ▼
                  SCHEDULE
                     │
                     ▼
                   EXECUTE
                     │
                     ▼
                  OBSERVE
                     │
                     ▼
                   CHECK
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
                    GATE
                     │
                     ▼
                  RELEASE
```

No stage may impersonate a later stage.

In particular:

```text
execution success     ≠ observation
observation     ≠ verification
verification     ≠ evidence
evidence     ≠ coverage
coverage     ≠ conformance
conformance     ≠ release eligibility
release eligibility     ≠ release
release     ≠ publication
publication     ≠ deployment
```

This separation is the core correctness property of the RFL-AE orchestration layer.

# End of RFL-AE Conformance Runner & Orchestration Specification v1.5
