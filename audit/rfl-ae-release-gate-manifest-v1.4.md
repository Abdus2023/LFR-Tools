# RFL-AE — Release Gate, Claim Derivation & Immutable Release Manifest Specification v1.4

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with list-structured, JSON, graph, pipeline, truth-table and table passages collapsed into inline single-backtick runs; line breaks, indentation, and fenced structure restored. Restored from their inline form: the object list, stage chains and gate pipeline (§998–§1004), the distinction and inequality chains (§999), the release-candidate and gate-evaluation JSON objects (§1000, §1004), the identity binding sets and digest formulas (§1001, §1002, §1032, §1057, §1064, §1102), the gate state and predicate enumerations (§1005, §1014), the gate predicate and policy JSON objects (§1013, §1015), the gate input closure (§1018), the release-requirement and policy-layer sets (§1023, §1024), the release class set (§1026), the artifact, source and build identity sets (§1031, §1035, §1037), the packaging and provenance chains (§1033, §1036, §1040), the CI binding and scope sets (§1044, §1049, §1052), the freeze and invalidation sets (§1056, §1058), the release-manifest JSON (§1061), the manifest completeness and traceability chains (§1067, §1068), the release-claim JSON (§1069), the containment relations (§1071), the claim-derivation chain (§1072), the signature and trust-root sets (§1076), the publication metadata set (§1080), the release status set and transition chains (§1088, §1090), the release state machine (§1091), the override identity set (§1094), the gate predicate-evidence JSON (§1100), the gate and manifest fixture and mutation enumerations (§1105, §1107, §1110, §1164, §1166), the manifest validation set (§1111), the release identity graph (§1115), the release closure predicate (§1116), the evidence bundle set (§1118), the release audit question set (§1124), the release report set (§1125), the claim scope matrix (§1131), the prohibited-promotion chain (§1132), the release delta set (§1134), the digest-mismatch inequalities (§1139–§1144), the security and trust boundary sets (§1150, §1151, §1152), the independent verification sets (§1153, §1154, §1156, §1157), the determinism and ordering rules (§1161–§1163), the canonical metadata classes (§1170–§1172), the blocking-reason JSON (§1179), the fail-fast and gate-coverage rules (§1181, §1183, §1186), the bootstrap chain and options (§1187, §1189, §1190), the Rust guidance and type-separation sets (§1193, §1194), the reference APIs (§1195–§1197), the error identity set (§1198), the golden fixture expectations (§1199), and the final chain, laws and architectural invariant (§1200). Declared as a transformation per the lineage's own rule: **no wording was added, removed, or reordered.**
>
> **Layout note for §1091.** The release state machine arrived as a collapsed box-drawing matrix. Its **token sequence** (CANDIDATE → FROZEN → GATED → the FAIL/BLOCKED/ERROR fan → new candidate → GATED → PASS → ELIGIBLE → RELEASED → PUBLISHED) is preserved exactly as supplied; the **row and column geometry** is the only thing reconstructed, because the inline form lost all alignment. This is the single block in this document where a reader cannot check the layout against the arrival form.
>
> **Recording conventions, declared.** Two presentation additions are present and are **not** part of the source: (1) a horizontal rule `---` before each section heading, one per section — this convention applies to **all fourteen recorded documents** in this corpus; (2) Markdown code fences with language tags in place of the source's inline single-backtick wrapping. Neither alters any word, number, identifier, or ordering. See [Part XIX](rfl-ae-prompt-packs-part19.md) §11.3.
>
> **Heading level note.** As with v1.2 and v1.3, v1.4 marks sections at `##` under a document-level `#` title. Preserved as supplied.
>
> **Standing.** Begins at **§998**, continuing [`v1.3`](rfl-ae-coverage-evaluation-conformance-aggregation-v1.3.md) (§801–§997). v1.3 ends at §997 and v1.4 opens at §998 — **the range is contiguous**. The effective specification is now **§0–§1200 across fourteen supplied documents**. **Citation rule: §1–§28 is occupied twice** (v0.1 §1–§28 and v0.2 §0–§40 share 28 numbers, zero identical headings) — citations in that range MUST name the document. See [Part XXI](rfl-ae-prompt-packs-part21.md) §7.
>
> **Note on §1000 and §1200.** Two round-numbered section numbers appear in this range (§1000 Release Candidate, §1200 Final Release Invariant). Both are ordinary positions in the sequence — §1000 is the 1000th assigned number in the union, not a milestone — and §1200 is simply the last section supplied. Neither carries meaning beyond its content, and neither should be cited as a boundary marker.
>
> **Status:** NORMATIVE
> **Predecessor:** RFL-AE v1.3 — Coverage Evaluation & Conformance Aggregation
> **Range:** §998–§1200
> **Purpose:** Define the release gate, release eligibility semantics, scoped claim derivation, artifact/source binding, CI authority, freeze protocol, release manifest, and immutable release identity.

---

## §998 — Purpose

RFL-AE v1.4 defines the final boundary between:

```text
CONFORMANCE
```

and:

```text
RELEASE
```

It defines:

```text
ReleaseCandidate
ReleaseGate
GatePredicate
GateEvaluation
ReleaseClaim
ReleaseManifest
ReleaseArtifact
ReleaseFreeze
ReleaseIdentity
```

The central objective is:

```text
A CONFORMANT RESULT
        ↓ must still pass
        ↓
AN EXPLICIT RELEASE GATE
        ↓ before
        ↓
A RELEASE CLAIM EXISTS
```

---

## §999 — Fundamental Separation

The following are distinct:

```text
CONFORMANT
RELEASE-ELIGIBLE
RELEASED
PUBLISHED
SIGNED
DEPLOYED
```

Therefore:

```text
CONFORMANT ≠ RELEASE-ELIGIBLE
RELEASE-ELIGIBLE ≠ RELEASED
RELEASED ≠ DEPLOYED
SIGNED ≠ VERIFIED
PUBLISHED ≠ VALID
```

No state may be inferred from another without an explicit protocol relation.

---

## §1000 — Release Candidate

A release candidate is an immutable candidate identity evaluated against a frozen release contract.

```json
{
  "release_candidate_id": "release-candidate:...",
  "subject_id": "subject:...",
  "artifact_id": "artifact:...",
  "protocol_digest": "sha256:...",
  "population_digest": "sha256:...",
  "evidence_population_digest": "sha256:...",
  "policy_digest": "sha256:..."
}
```

---

## §1001 — Release Candidate Identity

A release candidate MUST bind:

```text
subject
artifact
protocol
fixture population
evidence population
coverage
policy
release policy
```

Changing any identity-bearing input creates a new candidate identity.

---

## §1002 — Release Candidate Digest

The canonical release candidate digest is:

```text
candidate_digest = H(
  canonical(
    subject_identity
    + artifact_identity
    + protocol_identity
    + population_identity
    + evidence_population_identity
    + policy_identity
    + release_policy_identity
  )
)
```

---

## §1003 — Release Gate

A release gate is a deterministic predicate over a frozen release candidate.

```text
Gate(candidate) → GateEvaluation
```

The gate MUST NOT mutate the candidate.

---

## §1004 — Gate Evaluation

A gate evaluation MUST contain:

```json
{
  "gate_evaluation_id": "gate-evaluation:...",
  "candidate_id": "release-candidate:...",
  "gate_policy_id": "release-policy:...",
  "inputs": [],
  "predicates": [],
  "status": "PASS",
  "evaluation_digest": "sha256:..."
}
```

---

## §1005 — Gate States

Required gate states:

```text
PENDING
EVALUATING
PASS
FAIL
BLOCKED
ERROR
UNKNOWN
STALE
INVALID
```

---

## §1006 — Gate PASS

A gate may return `PASS` only when every required gate predicate evaluates successfully.

```text
GatePASS = ∀ p ∈ RequiredPredicates:
    p = PASS
```

---

## §1007 — Gate FAIL

A gate returns `FAIL` when a required predicate evaluates false.

A gate MUST preserve the failing predicate identity and evidence.

---

## §1008 — Gate BLOCKED

A gate is `BLOCKED` when a prerequisite required to evaluate the gate is unavailable.

Examples:

```text
required CI evidence missing
required artifact unavailable
required verifier unavailable
required dependency unresolved
```

`BLOCKED` is not `FAIL`.

---

## §1009 — Gate ERROR

A gate is `ERROR` when the gate evaluator itself cannot complete normally.

A gate error MUST NOT become `FAIL` merely because the process exited nonzero.

---

## §1010 — Gate UNKNOWN

A gate is `UNKNOWN` when required information cannot be established.

Unknown MUST remain unknown.

---

## §1011 — Gate STALE

A previously evaluated gate becomes `STALE` when an identity-bearing input changes.

Examples:

```text
artifact digest changes
population digest changes
policy digest changes
evidence is revoked
protocol digest changes
```

---

## §1012 — Gate INVALID

A gate is `INVALID` when its own record fails structural, identity, integrity, or semantic validation.

An invalid gate cannot establish release eligibility.

---

## §1013 — Gate Predicate

Each gate predicate MUST have stable identity.

```json
{
  "predicate_id": "release:required-fixtures-pass-v1",
  "version": "1.0",
  "definition_digest": "sha256:..."
}
```

---

## §1014 — Predicate Categories

Reference release predicates SHOULD include:

```text
PROTOCOL_FROZEN
SUBJECT_IDENTITY_VALID
ARTIFACT_IDENTITY_VALID
FIXTURE_POPULATION_FROZEN
REQUIRED_FIXTURES_COVERED
REQUIRED_FIXTURES_PASS
EVIDENCE_VALID
EVIDENCE_COMPLETE
MUTATION_REQUIREMENTS_SATISFIED
REPLAY_REQUIREMENTS_SATISFIED
CI_REQUIREMENTS_SATISFIED
DEPENDENCY_REQUIREMENTS_SATISFIED
RELEASE_METADATA_VALID
```

---

## §1015 — Gate Policy

Gate semantics MUST be defined by an immutable versioned policy.

```json
{
  "policy_id": "release-policy:strict-v1",
  "version": "1.0",
  "required_predicates": [],
  "blocking_states": [
    "FAIL",
    "ERROR",
    "UNKNOWN",
    "BLOCKED",
    "STALE",
    "INVALID"
  ]
}
```

---

## §1016 — Gate Policy Digest

Every gate evaluation MUST bind the exact policy digest.

Changing gate policy requires a new evaluation.

---

## §1017 — No Hidden Gate Rules

Gate evaluators MUST NOT contain undisclosed release exceptions.

Rules such as:

```text
ignore this fixture
ignore this failure
ignore missing CI
accept stale evidence
```

MUST be explicit policy data if permitted at all.

---

## §1018 — Gate Input Closure

The gate MUST establish identity and validity for all required inputs before evaluating release predicates.

```text
GateInputClosure =
    protocol
  ∧ subject
  ∧ artifact
  ∧ population
  ∧ evidence
  ∧ coverage
  ∧ policy
```

---

## §1019 — Gate Input Failure

If required gate input closure fails:

```text
Gate = BLOCKED
```

unless the policy explicitly defines the condition as `FAIL` or `INVALID`.

---

## §1020 — Conformance Input

The gate MUST consume the canonical conformance result.

It MUST NOT independently reinterpret raw test logs as a replacement for coverage evaluation.

---

## §1021 — Conformance Claim Binding

A gate MUST bind the exact conformance claim:

```text
conformance_claim_id
conformance_digest
```

A summary string is insufficient.

---

## §1022 — Required Conformance

A strict release gate normally requires:

```text
conformance.status = CONFORMANT
```

and:

```text
conformance_digest = expected/current digest
```

---

## §1023 — Release-Specific Requirements

Release requirements MAY exceed conformance requirements.

Examples:

```text
release artifact signature
CI execution
reproducible build
documentation completeness
license metadata
security checks
packaging checks
```

These are release predicates, not automatically protocol-conformance predicates.

---

## §1024 — Release Policy Layers

A release policy SHOULD be structured:

```text
BASE
├── identity requirements
├── conformance requirements
├── evidence requirements
├── artifact requirements
├── CI requirements
├── reproducibility requirements
├── mutation requirements
└── publication requirements
```

---

## §1025 — Release Policy Composition

Policy composition MUST be deterministic.

If:

```text
P = Base + Profile + ReleaseClass
```

then the compiled policy MUST have a stable semantic digest.

---

## §1026 — Release Classes

A system MAY define:

```text
DEVELOPMENT
INTERNAL
CONFORMANCE
RELEASE_CANDIDATE
RELEASE
```

Each class MUST have explicit requirements.

---

## §1027 — Development Release

Development artifacts MAY omit release-grade evidence.

They MUST NOT be represented as release-grade artifacts.

---

## §1028 — Conformance Release

A conformance artifact requires the applicable conformance evidence and coverage.

Additional publication requirements may still be absent.

---

## §1029 — Release Candidate

A release candidate MUST satisfy all candidate-specific gate requirements.

It MAY remain unpublished.

---

## §1030 — Release

A release requires:

```text
release gate PASS + immutable release manifest
```

and any required publication/signature requirements.

---

## §1031 — Release Artifact

A release artifact MUST have:

```text
artifact_id
artifact_version
artifact_digest
artifact_type
build_identity
```

where applicable.

---

## §1032 — Artifact Digest

The artifact digest MUST be calculated over the exact bytes of the released artifact.

```text
artifact_digest = H(exact_artifact_bytes)
```

---

## §1033 — Artifact Packaging

If an artifact is packaged:

```text
source
  ↓
build
  ↓
binary
  ↓
package
  ↓
release archive
```

each identity MAY differ.

The release manifest MUST identify the artifact layer relevant to the claim.

---

## §1034 — Package Digest

A package MUST have its own digest.

A binary digest MUST NOT be substituted for the package digest.

---

## §1035 — Source Identity

Where source binding is required:

```json
{
  "source_revision": "git:...",
  "source_digest": "sha256:..."
}
```

MUST be recorded.

---

## §1036 — Source/Artifact Relation

The release manifest MUST preserve the relation:

```text
source
    ↓
build
    ↓
artifact
```

A source commit alone does not prove the identity of the artifact.

---

## §1037 — Build Identity

A build identity SHOULD contain:

```text
compiler
compiler_version
toolchain
dependencies
build_configuration
build_script
build_environment
```

as required by the release class.

---

## §1038 — Reproducible Build Gate

Where reproducibility is required:

```text
build(source, environment_A)
```

and:

```text
build(source, environment_B)
```

MUST satisfy the declared reproducibility relation.

The relation may be:

```text
BYTE_IDENTICAL
SEMANTICALLY_IDENTICAL
```

but MUST be explicit.

---

## §1039 — Build Reproduction Evidence

Reproducibility claims MUST include evidence for each build.

A statement such as:

```text
"build is reproducible"
```

is not evidence.

---

## §1040 — Artifact Provenance

The artifact provenance chain SHOULD be:

```text
source digest
  ↓
dependency digest
  ↓
toolchain identity
  ↓
build configuration digest
  ↓
build identity
  ↓
artifact digest
```

---

## §1041 — Artifact Substitution

If the released artifact differs from the artifact tested:

```text
tested_artifact_digest != released_artifact_digest
```

the release gate MUST fail or become invalid according to policy.

---

## §1042 — Artifact Promotion

Promotion from candidate artifact to release artifact MUST preserve artifact identity.

If promotion transforms bytes, a new artifact identity is required.

---

## §1043 — CI Authority

Where CI is the designated execution authority:

```text
CI execution evidence
```

is required for release predicates that depend on CI.

Local execution does not substitute for CI execution.

---

## §1044 — CI Execution Record

CI evidence SHOULD bind:

```text
workflow_id
run_id
repository
source_revision
job_id
runner identity
artifact identity
execution identity
```

---

## §1045 — CI Source Binding

A CI run MUST identify the exact source revision actually executed.

Branch name alone is insufficient.

---

## §1046 — CI Artifact Binding

A CI release job MUST identify the exact artifact tested or produced.

A green workflow badge does not establish artifact identity.

---

## §1047 — CI Test Population

Where CI is authoritative for conformance, the CI execution MUST bind the fixture population or an equivalent immutable test manifest.

---

## §1048 — CI Summary Limitation

A CI summary:

```text
tests passed
```

is diagnostic unless its execution and evidence chain satisfies the release contract.

---

## §1049 — Remote Execution

Remote execution MUST preserve enough evidence to distinguish:

```text
requested
scheduled
started
completed
observed
verified
```

---

## §1050 — CI Missing Evidence

If CI is required but its execution evidence cannot be independently established:

```text
CI_REQUIREMENT = BLOCKED
```

---

## §1051 — CI Run Discovery

Searching a CI system and finding no matching run is evidence about observability, not proof that no run exists.

Therefore:

```text
NOT_FOUND
```

MUST NOT automatically become:

```text
NO_EXECUTION_OCCURRED
```

unless the source is authoritative for that conclusion.

---

## §1052 — CI Evidence Scope

CI evidence MUST distinguish:

```text
workflow
run
job
step
test process
artifact
```

These are separate identities.

---

## §1053 — CI Retry

CI retries MUST preserve individual run identities.

A later successful retry MUST NOT erase the failed attempt.

---

## §1054 — CI Cancellation

Cancelled CI runs MUST remain visible.

They are not successful execution evidence.

---

## §1055 — CI Timeout

A CI timeout is an execution result.

It is not automatically a semantic test failure.

---

## §1056 — Release Freeze

Before final gate evaluation, the release candidate MUST be frozen.

Freeze binds:

```text
protocol
subject
artifact
fixture population
evidence population
coverage
policies
release metadata
```

---

## §1057 — Freeze Identity

The frozen candidate MUST have a digest.

```text
freeze_digest = H(canonical(frozen_release_inputs))
```

---

## §1058 — Post-Freeze Mutation

Any mutation to a frozen release input MUST invalidate the freeze.

Examples:

```text
source changed
artifact replaced
fixture changed
evidence added
evidence revoked
policy changed
metadata changed
```

---

## §1059 — Re-Freeze

After a mutation, a new freeze MUST be created.

The old freeze remains historical.

---

## §1060 — Release Gate Race

The gate MUST prevent time-of-check/time-of-use divergence.

The artifact evaluated by the gate MUST be the artifact identified by the release manifest.

---

## §1061 — Release Manifest

The release manifest is the authoritative immutable identity record for a release.

Minimum structure:

```json
{
  "manifest_version": "1.0",
  "release_id": "release:...",
  "release_version": "...",

  "protocol": {},
  "subject": {},
  "source": {},
  "artifact": {},
  "build": {},

  "fixture_population": {},
  "evidence_population": {},
  "coverage": {},
  "conformance": {},

  "gate": {},
  "policies": {},

  "created_at": "...",
  "manifest_digest": "sha256:..."
}
```

---

## §1062 — Release ID

A release MUST have an immutable identity:

```text
release_id = "release:" + stable-id
```

The release ID MUST NOT depend solely on a mutable version string.

---

## §1063 — Release Version

Semantic or human-readable version numbers MAY identify the release for humans.

They MUST NOT replace content-addressed release identity.

---

## §1064 — Release Digest

The release manifest digest MUST bind all identity-bearing release fields.

```text
manifest_digest = H(canonical(manifest_without_manifest_digest))
```

---

## §1065 — Manifest Immutability

Once published as a release manifest:

```text
manifest_digest → manifest content
```

MUST remain immutable.

---

## §1066 — Manifest Replacement

A changed release manifest MUST receive a new digest and release identity.

In-place mutation is prohibited.

---

## §1067 — Manifest Completeness

The manifest MUST identify every release-critical dependency.

At minimum:

```text
protocol
subject
artifact
fixture population
coverage
conformance
gate policy
gate evaluation
```

---

## §1068 — Manifest Traceability

The release manifest MUST support:

```text
release
  ↓
gate
  ↓
conformance
  ↓
coverage
  ↓
evidence
  ↓
execution
  ↓
artifact
  ↓
source
```

traceability.

---

## §1069 — Release Claim

A release claim MUST be scoped.

Example:

```json
{
  "claim_id": "claim:release-...",
  "subject_id": "subject:...",
  "artifact_digest": "sha256:...",
  "release_id": "release:...",
  "scope": "RFL-AE protocol conformance",
  "status": "RELEASED"
}
```

---

## §1070 — Claim Semantics

A release claim means only what its scope declares.

For example:

```text
"RFL-AE protocol conformance verified"
```

does not imply:

```text
bug-free
secure against all attacks
production-safe
correct for all environments
```

unless those claims have separate evidence.

---

## §1071 — No Overclaiming

The release system MUST NOT generate broader claims than its evidence supports.

Formally:

```text
ClaimScope ⊆ EvidenceScope
```

and:

```text
ClaimedPopulation ⊆ CoveredPopulation
```

---

## §1072 — Claim Derivation

A release claim MUST be derived from:

```text
frozen candidate + conformance claim + release gate + release manifest
```

---

## §1073 — Claim Digest

Every release claim SHOULD have a canonical digest.

Changing claim scope MUST change the claim identity.

---

## §1074 — Release Signature

A release MAY be cryptographically signed.

A signature MUST bind the release manifest or another explicitly defined canonical release object.

---

## §1075 — Signature Semantics

A valid signature establishes authenticity of the signed content under the configured trust model.

It does not prove semantic correctness.

```text
SIGNATURE ≠ CONFORMANCE
```

---

## §1076 — Signer Identity

A signed release MUST identify:

```text
key_id
signature_algorithm
signature
signed_digest
```

---

## §1077 — Trust Root

Signature verification MUST use an explicit trust root.

Unknown or untrusted keys MUST NOT establish trusted release identity.

---

## §1078 — Key Rotation

Key rotation MUST preserve historical release signatures.

Historical manifests MUST NOT be rewritten to use new keys.

---

## §1079 — Unsigned Release

If signatures are optional, unsigned status MUST be explicit.

If signatures are required by release policy:

```text
unsigned → GATE BLOCKED
```

or `FAIL`, according to policy.

---

## §1080 — Publication

Publication is distinct from release identity.

A release MAY be valid before publication.

Publication metadata SHOULD include:

```text
repository
registry
distribution channel
publication timestamp
publication artifact digest
```

---

## §1081 — Published Artifact Verification

After publication, the externally retrievable artifact SHOULD be re-digested.

```text
published_digest == manifest.artifact_digest
```

must hold where exact byte identity is required.

---

## §1082 — Publication Race

If the published artifact differs from the gated artifact:

```text
GATE RESULT ≠ PUBLISHED ARTIFACT
```

The release claim MUST NOT be extended to the published artifact.

---

## §1083 — Deployment

Deployment is outside the basic release identity.

A deployment MUST identify the release artifact it deploys.

---

## §1084 — Deployment Binding

A deployment record SHOULD bind:

```text
release_id
artifact_digest
deployment_target
deployment_event
```

---

## §1085 — Rollback

Rollback MUST identify the exact historical release identity restored.

A rollback is not a mutation of the original release.

---

## §1086 — Release Revocation

A release MAY be revoked.

Revocation MUST preserve:

```text
release manifest
original gate result
original evidence
revocation reason
revocation event
```

---

## §1087 — Revocation Is Not Erasure

Revocation does not rewrite historical evidence.

It changes current release status.

---

## §1088 — Release Status

Required release statuses:

```text
CANDIDATE
GATED
ELIGIBLE
RELEASED
PUBLISHED
REVOKED
SUPERSEDED
```

---

## §1089 — Eligibility

A candidate is `ELIGIBLE` only when the release gate passes.

Eligibility MUST NOT be inferred from version metadata.

---

## §1090 — Release Transition

Reference transition:

```text
CANDIDATE
    ↓
FROZEN
    ↓
GATED
    ↓
ELIGIBLE
    ↓
RELEASED
    ↓
PUBLISHED
```

Failure transitions:

```text
GATED → FAIL
GATED → BLOCKED
GATED → ERROR
GATED → UNKNOWN
GATED → INVALID
```

---

## §1091 — Release State Machine

The complete release state machine is:

```text
                         ┌──────────────┐
                         │   CANDIDATE  │
                         └──────┬───────┘
                                │ freeze
                                ▼
                         ┌──────────────┐
                         │    FROZEN    │
                         └──────┬───────┘
                                │ evaluate
                                ▼
                         ┌──────────────┐
                         │    GATED     │
                         └──────┬───────┘
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
                  FAIL       BLOCKED      ERROR
                    │           │           │
                    └───────────┴───────────┘
                                │
                                ▼
                         new candidate
                                │
                              GATED
                                │
                              PASS
                                ▼
                         ┌──────────────┐
                         │   ELIGIBLE   │
                         └──────┬───────┘
                                │ release
                                ▼
                         ┌──────────────┐
                         │   RELEASED   │
                         └──────┬───────┘
                                │ publish
                                ▼
                         ┌──────────────┐
                         │  PUBLISHED   │
                         └──────────────┘
```

---

## §1092 — No Gate Bypass

The following transition is prohibited:

```text
CANDIDATE → RELEASED
```

without the required gate.

---

## §1093 — Manual Override

If emergency override exists, it MUST be represented as a separate explicit authority path.

It MUST NOT masquerade as ordinary gate PASS.

---

## §1094 — Override Identity

An override MUST identify:

```text
override_id
authority
scope
reason
timestamp
affected predicates
approver
```

where applicable.

---

## §1095 — Override Semantics

An override means:

```text
policy exception granted
```

not:

```text
predicate passed
```

The distinction MUST remain visible.

---

## §1096 — Override Audit

Every override MUST be preserved in the release audit trail.

---

## §1097 — No Silent Release Exception

A release MUST NOT become eligible because a human manually ignored an unresolved gate without producing an explicit override record.

---

## §1098 — Emergency Release

Emergency release MAY use a separate policy.

The emergency policy MUST have its own identity and digest.

---

## §1099 — Emergency Claims

An emergency release MUST NOT claim that omitted requirements were satisfied.

It may claim only that the applicable emergency release policy was satisfied.

---

## §1100 — Gate Evidence

Every gate predicate MUST reference the evidence supporting its evaluation.

Example:

```json
{
  "predicate_id": "release:required-fixtures-pass-v1",
  "status": "PASS",
  "evidence": [
    "coverage:...",
    "conformance:..."
  ]
}
```

---

## §1101 — Gate Evidence Completeness

A gate PASS without predicate-level supporting evidence is not release-grade.

---

## §1102 — Gate Evaluation Digest

The gate evaluation MUST have a canonical digest:

```text
gate_digest = H(canonical(gate_evaluation_without_digest))
```

---

## §1103 — Gate Replay

An independent evaluator SHOULD be able to replay the gate from:

```text
candidate
policies
coverage
conformance
required evidence
```

---

## §1104 — Gate Determinism

Given identical frozen inputs:

```text
Gate(Candidate) = Gate(Candidate)
```

semantically and canonically.

---

## §1105 — Gate Mutation Testing

The gate implementation MUST be mutation-tested.

Critical mutations include:

```text
ignore required predicate
convert BLOCKED → PASS
convert UNKNOWN → PASS
ignore stale evidence
ignore artifact mismatch
ignore population mismatch
ignore failed mutation
ignore missing CI evidence
ignore manifest mismatch
```

---

## §1106 — Gate Mutation Requirement

Every critical gate mutation MUST be detected.

A surviving critical gate mutation blocks release.

---

## §1107 — Gate Negative Fixtures

Required gate fixtures SHOULD include:

```text
all predicates pass
required predicate fails
required evidence missing
evidence stale
artifact mismatch
subject mismatch
population mismatch
policy mismatch
CI evidence missing
critical mutation survives
manifest mismatch
```

---

## §1108 — Gate Positive Fixtures

At least one complete valid release candidate MUST exist in the conformance corpus.

The candidate MUST contain all required identities and evidence.

---

## §1109 — Gate Near-Miss Fixtures

The corpus SHOULD contain candidates differing from the valid candidate by exactly one critical condition.

Purpose:

```text
detect gate over-acceptance
detect gate over-rejection
```

---

## §1110 — Release Manifest Fixture

The release manifest validator MUST have fixtures for:

```text
valid manifest
wrong digest
missing artifact
wrong artifact digest
wrong conformance digest
wrong coverage digest
wrong gate digest
stale evidence
invalid signature
unknown signer
```

---

## §1111 — Manifest Validation

Manifest validation MUST verify:

```text
schema
identity
digest
reference closure
gate status
claim scope
artifact binding
```

---

## §1112 — Manifest Self-Reference

The manifest digest MUST be calculated without recursively including itself.

The digest input MUST be explicitly defined.

---

## §1113 — Manifest Canonicalization

The release manifest MUST use the protocol's canonical serialization before digesting.

---

## §1114 — Manifest Content Addressing

The manifest MAY itself be stored content-addressably:

```text
release-manifest-id = sha256:...
```

---

## §1115 — Release Identity Graph

The complete release identity graph is:

```text
Protocol
    │
    ├── Fixture Population
    │        │
    │        └── Evidence
    │              │
    │              └── Coverage
    │                    │
    │                    └── Conformance
    │
    ├── Source
    │     │
    │     └── Build
    │           │
    │           └── Artifact
    │
    └── Release Policy
              │
              └── Gate
                     │
                     └── Release Manifest
```

---

## §1116 — Release Closure

A release is evidentially closed only when:

```text
ReleaseClosure =
    ProtocolValid
  ∧ SubjectValid
  ∧ ArtifactValid
  ∧ PopulationValid
  ∧ EvidenceValid
  ∧ CoverageValid
  ∧ ConformanceValid
  ∧ GateValid
  ∧ ManifestValid
```

---

## §1117 — Release Closure Failure

If any required closure predicate is false:

```text
RELEASE ≠ VERIFIED
```

and the corresponding release gate MUST prevent an unsupported release claim.

---

## §1118 — Release Evidence Bundle

A release SHOULD ship or reference an evidence bundle containing:

```text
protocol identity
subject identity
source identity
artifact identity
build identity
fixture population
execution
evidence
coverage
conformance
gate evaluation
release manifest
```

---

## §1119 — Evidence Bundle Digest

The release evidence bundle MUST have a stable digest.

---

## §1120 — Release Bundle Portability

An independent verifier SHOULD be able to validate a release bundle without access to the original development environment, except for explicitly declared external dependencies.

---

## §1121 — External Dependency Closure

If release verification requires external services, the release manifest MUST identify them.

A verifier unable to reach a required service MUST report:

```text
BLOCKED
```

or:

```text
UNKNOWN
```

not PASS.

---

## §1122 — Offline Verification

Where possible, release verification SHOULD support offline verification.

The release bundle SHOULD contain immutable evidence sufficient for offline validation.

---

## §1123 — Online Verification

Online verification MAY add fresh evidence.

Fresh online evidence creates a new evaluation; it does not mutate historical release evidence.

---

## §1124 — Release Audit

A release audit MUST be able to answer:

```text
Which protocol?
Which subject?
Which source?
Which artifact?
Which build?
Which fixtures?
Which executions?
Which verifier?
Which predicates?
Which coverage?
Which conformance?
Which release policy?
Which gate?
Which manifest?
Which signer?
```

---

## §1125 — Release Report

A human-readable release report SHOULD contain:

```text
release identity
artifact digest
source revision
protocol digest
fixture population digest
coverage digest
conformance digest
gate digest
manifest digest
signature status
```

---

## §1126 — Report Is Projection

The release report is not authoritative release identity.

The release manifest is authoritative.

---

## §1127 — Release Metadata

Metadata such as:

```text
author
description
release notes
homepage
documentation
```

MAY be included.

Identity-bearing metadata MUST be included in the manifest digest.

---

## §1128 — Release Notes

Release notes MAY describe changes.

They MUST NOT substitute for machine-readable release evidence.

---

## §1129 — Documentation Claims

Documentation MUST NOT state stronger verification claims than the release manifest supports.

---

## §1130 — Generated Documentation

Generated verification summaries SHOULD be derived from canonical release records.

---

## §1131 — Claim Scope Matrix

A release system SHOULD maintain:

| Claim | Required Evidence | Required Gate |
|---|---|---|
| Fixture PASS | fixture evidence | fixture predicate |
| Corpus coverage | coverage record | coverage predicates |
| Protocol conformance | conformance record | conformance predicates |
| Artifact tested | artifact/execution binding | artifact predicate |
| Release eligibility | gate evaluation | release gate |
| Released artifact | release manifest | release transition |

---

## §1132 — No Claim Promotion

The following promotion is prohibited without additional evidence:

```text
fixture PASS → protocol conformant
protocol conformant → release eligible
release eligible → released
```

Each transition requires its own contract.

---

## §1133 — Release Comparison

Two releases MUST be compared by immutable identity.

Version strings alone are insufficient.

---

## §1134 — Release Delta

A release delta SHOULD expose:

```text
source changes
artifact changes
fixture changes
policy changes
verifier changes
evidence changes
coverage changes
gate changes
```

---

## §1135 — Release Reproducibility

Given a release manifest and all declared reproducibility inputs, an independent system SHOULD be able to reproduce the declared artifact identity.

---

## §1136 — Reproducibility Failure

Failure to reproduce MUST be preserved as evidence.

It MUST NOT be hidden by retaining only the successful build.

---

## §1137 — Artifact Availability

A release manifest without access to the referenced artifact MAY remain historically valid.

Current release verification may become:

```text
BLOCKED
```

if artifact retrieval is required.

---

## §1138 — Manifest Availability

If the release manifest itself is unavailable or corrupt:

```text
release identity = UNRESOLVED
```

---

## §1139 — Manifest Digest Mismatch

If:

```text
declared_manifest_digest != computed_manifest_digest
```

the release manifest is invalid.

---

## §1140 — Artifact Digest Mismatch

If:

```text
declared_artifact_digest != computed_artifact_digest
```

the release artifact is invalid.

---

## §1141 — Conformance Digest Mismatch

If:

```text
manifest.conformance_digest != computed_conformance_digest
```

the release manifest is invalid or stale.

---

## §1142 — Coverage Digest Mismatch

If:

```text
manifest.coverage_digest != computed_coverage_digest
```

the release manifest is invalid or stale.

---

## §1143 — Gate Digest Mismatch

If:

```text
manifest.gate_digest != computed_gate_digest
```

the manifest cannot establish the claimed gate result.

---

## §1144 — Policy Digest Mismatch

If the current policy differs from the policy bound to a historical gate:

```text
historical gate ≠ current gate
```

A new evaluation is required.

---

## §1145 — Protocol Digest Mismatch

A release evaluated under one protocol identity MUST NOT be represented as evaluated under another protocol identity.

---

## §1146 — Release Manifest Freeze

Once a release is published:

```text
manifest
artifact
gate
claim
```

MUST be immutable.

Corrections create a new release identity.

---

## §1147 — Corrected Release

A corrected release SHOULD explicitly reference the superseded release.

Historical release evidence remains preserved.

---

## §1148 — Supersession

Supersession means:

```text
new release is current
old release remains historical
```

It does not mean the old release never existed.

---

## §1149 — Release Revocation Propagation

Revocation MUST propagate to dependent current claims.

Historical evidence remains intact.

---

## §1150 — Security Boundary

Cryptographic integrity, signatures, and hashes protect different properties.

```text
HASH → content integrity
SIGNATURE → signer authenticity
VERIFIER → semantic predicate evaluation
GATE → policy-based release eligibility
```

None may be substituted for another.

---

## §1151 — Trust Boundary

The release architecture has these trust boundaries:

```text
SOURCE
BUILD
ARTIFACT
EXECUTOR
VERIFIER
EVIDENCE STORE
COVERAGE EVALUATOR
GATE EVALUATOR
SIGNER
PUBLICATION TARGET
```

Each boundary MUST preserve identity.

---

## §1152 — No Trust Transitivity

Trust in one component does not automatically establish trust in another.

```text
trusted signer ≠ trusted artifact semantics
trusted CI ≠ trusted verifier semantics
trusted artifact ≠ conformant implementation
```

---

## §1153 — Independent Release Verification

A release-grade verifier SHOULD independently validate:

```text
manifest
artifact digest
gate digest
conformance digest
coverage digest
required evidence
signature
```

---

## §1154 — Minimal Release Verifier

A minimal offline release verifier SHOULD require only:

```text
canonicalization
hashing
schema validation
identity resolution
coverage/conformance validation
gate validation
manifest validation
signature verification where applicable
```

---

## §1155 — Release Verifier Independence

The release verifier SHOULD NOT depend on the mutable development workspace used to produce the release.

---

## §1156 — Release Verification Output

The independent verifier SHOULD produce:

```json
{
  "release_id": "release:...",
  "manifest": "PASS",
  "artifact": "PASS",
  "evidence": "PASS",
  "coverage": "PASS",
  "conformance": "PASS",
  "gate": "PASS",
  "signature": "PASS",
  "status": "VERIFIED"
}
```

The verifier's `VERIFIED` result is itself a new verification observation; it does not modify the release manifest.

---

## §1157 — Independent Verification Digest

Independent verification SHOULD produce its own evidence digest.

This permits multiple verifiers to coexist:

```text
release
  ├── verifier:A → evidence:A
  └── verifier:B → evidence:B
```

---

## §1158 — Verifier Disagreement

If independent verifiers disagree:

```text
verifier:A → PASS
verifier:B → FAIL
```

the release MUST NOT silently retain PASS.

The disagreement requires resolution or an explicit policy.

---

## §1159 — Release Gate Disagreement

Two independent gate evaluators producing different canonical results against identical inputs indicates:

```text
implementation defect
policy defect
canonicalization defect
specification ambiguity
```

and MUST block release until resolved.

---

## §1160 — Specification Ambiguity

If release eligibility cannot be determined uniquely from the normative specification:

```text
RELEASE = BLOCKED
```

---

## §1161 — Release Gate Determinism Test

The conformance corpus MUST contain a determinism fixture:

```text
same frozen candidate + same policy + same inputs → same gate digest
```

---

## §1162 — Gate Ordering Independence

Where gate predicates form a set:

```text
evaluate(P1,P2,P3)
```

MUST produce the same semantic result as:

```text
evaluate(P3,P1,P2)
```

unless ordering is explicitly semantic.

---

## §1163 — Release Identity Ordering

Manifest member ordering MUST NOT alter the manifest digest under canonical serialization.

---

## §1164 — Release Mutation Testing

The release subsystem MUST include mutations against:

```text
artifact digest
source digest
population digest
coverage digest
conformance digest
gate result
policy digest
manifest digest
signature
```

---

## §1165 — Critical Release Mutations

Every critical mutation MUST be detected.

A surviving critical release mutation is a release blocker.

---

## §1166 — Release Negative Corpus

The release corpus MUST contain at least:

```text
wrong artifact
wrong source
wrong population
wrong coverage
wrong conformance
wrong policy
stale evidence
failed gate
blocked gate
invalid manifest
invalid signature
```

---

## §1167 — Release Positive Corpus

The release corpus MUST contain a complete valid release candidate whose complete identity graph resolves.

---

## §1168 — Release Near-Miss Corpus

At least one fixture SHOULD mutate each critical release predicate individually.

---

## §1169 — Release Candidate Determinism

Identical frozen candidates MUST produce identical:

```text
gate digest
manifest digest
claim digest
```

where no intentionally nondeterministic metadata is included in canonical identity.

---

## §1170 — Non-Semantic Metadata

Fields such as:

```text
local display path
human-readable duration
logging verbosity
```

MAY be excluded from semantic digests if explicitly classified as non-semantic.

---

## §1171 — Semantic Metadata

Fields affecting:

```text
identity
scope
policy
result
artifact
protocol
population
```

MUST be identity-bearing.

---

## §1172 — Canonical Manifest

The canonical manifest MUST distinguish:

```text
semantic fields
provenance fields
transport fields
diagnostic fields
```

---

## §1173 — Release Evidence Retention

Release evidence MUST remain retrievable for the retention period defined by release policy.

---

## §1174 — Evidence Loss

If release-critical evidence is permanently lost:

```text
historical release record
```

MAY remain known, but current independent verification MAY become:

```text
NON_REPRODUCIBLE
```

The system MUST NOT reconstruct missing evidence from summaries.

---

## §1175 — Audit Reconstruction

An audit reconstruction MUST use preserved immutable records.

Logs alone are insufficient where stronger evidence records were required.

---

## §1176 — Release Audit Completeness

A release audit is complete only when all release-critical references resolve.

---

## §1177 — Release Audit Failure

An unresolved release-critical reference MUST be reported explicitly.

---

## §1178 — Release Gate Report

The gate report SHOULD contain:

```text
candidate identity
predicate table
predicate evidence
policy identity
result
blocking reasons
gate digest
```

---

## §1179 — Blocking Reason

Every blocked release MUST expose a machine-readable blocking reason.

Example:

```json
{
  "code": "REQUIRED_CI_EVIDENCE_MISSING",
  "predicate_id": "release:ci-required-v1"
}
```

---

## §1180 — Multiple Blocking Reasons

All observed release blockers SHOULD be preserved.

The evaluator MUST NOT stop after the first failure unless fail-fast is explicitly configured and the incomplete evaluation is marked accordingly.

---

## §1181 — Fail-Fast

A fail-fast gate MAY stop evaluation early.

If it does:

```text
evaluation_complete = false
```

MUST be recorded.

An incomplete fail-fast evaluation MUST NOT be represented as a complete predicate matrix.

---

## §1182 — Complete Gate Evaluation

A complete gate evaluation requires every required predicate to have an evaluated state or an explicit blocked state.

---

## §1183 — Gate Coverage

Gate predicates themselves form a declared population.

The gate evaluator SHOULD report:

```text
declared predicates
evaluated predicates
passed predicates
failed predicates
blocked predicates
unknown predicates
```

---

## §1184 — Gate Predicate Population

The gate predicate population MUST have an immutable digest.

---

## §1185 — Gate Predicate Coverage

No complete gate claim may be made when required gate predicates were omitted.

---

## §1186 — Release Gate Population

The release gate therefore has the same fundamental structure as fixture conformance:

```text
declared predicates + executed predicates + evidence + coverage + policy
```

---

## §1187 — Recursive Verification Boundary

The release gate MAY itself be verified by the same RFL-AE architecture.

This produces:

```text
protocol
  ↓
gate protocol
  ↓
gate fixtures
  ↓
gate evidence
  ↓
gate conformance
```

---

## §1188 — Bootstrap Requirement

The release verifier MUST have a bootstrap path independent enough to avoid circular validation.

---

## §1189 — Circular Trust

The following is prohibited as sole assurance:

```text
RFL-AE
  verifies RFL-AE
  using RFL-AE
```

without an independently trusted bootstrap layer.

---

## §1190 — Bootstrap Evidence

Bootstrap validation SHOULD be performed using:

```text
independent implementation
or
small trusted core
or
manually auditable canonical verifier
```

---

## §1191 — Release Bootstrap

The release package SHOULD include enough information for the minimal verifier to validate its own release manifest without executing the full system.

---

## §1192 — Trusted Computing Base

The release verifier SHOULD minimize its trusted computing base.

The more code required to validate a release, the larger the validation trust boundary.

---

## §1193 — Rust Implementation Guidance

A Rust implementation SHOULD:

```text
forbid unsafe where practical
use strong identifier types
use canonical serialization
use explicit error enums
avoid implicit string IDs
validate before construction
use Miri for relevant unsafe-adjacent/test paths
```

---

## §1194 — Type-Level Separation

Rust types SHOULD prevent accidental substitution:

```text
SubjectId
ArtifactId
FixtureId
EvidenceId
CoverageId
ClaimId
ReleaseId
Digest
```

must not all be interchangeable strings.

---

## §1195 — Release Gate API

A reference API is:

```text
evaluate_release(
    candidate,
    policy,
    coverage,
    conformance,
    evidence
) -> GateEvaluation
```

It SHOULD be pure.

---

## §1196 — Manifest API

Reference API:

```text
build_manifest(
    candidate,
    gate,
    conformance,
    coverage
) -> ReleaseManifest
```

The manifest builder MUST validate references before producing the manifest.

---

## §1197 — Release API

Reference release transition:

```text
release(
    frozen_candidate,
    passing_gate,
    valid_manifest
) -> Release
```

The function MUST reject:

```text
non-passing gate
stale candidate
invalid manifest
artifact mismatch
```

---

## §1198 — Release Transition Errors

Minimum error identities:

```text
E_CANDIDATE_NOT_FROZEN
E_GATE_NOT_PASS
E_GATE_STALE
E_GATE_INVALID
E_MANIFEST_INVALID
E_MANIFEST_DIGEST_MISMATCH
E_ARTIFACT_MISMATCH
E_CONFORMANCE_MISSING
E_COVERAGE_MISSING
E_REQUIRED_EVIDENCE_MISSING
E_CI_EVIDENCE_MISSING
E_MUTATION_REQUIREMENT_FAILED
E_POLICY_MISMATCH
E_RELEASE_IDENTITY_MISMATCH
```

---

## §1199 — Release Gate Golden Fixture

The canonical valid release candidate MUST have:

```text
expected_gate_status = PASS
expected_manifest_digest = ...
expected_release_identity = ...
```

---

## §1200 — Final Release Invariant

The complete RFL-AE verification chain is:

```text
                    SPECIFICATION
                          │
                          ▼
                     FROZEN CONTRACT
                          │
                          ▼
                    FIXTURE CORPUS
                          │
                          ▼
                       EXECUTION
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
                  RELEASE CANDIDATE
                          │
                          ▼
                     RELEASE FREEZE
                          │
                          ▼
                     RELEASE GATE
                          │
                          ▼
                   RELEASE MANIFEST
                          │
                          ▼
                     RELEASE CLAIM
                          │
                          ▼
                     PUBLICATION
```

The governing laws are:

```text
NO FROZEN CANDIDATE → NO RELEASE GATE
NO VALID GATE → NO RELEASE ELIGIBILITY
NO REQUIRED PREDICATE PASS → NO RELEASE
NO ARTIFACT IDENTITY → NO ARTIFACT RELEASE CLAIM
NO SOURCE/ARTIFACT BINDING → NO SOURCE-SPECIFIC ARTIFACT CLAIM
NO REQUIRED CI EVIDENCE → NO CI-BASED RELEASE CLAIM
NO COMPLETE COVERAGE → NO COMPLETE CONFORMANCE
NO CONFORMANCE → NO STRICT RELEASE
NO MANIFEST → NO IMMUTABLE RELEASE IDENTITY
NO MANIFEST DIGEST → NO CONTENT-ADDRESSABLE RELEASE IDENTITY
NO GATE EVIDENCE → NO RELEASE-GRADE GATE CLAIM
NO INDEPENDENT VERIFICATION → NO INDEPENDENT ASSURANCE CLAIM
NO CRITICAL MUTATION DETECTION → NO RELEASE
NO TRACEABLE CLAIM SCOPE → NO RELEASE CLAIM
NO RELEASE MANIFEST → NO FINAL RELEASE IDENTITY
```

The final architectural invariant is:

```text
EXECUTION ESTABLISHES WHAT RAN.
EVIDENCE ESTABLISHES WHAT WAS OBSERVED.
COVERAGE ESTABLISHES WHICH REQUIREMENTS HAVE EVIDENCE.
CONFORMANCE ESTABLISHES WHETHER THE REQUIRED POPULATION SATISFIES ITS CONTRACT.
THE RELEASE GATE ESTABLISHES WHETHER THE FROZEN CANDIDATE SATISFIES RELEASE POLICY.
THE RELEASE MANIFEST ESTABLISHES EXACTLY WHAT WAS RELEASED.

A VERSION NUMBER DOES NOT CREATE IDENTITY.
A GREEN CI BADGE DOES NOT CREATE EVIDENCE.
A TEST COUNT DOES NOT CREATE COVERAGE.
A CONFORMANCE RESULT DOES NOT CREATE RELEASE ELIGIBILITY.
A SIGNATURE DOES NOT CREATE SEMANTIC CORRECTNESS.
A RELEASE CLAIM CANNOT EXCEED ITS EVIDENCE SCOPE.

NO LAYER MAY PRETEND TO BE THE NEXT LAYER.
```

# End of RFL-AE Release Gate, Claim Derivation & Immutable Release Manifest Specification v1.4
