# RFL-AE Prompt Packs — Audit Part XXIII

**Subject:** `RFL-AE v1.2 — Evidence & Execution Record Specification`, §656–§800 (145 sections)
**Recorded verbatim at:** `audit/rfl-ae-evidence-execution-record-v1.2.md`
**Predecessor:** Part XXII (v1.1, §556–§655)
**Date of analysis:** 2026-09-28
**Method:** all counts below were produced by executed measurement scripts against the recorded
verbatim file, not by reading alone. Every measurement script was deleted after the run. Where a
finding rests on a lexical test rather than a semantic one, that is stated in the finding.

---

## §1 — Scope, method, and heading-format detection

### §1.1 The heading-format trap, eighth instance of the method error

v1.2 uses **`##` at section level** (`## §656 — Purpose`), where v0.8–v1.1 use `#`. This is the same
family of error recorded seven times in this audit (v0.1/v0.4/v0.6 bare numbering; v1.2 `##`). The
checker used for this Part detects `^#{1,2}\s*§N` and therefore covers both conventions; the recorded
file's header already declares this difference, so the finding for v1.2 is **no discrepancy** — the
discrepancy would exist only in a reader who assumed one convention.

### §1.2 What was measured

| Measurement | Method | Result |
|---|---|---|
| Section range | regex over `^#{1,2}\s*§N` | **§656–§800** |
| Section count | count of matches | **145** |
| Contiguity | `nums == range(656, 801)` | **True** |
| Separators | count of `^---$` | **145** |
| Continuity vs v1.1 | v1.1 last §655 → v1.2 first §656 | **adjacent, no gap** |
| Duplicate numbers | shared numbers with any prior document in 656–800 | **none** |
| Fenced blocks | block census + `block[n]` indexing (not first-block) | see §2 |

**Result:** the recorded document is **complete and contiguous over §656–§800** with one separator
per section. No restoration loss is detectable in the section spine.

### §1.3 The twelve-document union

| Document | Range | Sections | Supplied |
|---|---|---|---|
| v0.1 | §1–§28 | 28 | 1st |
| v0.2 | §0–§40 | 41 | 2nd |
| v0.3 | §41–§70 | 30 | 3rd |
| v0.4 | §71–§105 | 35 | 4th |
| v0.5 | §106–§149 | 44 | 5th |
| v0.6 | §150–§199 | 50 | 6th |
| v0.7 | §201–§271 | 71 | 7th |
| v0.8 | §272–§361 | 90 | 8th |
| v0.9 | §362–§455 | 94 | 9th |
| v1.0 | §456–§555 | 100 | 10th |
| v1.1 | §556–§655 | 100 | 11th |
| **v1.2** | **§656–§800** | **145** | **12th** |
| **union** | **§0–§800** | **828 numbers, §200 unassigned** | |

The effective specification is now **§0–§800 across twelve documents**. §200 remains the only
unassigned number. The §1–§28 double-occupancy rule (v0.1 vs v0.2, 28 shared numbers, zero identical
headings) is unaffected by v1.2.

### §1.4 v1.2 in one paragraph

v1.2 is the **evidence-layer completion** of the stage sequence. v1.0 declared conformance and
release; v1.1 declared and verified the fixture population; v1.2 declares **what an execution
record, an observation, a check result, an evidence record, an evidence store, a coverage record, a
trust level, a retention state and a release-gate input each are, and which of them may be turned
into a claim.** It introduces the lineage's **first multi-vocabulary status lattice**, its **first
cryptographic trust model**, its **first explicit rejection of a Boolean `verified` flag**, and its
**first explicitly defined absence tokens**. It also, in §749, inverts the lineage's release-critical
scope relation — the headline defect of this Part.

---

## §2 — Structural census of §656–§800

Twenty enumerated vocabularies were declared in the document; the ones that are pure token blocks
are listed here with the count actually measured inside the correct fenced block.

| § | Function | Declared tokens | Count |
|---|---|---|---|
| 656 | lifecycle objects | prose numbered list | **12** |
| 658 | evidence closure conjuncts | identity terms | **11** |
| 669 | execution states | SCREAMING tokens | **11** |
| 674 | observation sources | SCREAMING tokens | **11** |
| 677 | check statuses | SCREAMING tokens | **6** |
| 679 | check error codes | `E_`-prefixed | **9** |
| 684 | store operations | SCREAMING tokens | **4** |
| 688 | validation layers | `1.`–`4.` | **4** |
| 689 | structural rejects | bullets | **7** |
| 695 | environment requirement classes | SCREAMING tokens | **3** |
| 699 | validator verdicts | SCREAMING tokens | **4** |
| 701 | replay comparison verdicts | SCREAMING tokens | **6** |
| 717 | evidence statuses | SCREAMING tokens | **6** |
| 719 | evidence error codes | bare uppercase | **21** |
| 722 | bundle closure states | SCREAMING tokens | **3** |
| 728 | trust levels | SCREAMING tokens | **6** |
| 736 | explicit absence tokens | SCREAMING tokens | **4** |
| 742 | retention states | SCREAMING tokens | **4** |
| 743 | failure-evidence states | SCREAMING tokens | **3** |
| 757 | invalid evidence fixtures | lowercase prose lines | **16** (see F-19) |
| 758 | mutation targets | elements | **11** |
| 789 | aggregate rules | worked examples | **6** |
| 790 | release report matrix columns | table header | **7** |
| 793 | evidence classes | SCREAMING tokens | **5** |
| 800 | laws | `NO … → NO …` | **12** |

Two counts require an explicit correction of my own prompt-derived expectation, and both are
stated here rather than buried:

* **§658 is 11 conjuncts, not 12.** The fenced block has 15 non-empty lines: one prose sentence,
  `Define:`, **eleven identity terms**, and one absence rule. Counting block lines would have
  published a false 12.
* **§757 is 16 items, not 17** (F-19).

**Global structure:** §656 opens with 12 lifecycle objects; §657 states the distinction chain;
§658–§664 identity; §665–§666 environment and dependency identity; §667–§669 request/record/execution
status; §670–§672 event chain; §673–§675 observations; §676–§679 checks; §680–§687 evidence record,
digest, reference, store, write protocol, atomicity; §688–§691 validation layers; §692–§695 freshness
and environment binding; §696–§699 tool and validator independence; §700–§703 replay; §704–§705
time; §706–§708 output capture; §709–§712 completeness, partial records, claims, derivation;
§713–§717 coverage binding, duplicates, supersession, revocation, evidence status; §718–§724
validation result, error codes, bundle, closure, manifest, manifest digest; §725–§730 provenance
chain and trust; §731–§736 authority, injection, sanitization, serialization, unknown, null;
§737–§742 canonicalization, mutation, store, index, GC, retention; §743–§747 failure evidence;
§748–§750 authorization and scope; §751–§754 tool chains, result binding, replay protection,
contamination; §755–§760 cross-language, golden, invalid fixtures, mutation, metamorphic;
§761–§766 independent bootstrap, minimal validator, portability, import, export, compression;
§767–§771 cryptography; §772–§774 time ordering; §775–§782 environment, dependency, build and
artifact provenance; §783–§786 scope levels; §787–§791 aggregation; §792–§799 critical evidence,
classification, diagnostics, historical records, freeze, digest, gate input, separation; §800
architecture and the twelve laws.

---

## §3 — The status-vocabulary lattice (the v0.9 enum defect, resolved at last)

### F-1 — PROVED — v1.2 declares three mutually distinct status vocabularies and states the distinctness in prose; no type or identifier is declared for any of them

This is the resolution of a defect carried since Part XX: v0.9 declared `ResultStatus` (0 uses) and
used `CheckStatus` (0 declarations), with no evidence that execution state and check status were
separated. v1.2 separates them **lexically and by rule**:

| Vocabulary | § | Members | Pairwise distinctness stated? |
|---|---|---|---|
| execution status | §669 | `REQUESTED AUTHORIZED STARTED RUNNING COMPLETED FAILED_TO_START INTERRUPTED TIMEOUT CANCELLED CRASHED UNKNOWN` | §669 vs verification: **yes** |
| check status | §677 | `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` | §717 vs evidence: **yes** |
| evidence status | §717 | `VALID INVALID PARTIAL STALE REVOKED UNKNOWN` | §717 vs check: **yes** |
| retention state | §742 | `ACTIVE ARCHIVED EXPIRED DELETED` | **no** |
| trust level | §728 | `UNTRUSTED OBSERVED IMPORTED VALIDATED INDEPENDENTLY_VALIDATED RELEASE_BOUND` | **no** |
| bundle closure | §722 | `OPEN PARTIAL BLOCKED` | **no** |

Measured: `ResultStatus` = **0**, `CheckStatus` = **0**, `execution_status` = 1, `verification_status`
= 1, `evidence_status` = **0** (the field is never named in a record — only `"status"`).

**Classification: PARTIALLY PROVED.** The *semantic* separation is now mandated, which is more than
v0.9 did. But the separation is enforced **only by English sentences and by which tokens appear in
which block**. Nothing in v1.2 makes it impossible for a serializer to emit one vocabulary's token
in another vocabulary's field. `"status": "OBSERVED"` in §673 shows this is not hypothetical (F-3).

```
              ┌────────────────┐
              │ EXECUTION      │  §669  11 states
              │ (what happened)│
              └───────┬────────┘
                      │  §669: "distinct from verification status"
                      ▼
              ┌────────────────┐
              │ CHECK          │  §677   6 states   ← canonical, restated by §788
              │ (judgement)    │
              └───────┬────────┘
                      │  §717: "MUST be distinct from check status"
                      ▼
              ┌────────────────┐
              │ EVIDENCE       │  §717   6 states
              │ (validity)     │
              └───────┬────────┘
                      │  §789: aggregate derived from applicable policy
                      ▼
              ┌────────────────┐
              │ RELEASE        │  §791: no percentage conformance
              └────────────────┘

   Not adjacent in this lattice, so not covered by any distinctness statement:
     retention (§742)   trust (§728)   bundle closure (§722)   observation (§673)
```

### F-2 — PROVED — the token `UNKNOWN` carries five different meanings in one document, with no disambiguation rule

| § | Vocabulary | What `UNKNOWN` means there |
|---|---|---|
| 669 | execution status | execution outcome not determined |
| 677 | check status | checker did not resolve the predicate |
| 701 | replay verdict | comparison not resolvable |
| 717 | evidence status | evidence validity not established |
| 736 | explicit absence token | a **value** that is not knowable |

Under the lineage's own load-bearing rule — *"Never silently collapse distinct states"* (v0.1) — five
senses under one spelling is a collapse. The audit's own vocabulary (`UNKNOWN ≠ PASS`) is itself
using a sixth, unqualified sense. The practical consequence: a tool that stores `"status":
"UNKNOWN"` cannot say which of the five was meant, and a reader who applies `UNKNOWN ≠ PASS` to one
sense may wrongly extend it to another.

**Fix (dependency order, before any schema generation):** namespace the token in serialization —
`execution:UNKNOWN`, `check:UNKNOWN`, `evidence:UNKNOWN`, `replay:UNKNOWN`, `value:UNKNOWN` — or
require every citation of `UNKNOWN` to name its vocabulary. This is a one-line rule and it removes an
entire failure class.

### F-3 — PROVED — the bare key `"status"` carries five vocabularies across six record schemas, and one of the six vocabularies is never enumerated

Measured: `"status"` as a bare field name in fenced blocks occurs **six times**:

| Line | Owning § | Value | Vocabulary it comes from | Vocabulary enumerated? |
|---|---|---|---|---|
| 357 | §668 execution record | `"COMPLETED"` | §669 execution | yes |
| 479 | §673 observation record | `"OBSERVED"` | **none** — not in §669/§677/§717 | **no** |
| 540 | §676 check execution | `"PASS"` | §677 check | yes |
| 583 | §678 negative fixture verification | `"PASS"` | §677 check | yes |
| 642 | §680 evidence record (`result.status`) | `"PASS"` | §677 check | yes |
| 1374 | §718 evidence validation result | `"VALID"` | §717 evidence | yes |

Two findings in one:

1. **The observation status vocabulary is undeclared.** `OBSERVED` appears exactly **twice** in
   v1.2: as §673's observation status *value*, and as a **trust level** in §728. The relation between
   "an observation whose status is `OBSERVED`" and "a trust level of `OBSERVED`" is never stated.
   §674 enumerates eleven observation **sources** but no observation **status** set.
2. **The field name is not vocabulary-scoped.** §676's check carries `"status"`, §680's evidence
   record carries `result.status`, §718's validation result carries `"status"` — three vocabularies,
   one key, in records that are designed to be machine-read and cross-referenced.

**Fix:** rename to `execution_status`, `observation_status`, `check_status`, `evidence_status` at the
record level; enumerate observation status in §673/§674; state whether §728's `OBSERVED` trust level
is derived from observation status or independent of it.

### F-4 — PROVED — the check-status vocabulary has converged; the feared "eighth variant" is a third restatement of the canonical six

| Where | Set | Count |
|---|---|---|
| v1.1 §601 | `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` | 6 |
| v1.2 §677 | `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` | 6 |
| v1.2 §788 | `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` | 6 |

Against the seven known check-*specification* variants (v0.2 §11 ×2, v0.3 §47 ×2, v0.4 §90, v0.6
§162, v0.7 §222), v1.2 does **not** add a divergent eighth set. It adds a third faithful restatement
of the six-token set first mandated in v1.1 §601. **This is the first time in the lineage that a
status vocabulary has been reproduced verbatim three times.** The `SKIPPED`-unreachable finding
(Part XVII, 3rd occurrence) is likewise no longer compounded: v1.2 gives `SKIPPED` a resting place
in three separate enumerated sets (check status, aggregation, release manifest counts from v1.1
§650).

### F-5 — PROVED — execution states are the sixth distinct execution vocabulary and discard 6 of v0.8's 9 states without a migration statement

| Document | § | Execution vocabulary | Count |
|---|---|---|---|
| v0.5 | §116 | (execution states, later dropped — Part XVI) | — |
| v0.8 | §278 | `Created Authorized Running Succeeded Failed Error Cancelled TimedOut Blocked` | 9 |
| v1.2 | §669 | `REQUESTED AUTHORIZED STARTED RUNNING COMPLETED FAILED_TO_START INTERRUPTED TIMEOUT CANCELLED CRASHED UNKNOWN` | 11 |

Measured overlap, case-insensitive: **3 members** — `authorized`, `running`, `cancelled`. Gone:
`Created`, `Succeeded`, `Failed`, `Error`, `TimedOut`, `Blocked`. Gained: `REQUESTED`, `STARTED`,
`COMPLETED`, `FAILED_TO_START`, `INTERRUPTED`, `TIMEOUT`, `CRASHED`, `UNKNOWN`. `v0.9`'s
`ExecutionState` name survives (4 occurrences) but v1.2 declares no type and no supersession rule.

Note the semantic drift that is *not* merely renaming: v0.8's `TimedOut` → v1.2's `TIMEOUT` and
v0.8's `Blocked` → **absent** (v1.2 puts `BLOCKED` in the check vocabulary and in bundle closure
instead). `Blocked` moving from the execution vocabulary to the check vocabulary is a genuine
improvement — a blocked execution is a scheduling fact, a blocked check is a judgement — but it is
**unstated**, so an implementation migrating from v0.8 keeps `Blocked` as an execution state and
thereby puts a check token in an execution field.

---

## §4 — Headline: §749 inverts the lineage's release-critical scope relation

### F-6 — PROVED — the symbol `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` contradicts its own prose, contradicts v1.0 §545, and is the only containment symbol in the document

§749 verbatim (recorded file, block-index verified):

```
Execution evidence MUST bind the executed scope.

The relation is:

CLAIMED_SCOPE ⊇ EXECUTED_SCOPE

A claim exceeding executed scope is invalid.
```

| Source | Statement |
|---|---|
| v0.3 §41 | `CLAIMED ⊆ EXECUTED ⊆ DECLARED` |
| v1.0 §545 | `CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED` |
| v1.0 §545 (field form) | `claimed_scope ⊆ executed_scope` |
| **v1.2 §749** | **`CLAIMED_SCOPE ⊇ EXECUTED_SCOPE`** |
| **v1.2 §749 prose** | **"A claim exceeding executed scope is invalid."** |

Measured symbol census for v1.2: `⊆` occurs **0 times**; `⊇` occurs **exactly once** — in §749. So
there is no second symbol in v1.2 against which the first can be arbitrated. The prose is correct and
preserves v1.0's rule; the symbol states the reverse relation.

```
        v1.0 §545 (correct)                  v1.2 §749 (as written)
   ┌──────────────────────────┐        ┌──────────────────────────┐
   │        AUTHORIZED        │        │       CLAIMED_SCOPE      │
   │  ┌────────────────────┐  │        │  ┌────────────────────┐  │
   │  │      EXECUTED      │  │        │  │    EXECUTED_SCOPE  │  │
   │  │  ┌──────────────┐  │  │        │  │                    │  │
   │  │  │   CLAIMED    │  │  │        │  └────────────────────┘  │
   │  │  └──────────────┘  │  │        └──────────────────────────┘
   │  └────────────────────┘  │
   └──────────────────────────┘        An implementer of this relation
                                       *permits* a claim to exceed
   a claim may be narrower than        executed scope and *forbids* a
   what was executed; that is a        narrower claim — precisely the
   weakened claim, and it is legal.    opposite of the prose.
```

**Impact:** the relation is the release-critical predicate for "what may be claimed". An
implementation that follows the symbol satisfies every structural check a reader can write, and
still violates the document's own sentence and v1.0 §545. Two distinct failure modes follow — a
scope-inflated claim (release of unexecuted surface) and a refusal of legitimately narrow claims
(over-blocking, the inverse defect class already recorded as v1.1 §647's concern).

**Fix (highest priority):** `EXECUTED_SCOPE ⊆ CLAIMED_SCOPE ⊆ AUTHORIZED_SCOPE` **and** restore the
`⊆ AUTHORIZED` link, which §749 drops (F-7). Also state the relation in field form as v1.0 §545 does
(`claimed_scope ⊆ executed_scope`), so that the prose and the symbol carry the same content.

### F-7 — PROVED — v1.2 drops `AUTHORIZED` from the containment chain that v1.0 made release-critical

v1.0 §545 chains three sets. v1.2 §749 chains **two** and handles authorization separately in §748
("Authorization MUST precede execution" / "Evidence MUST NOT retroactively authorize an execution")
and §750 ("If execution exceeds authorized scope: `E_SCOPE_VIOLATION` MUST be recorded").

The separate treatment is sound in isolation. But the chained invariant `EXECUTED ⊆ AUTHORIZED` is
now nowhere stated as a containment relation in v1.2, and §749's symbol type has only one member.
The result is a **split invariant**: the reader must reconstruct `EXECUTED ⊆ AUTHORIZED` from §748 +
§750's error code, and must reconstruct `CLAIMED ⊆ EXECUTED` from §749's prose while ignoring §749's
symbol. `E_SCOPE_VIOLATION` exists in **two error spaces** with the same spelling (§679 prefixed,
§750 bare) — see F-18.

---

## §5 — Evidence closure: the first fully instantiated abstract closure in the lineage

### F-8 — PROVED — §658's eleven-conjunct closure is realised with no missing conjunct by §680's concrete evidence record

§658 defines:

```
Closure(E) =
    SubjectIdentity      ∧ ArtifactIdentity   ∧ VerifierIdentity
  ∧ EnvironmentIdentity  ∧ ProtocolIdentity   ∧ FixtureIdentity
  ∧ PredicateIdentity    ∧ ExecutionIdentity  ∧ ObservationIdentity
  ∧ ResultIdentity       ∧ EvidenceDigest
```

Mapping §680's canonical JSON record against those eleven conjuncts:

| §658 conjunct | §680 field | Present |
|---|---|---|
| SubjectIdentity | `subject: {}` | yes |
| ArtifactIdentity | `artifact: {}` | yes |
| VerifierIdentity | `verifier: {}` | yes |
| EnvironmentIdentity | `environment: {}` | yes |
| ProtocolIdentity | `protocol_version: "1.2"` | yes |
| FixtureIdentity | `fixture: {}` | yes |
| PredicateIdentity | `predicate: {}` | yes |
| ExecutionIdentity | `execution_id` + `execution_events_digest` | yes |
| ObservationIdentity | `observations: []` | yes |
| ResultIdentity | `result.status` | yes |
| EvidenceDigest | `evidence_id: "sha256:..."` | yes |

**11 of 11.** §658 closes with an absence rule — if a required component is absent, "the result MUST
NOT be represented as independently verified evidence" — which is a falsifiable predicate, not a
recommendation.

This is the **first time in twelve documents that an abstract closure is fully instantiated by a
concrete record with a countable, checkable mapping.** v0.9's `Closure`-style constructs (§384) and
v1.0's evidence requirements were stated at the prose level; v1.1 §574's five-edge closure was a
graph, not a per-record predicate. §658 + §680 together give a checker something to evaluate: eleven
lookups, eleven booleans, one `∧`.

### F-9 — PARTIALLY PROVED — the closure is complete but two of its conjuncts are unresolvable in the lineage

| Conjunct | Resolution status |
|---|---|
| ProtocolIdentity = `"1.2"` | resolvable (document number = version) |
| EvidenceDigest = `evidence_id` | resolvable (§682 defines the calculation) |
| Subject / Artifact / Verifier / Predicate / Environment / Fixture | resolvable only if each has its own digest — v1.2 provides `*_digest` fields in §723 but **not** in §680's record, where each is `{}` |
| ResultIdentity | `result.status` is a **check** status (§677), not a result identity; there is no result digest |

§680's identity fields are empty objects with no inner schema, and §658 requires *identities*, not
*names*. §723 supplies six digests (`subject_digest`, `artifact_digest`, `fixture_corpus_digest`,
`verifier_digest`, `predicate_digest`, `environment_digest`) — so the digests exist, but in the
**manifest**, not in the **record**. Whether `subject: {}` in §680 is expected to carry
`subject_digest` is not stated.

**Classification: PARTIALLY PROVED.** The closure is formally complete; its individual conjuncts are
not all resolvable from the record alone. **Fix:** give each empty object one required key —
`{"subject_digest": "sha256:..."}` — or state that the record's identity fields are defined by the
version's schema (which, per §1.2 of this Part, does not exist on disk — F-20).

---

## §6 — The manifest graph: §723 against §522, §575, and §724

### F-10 — PARTIALLY PROVED — §723 supplies the evidence reference that v1.0 §522 lacked, but leaves the two manifests unlinked

| Artifact | Document | Fields | Contains evidence list? |
|---|---|---|---|
| release manifest | v1.0 §522 | `release`, `protocol{specification_digest, schema_digest}`, `subject{commit, implementation_digest}`, `fixtures{manifest_digest}`, `verification{execution_id, conformance_id, coverage_id, gate_id}` | **no** |
| fixture corpus digest | v1.1 §575 | `corpus_digest = H(canonical_manifest)` over 7 covered classes | n/a |
| **evidence manifest** | **v1.2 §723** | `protocol_version`, 6 digests, `executions[]`, `evidence[]`, `coverage[]` | **yes** |
| manifest digest | v1.2 §724 | canonical digest over "the identity-bearing membership of the evidence set" | yes |

The carried finding from Part XXI — *"v1.0 §522's release manifest carries NO evidence reference"* —
is now **partially closed**: §723's manifest carries `evidence: [sha256:...]`, `executions:
[execution:...]` and `coverage: [coverage:...]`, which is the missing link at the evidence layer.

What is **not** closed:

1. Neither §522 nor §723 references the other. The release manifest has no `evidence_manifest_digest`
   field; the evidence manifest has no `gate_id` or `conformance_id`. Two manifests now describe
   overlapping parts of the same release with **no declared relation**.
2. §724 requires the manifest digest to cover "the identity-bearing membership of the evidence set"
   but does not name the digest algorithm or the canonicalization (contrast §682, which does state
   that canonicalization "MUST be declared by the protocol version").
3. §723's `protocol_version: "1.2"` pins the manifest to a document version; §680's record carries
   both `protocol_version: "1.2"` **and** `evidence_schema_version: "1.0"`. Whether the manifest
   should carry the schema version too is unstated (F-20).

```
   v1.1 §575                        v1.2 §723                       v1.0 §522
  ┌────────────┐   fixture_corpus   ┌──────────────┐              ┌──────────────┐
  │ corpus     │ ───digest────────► │  evidence    │   ???        │  release     │
  │ digest     │                    │  manifest    │ ◄───?──────► │  manifest    │
  └────────────┘                    └──────┬───────┘              └──────┬───────┘
                                           │ §724 canonical digest       │ no evidence field
                                           ▼                             ▼
                                     manifest_digest              release identity

  Solid arrow  = a declared consumption (first in the lineage).
  Dotted gap   = no declared relation. A release can name a gate id and an
                 evidence manifest can name evidence digests, with nothing
                 binding the two numbers together.
```

### F-11 — PROVED — v1.1 §575's orphaned corpus digest acquires its first consumer

Carried from Part XXII: v1.1's `corpus_digest` was defined and covered seven identity classes but was
referenced by nothing downstream. §723's `fixture_corpus_digest` is that consumer. This is a real
structural closure — the fixture population now has a machine-visible path into the release-grade
evidence set, which is exactly what v1.1 §655's law demanded ("THE TEST POPULATION MUST ITSELF BE
VERIFIED BEFORE ITS RESULTS CAN SUPPORT A CONFORMANCE CLAIM").

**Naming drift, MINOR:** v1.1 §575 spells it `corpus_digest`; v1.2 §723 spells it
`fixture_corpus_digest`. Two names, one concept, in adjacent documents, in a specification whose own
rule is `same ID ≠ same version ≠ same source ≠ same semantics` (v0.4). **Fix:** pick one spelling —
the v1.2 spelling is the better one, because `corpus` alone is ambiguous against
`evidence`/`coverage` corpora — and add a one-line note that it is v1.1 §575's digest.

---

## §7 — The digest-exclusion rule: fourth statement, still unconsumed by the pack boundary

### F-12 — OPEN (unchanged) — §682 restates the self-reference exclusion; the v0.4 §86 / v0.8 §239 compiled-pack circularity is untouched

§682 defines evidence identity as:

```
evidence_id =
  sha256(
    canonical(
      evidence_without_evidence_id
    )
  )
```

This is the **fourth statement of an exclusion rule in the lineage** — v0.7 §239, v0.8, v0.9, and now
v1.2 §682. Measured: the phrase `evidence_without_evidence_id` is present in v1.2; the wording variant
`recursively contain itself` is **absent**.

The rule is stated for **one** artifact only. The lineage's own build-blocking defect — v0.4 §86 vs
§88, the compiled pack whose digest cannot be computed because the digest is a member of the
structure being digested — is still unaddressed. v1.0 §541 relocated the pack digests *outside* the
artifact as evidence (Part XXI: PARTIALLY CLOSED BY RELOCATION), and v1.2 does not revisit it:
`CompiledPack` = **0**, `compiled_digest` = **0** in v1.2.

**Classification: OPEN.** This is now a **twelfth-document** carried item. The one-sentence fix
(v0.4 §102 step 1, or the general form "no artifact may contain its own digest; the digest is
computed over the canonical form with the digest field absent") remains unapplied, and v1.2 §682 is
the third precedent that proves the rule is expressible in one line.

---

## §8 — Failure taxonomy: `ERROR ≠ FAIL` finally gets fixtures

### F-13 — PROVED — v1.2 separates crash, timeout, failed-to-start and check failure across five sections, and §747 makes crash explicitly outside the predicate

| § | Rule | Effect |
|---|---|---|
| 678 | "generic nonzero exit code is insufficient" | exit code cannot stand in for semantic status |
| 743 | `FAILED_TO_START`, `CRASHED`, `TIMEOUT` enumerated as failure-evidence states | three distinct mechanical failures |
| 744 | crash "MUST NOT become a fixture FAIL" | mechanical ≠ semantic |
| 745 | timeout "is not equivalent to a semantic FAIL" | mechanical ≠ semantic |
| 747 | crash "explicitly evaluates" the checker that crashed | crash produces no predicate result |

Against the priors — v0.6 "A checker crash MUST produce `ERROR`, not `FAIL`"; v0.8 "A shell script
MUST NOT infer semantic status solely from `returncode != 0`"; v1.0 §487's `ERROR` class; v1.1 §602's
mandatory distinct fixtures — v1.2 is the first document to enumerate the failure states **as a
vocabulary** and to name the three of them (`FAILED_TO_START`, `CRASHED`, `TIMEOUT`) that are
*already* execution states in §669. Note §743's three are a **subset of §669's eleven**: consistent,
but the same three tokens now live in two vocabularies (execution status and failure evidence), which
is the same class of defect as F-2/F-3 and should be resolved by the same namespacing rule.

| Rule (this audit's own) | v1.2 status |
|---|---|
| `ERROR ≠ FAIL` | enforced (§678, §744, §745, §747) — **strongest statement in the lineage** |
| `SKIPPED ≠ PASS` | implied by §677's six-way list + §788's preservation rule |
| `UNKNOWN ≠ PASS` | enforced (§735 forbids `UNKNOWN → … → PASS`; §690 unresolved identity → not PASS) |
| `PARTIAL ≠ COMPLETE` | enforced at three layers: §709 (completeness computed, not asserted), §711 (partial records), §717 (`PARTIAL` is an evidence status) |

---

## §9 — Guard counts: how many rules now prevent a silent collapse to PASS

Part XXII counted **nine** rules across v1.0 and v1.1 that prevent a non-PASS state from being
reported as PASS. v1.2 adds the following, all measured as present:

| New guard | § | What it forbids |
|---|---|---|
| request is not evidence | 667 | a request MUST NOT be treated as evidence that execution occurred |
| completed ≠ verified | 669 | `execution_status = COMPLETED` does not imply `verification_status = PASS` |
| no coercion to PASS | 677 | "No non-PASS state may be silently coerced into PASS." |
| no Boolean verification | 681 | `{"verified": true}` is not authoritative evidence |
| unresolved identity blocks | 690 | unresolved required identity → `BLOCKED`/`UNKNOWN`, not PASS |
| self-report is not verification | 697 | runner self-report ≠ independent verification |
| no self-certification | 698 | a program certifying itself is prohibited |
| no successful-run selection | 702 | a failure "MUST NOT be hidden by selecting the successful run" |
| computed completeness | 709 | "Completeness MUST be computed, not asserted." |
| crash is not FAIL | 744 | mechanical failure cannot become a semantic FAIL |
| aggregate cannot conceal | 788 | `997 / 1000 passed` MUST NOT conceal the non-PASS states |
| no percentage conformance | 791 | (restates v1.1 §651; see F-15 for §791's example) |

Twelve new guards. The lineage's **total guard count against silent PASS is now at least 21**, spread
across eight documents, with no single index. That absence is itself the reason this number has to be
recounted by hand each Part — a specification that has stated the same prohibition twenty-one times
has a discoverability problem, not a coverage problem.

---

## §10 — Coverage: a third representation, and the one undefined column

### F-14 — OPEN — coverage now has three incompatible representations and still no general derivation rule

| Document | § | Shape | Fields |
|---|---|---|---|
| v1.0 | 504 | corpus scalars | `declared executed missing skipped failed status` |
| v1.1 | 650 | corpus scalars | `declared executed passed failed errored skipped blocked unknown missing` |
| v1.1 | 607/608 | **set algebra** (the only derivation) | `checked ⊆ declared` → `missing`, `unexpected`, `status = PARTIAL`; duplicates do not increase coverage |
| **v1.2** | **790** | **per-fixture projection matrix** | `Fixture Executed Evidence Predicate Result Digest Valid Covered` |

Three documents, three shapes, two field name sets, one derivation (v1.1's, an example rather than a
rule). §790 adds the sentence "This matrix is a projection. The underlying evidence records remain
authoritative." — which correctly subordinates the projection but does not say how the projection is
computed from the record, nor how it relates to §650's counts.

The Part XXII finding stands, unmoved: **coverage status is still underived in general**; v1.1's
worked example remains the only place a `missing`/`unexpected`/`status` triple is produced from sets.

### F-15 — PROVED — §790's `Covered` column is nowhere defined and its worked rows contradict v1.1 §607's semantics

§790 verbatim:

| Fixture | Executed | Evidence | Predicate | Result | Digest Valid | Covered |
|---|---|---|---|---|---|---|
| F1 | yes | yes | P1 | PASS | yes | yes |
| F2 | yes | yes | P2 | FAIL | yes | **no** |
| F3 | no | no | P3 | — | — | no |

`Covered` occurs **exactly once in v1.2** — as this table's column header. There is no definition,
no rule, no mapping. Under v1.1 §607's semantics, F2 is *declared, executed, checked and evidenced*:
its membership in the checked population is not in question, and marking it `Covered = no` therefore
means `Covered` is not `checked` but `checked ∧ passed` — the conjunction v1.1 §651 and v1.2 §791
exist to forbid from being reported as coverage-at-large.

This is a **provable contradiction between two documents in the same lineage**, not a stylistic
complaint: a reader implementing §790 produces a coverage population excluding every failing
fixture, which is exactly the "aggregate conceals failure" failure mode §788 prohibits — one layer
lower, at the projection instead of the summary.

**Classification: PROVED.** The contradiction is textual and reproducible from the two blocks. The
*intent* of the column cannot be disproved (it may mean "covered by a passing claim"), which is why
the fix is definitional, not numeric.

**Fix:** define `Covered` as `Executed ∧ Evidence ∧ DigestValid ∧ Predicate evaluated` and set F2's
value to `yes`; if the intent is "eligible for a passing claim", rename the column
(`PassedAndCovered`) and add it to the percentage-prohibition list of §791.

### F-16 — MINOR — §788's "three non-PASS states" counts items in an example, not members of the vocabulary it just printed

§788 prints six states (`PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED`) and then says a summary such as
`997 / 1000 passed` "MUST NOT conceal the three non-PASS states." Three is the arithmetic remainder
of the example, not a property of the vocabulary — which has **five** non-PASS members. Read as a
rule, the sentence licenses a summary that conceals two of the five.

**Fix:** "MUST NOT conceal the non-PASS states (`FAIL`, `ERROR`, `UNKNOWN`, `SKIPPED`, `BLOCKED`)",
or "the three non-PASS results in this example".

### F-17 — PARTIALLY PROVED — §791 restates the no-percentage rule with a second example but no mechanism

| Where | Prohibited form |
|---|---|
| v1.1 §651 | no percentage conformance |
| v1.2 §788 | `997 / 1000 passed` must not conceal non-PASS |
| v1.2 §791 | `99.7% passed` |

Three prohibitions, zero mechanisms. The rule is now stated in a form (`99.7% passed`) that
*contains* its own counter-example — 99.7 is exactly what `997 / 1000` becomes — so the lineage is
prohibiting the same artifact twice under two notations. The prohibition is still not paired with a
required data shape (a status vector, or the presence/absence of a `concealed` field). Note this is
the *inverse* of the audit's own README/997 finding and must not be conflated: **§788's `997 / 1000`
is the specification's illustrative counter-example; the audit's `997 sections` finding is a closed,
cosmetic repository matter (Part II §8 … Part VIII §112), and the two 997s are unrelated.**

---

## §11 — Cryptography: the lineage's first trust model

### F-18 — PROVED — §767–§771 build a four-layer trust model and, uniquely, disclaim it in the same breath

| § | Statement |
|---|---|
| 767 | `ENCRYPTED ≠ VERIFIED` |
| 768 | `SIGNATURE ≠ SEMANTIC PROOF` |
| 771 | signature validity does not imply the key is trusted |
| 728 | six trust levels, `UNTRUSTED` → `RELEASE_BOUND` |
| 761 | `producer ≠ validator` |
| 764 | "Import MUST NOT mean trust." |
| 699 | `STRUCTURALLY_VALID`, `IDENTITY_VALID`, `DIGEST_VALID`, `SEMANTICALLY_VALID` |
| 688 | four validation layers: `STRUCTURAL`, `IDENTITY`, `INTEGRITY`, `SEMANTIC` |
| 1384 | "The validator's own result MUST remain distinct from the evidence being validated." |
| 782 | `IMPLEMENTATION_IDENTITY = UNVERIFIED` when artifact provenance does not resolve |

This is, by a wide margin, v1.2's strongest section: it forbids the four standard false inferences
(encrypted ⇒ verified, signed ⇒ true, valid signature ⇒ trusted key, imported ⇒ trusted) and it
provides the trust ladder that makes the forbiddals actionable. It also supplies the last element of
the lineage's independence requirement: v1.1 §618 kept model output as a proposal; v1.2 §697/§698
keep the runner from certifying itself; §761 keeps the producer from validating; §1384 keeps the
validator's verdict separate from the evidence.

**One gap, carried:** `imported` is both a trust level (§728) and an evidence class (§793,
`IMPORTED`), and §764 forbids treating import as trust. That is consistent, but the **same token**
again carries a trust-level and a classification sense — the F-2/F-3 pattern. It should be captured
by the same namespacing fix.

---

## §12 — Carried items: what twelve documents have still not moved

| Item | First raised | Documents since | v1.2 status |
|---|---|---|---|
| `Claim` has no identifier (`ClaimId`/`claim_id`/`pub struct Claim` = 0) | Part XX (v0.9) | v1.0, v1.1, **v1.2** | **0 / 0 / 0 in v1.2** — §712 derives claims from `Evidence + Coverage + Applicable rules` and still gives the derived object no name |
| compiled-pack digest exclusion rule (v0.4 §86 vs §88) | Part XV | v0.6–**v1.2** | `CompiledPack` = 0, `compiled_digest` = 0; §682 proves the rule is one line |
| `ResultStatus` / `CheckStatus` as declared types | Part XX (v0.9) | v1.0, v1.1, **v1.2** | both **0**; separation achieved in prose only (F-1) |
| `EnvironmentAuthority` | Part XIII | v0.4–**v1.2** | **0 occurrences in v1.2** — the `EffectiveAuthority` intersection term remains undefined in the lineage's twelfth document |
| `evidence.schema.json` (named in the reference implementation's probe) | Part XV | v0.1–**v1.2** | `evidence.schema.json` = **0 in v1.2**; §680 now specifies the record it would describe, and §591 (v1.1) enumerates eight fixture cases for schemas that do not exist |
| coverage status underived in general | Part XX | v1.1 (by example), **v1.2** | unchanged (F-14) |
| `evidence_schema_version` | new in §680 | — | first appearance; no schema on disk; relation to `protocol_version` unstated |
| the README/997 repository claim | Part II | closed at Part III/IV | **CLOSED, cosmetic only** — never a live defect, never P0 |
| §1–§28 double occupancy | Part XXII | — | unbounded by v1.2 (its range starts at §656) |

---

## §13 — Remaining findings

### F-19 — MINOR — §757 lists 16 items, one of which is a line-join artifact, and the requirement level `SHOULD` contradicts v1.1's `SHALL` treatment of the same class

The block reads:

```
wrong evidence digest
wrong subject digest
wrong artifact digest
wrong verifier digest
wrong predicate digest
broken event chain
missing event
duplicate event
sequence missing observation        <-- two items joined
wrong observation digest
unknown fixture
unknown execution
stale subject
stale verifier
stale predicate
scope mismatch
```

**Measured: 16 items.** The user-facing summary of §757 described **17 invalid-evidence fixtures**;
the difference is exactly `sequence` + `missing observation`, which the recorded text carries as a
single line. The neighbouring v1.1 §612 replay family names `missing sequence` and `duplicate
sequence` as distinct cases, so the intended list is very likely 17 — **but the recorded document
says 16, and I record what is written, not what was intended.** A generator, validator or fixture
index built from this list will produce 16 cases, and the 17th will silently not exist.

Also note the requirement level: §757 says fixtures "SHOULD include". v1.1 §634 made *deleting* a
required fixture a conformance failure and v1.1 §655 made the population itself the subject of
verification. A `SHOULD` list of invalid-evidence fixtures is therefore weaker than the corpus
discipline it sits inside — an implementer may select sixteen, ten, or none.

**Fix:** repair the two joined lines into `sequence missing` and `missing observation` (or, following
v1.1 §612, `missing sequence` / `out-of-order sequence`), and raise the level to `SHALL` with an
explicit exemption mechanism if one is wanted.

### F-20 — MINOR — two naming conventions for error codes inside one document, and one concept spelled two ways

| § | Convention | Count | Examples |
|---|---|---|---|
| 679 | `E_`-prefixed | 9 | `E_SCHEMA_INVALID`, `E_STALE_SUBJECT`, `E_SCOPE_VIOLATION` |
| 719 | bare uppercase | 21 | `SUBJECT_UNRESOLVED`, `STALE_SUBJECT`, `EVENT_CHAIN_INVALID`, `STORE_CORRUPTION` |

`STALE_SUBJECT` exists in both spaces (`E_STALE_SUBJECT`, `STALE_SUBJECT`); so does `E_SCOPE_VIOLATION`
(§679) against §750's bare `E_SCOPE_VIOLATION` in a `text` block; and §679's generic
`E_DIGEST_MISMATCH` is decomposed in §719 into `SUBJECT_DIGEST_MISMATCH`,
`ARTIFACT_DIGEST_MISMATCH`, `VERIFIER_DIGEST_MISMATCH`, `PREDICATE_DIGEST_MISMATCH` and
`OBSERVATION_DIGEST_MISMATCH`. Nothing states whether the two spaces are disjoint, whether one is a
refinement of the other, or whether `E_` is a namespace prefix. This is the same defect class as the
seven check-specification variants, caught early: **two conventions introduced in one document**.

### F-21 — MINOR — the record demonstrates `null` exactly where §736 prefers an explicit absence token

§736 states: nullability MUST be defined by schema; the core evidence model SHOULD avoid ambiguous
nulls; where absence has semantic meaning an explicit status SHOULD be preferred, giving
`NOT_OBSERVABLE` / `NOT_PRESENT` / `NOT_REACHABLE` / `UNKNOWN` "rather than `"value": null`".
§680's canonical evidence record then carries `"coverage_ref": null` and §676 carries `"reason_code":
null`.

Read narrowly — §736 is about *values*, §680's nulls are *references* — there is no contradiction.
Read as a model-wide rule, the canonical record is its first counter-example. The distinction is
worth one sentence in §736: **"references may be null; values may not."** Without it, the two
sections are in tension in the one artifact every implementation will copy first.

**Positive counterpart:** §735 is the lineage's strongest statement of `UNKNOWN ≠ PASS` — it lists
the six pipeline stages through which `UNKNOWN` must survive (parse, validate, store, load,
revalidate, export) and names all four prohibited substitutions (`false`, `empty`, `zero`, `PASS`).
This directly answers the audit's *"Absence of an observation must not be confused with absence of
the object being observed."*

### F-22 — PROVED — §681 rejects a literal witness for this audit's own load-bearing rule

§681: evidence MUST be derivable from recorded observations and execution metadata; the system MUST
NOT accept `{"verified": true}` as authoritative evidence without the underlying closure; the
authoritative relation is `Observations + Check semantics + Identity bindings + Execution provenance
↓ Evidence`.

This is `NO EVIDENCE → NO VERIFIED CLAIM` written as a schema-level rejection, and it is the first
time in twelve documents that a *claim-shaped input* is explicitly refused. Read together with §667
(request ≠ evidence), §681 (Boolean ≠ evidence), §697/§698 (runner ≠ verifier), §761 (producer ≠
validator), §764 (import ≠ trust), §1384 (validator result ≠ evidence), v1.2 refuses **six**
classes of pseudo-evidence by name. No prior document refused more than two.

---

### F-23 — PROVED (executed, both directions) — the audited toolchain's `check_fences` returns a false PASS on an unclosed fence and a false FAIL on a valid quoted fence; four of this audit's own artifacts are false-FAILed

This finding was produced by the fence-verification step of this Part, and it is about the **audited
toolchain**, not about v1.2. It is recorded here because v1.2 §697/§698/§761 make
verifier-independence central, and this is the audit's fourth executed instance of the inverse:
a checker whose verdict is not a function of the property it names.

The implementation, verbatim from
`vendor/rfl-ae/skills/markdown-corpus-audit/scripts/auditlib.py:86–89`:

```python
def check_fences(text: str) -> Dict:
    langs = collections.Counter(re.findall(r"^```(\w+)", text, re.M))
    n = text.count("```")
    return {"total": n, "balanced": n % 2 == 0, "langs": dict(langs)}
```

`balanced` is **the parity of a substring count**. It is not a fence parse. Executed probe
(three synthetic specimens, `/tmp/fenceprobe/`, created, measured, deleted):

| Probe | Document | CommonMark depth | Toolchain verdict | Correct verdict |
|---|---|---|---|---|
| A | valid — a fenced block quoting a fence inside it | 0 | `balanced=True` | PASS ✓ |
| B | **invalid** — one unclosed fence **plus** one inline ```` ``` ```` in prose | 1 | `balanced=True` | **false PASS** |
| C | **valid** — a block containing one inner line beginning with ```` ```rust ```` | 0 | `balanced=False` | **false FAIL** |

Applied to this audit's own twelve-document artifacts (all twelve measured, all twelve re-checked with
a CommonMark closing-fence rule — a closer being a bare backtick run of length ≥ 3 with no info
string):

| Artifact | toolchain `total` | toolchain `balanced` | CommonMark parse | Verdict |
|---|---|---|---|---|
| `rfl-ae-prompt-packs-part15.md` | 33 | **False** | balanced | false FAIL |
| `rfl-ae-skills-audit-consolidated.md` | 191 | **False** | balanced | false FAIL |
| `rfl-ae-skills-review-part4.md` | 133 | **False** | balanced | false FAIL |
| `rfl-ae-skills-review-part7.md` | 81 | **False** | balanced | false FAIL |
| the other 32 `audit/*.md` | even | True | balanced | agree |

All four false FAILs share one cause: the document **quotes a specimen fence inside a fence** — which
is what a corpus-audit tool's own users write, and which is exactly what the negative fixtures in this
lineage are for. The documents are valid Markdown; the checker is a counting heuristic.

**Classification: PROVED, both directions executed.** Under the audit's own vocabulary this is a
**verifier failure**, not a verification failure (Part VII's distinction), and it is the same defect
class as `has_prov` (Part XVI): a named predicate whose reported value is not the property. It differs
from the other repository findings in one respect that makes it more urgent than its severity
suggests — **it is the first toolchain defect that this audit's own output already triggers.** Four
of the 36 committed artifacts would be reported as structurally broken by the toolchain that the
lineage proposes to trust.

**Fix:** replace the parity test with the CommonMark closing-fence rule (closer = a line of only
backticks, length ≥ opener length, no info string, no indentation beyond 3 spaces) and report
`{total, opened, closed, unclosed_at[]}` instead of a boolean. The corrected rule was executed in
this Part and returns zero unclosed blocks across all 36 artifacts.

---

## §14 — Corrections, in dependency order

Corrections are ordered so that each one is a prerequisite for the next: nothing here depends on a
later item.

| # | Correction | Depends on | Blocks |
|---|---|---|---|
| C-23.1 | **§749: replace `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` with `EXECUTED_SCOPE ⊆ CLAIMED_SCOPE`** and add the field form `claimed_scope ⊆ executed_scope`. | — | any scope claim; any release gate that evaluates scope; F-6 |
| C-23.2 | **§749: restore the `⊆ AUTHORIZED_SCOPE` link** (or state it as `EXECUTED ⊆ AUTHORIZED ∧ CLAIMED ⊆ EXECUTED`) so the three-set chain of v1.0 §545 is recoverable from one block. | C-23.1 | F-7 |
| C-23.3 | **Namespace the status tokens** before any schema is generated: `execution_status`, `observation_status`, `check_status`, `evidence_status`, and one disambiguation rule for `UNKNOWN` (§669/§677/§701/§717/§736). | — | C-23.4, C-23.5, every generated type; F-1, F-2, F-3 |
| C-23.4 | **Enumerate the observation status vocabulary** in §673/§674 and declare the relation to §728's `OBSERVED` trust level. | C-23.3 | §591's fixture cases; F-3 |
| C-23.5 | **Define `Covered` in §790**, mark F2's row correctly, and add the column to §791's prohibition list if it is a pass-derived projection. | C-23.3 | every coverage report; F-15 |
| C-23.6 | **Link the two manifests**: add `evidence_manifest_digest` to v1.0 §522's release manifest, or add `gate_id`/`conformance_id` to §723 — one direction is enough — and name §724's canonicalization. | — | F-10 |
| C-23.7 | **Fix `fixture_corpus_digest` vs `corpus_digest`**; keep v1.2's spelling and note it is v1.1 §575's digest. | — | F-11 |
| C-23.8 | **Repair §757 to 17 items** (`sequence missing` / `missing observation`) and raise `SHOULD` → `SHALL` with an exemption mechanism. | — | the invalid-evidence fixture family; F-19 |
| C-23.9 | **Unify the error-code spaces** of §679 and §719 and state whether `E_` is a prefix or a namespace. | — | generated error enums; F-20 |
| C-23.10 | **Add the null policy sentence to §736**: references may be null, values may not. | — | §680's canonical record; F-21 |
| C-23.11 | **Give §680's empty identity objects one required key** (`*_digest`) or bind them to the version's schema. | C-23.3 | identity checker for `Closure(E)`; F-9 |
| C-23.12 | **Give the derived claim an identity** (§712) — the same way §658 gave evidence one — so `ClaimId` stops being absent in a document that derives claims. | C-23.3 | claim provenance; carried item |
| C-23.13 | **Restate `Closure(E)`'s eleven conjuncts as a checklist table** in §658 (conjunct → required field), using §680 as the reference row. | — | the first executable closure checker; F-8 |
| C-23.14 | **Move the compiled-pack digest out of the artifact** per v0.4 §102 step 1, using §682 as the model. | — | the lineage's only build-blocking defect; F-12 |
| C-23.15 | **Replace `check_fences`** with a CommonMark closing-fence rule and report unclosed positions rather than a boolean. | — | repository-side; F-23 |

C-23.1 and C-23.3 are the two that must precede everything else: the first decides *what a claim may
say*, the second decides *what a record may store*. Nothing downstream of either can be made correct
while they are wrong.

---

## §15 — Closing judgement

### §15.1 The stage

v1.2 is the **EXECUTION EVIDENCE** stage of the sequence announced in Part XI
(`PROPOSAL → NORMATIVE SPECIFICATION → SCHEMA → REFERENCE IMPLEMENTATION → TEST FIXTURES →
EXECUTION EVIDENCE`). After v1.0 declared conformance and release, v1.1 declared the *population*,
v1.2 declares what the *products of execution* are and which of them may become a claim. The stage
sequence is now complete on paper:

```
  §151–§171  Part XI   PROMPT PACK PROTOCOL              NORMATIVE SPECIFICATION
  §201–§271  v0.7      TYPES                             SCHEMA
  §272–§361  v0.8      REFERENCE IMPLEMENTATION BLUEPRINT REFERENCE IMPLEMENTATION
  §362–§455  v0.9      EXECUTABLE PROTOCOL KERNEL         REFERENCE IMPLEMENTATION
  §456–§555  v1.0      CONFORMANCE / EVIDENCE / RELEASE   TEST + EVIDENCE
  §556–§655  v1.1      FIXTURE CORPUS + CONFORMANCE       TEST FIXTURES
  §656–§800  v1.2      EVIDENCE + EXECUTION RECORD        EXECUTION EVIDENCE
```

Nothing in §0–§800 has been *built*. The specification is complete at the descriptive level and
empty at the artefact level: no schema named by v1.2 exists, and the only executable thing in the
audited repository remains the upstream toolchain — which the audit has shown to be defective in
four ways (`has_prov` inverted, `neg()` unstructured, crash-as-pass, the `997` cosmetic gap).

### §15.2 What v1.2 gets right that no predecessor did

1. **An abstract closure instantiated, 11 of 11** (F-8) — the first countable mapping from a
   specification predicate to a concrete record.
2. **A three-vocabulary status separation stated as a rule** (F-1) — the v0.9 enum defect is
   answered in prose.
3. **Six classes of pseudo-evidence refused by name** (F-22) — `{"verified": true}` is now a schema
   violation.
4. **A cryptographic trust ladder with all four standard false inferences disclaimed** (F-18).
5. **Explicit absence tokens replacing `null`** (F-21) — `NOT_OBSERVABLE`, `NOT_PRESENT`,
   `NOT_REACHABLE`, `UNKNOWN`.
6. **A corpus digest finally consumed downstream** (F-11).
7. **The check-status vocabulary stable across three documents** (F-4) — the one vocabulary in the
   lineage that has stopped moving.

### §15.3 What v1.2 gets wrong

One release-critical inversion (§749, F-6), one provable cross-document contradiction (F-15), one
undeclared vocabulary (§673, F-3), one five-sense overload (`UNKNOWN`, F-2), one manifest relation
left unstated (F-10), and sixteen carried items of which the compiled-pack digest remains the oldest
and most consequential (F-12).

### §15.4 The twelve laws

§800 closes with twelve `NO … → NO …` laws and one central invariant:

```
NO EXECUTION RECORD                     → NO EXECUTION CLAIM
NO SUBJECT IDENTITY                     → NO SUBJECT-SPECIFIC EVIDENCE
NO ARTIFACT IDENTITY                    → NO EXECUTABLE-ARTIFACT CLAIM
NO VERIFIER IDENTITY                    → NO VERIFIER-SPECIFIC EVIDENCE
NO PREDICATE IDENTITY                   → NO STABLE VERIFICATION SEMANTICS
NO OBSERVATION                          → NO OBSERVATION-BASED CLAIM
NO DIGEST                                → NO IMMUTABLE CONTENT IDENTITY
NO EVIDENCE CLOSURE                      → NO VERIFIED CLAIM
NO COVERAGE                              → NO COMPLETE-SCOPE CLAIM
NO INDEPENDENT VALIDATION                → NO RELEASE-GRADE EVIDENCE ASSURANCE
NO CRITICAL MUTATION DETECTION           → NO RELEASE
NO FROZEN EVIDENCE POPULATION            → NO IMMUTABLE RELEASE CLAIM
```

*"EXECUTION PRODUCES FACTS. CHECKS INTERPRET FACTS. EVIDENCE BINDS FACTS TO IDENTITY. COVERAGE BINDS
EVIDENCE TO POPULATION. GATES BIND COVERAGE TO POLICY. RELEASE BINDS THE GATE TO AN IMMUTABLE
ARTIFACT. NO LAYER MAY PRETEND TO BE THE NEXT LAYER."*

The twelve laws are **strictly stronger** than v0.8 §361's eleven and comparable in kind to v0.9
§455's sixteen in one respect that matters: **eleven of the twelve are identity-required laws**, so
each is decidable by a missing field rather than by an interpretation. Eleven of the twelve also name
an object v1.2 has just specified in its own text. The twelfth — `NO FROZEN EVIDENCE POPULATION → NO
IMMUTABLE RELEASE CLAIM` — is the exception: it leans on **v1.1 §575–§576's corpus digest and
immutability rule** ("Once a corpus is frozen for release: …") **and v1.1 §637–§638's discovery
reconciliation and drift rules**, none of which v1.2 restates. That is the first explicit cross-document dependency in
§0–§800, and it is the correct form: v1.2 states the consequence, v1.1 owns the mechanism. A reader
who has only v1.2 cannot evaluate the twelfth law; a reader who has both can.

### §15.5 Architecture

§800 prints the lineage's first end-to-end architecture diagram. Quoted verbatim from the recorded
file:

```
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

The diagram is faithful to the layer separation the document enforces: `EXECUTION` splits into events
and environment and rejoins at `EVIDENCE RECORD`, which is precisely §658's `ExecutionIdentity ∧
EnvironmentIdentity` pair. It also encodes §712's chain — evidence plus coverage precede the claim —
and the claim precedes the gate. **One omission:** the diagram has no edge from
`FROZEN SPECIFICATION` to `EVIDENCE RECORD`, so the `ProtocolIdentity` conjunct of `Closure(E)` has
no picture-level source even though it has a field-level one (`protocol_version`). A dotted edge
labelled *identity* would make the diagram and §658 agree.

### §15.6 Verdict

**v1.2 is the strongest document in the lineage on assurance and the weakest on internal
consistency.** Its assurance content — six refused pseudo-evidence classes, a four-layer trust model,
twelve identity-required laws, an instantiated eleven-conjunct closure, and explicit absence tokens —
is a genuine advance over v1.0 and v1.1, and the check-status convergence shows the specification can
stabilize its own vocabulary once it states the same set three times.

Against that, v1.2 introduces **one release-critical inversion** (§749's symbol), **one provable
cross-document contradiction** (§790's `Covered` column against v1.1 §607), **one undeclared
vocabulary** (§673's observation status), and **its own namespacing defect** (`"status"` and
`UNKNOWN` each carrying five senses). All four are correctable in one editing pass and none require
design work. Two of them — §749 and `Covered` — are the kind of defect this audit exists to find:
each is a single token whose inversion silently licenses exactly the claim the surrounding prose
forbids.

Applied to its own standard, v1.2 fails on one line and passes on eleven: of §800's twelve laws,
`NO EVIDENCE CLOSURE → NO VERIFIED CLAIM` is violated by §749 as written, because the relation that
binds a claim to executed scope is stated backwards in the one place it is stated at all.

**Classification of the document overall: PARTIALLY PROVED** — a specification whose assurance
architecture is sound and whose text requires four corrections before any implementation may be
generated from it.

### §15.7 A defect found while verifying this Part — and why it is not a footnote

Verifying the twelve recorded documents for fence integrity (the check whose result I reported as OK
in Part XXII) turned up a defect **in the audited toolchain**, not in the corpus: `check_fences`
returns a true/false verdict from the parity of `text.count("```")`. Executed probes prove **both**
directions — an unclosed fence plus one inline ```` ``` ```` in prose reports `balanced=True` (false
PASS), and a valid document that quotes a fence inside a fence reports `balanced=False` (false FAIL).
Four of this audit's own 36 artifacts are false-FAILed by that rule and are valid Markdown.

Two consequences that belong in the record rather than in a footnote:

1. **The audit's own output is now a test case for the toolchain.** Every previous repository finding
   (the `has_prov` inversion, `neg()`'s unstructured findings, crash-as-pass, the 997 cosmetic gap)
   was found by probing the toolchain with specimens. This one was found by the toolchain's rule
   being applied to the corpus that documents it. That is a stronger form of the same evidence, and
   it is why `check_fences` joins the fixed-P0 list as **item 7** — classified **high**, not
   critical, because unlike C-1 it fabricates nothing and unlike C-2 it corrupts nothing: it
   misreports a property, in both directions, on documents that are valid.
2. **The earlier "fence check OK" claim stands, with its scope now stated.** It was made with a
   paired-fence reading, and that reading is correct: a CommonMark parse of all 36 artifacts returns
   zero unclosed blocks. Under the toolchain's own rule the same artifacts fail. Both statements are
   true of different tests; conflating them is the `verification failure vs verifier failure`
   distinction this audit has carried since Part VII, and the difference here is entirely a property
   of the checker.

For v1.2 specifically the episode is not incidental: §697 (runner self-report is not verification),
§698 (no self-certification) and §761 (producer ≠ validator) are the specification stating the rule
that this probe violated. A specification that names verifier independence in three sections, sitting
on a toolchain whose fence check can report PASS for an unclosed block, is the exact gap v1.2 exists
to describe — and the reason the corrected fence rule was executed here rather than asserted.

---

*Part XXIII ends. Part XXIV will analyse the next supplied document, or, if the next instruction is
to build, will begin with C-23.1 and C-23.3 — the scope relation and the status namespace — because
nothing downstream of either can be correct while they are wrong.*
