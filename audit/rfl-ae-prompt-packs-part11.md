# RFL-AE Prompt Packs — Part XI: Normative Specification

**Subject:** `RFL-PACK-V01` — the normative Prompt Pack Protocol
**Series:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md) · [`part5`](rfl-ae-skills-review-part5.md) · [`part6`](rfl-ae-skills-review-part6.md) · [`part7`](rfl-ae-skills-review-part7.md) · [`part8`](rfl-ae-skills-review-part8.md) · [`part9`](rfl-ae-prompt-instruction-packs.md) · [`part10`](rfl-ae-prompt-packs-part10.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## ⚠️ Category: NORMATIVE SPECIFICATION — with an unmet gate
>
> Three categories now exist in this series, and the progression is deliberate:
>
> ```text
> Parts I–VIII   AUDIT                     executed against a pinned snapshot; empirical
> Parts IX–X     PROPOSAL                  no empirical claims; forward-looking
> Part XI        NORMATIVE SPECIFICATION   binding requirements, not yet implemented
> ```
>
> This part sits at stage 2 of the six-stage progression it defines:
>
> ```text
> PROPOSAL  →  NORMATIVE SPECIFICATION  →  SCHEMA
>           →  REFERENCE IMPLEMENTATION  →  TEST FIXTURES  →  EXECUTION EVIDENCE
> ```
>
> **The distinction that matters: this document specifies a protocol and defines its release gate. It does not pass that gate.** There is no `packc`, no pack schema, no fixture, no compiled artifact, and no execution evidence. §170's fifteen `PACK-001 … PACK-015` conditions are **requirements the implementation must satisfy**, not results obtained.
>
> Read alone, §170 could be mistaken for a compliance report. It is a specification of what compliance would mean.
>
> **What *is* verifiable here**, and was checked: six of the fifteen gate conditions are evaluable against the *existing* skills toolchain, and it fails all six. See §170.1.

---

## 151. `RFL-PACK-V01` — Normative Protocol

Freeze the design into a protocol with an explicit distinction:

```text
PROPOSAL
   ↓
NORMATIVE SPECIFICATION
   ↓
SCHEMA
   ↓
REFERENCE IMPLEMENTATION
   ↓
TEST FIXTURES
   ↓
EXECUTION EVIDENCE
```

The central object should be:

```text
Pack
├── identity
├── version
├── classification
├── authority
├── scope
├── dependencies
├── rules
├── procedures
├── tools
├── claims
├── stop conditions
├── outputs
└── evidence requirements
```

### 151.1 Normative rule

The pack must be data that can be interpreted, not prose whose meaning depends on an LLM.

```text
PACK SOURCE
    │
    ▼
PACK IR
    │
    ├── validate
    ├── resolve
    ├── canonicalize
    └── hash
    │
    ▼
COMPILED PACK
```

The LLM should receive the compiled representation as an execution contract.

> **Note.** This constraint is the design-level response to the audit's single most frequently recurring finding: **a check that reports success when it did not run.** In every instance, the check's *semantics* were defined implicitly, by whatever the implementation happened to do rather than by a declaration:
>
> | Component | Semantics defined by | Receipt |
> |---|---|---|
> | `has_prov` | a substring test on the document | Part V §0.1 |
> | `neg()` | `rc != 0` | Part VII §0.2 |
> | `check_prov` | a filename plus an integer | Part V §50 |
> | `check_fences` | `text.count("```")` | Part I §12 |
> | `check_rust_balance` | character counts, not nesting | Part I §11 |
>
> §151.1 forbids prose-with-implied-meaning at the protocol level. It is the same correction applied one layer up from where the defects were found.

---

## 152. Pack Identity

Define identity before compilation.

```yaml
pack_id: RFL-AUDIT
version: 0.1.0
revision: 1
kind: MODE
```

Canonical identity:

```text
PackIdentity = SHA256(CanonicalPackDefinition)
```

Do not use filename, timestamp, branch name, or GitHub URL as identity. Those are provenance metadata.

> **Note.** "Do not use filename … as identity" has a direct precedent. Part V §50 established that provenance in the corpus identifies a source by `RFL-LEDGER-V01.md §12` — a filename plus an integer — and that this cannot express *which revision*. Part IV §0.1 showed the consequence: `renumber.py` emitted `<!-- source: SRC.md §2 -->` for a source with no §2, the string was well-formed, and the auditor certified it with `mapping errors=0`.
>
> §152 is the pack-layer application of Part V §50's remedy (`SourceIdentity { repository, commit, path, section, content_digest }`).

---

## 153. Pack Classes

Keep the initial type system small:

```text
BASE · MODE · DOMAIN · TASK · POLICY
```

Composition:

```text
BASE + MODE + DOMAIN + TASK + POLICY
```

But not every class can override every other class. For example:

```text
TASK    ✗ cannot override BASE / IMMUTABLE
DOMAIN  ✗ cannot grant authority
```

This prevents a task-level prompt from escalating its own permissions.

---

## 154. Authority Is Separate From Instructions

This is one of the most important protocol decisions.

Do not encode:

```text
"you are allowed to modify the repository"
```

as ordinary prose. Instead:

```yaml
authority:
  repository:
    read: true
    write: false

  git:
    commit: false
    push: false

  external_network:
    allowed: false
```

Then an implementation pack can explicitly request:

```yaml
authority:
  repository:
    read: true
    write: true

  git:
    commit: true
    push: false
```

But the effective authority must still be intersected with the actual authorization context:

```text
EffectiveAuthority = PackAuthority ∩ GrantedAuthority
```

Never:

```text
PackAuthority → grants itself authority
```

> **Note.** The `git.push: false` field above is a precise response to a measured gap. Part VII §80 established that `spec-turn-closeout`'s SKILL claims to cover *"commit, push, and verification that all of it actually happened"*, that its trap section **prescribes the exact command** for verifying a push (`git ls-remote origin refs/heads/<branch>`), and that `verify_closeout.py` cannot invoke `git` at all. The publication portion of the ritual is described, prescribed, and unimplemented.
>
> In the current repository, publication authority exists only as prose in a Markdown file. §154 is where it becomes a declared, intersectable field.

---

## 155. Scope Is a First-Class Object

This is the highest-value part of the protocol.

```yaml
scope:
  repository:
    owner: Abdus2023
    name: RFL-AE
    revision: 1090511...

  paths:
    include:
      - skills/**
    exclude:
      - .git/**

  document_classes:
    - CORPUS_SPEC

  checks:
    required:
      - RFL-MD-SECTION-001
      - RFL-MD-FENCE-001
      - RFL-MD-PROVENANCE-001
```

Then execution produces:

```text
DeclaredScope
      │
      ▼
ExecutedScope
      │
      ▼
CoverageRelation
```

with:

```text
EXECUTED ⊆ DECLARED
CLAIMED  ⊆ EXECUTED
```

The dangerous condition is:

```text
CLAIMED > EXECUTED
```

which must be impossible to classify as `VERIFIED`.

> **Note — this is the highest-value section in Part XI, and the audit says so independently.** Part VI §78's kernel axiom was cross-checked against every substantive defect found across Parts I–VIII. Seven defects reduced to three conjuncts:
>
> | Conjunct | Defects |
> |---|---|
> | `SCOPE_COVERED` | **4** — Parts III §21, IV §0.1, V §0.1, V §51 |
> | `DECLARED` | 2 — Parts V §55, VI §0.1 |
> | `VERIFIER_BOUND` | 1 — Part V §56 |
> | `EXECUTED`, `SUBJECT_BOUND` | 0 |
>
> **No component of the current toolchain models scope at all.** §155's `DeclaredScope → ExecutedScope → CoverageRelation` triple is the concrete form of the conjunct that four separate defects reduce to.

### 155.1 The three named checks all have audit receipts against them

Worth stating plainly, because §155's example names three checks as `required:` and all three are implemented by components the audit found defective:

| Check | Implemented by | Audit finding |
|---|---|---|
| `RFL-MD-SECTION-001` | `auditlib.sections()` | **Fence-blind** — a `## N.` line inside a code fence is counted as a section (Part II §12, §18) |
| `RFL-MD-FENCE-001` | `check_fences()` | `text.count("```")` — counts literal backticks in prose; not a fence parser (Part I §12) |
| `RFL-MD-PROVENANCE-001` | `check_provenance()` + `has_prov` | **Non-monotonic** — 10 of 33 documents fall outside the invariant entirely (Part V §0.1) |

So a pack declaring these three as required checks would, today, be declaring requirements that the corpus layer cannot faithfully establish. That is not an objection to §155 — it is the argument for sequencing §171's `PHASE 0` before the pack work, and it is why the `canonical Markdown representation` item belongs in the plan (see §171.1).

---

## 156. Rules Become Predicates

A rule should not merely be a sentence.

```yaml
id: RFL-SCOPE-001
class: IMMUTABLE

predicate:
  operator: subset
  left: claimed_scope
  right: executed_scope

on_violation:
  status: FAIL
```

This gives the protocol an eventual formal semantics. The same mechanism can represent:

```text
requires · subset · equals · contains · excludes · implies · exists · forall
```

That is enough for the first version. Do not build a general theorem prover yet.

---

## 157. Rule Evaluation

The evaluation result should be structured:

```text
RuleResult {
    rule_id
    status
    inputs
    observations
    evidence[]
}
```

Where:

```text
status ∈ { PASS, FAIL, ERROR, UNKNOWN, SKIPPED, BLOCKED }
```

This is stronger than a boolean.

---

## 158. UNKNOWN Must Be Semantically Different

For example, `Dependency unavailable` should not automatically mean `FAIL`, and definitely not `PASS`. Instead:

```text
dependency unavailable
        ↓
check = UNKNOWN
        ↓
required check unresolved
        ↓
gate = NOT_GATEABLE
```

Similarly:

```text
authorization missing
        ↓
BLOCKED
```

The distinction should survive serialization.

> **Note.** Both examples have measured counterexamples in the current toolchain.
>
> **Dependency unavailable:** Part VIII §0.1 ran the same checker at the same commit with and without `markdown` installed. The negative test reported `PASS` in both cases — with **6 findings** in the bare environment and **8** with `markdown`. Two of the eight checks (including the unclassified-fence check for the incident that created the strict-mode rule) simply did not run, and the harness could not tell the difference.
>
> **The distinction surviving serialization:** Part IV §37 established that `run_all.sh` collapses every stage outcome into a single `fail=1` bit, and Part IV §36 that uncaught exceptions exit 1 — the same code as a content finding. The five-valued status set has no representation today.

---

## 159. Claims Are Derived

A pack should never directly emit:

```text
verified: true
```

Instead:

```text
Evidence + Rule Results + Coverage + Authority + Subject Identity
        │
        ▼
     Claim
```

For example, `Claim: execution_verified` is derivable only when:

```text
execution.exists
subject.bound
verifier.bound
required_checks.complete
coverage.complete
no required check ERROR
no required check UNKNOWN
no required check SKIPPED
required predicates PASS
```

So `VERIFIED` becomes a **derived state**, not an input.

> **Note.** Part IX §124 made the same decision at the schema level — `"verified": true` is intentionally absent from `EvidenceRecord`. §159 generalizes it to the claim layer.
>
> The precedent is exact. Part V §0.1's five-state experiment produced a `PROV` cell reading `n/a` inside a run reported as `ALL FILES OK`: a *derived verdict* (`nothing wrong here`) standing in for a *missing observation*. Had the record stored the observation (`has_prov = false`, `comments = 0`) rather than the derived verdict, the bypass would have been visible on first reading.
>
> Note also the `verifier.bound` clause. Part V §56 searched the entire toolchain for commit, version, or hash binding and found **nothing**. §159 makes it a precondition for a claim that the current system cannot satisfy.

---

## 160. Pack Compilation

The compiler should produce two outputs:

```text
packc
├── compiled-pack.json
└── execution-prompt.txt
```

The JSON is authoritative. The text is an LLM-facing projection.

```text
compiled-pack.json       = normative machine artifact
execution-prompt.txt     = presentation/execution projection
```

If the text representation changes formatting without changing semantics, `PackSemanticDigest` should remain stable.

---

## 161. Canonicalization

Use canonical serialization:

```text
Pack Source
    ↓
Parse
    ↓
Normalize
    ↓
Sort unordered collections
    ↓
Canonical JSON
    ↓
Digest
```

Therefore different whitespace, YAML ordering, and Markdown formatting must not necessarily produce a different semantic identity. But a changed rule, scope, authority, or dependency must.

---

## 162. Dependency Identity

A pack dependency should be pinned:

```yaml
requires:
  - id: RFL-BASE
    version: 0.1.0
    digest: sha256:...
```

Never:

```yaml
requires:
  - RFL-BASE
```

because that makes historical execution ambiguous. The dependency graph becomes:

```text
RFL-TASK
   │
   ├── RFL-AUDIT
   │      │
   │      └── RFL-BASE
   │
   └── RFL-RUST
          │
          └── RFL-BASE
```

The compiler resolves the graph and detects: `cycle`, `missing dependency`, `version conflict`, `digest mismatch`.

---

## 163. Conflict Detection

Example:

```text
BASE:  repository.write = forbidden
TASK:  repository.write = required
```

The compiler must not silently choose one. Result: `CONFLICT`.

Likewise:

```text
Pack A:  network = forbidden
Pack B:  network = required
```

must produce `PACK-CONFLICT-001`, unless an explicitly defined precedence rule resolves it.

---

## 164. Instruction Provenance

Every compiled instruction should be traceable:

```text
CompiledRule
    ├── source_pack
    ├── source_rule
    ├── source_digest
    └── resolution_reason
```

Then the agent can produce:

```text
Rule RFL-EVIDENCE-001
source: RFL-BASE@0.1.0
digest: sha256:...
```

This makes instruction provenance inspectable.

---

## 165. Execution Binding

The execution record should contain:

```yaml
execution_id: sha256:...

pack:
  id: RFL-AUDIT
  version: 0.1.0
  digest: sha256:...

compiled_pack:
  digest: sha256:...

task:
  digest: sha256:...

subject:
  repository: Abdus2023/RFL-AE
  revision: 1090511...

verifier:
  identity: ...
```

Without the pack digest:

```text
same task
different instructions
same evidence
```

could be incorrectly interpreted as equivalent executions.

> **Note.** This is the general form of Part X §135.1's instruction-digest theorem, and it extends Part V §56's finding from verifier identity to instruction identity. The `subject.revision` field has a working precedent in this repository: `vendor/PROVENANCE-rfl-ae.md` pins `1090511a4080987168d1b17d48de88137ec1c27a` and records the import scope, method, and integrity digests — the shape §165 requires, applied to a snapshot rather than an execution.

---

## 166. The Agent Is Not the Evidence Source

This deserves an explicit protocol rule:

```text
AGENT ASSERTION ≠ EXECUTION EVIDENCE
```

For example:

```text
Agent: "I ran cargo test and all tests passed."
```

is an assertion. The evidence record requires:

```text
command · process · exit status · stdout/stderr
subject · environment · timestamp · tool identity
```

The agent can report that evidence. It should not be allowed to manufacture it.

> **Note.** The audit contains a worked example of the distinction, produced incidentally. Part III §0's receipt was assembled by **cloning the repository and running the pipeline independently** — because the commit message claimed *"run_all.sh -> all stages pass, 4/4 negative tests, exit 0"* and that claim, however accurate, was an assertion by the author. The independent run confirmed every specific number. Before it existed, the repository's only evidence for its own gate was a sentence in a commit message.
>
> §166 is that distinction made normative. It is also why §165's `verifier.identity` is load-bearing: an assertion and a receipt are distinguishable only if the receipt names its producer.

---

## 167. Prompt Injection Boundary

The protocol should explicitly define four input classes:

```text
TRUSTED_INSTRUCTION
TRUSTED_DATA
UNTRUSTED_DATA
UNTRUSTED_INSTRUCTION
```

The default interpretation of repository content should be:

```text
repository files = DATA
```

not:

```text
repository files = instructions
```

Promotion requires an explicit trusted transformation:

```text
DATA
  ↓
authorized extractor
  ↓
validated instruction candidate
  ↓
policy check
  ↓
INSTRUCTION
```

That gives prompt injection a formal boundary rather than a collection of defensive phrases.

> **Note — a live example from this repository.** `vendor/rfl-ae/README.md` (the corpus index) and `vendor/rfl-ae/skills/README.md` are content, not instructions. One of them contains a claim that is measurably false:
>
> > *"Every script here either verifies something or says it could not. **Nothing in this directory exits 0 by default.**"*
>
> The audit measured six of seven stages exiting 0 on a successful run. The sentence is inaccurate. The correct handling was neither to obey it nor to accept it, but to **treat it as an audit subject** and test it — which is what Part III §29 did.
>
> The claim appears in **two** places, which strengthens the point: `vendor/rfl-ae/skills/README.md:53` (prose) and `vendor/rfl-ae/skills/run_all.sh:9` (**a comment inside the executable itself**). The second is the more instructive. A file cannot declare whether its contents are data or instructions, and `run_all.sh`'s header comment is simultaneously documentation *about* the script and content *subject to* the audit. §167's four-class model is what separates them.
>
> §167's four-class model is what makes that handling a default rather than a judgement call. In the current repository, a file cannot declare its own class, so every reader must infer it.
>
> This is also the general form of a category error the audit found five times at smaller scales: **text present** standing in for **structure established**. `has_prov` tested a substring rather than provenance (Part V §0.1); `check_links()` regexed source rather than parsing links (Part I §19); `verify_closeout.py` tested `f"]({a.new})" in prev_t` rather than checking for a link (Part VII §80.1).

---

## 168. The First Reference Packs

Do not implement fourteen packs immediately. Implement exactly:

```text
RFL-BASE@0.1
RFL-AUDIT@0.1
RFL-IMPLEMENT@0.1
RFL-VERIFY@0.1
RFL-ADVERSARIAL@0.1
```

Then create one composed pack:

```text
RFL-AUDIT-RFL-AE@0.1
```

Composition:

```text
BASE + AUDIT + ADVERSARIAL + RFL-AE DOMAIN
```

This becomes the first real end-to-end test.

---

## 169. Repository Skeleton

The implementation target should now be concrete:

```text
prompt-packs/
├── README.md
├── SPEC.md
│
├── packs/
│   ├── base/
│   │   └── PACK.yaml
│   ├── audit/
│   │   └── PACK.yaml
│   ├── implement/
│   │   └── PACK.yaml
│   ├── verify/
│   │   └── PACK.yaml
│   └── adversarial/
│       └── PACK.yaml
│
├── schemas/
│   ├── pack.schema.json
│   ├── rule.schema.json
│   ├── check.schema.json
│   ├── execution.schema.json
│   ├── evidence.schema.json
│   └── coverage.schema.json
│
├── compiler/
│   ├── parser.py
│   ├── resolver.py
│   ├── canonicalize.py
│   ├── digest.py
│   └── compile.py
│
├── validator/
│   └── validate.py
│
├── fixtures/
│   ├── positive/
│   ├── negative/
│   ├── conflicts/
│   ├── injection/
│   └── determinism/
│
└── tests/
    ├── test_schema.py
    ├── test_resolution.py
    ├── test_authority.py
    ├── test_scope.py
    ├── test_claims.py
    └── test_determinism.py
```

> **Note.** The `fixtures/negative/` directory has a ready-made starting set, available without building anything. Part VIII §0.1 recorded that **four mutation operators already have surviving mutants** in the existing toolchain — five if the provenance deletion is counted separately:
>
> | Mutation | Survives | Receipt |
> |---|---|---|
> | corrupt source number | ✅ | Part IV §0.1 — `§3` → `§2`, certified |
> | change offset | ✅ | Part IV §0.1 — `mapping errors=0` |
> | delete all provenance | ✅ | Part V §0.1 — `n/a`, passes |
> | delete unclassified-fence detection | ✅ | Part VIII §0.1 — 6 of 8, `PASS` |
>
> These are real fixtures for real defects in the layer the packs will govern. §169's `fixtures/negative/` is where they belong.

---

## 170. Release Gate

`RFL-PACK-V01` should not be considered released merely because `packc` runs.

Minimum gate:

```text
PACK-001  schema valid
PACK-002  dependencies resolve
PACK-003  no conflicts
PACK-004  canonicalization deterministic
PACK-005  digest deterministic
PACK-006  authority cannot self-escalate
PACK-007  scope cannot be silently broadened
PACK-008  ERROR ≠ FAIL
PACK-009  UNKNOWN ≠ PASS
PACK-010  partial coverage cannot produce VERIFIED
PACK-011  hostile input cannot alter authority
PACK-012  execution binds pack digest
PACK-013  evidence binds subject digest
PACK-014  repeated compilation produces identical semantic artifact
PACK-015  negative fixtures are actually detected
```

And crucially:

```text
NO EXECUTION RECEIPT
        ↓
NO VERIFIED RELEASE
```

### 170.1 Six of the fifteen conditions are already evaluable — and all six fail

`PACK-001` through `PACK-007` and `PACK-012`–`PACK-013` concern the pack system itself, which does not exist; neither pass nor fail can be claimed. But **six conditions are properties of any verification toolchain**, and the existing `skills/` layer can be evaluated against them. It fails all six.

| Gate | Property | Current toolchain | Receipt |
|---|---|---|---|
| `PACK-008` | `ERROR ≠ FAIL` | ❌ **FAIL** | `audit_file.py` has **zero** `try`/`except`; an uncaught exception exits 1, identical to a content finding. `verify_closeout.py` raises `FileNotFoundError` traceback, exit 1 (Part IV §36; Part V N-6) |
| `PACK-009` | `UNKNOWN ≠ PASS` | ❌ **FAIL** | `n/a` renders inside a run reported as `ALL FILES OK`; 10 of 33 documents (Part V §0.1) |
| `PACK-010` | partial coverage cannot produce `VERIFIED` | ❌ **FAIL** | Corpus sweep runs 5 of 11 invariants and prints `ALL FILES OK`; single-document audit runs 11 over 1 document (Part III §21) |
| `PACK-011` | hostile input cannot alter authority | ❌ **FAIL** | `has_prov = "<!-- source: " in text` — document content determines whether the corpus *scope* of an invariant applies (Part V §0.1) |
| `PACK-014` | repeated compilation → identical artifact | ❌ **FAIL** | Same commit, same fixture, two environments: 6 findings vs 8, both `PASS`. Digest of the *output* would be equal in both (Part VIII §0.1) |
| `PACK-015` | negative fixtures are actually detected | ❌ **FAIL** | `neg()` gates on `rc != 0`; a `ModuleNotFoundError` satisfies it. The `8 planted defects` count is interpolated, never asserted (Part VII §0.2; Part VIII §103) |

**This is the most useful property of the gate: it is calibrated against observed failures rather than constructed from first principles.** Six of its fifteen conditions are precisely the conditions the audit demonstrated the existing toolchain violating. A gate whose items were invented would be unlikely to coincide with six independently-measured defects.

Two consequences:

1. **`PACK-008`–`PACK-011` and `PACK-014`–`PACK-015` are not pack-system requirements.** They are requirements on any verifier the pack system will call. Building `packc` while the underlying checks fail them means the pack protocol's evidence chains terminate in a checker whose result cannot distinguish `ERROR` from `FAIL`, whose scope is set by document content, and whose sensitivity varies with the environment.
2. **It reinforces §171's ordering, and raises the stakes on it.** `PHASE 0` is not merely tidier first — five of its seven items are precisely what would move `PACK-008`, `PACK-009`, `PACK-010`, `PACK-014`, and `PACK-015` from failing to passing.

> **On the last line of §170** — *"NO EXECUTION RECEIPT → NO VERIFIED RELEASE"* — the audit supplies a precedent for the rule and a counterexample to its current application. Part III §0's receipt exists because the repository's evidence for its own gate was a claim in a commit message. That receipt was produced by an independent party; it is currently the only third-party execution evidence for the audited revision, and it is stored at `audit/rfl-ae-runall-receipt.log` in this repository with the exact revision pinned in `vendor/PROVENANCE-rfl-ae.md`.

---

## 171. Correct Work Order

Given what is actually present in LFR-Tools, the overall sequence should be:

```text
PHASE 0 — LIVE DEFECTS
────────────────────────
C-1  renumber source number
C-2  fence awareness
C-3  remove has_prov gate
C-7  structured negative assertions
     temporary-directory isolation
     $PY consistency
     ERROR/FAIL separation

PHASE 1 — FOUNDATION
────────────────────────
RFL-PACK-V01
Pack IR
Rule IR
Authority
Scope
Claim model
Status model

PHASE 2 — SERIALIZATION
────────────────────────
pack.schema.json
rule.schema.json
execution.schema.json
evidence.schema.json
coverage.schema.json

PHASE 3 — COMPILER
────────────────────────
parser
resolver
canonicalizer
digest
compiled-pack artifact

PHASE 4 — VERIFICATION
────────────────────────
positive fixtures
negative fixtures
conflict fixtures
injection fixtures
determinism
mutation

PHASE 5 — INTEGRATION
────────────────────────
Pack → Skill
Pack → Check
Check → ExecutionRecord
ExecutionRecord → EvidenceRecord
Evidence → Gate

PHASE 6 — PUBLICATION
────────────────────────
CI
commit binding
artifact binding
remote SHA
release evidence
```

> **The correction from Part X is absorbed.** Part X §149 deferred the P0 fixes behind a protocol freeze; Part X §149.2 identified that inversion — the stated prerequisite ("the pack system will require reliable provenance and transformation identity") argued for fixing first, not deferring. `PHASE 0` now precedes `PHASE 1`, and `C-3` (`has_prov`), which Part X's P0 list omitted entirely, is present.
>
> Two independent orderings now agree. The consolidated audit's P0 (items 1–5) and Part XI's `PHASE 0` (seven items) overlap on all five, with `PHASE 0` adding `$PY` consistency and `ERROR`/`FAIL` separation. Convergence from separate routes is a reasonable signal that the defect set is complete and correctly ranked.

### 171.1 Two items have dropped out of the phase plan

`PHASE 0` is complete with respect to the *live defects* — it covers every item in the consolidated audit's P0 plus two more. But two work items from the preceding passes appear in no phase of §171.

**Item 1 — the canonical Markdown representation and canonical slug algorithm.** Present in Part X as `P0-D`, absent here.

This is not a minor omission. The consolidated audit identified the Markdown lexical layer as **the highest-leverage single component in the plan**, because five independent components parse Markdown by concatenating regexes over raw text, and the layer is a prerequisite for four separate findings:

| Component | Blind spot | Consequence |
|---|---|---|
| `renumber.py` | rewrites fenced bodies | **corrupts documents** (C-2) |
| `auditlib.sections()` | counts fenced `## N.` as sections | corrupts numbering, provenance, counts, anchors |
| `has_prov` | fence text satisfies the gate | **entire invariant bypassed** (C-3) |
| `splice.py` | narrow fence regex | dialect limitation |
| `verify_closeout.py` | substring tests | false positives |

And §155's example `scope.checks.required` names three checks — `RFL-MD-SECTION-001`, `RFL-MD-FENCE-001`, `RFL-MD-PROVENANCE-001` — **all three of which are implemented by components listed in that table** (§155.1). A pack declaring them required would be declaring requirements the corpus layer cannot faithfully establish.

**Item 2 — `renumber.py`'s gap-preservation regression test.** Present in the consolidated audit's P0 as item 5, and the closing recommendation of Parts IV, VII, and VIII. It appears in no phase.

This is the cheapest available action in the entire series. It is one file. It fails today. It would have caught C-1 on the day the code was written — the audit's most-repeated recommendation, and the one that keeps being displaced by larger architectural items.

**Recommended insertion:**

```text
PHASE 0 — LIVE DEFECTS
────────────────────────
C-1  renumber source number
C-2  fence awareness
C-3  remove has_prov gate
C-7  structured negative assertions
     temporary-directory isolation
     $PY consistency
     ERROR/FAIL separation
+    renumber.py gap-preservation regression test      ← cheapest, fails today
+    canonical Markdown representation                  ← prerequisite for 4 findings
+    canonical slug algorithm                            ← Part II §16
```

The second and third additions are larger than the other seven items combined, and they could legitimately be split into a `PHASE 0.5`. But they must appear in the plan, because `PHASE 1`'s `Scope` object and `PHASE 5`'s `Pack → Check` integration both depend on the check layer being sound.

---

## Closing note on this part's standing

| Part XI section | Standing |
|---|---|
| §151–§153 | **Normative** — protocol objects and class system |
| §154 Authority | **Normative**; `git.push: false` answers a measured gap (Part VII §80) |
| §155 Scope | **Normative — highest-value.** Addresses the conjunct that 4 of 7 substantive defects reduce to |
| §155.1 | **Verified** — all three named checks have audit receipts against their implementations |
| §156–§158 | Normative; both §158 examples have measured counterexamples |
| §159 Claims derived | **Normative**; `verifier.bound` is unsatisfiable today (Part V §56) |
| §160–§165 | Normative; §165 has a working precedent in `vendor/PROVENANCE-rfl-ae.md` |
| §166 Agent not evidence source | **Normative**; Part III §0 is a worked example |
| §167 Injection boundary | **Normative**; §167's example is a live, measurably false claim in `vendor/rfl-ae/skills/README.md` |
| §168–§169 | Implementation target |
| §170 Release gate | **Normative and UNMET** — see header. §170.1: 6 of 15 conditions evaluable, all 6 fail |
| §171 Work order | **Corrected** — absorbs Part X §149.2; §171.1 flags 2 dropped items |

**Three things are actionable without writing a line of the pack system:**

1. **§171.1 item 2** — write `renumber.py`'s gap-preservation regression test. One file, fails today, catches C-1.
2. **§171.1 item 1** — restore the canonical Markdown representation to the plan. It is absent from §171, and `PHASE 1`'s `Scope` object depends on it.
3. **§170.1** — treat `PACK-008`–`PACK-011` and `PACK-014`–`PACK-015` as `PHASE 0` acceptance criteria. Five of the seven `PHASE 0` items exist precisely to move them from failing to passing, and they are the pack system's own prerequisites.

The protocol is well-formed. Its release gate is correctly calibrated — six of its conditions were derived independently by the audit before the gate was written, and the current toolchain fails all six. What remains is the implementation, and §171 now orders it correctly.
