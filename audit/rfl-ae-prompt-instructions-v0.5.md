# RFL-AE PROMPT INSTRUCTIONS — Runtime & Execution Contract v0.5

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with several list-structured and YAML-shaped passages collapsed onto single lines; line breaks and indentation restored, with tree/chain/YAML structures reconstructed from their inline form. Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Standing.** Begins at **§106**, continuing [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md) (§71–§105), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md) (§41–§70), and [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md) (§0–§40). The effective specification is now **§0–§149 across four documents**.
>
> **The runtime contract.** v0.2–v0.3 specify the agent's behaviour, v0.4 specifies the pack artifact; v0.5 specifies **the component that enforces both** — and §106 states the reason it must exist: *"The runtime MUST NOT trust the model to enforce the policy that constrains the model."*

---

## 106. PURPOSE

The runtime is the enforcement boundary between:

```text
instruction
authority
model
tool execution
observation
evidence
verification
```

The runtime MUST NOT trust the model to enforce the policy that constrains the model.

The runtime enforces the compiled policy.

```text
CompiledPack
      │
      ▼
Runtime Policy
      │
      ├── Authority Gate
      ├── Scope Gate
      ├── Tool Gate
      ├── Argument Gate
      ├── Execution Gate
      └── Evidence Gate
               │
               ▼
           Tool / Process
```

---

## 107. RUNTIME IS THE AUTHORITY ENFORCEMENT POINT

The model may request:

```text
TOOL_CALL
```

The runtime decides:

```text
ALLOW
DENY
MODIFY
BLOCK
```

The model MUST NOT be able to directly execute a privileged operation.

```text
MODEL
   │
   │ request
   ▼
RUNTIME
   │
   ├── policy evaluation
   ├── authority evaluation
   ├── scope evaluation
   └── argument validation
        │
        ├── DENY
        └── ALLOW
              │
              ▼
            TOOL
```

---

## 108. TOOL REQUEST CONTRACT

Every tool request should have an explicit identity:

```yaml
tool_request:
  request_id: ...
  tool_id: ...
  operation: ...
  subject: ...
  arguments: ...
  requested_capabilities: []
  requested_scope: ...
```

The runtime MUST independently determine:

```text
effective_capabilities
effective_scope
```

The model's declaration is not authoritative.

---

## 109. AUTHORIZATION ALGORITHM

Conceptually:

```text
authorize(request):
    identify subject
    identify tool
    identify operation
    resolve requested capability
    resolve granted capability
    resolve environment capability
    resolve effective scope
    evaluate immutable rules
    evaluate required rules
    evaluate safety rules
    evaluate argument constraints
    if any mandatory rule fails:
        DENY
    if authority insufficient:
        BLOCK
    if scope exceeds authority:
        BLOCK
    otherwise:
        ALLOW
```

No implicit fallback to broader permissions is permitted.

---

## 110. CAPABILITY CHECK

A tool request is allowed only when:

```text
requested_capability ⊆ effective_capabilities
```

Example:

```text
request:   repository.write
effective: repository.read
```

Result:

```text
BLOCKED
```

Never interpret read authority as implied write authority.

---

## 111. SCOPE CHECK

Authorization requires both:

```text
CAPABILITY + SCOPE
```

Example:

```text
capability: repository.write
scope:      docs/**
```

does not authorize:

```text
src/**
```

even if write capability exists.

Therefore:

```text
AUTHORITY = CAPABILITY ∩ SCOPE
```

---

## 112. TOOL IDENTITY

Tools MUST be identified independently of natural-language names.

Use:

```yaml
tool:
  id: rfl.corpus-audit
  version: 0.5.0
  implementation_digest: ...
```

Avoid treating:

```text
"the audit script"
```

as sufficient identity.

Two implementations with the same name are not necessarily the same verifier.

---

## 113. TOOL VERSION BINDING

For evidence-bearing execution:

```text
ToolID + ToolVersion + ToolDigest
```

should be captured where practical.

If the implementation changes:

```text
old evidence
```

must not silently become evidence for:

```text
new implementation
```

---

## 114. ARGUMENT VALIDATION

Tool arguments are part of the execution identity.

Example:

```text
audit --root skills --strict
```

is not equivalent to:

```text
audit --root docs
```

Record canonicalized arguments.

```text
ArgumentDigest = H(CanonicalArguments)
```

Then:

```text
ExecutionDigest = H(
    SubjectDigest
    + ToolDigest
    + ArgumentDigest
    + EnvironmentDigest
)
```

---

## 115. PRE-EXECUTION RECORD

Before execution create:

```yaml
execution:
  execution_id: ...
  state: scheduled

  subject:
    id: ...
    revision: ...
    digest: ...

  check:
    id: ...
    digest: ...

  tool:
    id: ...
    version: ...
    digest: ...

  arguments:
    digest: ...

  scope:
    declared: ...

  authorization:
    effective_capabilities: ...
```

This record describes what was authorized and scheduled.

It does NOT prove execution occurred.

---

## 116. EXECUTION STATES

Use:

```text
SCHEDULED
RUNNING
COMPLETED
FAILED_TO_START
TIMEOUT
CANCELLED
INTERRUPTED
CRASHED
```

These are execution states.

They must not be confused with verification states.

For example:

```text
execution  = COMPLETED
verification = FAIL
```

is perfectly valid.

Likewise:

```text
execution  = CRASHED
verification = ERROR
```

is valid.

---

## 117. EXECUTION RESULT

After execution:

```yaml
execution:
  state: completed

  exit:
    code: 0

  stdout:
    digest: ...

  stderr:
    digest: ...

  artifacts:
    - id: ...
      digest: ...

  timing:
    started: ...
    ended: ...
```

The execution record establishes that an execution event occurred.

It does not by itself establish that the checked property passed.

---

## 118. OBSERVATION CONTRACT

The runtime converts execution into observations.

```yaml
observation:
  observation_id: ...
  execution_id: ...

  type: structured_result

  data:
    findings: ...

  digest: ...
```

The observation must retain provenance to its execution.

No detached observation should be accepted as verification evidence.

---

## 119. CHECK EVALUATION

The verifier evaluates:

```text
Check + Subject + Scope + Observation
```

and returns:

```yaml
result:
  status: PASS | FAIL | ERROR | UNKNOWN | SKIPPED | BLOCKED
  predicate: ...
  explanation: ...
```

The explanation is informational.

The predicate is normative.

---

## 120. RESULT INTEGRITY

The runtime MUST NOT accept:

```text
result.status = PASS
```

as sufficient.

It must establish:

```text
predicate exists
predicate executed
observation exists
observation belongs to execution
subject matches
scope matches
verifier matches
```

Only then may PASS become a candidate verified result.

---

## 121. PASS DERIVATION

Conceptually:

```text
PASS requires:

valid subject
AND valid check
AND valid verifier
AND authorized execution
AND successful observation
AND predicate evaluated
AND predicate satisfied
AND required coverage
AND required evidence
```

If any required condition is absent:

```text
PASS is not established.
```

---

## 122. FAIL DERIVATION

FAIL requires an evaluated predicate:

```text
predicate evaluated
AND predicate violated
```

Therefore:

```text
checker crash
```

does not become:

```text
FAIL
```

by default.

It becomes:

```text
ERROR
```

---

## 123. UNKNOWN DERIVATION

UNKNOWN means:

```text
available evidence < evidence required to determine predicate
```

Examples:

```text
subject identity unavailable
required artifact missing
insufficient observation
incomplete information
unsupported semantic case
```

UNKNOWN is a legitimate result.

Do not force binary answers where the evidence does not support them.

---

## 124. BLOCKED DERIVATION

BLOCKED means execution could not legitimately proceed.

Examples:

```text
missing authorization
missing dependency
forbidden operation
unavailable required resource
scope violation
```

A blocked check has not passed or failed.

---

## 125. EVIDENCE GATE

Evidence validity requires:

```text
subject identity
+ check identity
+ verifier identity
+ execution identity
+ observation identity
+ scope
+ result
```

Conceptually:

```text
EvidenceValid =
    SubjectBound
    ∧ CheckBound
    ∧ VerifierBound
    ∧ ExecutionBound
    ∧ ObservationBound
    ∧ ScopeBound
    ∧ ResultBound
```

---

## 126. EVIDENCE STATUS

Evidence itself has a lifecycle:

```text
CREATED
   ↓
BOUND
   ↓
VALIDATED
   ↓
ADMISSIBLE
   ↓
USED_BY_GATE
```

Possible invalid states:

```text
UNBOUND
STALE
INCOMPLETE
CONFLICTED
CORRUPTED
MISMATCHED
```

Invalid evidence MUST NOT silently participate in a release gate.

---

## 127. STALE EVIDENCE

Evidence becomes stale when its bound subject or verifier no longer matches the current target.

Example:

```text
Evidence E1 subject = commit A
```

Current subject:

```text
commit B
```

Then:

```text
E1 ≠ evidence for B
```

unless an explicit equivalence relation is established.

Default:

```text
STALE
```

---

## 128. EVIDENCE REUSE

Evidence reuse requires an explicit rule.

Possible basis:

```text
same subject digest
same check digest
same verifier digest
same relevant inputs
same required environment semantics
```

If these cannot be established:

```text
do not reuse evidence.
```

---

## 129. COVERAGE GATE

Coverage must be evaluated separately from result.

Example:

```text
10/10 files checked
predicate PASS
```

may establish:

```text
PASS
coverage COMPLETE
```

But:

```text
10/100 files checked
predicate PASS
```

establishes at most:

```text
PASS for executed scope
coverage PARTIAL
```

It does not establish:

```text
full corpus PASS
```

---

## 130. COMPOSITE GATES

A gate may combine checks:

```yaml
gate:
  id: repository-release

  requires:
    - corpus.numbering
    - corpus.provenance
    - links.integrity
    - tests.regression

  coverage:
    required: complete

  evidence:
    required: true
```

Gate evaluation:

```text
ALL required checks admissible
AND ALL required checks satisfy predicates
AND coverage requirements satisfied
AND evidence requirements satisfied
```

Otherwise the gate does not pass.

---

## 131. GATE NON-MONOTONICITY

Adding evidence can change:

```text
UNKNOWN → PASS
```

or:

```text
UNKNOWN → FAIL
```

Adding evidence must never be assumed to improve the result.

Likewise:

```text
new subject revision
```

may invalidate an earlier PASS.

Therefore verification is revision-bound.

---

## 132. EXECUTION RECEIPT

For high-value executions produce:

```yaml
execution_receipt:
  execution_id: ...
  subject_digest: ...
  pack_digest: ...
  task_digest: ...
  check_digest: ...
  tool_digest: ...
  argument_digest: ...
  environment_digest: ...

  state: completed

  result:
    exit_code: 0

  observation_digest: ...

  receipt_digest: ...
```

This is the runtime's proof-of-execution artifact.

It does not by itself prove semantic correctness.

---

## 133. EVIDENCE RECEIPT

An evidence receipt binds:

```text
subject
check
verifier
execution
observation
coverage
result
```

Conceptually:

```text
EvidenceReceipt = H(
    Subject
    +
    Check
    +
    Verifier
    +
    Execution
    +
    Observation
    +
    Coverage
    +
    Result
)
```

---

## 134. MODEL / RUNTIME BOUNDARY

The model produces:

```text
intent
reasoning
proposal
tool request
candidate interpretation
candidate report
```

The runtime produces:

```text
authorization
execution
observation
execution receipt
evidence binding
gate result
```

Therefore:

```text
MODEL ≠ RUNTIME
```

The model cannot forge runtime authority by producing runtime-shaped text.

---

## 135. RUNTIME / VERIFIER BOUNDARY

The runtime executes.

The verifier evaluates.

Do not collapse them.

```text
RUNTIME
   ↓
OBSERVATION

VERIFIER
   ↓
PREDICATE RESULT
```

A runtime reporting:

```text
exit_code = 0
```

does not mean the verifier's predicate passed.

---

## 136. VERIFIER / GATE BOUNDARY

The verifier evaluates an individual predicate.

The gate combines admissible verification results.

```text
CHECK
   ↓
RESULT

RESULTS
   ↓
GATE
```

A gate must not reinterpret an ERROR as PASS.

---

## 137. GATE / RELEASE BOUNDARY

A release gate determines whether release conditions are satisfied.

It does not itself perform publication.

```text
VERIFICATION GATE
        ↓
RELEASE AUTHORIZATION
        ↓
PUBLISH
```

Publication remains an independently authorized operation.

---

## 138. AUDIT LOG

The runtime should preserve an append-only logical event stream:

```text
TASK_ACCEPTED
PACK_RESOLVED
AUTHORITY_RESOLVED
SCOPE_RESOLVED
TOOL_REQUESTED
AUTHORIZATION_GRANTED
AUTHORIZATION_DENIED
EXECUTION_STARTED
EXECUTION_COMPLETED
EXECUTION_FAILED
OBSERVATION_CREATED
CHECK_EVALUATED
EVIDENCE_BOUND
COVERAGE_EVALUATED
GATE_EVALUATED
REPORT_GENERATED
```

Events should reference stable IDs rather than relying solely on prose.

---

## 139. EVENT INTEGRITY

Each event should identify:

```text
event_id
event_type
timestamp
subject
actor
parent_event
payload_digest
```

Where appropriate:

```text
previous_event_digest
```

can provide a tamper-evident event chain.

---

## 140. ACTOR MODEL

The actor producing an event must be explicit.

Possible actors:

```text
USER
MODEL
RUNTIME
TOOL
VERIFIER
GATE
SYSTEM
```

Do not attribute runtime observations to the model.

Do not attribute model proposals to the runtime.

Do not attribute verifier conclusions to the tool.

---

## 141. ACTOR AUTHORITY

Actors have different authority.

Example:

```text
MODEL:    request execution
RUNTIME:  authorize and execute
TOOL:     produce observation
VERIFIER: evaluate predicate
GATE:     evaluate release conditions
```

No actor should silently inherit another actor's authority.

---

## 142. REPLAY

A verification execution should be replayable where the environment permits.

Replay requires:

```text
subject identity
pack identity
check identity
tool identity
arguments
relevant environment inputs
```

Replay result:

```text
MATCH
MISMATCH
INDETERMINATE
```

A replay mismatch is evidence requiring investigation.

It is not automatically an artifact failure.

---

## 143. DETERMINISTIC REPLAY

For deterministic checks:

```text
Replay(E)
```

should reproduce:

```text
same relevant observation
same predicate result
```

If not:

```text
NONDETERMINISM
```

must be investigated.

Possible causes:

```text
time
randomness
network
filesystem ordering
environment
dependency versions
external services
```

---

## 144. SECURITY PROPERTY

The runtime MUST enforce:

```text
UNTRUSTED DATA
     ✕
AUTHORITY ESCALATION
```

and:

```text
MODEL OUTPUT
     ✕
DIRECT PRIVILEGED EXECUTION
```

and:

```text
EVIDENCE
     ✕
SUBJECT MISMATCH
```

and:

```text
STALE EVIDENCE
     ✕
CURRENT GATE
```

---

## 145. MINIMUM RUNTIME TEST MATRIX

The runtime MUST eventually test at least:

```text
R01  authorized read → ALLOW
R02  unauthorized write → BLOCK
R03  out-of-scope write → BLOCK
R04  missing subject → BLOCK/UNKNOWN
R05  missing dependency → BLOCK
R06  tool crash → ERROR
R07  predicate violation → FAIL
R08  predicate satisfied → PASS
R09  insufficient observation → UNKNOWN
R10  stale evidence → REJECT
R11  mismatched subject evidence → REJECT
R12  model authority escalation → REJECT
R13  repository prompt injection → DATA ONLY
R14  deterministic compilation → SAME DIGEST
R15  mutation surviving verifier → DETECT GAP
```

---

## 146. RUNTIME RELEASE GATE

The runtime MUST NOT be considered verification infrastructure merely because it can execute tools.

Minimum gate:

```text
authority enforcement verified
scope enforcement verified
tool identity verified
argument identity verified
execution records verified
observation binding verified
status semantics verified
evidence binding verified
coverage enforcement verified
stale evidence rejected
subject mismatch rejected
prompt injection contained
replay semantics tested
negative tests verified
```

---

## 147. FUNDAMENTAL RUNTIME INVARIANT

```text
THE MODEL MAY REQUEST.
THE RUNTIME MAY AUTHORIZE.
THE TOOL MAY EXECUTE.
THE TOOL MAY OBSERVE.
THE VERIFIER MAY EVALUATE.
THE EVIDENCE LAYER MAY BIND.
THE GATE MAY DECIDE.
NO COMPONENT MAY CLAIM THE AUTHORITY OF ANOTHER COMPONENT.
```

---

## 148. FINAL EXECUTION LAW

```text
REQUEST
   ↓
AUTHORIZE
   ↓
EXECUTE
   ↓
OBSERVE
   ↓
EVALUATE
   ↓
BIND EVIDENCE
   ↓
ESTABLISH COVERAGE
   ↓
DERIVE CLAIM
   ↓
EVALUATE GATE
```

Never replace this with:

```text
PROMPT
   ↓
LLM
   ↓
PASS
```

The former is an auditable execution protocol.

The latter is an assertion.

---

## 149. FINAL v0.5 PRINCIPLE

```text
THE AGENT IS NOT TRUSTED TO VERIFY ITSELF.
THE AGENT IS CONSTRAINED BY THE RUNTIME.
THE RUNTIME IS OBSERVED BY THE EVIDENCE SYSTEM.
THE EVIDENCE IS EVALUATED BY VERIFICATION LOGIC.
THE RELEASE GATE ACCEPTS ONLY ADMISSIBLE, SCOPE-BOUND,
SUBJECT-BOUND EVIDENCE.
```

Therefore:

```text
NO AUTHORIZATION       → NO EXECUTION
NO EXECUTION           → NO EXECUTION EVIDENCE
NO VALID OBSERVATION   → NO PREDICATE RESULT
NO VALID PREDICATE RESULT → NO VERIFIED RESULT
NO COVERAGE            → NO COMPLETE CLAIM
NO ADMISSIBLE EVIDENCE → NO RELEASE GATE PASS
```
