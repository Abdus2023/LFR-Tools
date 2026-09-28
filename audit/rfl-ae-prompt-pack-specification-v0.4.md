# RFL-AE PROMPT PACK SPECIFICATION v0.4

> **Provenance of this file.** Supplied by the repository owner; recorded verbatim. Source text arrived with several list-structured and YAML-shaped passages collapsed onto single lines; line breaks and indentation restored, with tree/chain/YAML structures reconstructed from their inline form. Declared as a transformation per §104 of the lineage: **no wording was added, removed, or reordered.**
>
> **Standing.** Begins at **§71**, continuing [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md) (§41–§70), which continues [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md) (§0–§40). The effective specification is now **§0–§105 across three documents**. See [`rfl-ae-prompt-packs-part15.md`](rfl-ae-prompt-packs-part15.md).
>
> **First directly implementable document in the lineage** — §72–§94 specify concrete schemas, and §101 defines a machine-checkable release gate.

---

## 71. PACK AS AN EXECUTABLE POLICY ARTIFACT

A Prompt Pack is a versioned, typed, canonical policy artifact.

It is not merely a prompt template.

```text
PACK SOURCE
   ↓
PARSE
   ↓
VALIDATE
   ↓
RESOLVE
   ↓
CANONICALIZE
   ↓
DIGEST
   ↓
COMPILED PACK
   ↓
EXECUTION
```

The normative object is the **compiled semantic pack**.

Natural-language prompt text is only one possible projection of that object.

---

## 72. MINIMUM PACK SCHEMA

Every pack MUST contain:

```yaml
api_version: rfl-ae.prompt-pack/v0.4
id: rfl-base
version: 0.4.0
kind: base

metadata:
  name: RFL-AE Base
  description: Evidence-first engineering agent policy

authority:
  mode: read_only

scope:
  operations:
    - inspect
    - analyze
    - verify

dependencies: []

rules: []

procedures: []

checks: []

stop_conditions: []

evidence_requirements: []

output_contract: {}

verification_contract: {}
```

The following fields are normative:

```text
api_version
id
version
kind
authority
scope
rules
dependencies
```

Optional metadata must never alter semantics.

---

## 73. PACK IDENTITY

Identity consists of:

```text
PackID
PackVersion
SourceDigest
CompiledDigest
```

`id` identifies the logical pack.

`version` identifies its declared version.

`SourceDigest` identifies the source representation.

`CompiledDigest` identifies the canonical semantic artifact.

Therefore:

```text
same ID ≠
same version ≠
same source ≠
same semantics
```

Do not use filenames as semantic identity.

---

## 74. PACK KINDS

Initial kinds:

```text
base
mode
domain
task
extension
```

Recommended composition:

```text
base
   +
mode
   +
domain
   +
task
   ↓
compiled pack
```

Examples:

```text
rfl-base
rfl-audit
rfl-rust
rfl-repository-audit
rfl-implementation
```

A `base` pack establishes invariant behavior.

A `task` pack supplies task-specific requirements.

---

## 75. AUTHORITY SCHEMA

Authority is capability-based.

```yaml
authority:
  mode: read_only

  capabilities:
    - repository.read
    - repository.inspect
    - process.execute
```

Possible capability families:

```text
repository.read
repository.write
repository.commit
repository.push

filesystem.read
filesystem.write

process.execute

network.read
network.write

external.side_effect
```

The pack can request capabilities.

It cannot grant capabilities that the execution environment has not authorized.

---

## 76. AUTHORITY RESOLUTION

Effective authority:

```text
EffectiveAuthority =
    RequestedAuthority
  ∩ GrantedAuthority
  ∩ EnvironmentAuthority
```

Example:

```text
PACK REQUESTS: repository.write
USER GRANTS:   repository.read
ENVIRONMENT:   repository.read
```

Result:

```text
repository.write = DENIED
repository.read  = ALLOWED
```

Never upgrade the result because the task would be easier.

---

## 77. SCOPE SCHEMA

Scope is structured:

```yaml
scope:
  subjects:
    - repository

  revisions:
    - exact_commit

  paths:
    include:
      - skills/**
    exclude:
      - "**/.git/**"

  operations:
    allow:
      - inspect
      - execute
    deny:
      - write
      - commit
      - push
```

Scope resolution must produce an explicit effective scope.

---

## 78. RULE SCHEMA

A rule has:

```yaml
- id: authority.no_mutation
  kind: authority
  strength: immutable

  predicate:
    operation: mutate_repository

  requirement:
    authority:
      capability: repository.write

  violation:
    status: blocked
```

Minimum rule fields:

```text
id
kind
strength
predicate
```

Rules MUST have stable IDs.

---

## 79. RULE STRENGTH

Supported strengths:

```text
IMMUTABLE
REQUIRED
OVERRIDABLE
DEFAULT
ADVISORY
```

Ordering:

```text
IMMUTABLE > REQUIRED > OVERRIDABLE > DEFAULT > ADVISORY
```

However:

```text
precedence ≠ authority
```

A `REQUIRED` rule cannot grant a capability.

---

## 80. RULE CONFLICTS

A conflict occurs when two effective rules with incompatible semantics apply to the same target.

Example:

```text
rule X: unsafe_code = forbidden
rule Y: unsafe_code = permitted
```

If no explicit resolution exists:

```text
STATUS = ERROR
```

Never resolve conflicts using:

- source ordering;
- filename ordering;
- parser order;
- whichever instruction appeared last;
- model preference.

---

## 81. RULE RESOLUTION ALGORITHM

Conceptually:

```text
resolve(packs):
    validate identities
    resolve dependencies
    detect cycles
    collect rules
    normalize rule IDs
    group rules by semantic target
    detect conflicts
    apply declared precedence
    reject unresolved conflicts
    intersect authority
    intersect scope
    validate resulting policy
    return effective policy
```

The algorithm must be deterministic.

---

## 82. DEPENDENCIES

Dependencies are explicit:

```yaml
dependencies:
  - id: rfl-base
    version: ">=0.4,<0.5"
```

The resolver MUST detect:

```text
missing dependency
incompatible version
duplicate identity
cyclic dependency
ambiguous dependency
```

A dependency failure is:

```text
BLOCKED
```

unless the failure is itself a malformed pack definition, in which case:

```text
ERROR
```

---

## 83. CYCLIC DEPENDENCY

Example:

```text
A → B
B → C
C → A
```

must never be silently accepted.

Result:

```text
ERROR CYCLIC_DEPENDENCY
```

The compiler must identify the cycle.

---

## 84. CANONICALIZATION

Canonicalization removes representational ambiguity.

These differences must not alter semantic identity:

```text
YAML key ordering
JSON key ordering
insignificant whitespace
formatting
serialization ordering
```

Canonicalization must define:

```text
key ordering
array ordering
string normalization
number representation
null semantics
omitted/default fields
encoding
```

Do not rely on the implementation language's default serializer.

---

## 85. DEFAULT VALUES

Defaults must be explicit.

Bad:

```text
missing authority field
```

followed by:

```text
compiler assumes read/write
```

Good:

```text
missing authority
    → schema/default resolution
    → explicit read_only
```

Every semantic default must appear in the canonical representation.

---

## 86. DIGEST

After canonicalization:

```text
CanonicalPack
   ↓
SHA-256
   ↓
CompiledPackDigest
```

Conceptually:

```text
CompiledPackDigest = SHA256(CanonicalCompiledPack)
```

The digest must be calculated over semantics, not incidental formatting.

---

## 87. SOURCE DIGEST

The source representation may also be identified:

```text
SourceDigest = SHA256(canonical source representation)
```

Keep:

```text
SourceDigest
CompiledPackDigest
```

separate.

A transformation can preserve semantics while changing source representation.

---

## 88. COMPILATION RESULT

Compilation should produce:

```yaml
compiled_pack:
  api_version: rfl-ae.compiled-pack/v0.4

  identity:
    id: rfl-audit
    version: 0.4.0
    source_digest: ...
    compiled_digest: ...

  dependencies: []

  authority: {}

  scope: {}

  rules: []

  procedures: []

  checks: []

  stop_conditions: []

  evidence_requirements: []

  output_contract: {}

  verification_contract: {}

  compiler:
    id: rfl-pack-compiler
    version: ...
```

The compiler itself becomes part of provenance.

---

## 89. PROCEDURES

Procedures define permitted execution logic.

Example:

```yaml
procedures:
  - id: inspect_repository
    steps:
      - establish_subject
      - establish_revision
      - establish_scope
      - execute_checks
      - collect_evidence
      - evaluate_coverage
```

A procedure MUST NOT silently introduce authority.

---

## 90. CHECK CONTRACT

A check must define:

```yaml
checks:
  - id: corpus.numbering

    predicate:
      type: unique_section_numbers
      subject:
        type: markdown_corpus

    procedure: audit_corpus

    expected:
      status: pass

    evidence:
      required:
        - subject_identity
        - execution_record
        - observation
        - coverage_record
```

This gives the runtime something concrete to execute.

---

## 91. EVIDENCE REQUIREMENTS

Evidence requirements should be declarative:

```yaml
evidence_requirements:
  - id: execution_identity
    requires:
      - execution_id
      - tool_id
      - tool_version

  - id: subject_binding
    requires:
      - subject_digest

  - id: coverage
    requires:
      - declared_scope
      - executed_scope
```

The pack therefore describes not only what to do but what must be retained to justify the result.

---

## 92. STOP CONDITIONS

Example:

```yaml
stop_conditions:
  - id: missing_subject_identity
    when:
      subject.identity: unknown
    result:
      status: blocked

  - id: unauthorized_mutation
    when:
      operation.requires: repository.write
      authority.allows: false
    result:
      status: blocked
```

Stop conditions take precedence over normal execution.

---

## 93. OUTPUT CONTRACT

Output semantics should be structured.

```yaml
output_contract:
  required_sections:
    - status
    - subject
    - scope
    - evidence
    - findings
    - unknowns

  prohibited_claims:
    - unsupported_completeness
    - unsupported_verification
    - unsupported_release
```

The natural-language report is generated from this contract.

---

## 94. VERIFICATION CONTRACT

Example:

```yaml
verification_contract:
  required:
    - subject_identity
    - check_identity
    - verifier_identity
    - execution_record
    - observation
    - coverage_record

  claim_rules:
    no_evidence: no_verified_claim
    partial_coverage: no_complete_claim
    unknown: not_pass
    error: not_fail
```

These rules are invariant.

---

## 95. COMPILED PROMPT PROJECTION

The compiler may generate an LLM-facing prompt:

```text
SYSTEM POLICY

You are operating under RFL-AE v0.4.

Effective authority: READ ONLY.

Effective scope: skills/**

Mandatory rules: ...

Required procedure: ...

Evidence requirements: ...

Forbidden operations: ...

Stop conditions: ...
```

But this text is not the authoritative source.

The authoritative artifact remains:

```text
CompiledPack
```

---

## 96. PROMPT PROJECTION INTEGRITY

The runtime should be able to identify:

```text
PromptProjectionDigest
CompiledPackDigest
```

The model receives a projection derived from the compiled pack.

If the projection is materially different from the compiled semantics:

```text
PROJECTION_MISMATCH
```

must be possible.

---

## 97. MODEL OUTPUT IS NOT AUTHORITY

The LLM cannot modify the effective policy by saying:

```text
"I authorize myself to write."
```

Likewise it cannot modify:

```text
authority
scope
verification requirements
evidence requirements
stop conditions
```

through generated text.

The execution engine remains authoritative.

---

## 98. MODEL OUTPUT IS NOT EVIDENCE

The model saying:

```text
"The test passed."
```

does not constitute evidence.

Evidence must originate from the execution/evidence layer.

Likewise:

```text
"I verified the repository."
```

is merely a claim made by the model until evidence supports it.

---

## 99. MODEL OUTPUT IS A PROPOSAL UNTIL COMMITTED BY THE RUNTIME

Separate:

```text
MODEL INTENT
MODEL PROPOSAL
TOOL REQUEST
AUTHORIZED EXECUTION
OBSERVATION
EVIDENCE
```

The model can propose:

```text
run cargo test
```

The runtime decides whether that operation is authorized.

The runtime executes it.

The runtime records the observation.

The evidence layer binds the result.

---

## 100. END-TO-END CONTRACT

The complete system becomes:

```text
                    USER TASK
                        │
                        ▼
                 TASK PARAMETERS
                        │
                        ▼
                  PACK RESOLUTION
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
          AUTHORITY             SCOPE
              │                   │
              └─────────┬─────────┘
                        ▼
                  COMPILED PACK
                        │
                        ▼
                PROMPT PROJECTION
                        │
                        ▼
                      MODEL
                        │
                        ▼
                 TOOL PROPOSAL
                        │
                        ▼
              AUTHORIZATION GATE
                        │
                 ┌──────┴──────┐
                 │             │
              DENIED       AUTHORIZED
                 │             │
              BLOCKED          ▼
                          EXECUTION
                              │
                              ▼
                         OBSERVATION
                              │
                              ▼
                            CHECK
                              │
                              ▼
                           RESULT
                              │
                              ▼
                          EVIDENCE
                              │
                              ▼
                          COVERAGE
                              │
                              ▼
                     CLAIM DERIVATION
                              │
                              ▼
                    VERIFICATION GATE
```

The critical boundary is:

```text
MODEL ≠ AUTHORITY
MODEL ≠ EXECUTION
MODEL ≠ EVIDENCE
MODEL ≠ VERIFICATION
```

The model is one component inside the verification system, not the system's source of truth.

---

## 101. MINIMUM v0.4 RELEASE GATE

A Prompt Pack implementation is not ready merely because the YAML parses.

The release gate MUST establish:

```text
G01  schema validation
G02  identity validation
G03  dependency resolution
G04  cycle detection
G05  rule conflict detection
G06  authority non-escalation
G07  scope non-expansion
G08  deterministic canonicalization
G09  deterministic digest
G10  positive fixtures
G11  negative fixtures
G12  injection fixtures
G13  evidence-schema validation
G14  status-transition validation
G15  compiled-pack reproducibility
```

Required invariant:

```text
NO GATE EXECUTION → NO VERIFIED GATE RESULT
```

---

## 102. FIRST IMPLEMENTATION

Do not implement the complete system at once.

Implement:

```text
PACK.yaml
   ↓
schema validator
   ↓
canonicalizer
   ↓
SHA-256 digest
   ↓
compiled-pack.json
```

Then add:

```text
dependency resolver
   ↓
rule resolver
   ↓
authority resolver
   ↓
scope resolver
```

Then:

```text
execution records
   ↓
evidence records
   ↓
coverage records
   ↓
verification gates
```

Then:

```text
LLM prompt projection
```

The model-facing layer should therefore be **late**, not foundational.

---

## 103. NON-GOALS FOR v0.4

Do NOT introduce yet:

```text
multi-agent orchestration
automatic self-modification
autonomous authority escalation
complex policy languages
probabilistic rule resolution
LLM-generated security policy
opaque plugin trust
implicit network permissions
automatic release
```

First establish deterministic semantics.

---

## 104. DESIGN PRINCIPLE

The architecture must make this progression possible:

```text
PROMPT
   ↓
POLICY
   ↓
COMPILED POLICY
   ↓
EXECUTION
   ↓
EVIDENCE
   ↓
VERIFICATION
```

and make this progression impossible:

```text
PROMPT
   ↓
MODEL SAYS PASS
   ↓
SYSTEM SAYS VERIFIED
```

The first is an engineering protocol.

The second is verification theater.

---

## 105. FINAL v0.4 INVARIANT

```text
A PROMPT MAY REQUEST AN ACTION.
A PACK MAY DEFINE A PROCEDURE.
AUTHORITY MUST AUTHORIZE THE ACTION.
THE RUNTIME MUST EXECUTE THE ACTION.
THE EXECUTION MUST PRODUCE AN OBSERVATION.
THE CHECK MUST EVALUATE THE OBSERVATION.
THE EVIDENCE MUST BIND THE RESULT.
THE COVERAGE MUST BOUND THE CLAIM.
THE GATE MUST DERIVE THE RELEASE DECISION.
NO LAYER MAY PRETEND TO BE THE NEXT LAYER.
```

Therefore:

```text
REQUEST ≠
AUTHORIZATION ≠
EXECUTION ≠
OBSERVATION ≠
EVIDENCE ≠
VERIFICATION ≠
RELEASE
```

This separation is the fundamental contract of RFL-AE Prompt Instructions.
