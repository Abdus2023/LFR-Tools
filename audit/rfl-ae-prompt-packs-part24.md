# RFL-AE Prompt Packs — Audit Part XXIV

**Subject:** `RFL-AE v1.3 — Coverage Evaluation & Conformance Aggregation Specification`, §801–§997 (197 sections)
**Recorded verbatim at:** `audit/rfl-ae-coverage-evaluation-conformance-aggregation-v1.3.md`
**Predecessor:** Part XXIII (v1.2, §656–§800)
**Date of analysis:** 2026-09-28
**Method:** all counts below were produced by executed measurement scripts against the recorded
verbatim file. Where a finding rests on a lexical test rather than a semantic one, that is stated in
the finding. Where two lists differ in spelling but not in intent, the mapping is shown rather than
the raw set difference.

---

## §1 — Scope, method, and verification of the recording

### §1.1 What was measured

| Measurement | Method | Result |
|---|---|---|
| Section range | regex over `^#{1,2}\s*§N` | **§801–§997** |
| Section count | count of matches | **197** |
| Contiguity | `nums == range(801, 998)` | **True** |
| Separators | count of `^---$` | **197** (one per section) |
| Continuity vs v1.2 | v1.2 ends §800 → v1.3 opens §801 | **adjacent, no gap** |
| Heading format | detected before any counting | `##` at section level, as in v1.2 |
| Restored blocks | block-index census, not first-block | **§801–§997, 60+ blocks** |
| Restored tables | Markdown table rows | **§879 (3 rows), §984 (3 rows)** |
| Union across thirteen documents | set union of all ranges | **§0–§997, one gap: §200** |

### §1.2 The restoration, declared

The supplied text arrived with every list, JSON object, pipeline, truth-table, tree, matrix and
diagram collapsed into inline single-backtick runs — the same arrival form as v1.0, v1.1 and v1.2.
The provenance header of the recorded file enumerates every restored passage by section. As always:
`---` separators and fenced code blocks with language tags are **my presentation additions**, declared
as such, applied to all thirteen recorded documents; no wording, number, identifier or ordering was
altered.

Two blocks needed structural judgement rather than mechanical unwrapping, and both are noted here
because a reader cannot check them against the inline form:

* **§879's cross-language matrix** arrived as a run of tokens (`Fixture Rust TypeScript Python
  Required ... F1 PASS PASS PASS yes ...`). It is recorded as a five-column Markdown table whose rows
  are exactly `F1/F2/F3` as supplied.
* **§984's traceability matrix** arrived the same way (`Claim Fixture Evidence Result Requirement
  ... C1 F1 E1 PASS REQUIRED ...`). Recorded as a five-column table.

In both cases the *content* is the supplied token order; only the row/column structure is mine.

### §1.3 The thirteen-document union

| Document | Range | Sections | Supplied |
|---|---|---|---|
| v0.1–v1.2 (twelve documents) | §1–§800 | 801 numbers | 1st–12th |
| **v1.3** | **§801–§997** | **197** | **13th** |
| **union** | **§0–§997** | **997 numbers, §200 unassigned** | |

The effective specification is now **§0–§997 across thirteen documents**. §200 is still the only
unassigned number; the §1–§28 double-occupancy rule is untouched by v1.3.

### §1.4 Three coincidences with the number 997, none of them a finding

The union's highest section number is now **§997**. That collides in appearance with two unrelated
things, and the collision is recorded here so that no future reader — including me — conflates them:

| # | What | Status |
|---|---|---|
| 1 | The **audited repository's** own "997 sections" claim (README vs corpus) | **CLOSED** since Part III/IV; cosmetic; never a live defect |
| 2 | v1.2 §788's illustrative `997 / 1000 passed` and §791's `99.7% passed` | a specification counter-example, not a corpus fact |
| 3 | v1.3 §924's `997` required fixtures and §926's `997/997` | the same illustrative number, third use |
| 4 | **The union's top section number is §997** | arithmetic: 801 + 196 |

Four distinct 997s. The audit's closed finding is (1) only. This is exactly the class of defect the
lineage itself legislates against — `same ID ≠ same version ≠ same source ≠ same semantics` (v0.4) —
and it would be ironic for the audit to commit it.

---

## §2 — Structural census of §801–§997

### §2.1 Inventories, as actually written

| § | Function | Count |
|---|---|---|
| 801 | normative objects declared | **9** |
| 801 | pipeline stages | **8** |
| 802 | fundamental distinctions | **9** |
| 802 | stated inequalities | **4** |
| 804 | discovery categories | **4** |
| 805 | population identity bindings | **7** |
| 807 | coverage-record fields | **7** |
| 809 | coverage states | **10** |
| 827 | requirement classes | **3** |
| 832 | fixture result values | **6** |
| 867 | required accounting counts | **13** |
| 881 | partitionable components | **9** |
| 892 | coverage-invalidation triggers | **6** |
| 911 | release-rejection conditions | **8** |
| 936 | critical aggregation mutations | **9** |
| 993 | required conformance-fixture categories | **13** |
| 994 | mandatory-detection mutations | **9** |
| 996 | release-record digests | **9** |
| 997 | governing laws | **10** |
| 997 | central-invariant lines | **10** |
| 997 | architecture-diagram lines | **31** |

### §2.2 Shape of the document

v1.3 is the **aggregation and closure document**. It opens by naming nine objects and one pipeline
(§801), fixes the distinction chain that the whole document exists to protect (§802), then spends
§803–§808 on the population and the coverage record, §809–§811 on the coverage states and the
Covered/Uncovered predicates, §812–§826 on evidence selection and matching, §827–§839 on requirement
classes and the six-valued fixture result, §840–§847 on requirement evaluation and the coverage and
claim digests, §848–§852 on determinism and policy, §853–§875 on failure visibility, missingness,
negative fixtures, retries and flakiness, §876–§884 on replay, cross-language and partitioned
coverage, §885–§896 on freeze, staleness and deltas, §897–§912 on reports, gates and CI authority,
§913–§926 on purity, propagation and explanation, §927–§937 on the two closing predicates and
mutation of the aggregator, §938–§950 on reconciliation and late evidence, §951–§958 on store,
import/export and report integrity, §959–§966 on evaluator identity and ambient state, §967–§976 on
monotonicity and policy change, §977–§990 on revocation propagation and claim binding, §991–§996 on
independent re-evaluation and the release record, and §997 on the architecture and the laws.

---

## §3 — Headline A: the Claim is completed — except for its scope

### F-24.1 — PROVED — the claim acquires an identifier, a type name, a derivation rule, a digest and six bindings; the carried `ClaimId` finding is closed after five documents

The finding carried since Part XX (v0.9) was: *"`Claim` has no type or identifier — `ClaimId`,
`claim_id` and `pub struct Claim` are all 0 in v1.0 and v1.1 despite the lineage deriving claims."*
Measured across the recorded documents:

| Document | `claim_id` | `ClaimId` | `pub struct Claim` | `ConformanceClaim` |
|---|---|---|---|---|
| v1.0 | 0 | 0 | 0 | 0 |
| v1.1 | 0 | 0 | 0 | 0 |
| v1.2 | 0 | 0 | 0 | **1** (named in §657's chain only) |
| **v1.3** | **1** | 0 | 0 | **3** |

§843 gives the claim a canonical record with `"claim_id": "claim:..."`; §801 lists
`ConformanceClaim` as one of nine normative objects; §965's evaluation contract ends
`Conformance( CoverageRecord, Policy ) -> ConformanceClaim`; §845 fixes its derivation
(`Population + Coverage + Evidence + AggregationPolicy`); §846 fixes its digest bindings; §987–§990
add four further binding requirements.

**Classification: PROVED.** The claim is now a first-class identified object with a derivation rule
and a digest — the strongest single closure in v1.3. The type **name** existed in v1.2 §657; what
v1.3 adds is the definition.

**Residual:** `ClaimId` (CamelCase, the type-system spelling) is still 0 and `pub struct Claim` is
still 0 — but v1.3 is a semantics document, not a types document, so this residual belongs to the
types lineage (v0.7/v0.9) and not to v1.3. It is recorded, not charged.

### F-24.2 — PROVED — §844 requires a claim to state its scope; §843's canonical claim record has no scope member

| § | Statement |
|---|---|
| 843 | canonical claim JSON: `claim_id`, `subject_id`, `protocol_version`, `population_digest`, `coverage_digest`, `policy_id`, `status` — **7 keys** |
| 844 | *"A claim MUST state its scope."* — examples `FIXTURE`, `FIXTURE_SET`, `PROTOCOL`, `IMPLEMENTATION`, `RELEASE` |

Measured: `"scope"` as a key in §843's JSON = **absent**. The field is required by §844 and missing
from the one record §843 declares canonical, while §843's record is the object §846 digests and §965
returns.

This is the same defect shape as v1.2 §749 (prose requires one thing, the formal statement says
another) and it has the same consequence: an implementer who generates the claim record from §843
produces a claim that cannot satisfy §844, and a checker written from §844 rejects every claim §843
produces. Concretely, §844's own example matters — *"A fixture claim MUST NOT be silently promoted to
an implementation claim"* is enforceable **only** if the scope is a member of the claim, because
promotion is otherwise indistinguishable from a correct implementation claim.

**Fix:** add `"scope": "IMPLEMENTATION"` (or the applicable member) to §843's record and to §846's
digest bindings.

### F-24.3 — PROVED — the claim's `status` value comes from a vocabulary that is never enumerated

§843's record carries `"status": "CONFORMANT"`. Measured: `CONFORMANT` occurs **7 times** in v1.3, in
six distinct roles —

| Where | Role of `CONFORMANT` |
|---|---|
| §802 | one of nine fundamental **distinctions** |
| §802 | the right-hand side of `COVERED ≠ CONFORMANT` |
| §843 | a **field value** in the canonical claim record |
| §924 | the `result:` line of an explanatory example |
| §929 | the left-hand side of `CONFORMANT ≠ RELEASED` |
| §995 | the token the evaluator *MUST NOT report* when a critical mutation survives |

— and **no section lists the permitted values of a claim status**. `NON_CONFORMANT` = 0. The domain
is *derivable* from three sections taken together (§850's `FAIL → FAIL`, §823's `CONFORMANCE =
UNKNOWN | BLOCKED`, §995's prohibition) but it is never *stated*, so the canonical record's `status`
field has no declared type.

This is the third instance of the same defect and the second in the claim/evidence layer:

| Document | § | Undeclared vocabulary used as a field value |
|---|---|---|
| v1.2 | 673 | observation status — `"status": "OBSERVED"` (token also used as a §728 trust level) |
| v1.2 | 801–§? | — |
| **v1.3** | **843** | **claim status — `"status": "CONFORMANT"`** |

**Fix:** enumerate the claim status set in §843 (`CONFORMANT`, `NON_CONFORMANT`, `BLOCKED`,
`UNKNOWN`, and `STALE` if §891's state applies to claims) and bind it to §850's rules by name.

---

## §4 — Headline B: the coverage derivation becomes general — and the state assignment does not

### F-24.4 — PROVED — v1.3 supplies the general coverage derivation that the lineage has lacked since v0.9

The carried finding, in its final form after Part XXIII, was: *"coverage status is still underived in
general; v1.1 §607's worked example remains the only place a `missing`/`unexpected`/`status` triple is
produced from sets."* v1.3 replaces the example with a rule at four levels:

| Level | § | Statement | Form |
|---|---|---|---|
| fixture | 810 | `Covered(f) = Declared(f) ∧ RequiredEvidencePresent(f) ∧ EvidenceValid(f) ∧ RequiredChecksEvaluated(f)` | 4-conjunct predicate |
| population | 841 | `∀ f ∈ RequiredPopulation: Covered(f)` | universal quantifier |
| conformance | 842 | `∀ f ∈ RequiredPopulation: RequirementSatisfied(f)` where `RequirementSatisfied(f) = Covered(f) ∧ Result(f) = PASS` (§840) | universal quantifier over a conjoined predicate |
| subsystem | 927 | `Conformant(S,P,E,R)` — 6 conjuncts: `SubjectValid ∧ PopulationValid ∧ EvidenceValid ∧ CoverageComplete ∧ RequiredResultsPass ∧ CriticalRequirementsSatisfied` | 6-conjunct predicate |

and it adds the three arithmetic invariants that make the sets *countable*:

| § | Invariant |
|---|---|
| 868 | `declared_count = number of unique declared fixture IDs`; duplicates do not increase it |
| 869 | `sum(state_counts) = declared_count` — each declared fixture in exactly one coverage state |
| 870 | evidence count counts **unique evidence identities** — not log lines, stdout occurrences or repeated database rows |
| 871 | execution count counts **unique execution identities**; a retry with a new identity is a distinct execution |

Reading §810 against §842 gives the derivation the lineage never had:

```
   evidence exists  ─────────────────────────────►  COVERED      (§810)
        │                                              │
        │  ∧ all required checks = PASS                │  §842
        ▼                                              ▼
   CONFORMANT (for that fixture)              COVERAGE ≠ CONFORMANCE
        │                                     §842: "coverage = evidence exists
        │  ∧ artifact ∧ mutation ∧ replay            conformance = required evidence
        │    ∧ CI ∧ gate                              establishes required result"
        ▼
   RELEASE-ELIGIBLE                           (§928, 6 terms)
```

This closes a finding open since Part XX (v0.9 dropped v0.8 §305's algorithm), partially closed by
v1.1 §607 by example, and listed in Part XXIII as *"unchanged"*. **Classification: PROVED — closed by
rule, not by example.** The three arithmetic invariants are the part that makes the closure checkable
rather than declarative, and §870's prohibition is the first statement in the lineage that a count
must count *identities*.

### F-24.5 — PROVED — the partition invariant of §869 has no state-assignment function behind it

§869 declares:

```
Every declared fixture MUST belong to exactly one current coverage state at evaluation time.
sum(state_counts) = declared_count
unless the report explicitly defines orthogonal dimensions.
```

§809 declares ten coverage states:

```
NOT_DECLARED  DECLARED  SCHEDULED  EXECUTED  EVIDENCED  COVERED
UNCOVERED     PARTIAL   BLOCKED    INVALID
```

Measured: `precedence` = 0, `state =` = 0, `highest state` = 0, `determine the state` = 0,
`state selection` = 0. No section assigns a fixture to a state; §810 gives a *necessary condition*
for `COVERED`, §811 a sufficient condition for `UNCOVERED`, §812 for `PARTIAL` — and nothing decides
between them.

The two readings are both textually supported and give different arithmetic:

| Reading | A declared, executed, evidenced, passing fixture | `sum(state_counts)` |
|---|---|---|
| **Ladder** (§809's ordering reads as accumulation) | in `EXECUTED` *and* `EVIDENCED` *and* `COVERED` | **> declared_count** |
| **Partition** (§869's stated invariant) | in `COVERED` only | **= declared_count** |

So §869 is either false as written or true only under an assignment rule that the document does not
state — and the escape clause (*"unless the report explicitly defines orthogonal dimensions"*) lets an
implementation declare the states orthogonal and escape the invariant entirely, without any
constraint on when that is legitimate.

This is the **same defect shape as v1.2 §790's `Covered` column** (a partition whose membership rule
is absent), one layer up, and it is the second time the audit has found it. The difference is that
v1.3 *states the invariant* — which makes the omission checkable. That is progress: the defect moved
from "undefined term" to "stated invariant with an undefined assignment function".

**Fix:** add a state-assignment table to §809 — for each combination of (declared?, scheduled?,
executed?, evidenced?, evidence valid?, required checks evaluated?, required checks passed?), the
resulting state — and restrict §869's escape clause to reports that declare the dimensions **and**
their membership rules.

### F-24.6 — PROVED — six of §801's nine declared objects are never used again in the document

§801: *"RFL-AE v1.3 defines the normative semantics of:"* then nine objects. Measured usage outside
§801:

| Object | Total | Outside §801 | Verdict |
|---|---|---|---|
| `CoverageRecord` | 4 | 3 | used (§913, §965, §992) |
| `ConformanceClaim` | 3 | 2 | used (§965, §992) |
| `AggregationPolicy` | 2 | 1 | used (§845) |
| `CoveragePopulation` | 1 | **0** | declared only |
| `CoverageEntry` | 1 | **0** | declared only |
| `EvidenceSelection` | 1 | **0** | declared only |
| `RequirementEvaluation` | 1 | **0** | declared only |
| `ConformanceEvaluation` | 1 | **0** | declared only |
| `CoverageDigest` | 1 | **0** | declared only |

**Six of nine.** `CoveragePopulation` is not used even where the document manifestly means it: §965's
contract takes `Population`, and §803's record carries `population_id`. `CoverageEntry` is never used
where §808 defines "coverage entry". `CoverageDigest` is never used where §847 defines
`coverage_digest` (7 occurrences).

This is the v0.9 `ResultStatus` pattern (declared, 0 uses) generalised from one type to six objects in
one section. **Classification: PROVED** — the measurement is lexical, but the claim ("v1.3 defines
their normative semantics") is semantic, and a document cannot define the semantics of a name it
never mentions again.

**Fix:** either use the names in the sections that define the concepts, or move the six to a
"terminology" list that does not claim normative definition.

### F-24.7 — PROVED — §801's object names and §965's contract names are different names, and `CoverageStatus` is silently dropped

| Concept | §801 / normative prose | §965 contract | Field name |
|---|---|---|---|
| the population | `CoveragePopulation` | `Population,` | `population_id` |
| the policy | `AggregationPolicy` | `Policy` | `policy_id` |
| the coverage type | `CoverageDigest` | — | `coverage_digest` |

and, measured across the corpus:

| Document | `CoverageStatus` | `coverage_status` |
|---|---|---|
| v0.7 | 0 | 1 |
| v0.8 §277 | **1** (declared) | 0 |
| v0.9 | **1** | 0 |
| v1.3 | **0** | **1** |

v0.8 §277 declared `pub enum CoverageStatus`; v1.3 uses the field `coverage_status` with string
values from §809 and never the type name. That is the **fourth silently dropped type name** in the
lineage, after v0.5 §116's execution-state vocabulary, `claimed`, `NONE`, and `CompiledPackDigest`:

| Dropped name | Last appeared | Replaced by |
|---|---|---|
| v0.5 §116 execution states | v0.5 | nothing (v0.8 §278 introduced a new set) |
| `claimed` | v0.6 | `claimed` does not reappear until v1.2 §749's `CLAIMED_SCOPE` |
| `NONE` | v0.5 | `UNCOVERED` / explicit absence tokens (v1.2 §736) |
| `CompiledPackDigest` | v0.8 | `compiled_digest` (also 0 since v0.9) |
| **`CoverageStatus`** | **v0.9** | **`coverage_status` + §809's ten states** |

**Classification: PROVED.** Each drop is individually defensible; collectively they mean a reader
following the lineage's type names cannot compile across documents.

---

## §5 — Headline C: the two mutation lists do not agree, and the shorter one is the mandatory one

### F-24.8 — PROVED — §936's nine critical aggregation mutations and §994's nine mandatory-detection mutations differ by two mutations in each direction, and §994 states a *minimum*

| § | Statement | Count |
|---|---|---|
| 936 | *"Critical mutations include:"* — then nine | 9 |
| 937 | *"Every critical aggregation mutation MUST be detected. A surviving mutation blocks release."* | — |
| 994 | *"At minimum, the mutation suite MUST detect:"* — then nine | 9 |
| 995 | *"The evaluator MUST NOT report `CONFORMANT` when a required critical mutation survives."* | — |
| 861 | *"A surviving critical mutation blocks the applicable release gate."* | — |

Because the two lists are spelled differently for the same intent, the raw set difference is
misleading. Mapping them by intent:

| # | §936 (critical) | §994 (mandatory minimum) | Match |
|---|---|---|---|
| 1 | ignore required fixture | drop-required-fixture | same intent |
| 2 | convert FAIL → PASS | force-pass | same intent |
| 3 | convert UNKNOWN → PASS | ignore-unknown | same intent |
| 4 | **convert SKIPPED → PASS** | — | **no counterpart in §994** |
| 5 | ignore stale evidence | accept-stale | same intent |
| 6 | accept wrong subject | accept-wrong-subject | identical |
| 7 | accept wrong population | accept-wrong-population | identical |
| 8 | ignore policy digest | ignore-policy | same intent |
| 9 | **drop evidence identity** | — | **no counterpart in §994** |
| — | — | ignore-failure | **no counterpart in §936** |
| — | — | ignore-blocked | **no counterpart in §936** |

**Two §936-critical mutations have no home in §994**: `convert SKIPPED → PASS` and `drop evidence
identity`. A suite written to satisfy §994's stated minimum — the only list the document calls a
minimum, and the one a reader will implement — can therefore satisfy §994 exactly and still leave two
mutations undetected that §937 makes release-blocking. Note also which two they are:

* `convert SKIPPED → PASS` is precisely the defect the lineage has carried since **Part XVII**, where
  v0.6 §168 made `SKIPPED` unreachable — the audit has now recorded three separate statements of that
  concern (v0.6, v1.1 §601, v1.3 §917) and the mutation that would detect it is absent from the
  mandatory list.
* `drop evidence identity` is precisely Part XXIII's F-1/F-2/F-3 class (a record that loses track of
  which vocabulary its status belongs to).

The inverse direction is milder: §994's `ignore-failure` and `ignore-blocked` are covered in intent by
§853/§919/§918, so their absence from §936 is a wording gap rather than a coverage gap.

**Classification: PROVED.** Both lists were read verbatim; the mapping is a judgement, the asymmetry
is arithmetic. **Fix:** make §994 a superset of §936 — twelve mutations, not nine — and state in §936
that it is the definition and §994 the fixture projection.

---

## §6 — A successor document resolves a Part XXIII finding

### F-24.9 — PROVED — §810 and §842 resolve the v1.2 §790 `Covered` contradiction in the direction the audit identified

Part XXIII **F-15** recorded: *"§790's `Covered` column is nowhere defined and its worked rows
contradict v1.1 §607's semantics"* — specifically, v1.2's F2 row marked a fixture that executed with
valid evidence and a failing result as `Covered = no`.

v1.3 §810 defines `Covered(f)` as `Declared ∧ RequiredEvidencePresent ∧ EvidenceValid ∧
RequiredChecksEvaluated` — **result is not a conjunct** — and §842 states the two relations apart in
one block:

```
coverage    = evidence exists
conformance = required evidence establishes required result
```

Applied to v1.2's F2 row (`executed = yes`, `evidence = yes`, `digest valid = yes`, `result = FAIL`):
`Covered = true`, conformant = false. That is the audit's reading, arrived at independently in the
next document.

**Classification: PROVED, and this is the first instance in this audit of a successor document
confirming the audit's correction of its predecessor.** The mechanism is worth naming, because it is
the mechanism the whole lineage is built on: the contradiction was found by translating two worked
examples into set membership, and it was resolved by a later document translating the same two
concepts into an algebraic definition. Neither operation required running anything.

**Consequence for the record:** Part XXIII F-15 is **CLOSED — corrective, superseded by v1.3 §810 +
§842.** It should not be counted as a live defect in any future severity table. v1.2's own text still
says what it says; the specification no longer does.

---

## §7 — Vocabulary ledger

### §7.1 Stabilized

| Vocabulary | § | Members | Prior statement | Verdict |
|---|---|---|---|---|
| check status | v1.1 §601, v1.2 §677, v1.2 §788 | `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` | 3rd restatement | stable |
| **replay outcome** | v1.2 §701, **v1.3 §877** | `IDENTICAL SEMANTICALLY_EQUIVALENT DIFFERENT NON_REPRODUCIBLE BLOCKED UNKNOWN` | **6 of 6 verbatim identical** | **stable** |
| fixture result | v1.1 §601, v1.2 §677, **v1.3 §832** | the same six | 4th restatement | stable |
| aggregation mapping | — | §850's six rules | new | declared, policy-scoped |

Measured: v1.2 §701's block and v1.3 §877's block are **identical as sets and as sequences**. That is
the **second vocabulary in the lineage to be reproduced verbatim across documents**, after the
check-status six. Both are six-element sets drawn from a pool that includes `BLOCKED` and `UNKNOWN` —
see §7.2.

### §7.2 Collided

| Token | v1.2 senses | v1.3 senses | Total distinct senses |
|---|---|---|---|
| `BLOCKED` | check status (§677), bundle closure (§722), replay outcome (§701) | coverage state (§809), conformance outcome (§823), policy rule outcome (§850) | **6** |
| `UNKNOWN` | execution status (§669), check status (§677), replay outcome (§701), evidence status (§717), absence token (§736) | conformance outcome (§823), propagation (§916) | **7** |
| `PARTIAL` | evidence status (§717), bundle closure (§722) | coverage state (§809), per-fixture condition (§812) | **4** |
| `INVALID` | evidence status (§717) | coverage state (§809) | **2** |
| `SKIPPED` | check status (§677), aggregation (§788) | fixture result (§832), policy rule outcome (§850), propagation (§917) | **5** |

The count grew because v1.3 adds a **tenth-vocabulary** — coverage states — drawn from the same token
pool as the evidence and check vocabularies, and **three of §809's ten states (`PARTIAL`, `BLOCKED`,
`INVALID`) are already valued in the immediately preceding document's evidence layer.** Part XXIII
F-1/F-2/F-3 proposed a namespacing rule before schema generation; v1.3 raises the collision count
instead, from five senses of `UNKNOWN` to seven.

### §7.3 Partially answered, and how

| Part XXIII finding | v1.3 response | Verdict |
|---|---|---|
| F-3: bare `"status"` key carries five vocabularies | §808 names its fields: `"result"` (6-valued) beside `"coverage_status"` (10-valued), and §809's closing sentence states the separation | **PARTIALLY ANSWERED** — field-level naming at the coverage entry; §843 still uses a bare `"status"` for a fourth vocabulary |
| F-2: `UNKNOWN` carries five senses | §916 forbids `UNKNOWN → PASS` through aggregation; the token is not namespaced | **NOT ANSWERED** |
| F-15: `Covered` undefined | §810 defines it | **CLOSED** (§6) |
| F-11: corpus-digest naming drift | v1.3 §803 uses `fixture_corpus_digest` (v1.2 §723's spelling), not v1.1 §575's `corpus_digest` | **SETTLED 2:1** in favour of v1.2's spelling |
| F-10: two manifests unlinked | see §8 | **NOT ANSWERED** |
| F-6/F-7: §749's inverted scope relation | not restated; §820 gives the correct direction in prose | **NOT ANSWERED** (§10, F-24.12) |

### §7.4 New pseudo-evidence refusals

v1.2 refused six classes of claim-shaped input (`{"verified": true}` §681, requests §667, runner
self-report §697, self-certification §698, producer-as-validator §761, import-as-trust §764). v1.3 adds
seven:

| § | Refused | Phrasing |
|---|---|---|
| 902 | producer-provided `{"coverage": 1.0}` | *"is not authoritative coverage"* |
| 903 | CI summary output | MAY be diagnostic; MUST NOT become release-grade without six bindings |
| 904 | `CI claimed PASS` | *"does not establish: verified evidence"* |
| 906 | a screenshot of a green check | *"is not equivalent to a machine-readable execution record"* |
| 912 | human prose as gate input | the gate *"MUST NOT parse human prose as authoritative conformance input"* |
| 923 | hidden special cases in the evaluator | `if fixture_id == ... ignore failure` is prohibited unless it is policy data |
| 950 | reconstruction from summary counts | *"MUST NOT be reconstructed from summary counts"* |

§906's sentence is the strongest single line in v1.3 and among the strongest in the lineage: it names
the exact artifact that every CI-based conformance claim in practice relies on, and refuses it.

### §7.5 New guards against silent PASS

Part XXIII counted 21 guards across the lineage. v1.3 adds twelve that are new in kind:

| § | Guard |
|---|---|
| 823 | conflicting evidence MUST NOT be resolved by choosing PASS |
| 839 | no informal severity ordering (`PASS > FAIL > ERROR` is prohibited) |
| 853 | `FAIL/ERROR/UNKNOWN/BLOCKED` MUST NOT be reduced to `NOT_PASS` without retaining the original |
| 854 | multiple failures MUST remain three separate facts |
| 855 | missing evidence is a result — `missing ≠ PASS`, `missing ≠ FAIL`, `missing = UNCOVERED` |
| 858 | over-broad rejection is a defect — a checker that rejects everything is not conformant |
| 874 | flakiness MUST NOT be hidden by selecting PASS |
| 896 | `coverage_delta > 0` does not imply `conformance = PASS` |
| 916 | `UNKNOWN` MUST NOT become PASS through aggregation |
| 917 | skipped required fixtures remain uncovered; completion of the runner is not evidence |
| 926 | counts alone cannot establish conformance without population, evidence and policy identity |
| 995 | no `CONFORMANT` while a required critical mutation survives |

Running total: **at least 33**, spread across nine documents, still with no single index. §7.5's
twelve are the first set that is *aggregation-aware*: every previous guard protected a status at the
point it was produced; these protect it through composition.

---

## §8 — The manifest graph gains a third node, still with no edges

### F-24.10 — OPEN — three manifest-like records now exist and none references another by identity

| Record | Document | § | Identity fields | References another record? |
|---|---|---|---|---|
| release manifest | v1.0 | 522 | `specification_digest`, `schema_digest`, `commit`, `implementation_digest`, `manifest_digest`, `execution_id`, `conformance_id`, `coverage_id`, `gate_id` | no |
| evidence manifest | v1.2 | 723 | 6 digests + `executions[]`, `evidence[]`, `coverage[]` | no |
| **coverage release record** | **v1.3** | **996** | `protocol_digest`, `subject_digest`, `artifact_digest`, `fixture_population_digest`, `evidence_population_digest`, `policy_digest`, `evaluator_digest`, `coverage_digest`, `conformance_digest` — **9** | **no** |

Part XXIII's carried finding was that §522's release manifest has no evidence reference and the two
manifests are unlinked. v1.3 adds a third record with a richer digest set — including
`evidence_population_digest`, which is the concept §522 lacks — and again declares no edge.

```
   v1.0 §522            v1.2 §723              v1.3 §996
  ┌──────────┐         ┌──────────┐           ┌──────────────────┐
  │ release  │         │ evidence │           │ coverage release │
  │ manifest │         │ manifest │           │ record           │
  └────┬─────┘         └────┬─────┘           └────────┬─────────┘
       │                    │                          │
       │  no edge           │  no edge                 │  no edge
       └────────────────────┴──────────────────────────┘
                  three islands, nine + six + nine digests,
                  zero pointers between them
```

The good news is that §996's field list contains everything needed to close this: `conformance_digest`
and `coverage_digest` are exactly the two pointers §522's `verification` block would need, and
`evidence_population_digest` is exactly §522's missing evidence reference. **The remedy is now one
line in v1.0 §522**, which is the cheapest possible form of this fix.

Note also that §996 says **SHOULD** while §888 and §952 say **MUST** for their own digests — a
requirement-level asymmetry in the one record that would tie the graph together.

---

## §9 — Carried items: the thirteenth-document census

| Item | First raised | Status in v1.3 |
|---|---|---|
| §749's inverted containment symbol | Part XXIII | **`⊆` = 0, `⊇` = 0, `CLAIMED_SCOPE` = 0** — untouched; §820 restates the correct direction in prose (third prose statement, one symbol still unrepaired) |
| compiled-pack digest circularity (v0.4 §86 vs §88) | Part XV | `CompiledPack` = 0, `compiled_digest` = 0 — **thirteenth document**, still open |
| `EnvironmentAuthority` | Part XIII | **0** — the `EffectiveAuthority` intersection term is undefined in the thirteenth document |
| `evidence.schema.json` | Part XV | **0** — and v1.3 §901 now specifies nine things that schema would validate |
| `ResultStatus` / `CheckStatus` as types | Part XX | both **0** — §832 restates the six values without naming a type |
| `ClaimId` as a type-system name | Part XX | `claim_id` now present (§843); `ClaimId` still 0 — see F-24.1's residual |
| two manifests unlinked | Part XXI | three records, still unlinked (§8) |
| coverage status underived | Part XX | **CLOSED** (§4) |
| v1.2 §790 `Covered` contradiction | Part XXIII | **CLOSED** (§6) |
| README/997 repository claim | Part II | **CLOSED, cosmetic** — see §1.4 for the four-way 997 disambiguation |

---

## §10 — Remaining findings

### F-24.11 — MINOR — §924's worked `CONFORMANT` example is insufficient under §926, three sections later

§924's example exposes: required fixtures, covered, passed, failed, blocked, unknown, policy, result.
§926 says counts are insufficient if *"population identity unknown"*, *"evidence identity unknown"* or
*"policy identity unknown"*. Measured: §924's example carries `policy: strict-required-v1` and **no**
population identity and **no** evidence identity.

Read as illustrative arithmetic, the example is fine — it is showing the *shape* of an explanation.
Read as the template §924 recommends ("Every conformance result SHOULD expose the rules that produced
it"), it teaches a form that §926 rejects. **Fix:** add one line to §924's example —
`population: sha256:...` / `evidence_population: sha256:...` — so the template satisfies the rule it
is adjacent to.

### F-24.12 — PROVED — v1.3 restates the correct scope direction in prose and leaves the symbol unrepaired

| Source | Statement | Form |
|---|---|---|
| v0.3 §41 | `CLAIMED ⊆ EXECUTED ⊆ DECLARED` | symbol |
| v1.0 §545 | `CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED` | symbol |
| v1.2 §749 | `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` | **symbol — inverted** |
| v1.2 §749 | *"A claim exceeding executed scope is invalid."* | prose — correct |
| **v1.3 §820** | *"A broader claim cannot substitute for missing evidence of the required operation unless the protocol explicitly defines the relation."* | **prose — correct** |

Measured in v1.3: `⊆` = 0, `⊇` = 0, `CLAIMED_SCOPE` = 0. So the prose statement of the correct
direction now outnumbers the symbol **three to one**, and the symbol is still wrong where it exists.
This is the cleanest possible form of the defect: a reader who reads prose gets it right twice and
wrong never; a reader who reads symbols gets it right once (v1.0) and wrong once (v1.2).

**Classification: PROVED, carried.** C-23.1 remains the highest-value one-line correction in the
lineage, and it is now cheaper to justify because two documents state the opposite of the symbol.

### F-24.13 — PROVED — both error-code conventions are consumed inside v1.3

| § | Code used | Convention | Origin |
|---|---|---|---|
| 856 | `E_DUPLICATE_ID` | `E_`-prefixed | v1.2 §679 (check error codes, 9) |
| 826 | `STALE_SUBJECT` | bare uppercase | v1.2 §719 (evidence validation errors, 21) |

Part XXIII's F-20 found two conventions inside v1.2 with no statement of the relation. v1.3 — written
after that defect was introduced — uses one code from each space, six sections apart, in the same
document. The conventions are therefore not being phased out in favour of one; they are both being
consumed. Note also that §856's code is doing **release-relevant work** (it is the worked example for
negative requirements, the case that proves `result: PASS` with a nonzero exit is legal), so the
inconsistency sits under the one example that a fixture author is most likely to copy.

**Fix:** unify the spaces in v1.2 §679/§719 as C-23.9 proposed, and state in v1.3 whether
`reason_code` values in §826 come from the same space as `error_code` values in §856.

### F-24.14 — MINOR — four requirement levels are used for the same kind of obligation across §888–§958

| § | Level | Object |
|---|---|---|
| 888 | MUST retain | `population_digest`, `evidence_population_digest`, `policy_digest` |
| 889 | SHOULD have | `evaluation_id` |
| 952 | MUST be checked | `coverage_digest`, `population_digest`, `policy_digest`, evidence references |
| 957 | SHOULD contain | `population_digest`, `coverage_digest`, `policy_digest`, `subject_digest`, `evaluation_id` |
| 996 | SHOULD bind | 9 digests |
| 960 | MUST have | `evaluator_id`, `version`, `source_revision`, `artifact_digest` |

The pattern is coherent on inspection — MUST for the inputs the evaluator holds, SHOULD for what
records carry — but the *release record* is the artifact that a gate consumes, and it carries the
weakest level of the three (§996 SHOULD). §911 then makes *"coverage digest invalid"* a
release-blocking condition, which means the record that must carry the digest is the one a
conforming implementation is least obliged to produce.

**Fix:** promote §996 to MUST, or state why the release-grade record is optional.

---

## §11 — Corrections, in dependency order

| # | Correction | Depends on | Blocks |
|---|---|---|---|
| C-24.1 | **Add `scope` to §843's claim record**, with §844's five members, and to §846's digest bindings. | — | claim promotion prevention; F-24.2 |
| C-24.2 | **Enumerate the claim status vocabulary** in §843 and bind each member to §850/§823/§995. | — | generated claim types; F-24.3 |
| C-24.3 | **Add a state-assignment table to §809** and constrain §869's escape clause to reports that declare both dimensions and membership rules. | C-24.2 | `sum(state_counts) = declared_count`; F-24.5 |
| C-24.4 | **Make §994 a superset of §936** (twelve mutations) and state that §936 defines and §994 projects. | — | release eligibility; F-24.8 |
| C-24.5 | **Adopt §801's names, or stop claiming normative definition for them** — six objects have zero uses. | — | the object vocabulary; F-24.6 |
| C-24.6 | **Reconcile `CoveragePopulation`/`Population`, `AggregationPolicy`/`Policy`, `CoverageDigest`/`coverage_digest`**, and record `CoverageStatus`'s supersession. | C-24.5 | cross-document type names; F-24.7 |
| C-24.7 | **Namespace the status tokens** (`coverage:BLOCKED`, `check:BLOCKED`, …) — the count rose from five senses of `UNKNOWN` to seven. | — | every generated enum; §7.2 |
| C-24.8 | **One edge, one line:** add `conformance_digest` and `coverage_digest` to v1.0 §522's `verification` block. | — | the manifest graph; F-24.10 |
| C-24.9 | **Promote §996 to MUST** or justify its SHOULD. | C-24.8 | release record; F-24.14 |
| C-24.10 | **Repair §749's symbol** (C-23.1, still open) — now supported by three prose statements against one symbol. | — | scope claims; F-24.12 |
| C-24.11 | **Unify v1.2's two error-code spaces** (C-23.9) and state which space §826 and §856 draw from. | — | generated error enums; F-24.13 |
| C-24.12 | **Add `population` and `evidence_population` to §924's example.** | — | explanation templates; F-24.11 |
| C-24.13 | **Move the compiled-pack digest out of the artifact** (C-23.14, open since Part XV). | — | the lineage's oldest defect; §9 |

C-24.1 through C-24.4 are the four that change behaviour; the rest change the record. As in Part
XXIII, the ordering matters: C-24.2 (claim status) and C-24.3 (state assignment) must precede any
generated coverage or claim type, because both decide what a record may contain.

---

## §12 — Closing judgement

### §12.1 The stage

v1.3 is the **aggregation and closure** stage. The declared sequence from Part XI was
`PROPOSAL → NORMATIVE SPECIFICATION → SCHEMA → REFERENCE IMPLEMENTATION → TEST FIXTURES → EXECUTION
EVIDENCE`; v1.3 is the first document that closes the loop **upward** — it takes the evidence layer
v1.2 produced and defines how it becomes a verdict. Measured, the lineage now has a complete
declarative chain:

```
§151–§171  Part XI  PROMPT PACK PROTOCOL                 NORMATIVE SPECIFICATION
§201–§271  v0.7     TYPES                                SCHEMA
§272–§361  v0.8     REFERENCE IMPLEMENTATION BLUEPRINT   REFERENCE IMPLEMENTATION
§362–§455  v0.9     EXECUTABLE PROTOCOL KERNEL            REFERENCE IMPLEMENTATION
§456–§555  v1.0     CONFORMANCE / EVIDENCE / RELEASE      TEST + EVIDENCE
§556–§655  v1.1     FIXTURE CORPUS + CONFORMANCE          TEST FIXTURES
§656–§800  v1.2     EVIDENCE + EXECUTION RECORD           EXECUTION EVIDENCE
§801–§997  v1.3     COVERAGE + CONFORMANCE AGGREGATION    DERIVATION + GATE INPUT
```

Nothing in §0–§997 has been built. The specification is now closed at the declarative level from
prompt packing to release gating — and the only executable thing in the audited repository remains
the upstream toolchain, whose four measured defects (the `has_prov` inversion, `neg()`'s unstructured
findings, crash-as-pass, the 997 cosmetic gap) and one newly measured defect (`check_fences`'s parity
verdict, Part XXIII F-23) are all in its verification layer.

### §12.2 What v1.3 gets right that no predecessor did

1. **The coverage derivation is a rule, not an example** (§810, §840–§842, §927) — with three
   arithmetic invariants (§868–§871) that make it checkable.
2. **The distinction the whole lineage has been circling is stated in one line**: *"coverage =
   evidence exists; conformance = required evidence establishes required result"* (§842).
3. **The claim becomes an object**: identifier, type name, derivation, digest, four bindings
   (§843–§846, §987–§990).
4. **The evaluator becomes a subject**: identity, version, source revision, artifact digest
   (§960), separate from policy (§961), and required to be reproducible by a second implementation
   (§932, §933).
5. **Seven new pseudo-evidence refusals**, including the two that matter most in practice: a CI
   summary (§903) and a screenshot of a green check (§906).
6. **`FLAKY`** (§874) and retry visibility (§872) — the first treatment in the lineage of
   nondeterminism as a reportable fact rather than a nuisance to smooth over.
7. **The second verbatim-stabilized vocabulary** (§877 ≡ v1.2 §701).

### §12.3 What v1.3 gets wrong

One stated invariant without an assignment function (§869 over §809's ten states — F-24.5), one
required field missing from a canonical record (§844 vs §843 — F-24.2), one undeclared field domain
(claim status — F-24.3), six declared-only objects (§801 — F-24.6), two mandatory mutation lists that
disagree by two items in each direction (F-24.8), and a manifest graph that grew by one island
(F-24.10). None of the six requires design work; four are one-line or one-table edits.

### §12.4 The laws of §997

Ten `NO … → NO …` laws, compared with the lineage's earlier closers:

| Document | § | Laws | Character |
|---|---|---|---|
| v0.8 | 361 | 11 | `NO … → NO …`, implementation-shaped |
| v0.9 | 455 | 16 | `NO … → NO …`, type-and-engine-shaped |
| v1.2 | 800 | 12 | identity-required, decidable by a missing field |
| **v1.3** | **997** | **10** | **derivation-required, decidable by an unbound record** |

```
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

The central invariant — *"NO SUMMARY MAY CREATE EVIDENCE THAT DOES NOT EXIST. NO AGGREGATOR MAY
CREATE COVERAGE THAT DOES NOT EXIST. NO COVERAGE RECORD MAY CREATE A RESULT THAT ITS EVIDENCE DOES
NOT SUPPORT."* — is the lineage's most complete statement of the no-creation property, and it is
aimed at exactly the artifact the audit has spent twenty-four Parts on: an aggregate that is
authoritative because it is a summary.

Two of the ten laws are, however, broken by v1.3's own text as written, in the same way:

* `NO CRITICAL MUTATION DETECTION → NO RELEASE ELIGIBILITY` — §994's minimum omits two of §936's
  critical mutations, so a suite can satisfy the mandatory list and miss two release-blocking ones
  (F-24.8).
* `NO POLICY IDENTITY → NO STABLE AGGREGATION SEMANTICS` — §843's claim binds `policy_id` but the
  claim's own `status` domain is undefined, so two conforming implementations can produce different
  statuses from the same policy identity (F-24.3).

### §12.5 Verdict

**v1.3 is the most constructively closed document in the lineage and the least self-consistent of the
last three.** It closes two carried findings outright — the coverage derivation (open since Part XX)
and the v1.2 §790 `Covered` contradiction (open since Part XXIII turned) — and it is the first
document whose closures were *checkable by arithmetic*: `sum(state_counts) = declared_count`,
`Covered(f)`'s four conjuncts, `Conformant`'s six, `ReleaseEligible`'s six. Against that, every defect
it introduces is of one single shape — **a rule stated whose domain is not**:

| Rule | Domain not stated |
|---|---|
| §869 partition invariant | the state-assignment function |
| §844 claim scope | the field in the canonical record |
| §843 claim status | the vocabulary |
| §936 critical mutations | the mandatory fixture projection |
| §996 release record | its relation to the two manifests it would complete |

That is a recognisable, single-fix-class pattern: **v1.3 states invariants over sets it never
enumerates.** It is a better failure mode than v1.2's — a missing enumeration is found by a validator,
where v1.2's inverted symbol was found only by reading the prose against the algebra — but it is the
same underlying habit, and it is worth naming once so that it is not rediscovered four times.

**Classification of the document overall: PARTIALLY PROVED** — closed at the level of derivation,
incomplete at the level of enumeration; a specification whose five arithmetic invariants are correct,
whose six declared-only objects are aspirational, and whose two mandatory mutation lists must be
reconciled before any evaluator is generated from it.

---

*Part XXIV ends. The lineage stands at §0–§997 across thirteen documents. Open items carried forward,
in priority order: §749's symbol (C-23.1/C-24.10), the status-token namespace (C-23.3/C-24.7), the
state-assignment table (C-24.3), the mutation reconciliation (C-24.4), the claim scope and status
(C-24.1, C-24.2), and the compiled-pack digest (C-23.14), open since Part XV.*
