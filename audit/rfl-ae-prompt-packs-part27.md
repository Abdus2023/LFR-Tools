# RFL-AE Prompt Packs — Audit Part XXVII

**Subject:** `RFL-AE v1.6 — Runner Reference Implementation & CLI Contract`, §1401–§1600 (200 sections)
**Recorded verbatim at:** `audit/rfl-ae-runner-reference-implementation-cli-contract-v1.6.md`
**Predecessor:** Part XXVI (v1.5, §1201–§1400)
**Date of analysis:** 2026-09-28
**Method:** every count below was produced by an executed measurement script against the recorded
verbatim files. Section sets, heading texts, enumeration members and identifier sets were extracted
and compared as sets, not sampled. Where a finding rests on a lexical test rather than a semantic
one, that is stated in the finding.

---

## §1 — Scope, method, and one correction to this Part's own first reading

### §1.1 What was measured

| Measurement | Method | Result |
|---|---|---|
| Section range | regex over `^#{1,2}\s*§N` | **§1401–§1600** |
| Section count / contiguity | count, set comparison | **200**, contiguous |
| Separators | `^---$` count | **200** (one per section) |
| Continuity vs v1.5 | v1.5 ends §1400 | **adjacent, no gap** |
| Fences | substring parity + CommonMark walk | even, closed |
| Union across sixteen documents | set union | **§0–§1600, one gap: §200** |

### §1.2 The method note, stated before the findings

This Part's first reading of §1414 — *"the lineage's first typed enum matching the canonical check
vocabulary"* — was **disproved by measurement**. `v0.8 §277` already declares:

```rust
pub enum CheckStatus {
    Pass,
    Fail,
    Error,
    Unknown,
    Skipped,
    Blocked,
}
```

member for member identical to v1.6 §1414. The claim would have been a false positive of exactly the
kind this corpus has recorded seven times: a lexical impression published as a semantic finding. It is
recorded here because the corrected reading is *stronger* than the wrong one: the check-status
vocabulary has been stable for **thirteen documents**, and the finding is not that v1.6 introduces it
but that v1.6 is the **fifth** document to restate it without citing the first.

That correction also produced this Part's organising question: if §1414 is a restatement of v0.8 §277,
how much else of v1.6 is a restatement of v0.8? §3 answers it by measurement.

---

## §2 — Structural census of §1401–§1600

| § | Function | Count |
|---|---|---|
| 1401 | purpose / converted material | 11 items |
| 1402 | implementation-direction chain | 6 stages |
| 1404 | repository tree | **15 crates**, 5 protocol subdirs |
| 1405 | dependency chain | **8 nodes** |
| 1406 | kernel / adapter separation | 7 + 9 |
| 1407 | purity prohibitions | 4 |
| 1409 | strong identifier types | **8** |
| 1414 | `CheckStatus` enum | **6** (= v0.8 §277) |
| 1416–1419 | task, subject, scope, tool types | 6 + 4 + 5 + 5 fields |
| 1420–1439 | executor, observer, checker, evidence, coverage, gate, store traits | 11 traits |
| 1435 | evidence-validation states | **5** |
| 1443 | filesystem store layout | **10 entries** |
| 1452 | `TransitionError` variants | **6** |
| 1457 | `RunStatus` states | **5** |
| 1460 | runner state machine | **12** |
| 1464 | authorization-record fields | 6 |
| 1477 | fixture-mismatch states | 2 |
| 1487 | resource-aware scheduling inputs | 5 |
| 1490 | worker result bindings | 5 |
| 1497 | cache-safety identities | 4 |
| 1502 | event fields | 6 |
| 1520 | exit codes | **8** |
| 1531 | dry-run plan output | 6 |
| 1535 | deterministic-mode freezes | 4 |
| 1542 | configuration precedence levels | 6 |
| 1559 | truncation fields | 3 |
| 1562 | redaction identity fields | 3 |
| 1563 | error classes | **17** |
| 1570 | property-test targets | 6 |
| 1571 | fuzz targets | 6 |
| 1572 | negative-test classes | **5** |
| 1573 | mutation targets | **10** |
| 1583 | recovery states | 5 |
| 1595 | release-verification items | **9** |
| 1599 | release-gate test classes | **10** |
| 1600 | governing laws / central law | **17 laws**, 9-sentence law |

**Shape.** v1.6 is the lineage's **first document written as a construction order**: it does not describe
semantics at all in 150 of its 200 sections — it names types, traits, crates, commands and flags, and
specifies the tests that will establish them. §1570–§1600 in particular is the lineage's first
verification methodology for its *own implementation* rather than for a subject implementation.

---

## §3 — Headline A: the sixteenth document re-issues the eighth

### F-27.1 — PROVED — fourteen section headings in v1.6 are identical to headings in v0.8 §272–§361, and the shared contracts have narrowed

Measured: **14 identical heading texts** across the two documents.

| §1401–§1600 (v1.6) | §272–§361 (v0.8) | What changed |
|---|---|---|
| §1404 Repository Boundary | §273 Repository Boundary | 8 crates → 15 crates; fixture subdirs and `packages/` dropped; `policies/` added |
| §1405 Dependency Direction | §274 Rust Workspace Boundary | 6-node chain → 8-node chain; two crates (**`rfl-validation`, `rfl-transition`**) survive in the chain but vanish from the tree |
| §1409 Strong Identifier Types | §275 Strong Identifier Types | **13 → 8 newtypes**; the prefix-validation law *"Each type SHALL validate its prefix"* with its `TaskId("task:...")` example **is not restated** |
| §1411 Digest Type | §276 Digest Type | `value: String` → `bytes: Vec<u8>`; *"SHALL validate algorithm-specific length"* **not restated** |
| §1414 Closed Status Types | §277 Status Enums | same `CheckStatus`; v0.8's `CoverageStatus` and `AuthorizationDecision` enums **not restated** |
| §1430 Checker Independence | §298 Checker Independence | preserved |
| §1431 Predicate Identity | §299 Predicate Identity | preserved |
| §1432 Evidence Builder | §300 Evidence Builder | preserved |
| §1434 Evidence Validator | §301 Evidence Validator | preserved |
| §1436 Coverage Evaluator | §304 Coverage Evaluator | algorithm `declared − checked` → trait taking `population + results` (§5) |
| §1438 Gate Evaluator | §307 Gate Evaluator | **explicit inputs lost**: `(policy, checks, evidence, coverage)` → opaque `GateInput` |
| §1520 Exit Code Contract | §318 Exit Code Contract | **6 codes → 8, and codes 3 and 4 swap meanings** (§4) |
| §1563 Error Taxonomy | §319 Error Taxonomy | **15 machine codes → 17 type names, no codes** (§4) |
| §1576 Cross-Language Golden Contract | §343 Cross-Language Golden Contract | **divergence classification dropped**: v0.8 required Rust/TypeScript/JSON-Schema results compared and classified `EXPECTED` / `BUG` / `SPECIFICATION_AMBIGUITY` / `IMPLEMENTATION_DIVERGENCE`; v1.6 requires only that alternate implementations consume the same fixtures |
| §1570 Property Tests | §331 Property Tests | **8 targets → 6**: `reference integrity` and `evidence invalidation` are dropped — the two that §1573's `digest → unchecked` mutation exercises |
| §1569 Miri | §334 Miri | **the evidence record is dropped**: v0.8 required six recorded fields (tool identity, Rust version, Miri version, commit, test selection, result) and the rule *"A local Miri run is execution evidence for that run. It is not automatically CI evidence"*; v1.6 keeps one sentence |
| §1572 Negative Tests | §286 Canonicalization Negative Tests | scope widened from canonicalization to all validators — a genuine extension |

**Classification: PROVED.** The heading set is extracted from both files; the content deltas are
quoted from both.

**Why it matters, in the lineage's own terms.** The README's stage sequence declared eight stages, of
which stage 4 is `REFERENCE IMPLEMENTATION` — occupied by v0.8. v1.6 occupies it a second time, under
a new name (`Runner Reference Implementation & CLI Contract`), without a supersession statement and
without citing v0.8 §273–§343 in any of the fourteen shared sections. The lineage therefore now has
**two normative reference-implementation contracts in force**, and **eleven** of the contracts they
share have narrowed — identity sets, prefix validation, digest representation and length validation,
the `CoverageStatus`/`AuthorizationDecision` enums, gate inputs, exit codes, error codes, property-test
targets, the Miri evidence record, cross-language divergence classification, and the repository
tree.

```
v0.8 §272–§361  REFERENCE IMPLEMENTATION BLUEPRINT ─┐
                                                    ├── same layer, two documents, no ledger entry
v1.6 §1401–§1600 RUNNER REFERENCE IMPL. & CLI ──────┘
```

Per the supersession ledger's three buckets, this is a **fourth bucket** the ledger does not yet
have: **re-issued with loss** — later text that neither closes, supersedes, nor merely restates, but
replaces a contract with a weaker one. Eleven instances are tabulated above. **C-27.1** proposes adding it.

---

## §4 — Headline B: four contracts that v1.6 states less strictly than v0.8

### F-27.2 — PROVED — the strong-identifier set shrank from 13 to 8, and v1.6's eight share only five with v0.8's and three with v1.4's

| Source | Members | Count |
|---|---|---|
| v0.8 §275 | TaskId, SubjectId, ScopeId, AuthorityId, ToolId, ToolRequestId, ExecutionId, ObservationId, CheckId, CheckResultId, EvidenceId, CoverageId, GateId | **13** |
| v1.4 §1194 | SubjectId, ArtifactId, FixtureId, EvidenceId, CoverageId, ClaimId, ReleaseId, Digest | **8** |
| **v1.6 §1409** | FixtureId, ExecutionId, AttemptId, CheckId, EvidenceId, CoverageId, GateId, PlanDigest | **8** |

Measured overlaps: v1.6 ∩ v0.8 = **5** (`ExecutionId`, `CheckId`, `EvidenceId`, `CoverageId`,
`GateId`). v1.6 ∩ v1.4 = **3** (`FixtureId`, `EvidenceId`, `CoverageId`).

Two losses are load-bearing:

1. **v0.8 §275's prefix-validation law is gone.** v0.8 required each identifier type to validate its
   prefix and gave the example that separates `TaskId("task:...")` from `TaskId("execution:...")` — a
   *runtime* identity check. v1.6 §1409 gives eight tuple structs and no validation rule, so the
   types are non-interchangeable to the compiler but not verifiable at a boundary. §1410 states the
   non-substitution property as `FixtureId ≠ ExecutionId` / `ExecutionId ≠ EvidenceId`, exercising
   **3 of the 8** declared types.
2. **`ClaimId` and `ReleaseId` are dropped** from the strong-ID set that v1.4 §1194 introduced — in
   the document that also introduces `rfl release verify` (§1595) and `RunResult.manifest` (§1456).
   v0.8 §275 carried `ClaimId`? — it carried `ToolRequestId` and `CheckResultId`, not `ClaimId`; the
   claim/release identifiers exist only in v1.4 §1194. So the lineage's newest strong IDs are the
   ones v1.6 leaves out, while §1600's first law is `NO STRONG IDENTITY → NO SAFE CORE API`.

**Fix (C-27.2):** take v0.8 §275 as the union basis, state the set once, and restore the
prefix-validation requirement.

### F-27.3 — PROVED — the two exit-code contracts disagree on six of their members and **swap the meanings of 3 and 4**

| Code | v0.8 §318 | v1.6 §1520 |
|---|---|---|
| 0 | command completed / requested predicate passed | command completed with requested semantic operation successful |
| 1 | requested predicate failed | semantic operation produced failure/non-conformance |
| 2 | usage/schema error | invalid CLI usage |
| **3** | **execution error** | **blocked/precondition unavailable** |
| **4** | **blocked/unauthorized** | **internal execution error** |
| 5 | infrastructure error | unknown/incomplete result |
| 6 | — | stale/identity mismatch |
| 7 | — | cancelled |

Both documents end with the same self-deferral — v0.8: *"The exact mapping SHALL be frozen before
release"*; v1.6: *"Exact numeric values MUST be frozen before release."* Thirteen documents apart,
neither frozen, and a script written against v0.8's table reads v1.6's code 3 as execution failure
when the runner means *blocked*.

**Classification: PROVED.** **Fix (C-27.3):** freeze the table in one place and delete the other, or
state that v1.6 supersedes §318 by name.

### F-27.4 — PROVED — the error taxonomy loses its machine codes: v0.8's fifteen coded errors become v1.6's seventeen type names, and §1564 then demands codes that no list supplies

| Source | Form | Members |
|---|---|---|
| v0.8 §319 | machine codes, with the stated purpose *"so negative tests can assert the actual failure class"* | **15**: `INPUT_INVALID`, `SCHEMA_INVALID`, `SEMANTIC_INVALID`, `UNAUTHORIZED`, `OUT_OF_SCOPE`, `TOOL_UNAVAILABLE`, `EXECUTION_ERROR`, `OBSERVATION_ERROR`, `CHECK_ERROR`, `EVIDENCE_INVALID`, `EVIDENCE_STALE`, `COVERAGE_INCOMPLETE`, `TRANSITION_INVALID`, `REPLAY_INVALID`, `INTERNAL_ERROR` |
| v1.6 §1563 | Rust type names | **17**: `ConfigurationError` … `InternalError` |
| v1.6 §1564 | — | *"Errors MUST contain stable machine-readable codes."* — **no code list follows** |

Measured: **0** members of §1563's list are machine codes; **0** `E_`-prefixed codes appear in v1.6;
and the two validation kinds §1448/§1449 introduce (schema validity, semantic validity) have **no
class in the taxonomy** — v0.8's `SCHEMA_INVALID` and `SEMANTIC_INVALID` are both dropped.
`TransitionError` is declared as an enum in §1452 and is **absent from §1563's taxonomy**.

This is the fourth document in a row in which the error space is restated with a different form
(v1.2 §679 `E_`-codes, v1.2 §719 bare codes, v1.4 §1198 `E_`-codes, v1.6 §1563 type names) while the
requirement that errors be machine-readable never changes. **C-27.4:** publish one registry, keyed by
code, and let type names be the projection.

### F-27.5 — PROVED — `Digest` keeps its name and changes its representation; the algorithm-length rule is dropped

```rust
// v0.8 §276
pub struct Digest { pub algorithm: DigestAlgorithm, pub value: String }
// v1.6 §1411
struct Digest { algorithm: DigestAlgorithm, bytes: Vec<u8> }
```

`value: String` and `bytes: Vec<u8>` are not interchangeable, and v0.8's accompanying requirement —
*"The implementation SHALL validate algorithm-specific length"* — is absent from v1.6. §1412 fixes
`SHA-256` as the initial required algorithm and requires algorithm identity to be preserved, which
makes the missing length rule the natural companion check. Also absent: any declaration of
`DigestAlgorithm` (referenced twice, declared nowhere in v1.6), so §1415's forward-compatibility rule
— unknown values must be represented as an explicit unknown/unsupported state — has no target for the
digest algorithm field in this document.

---

## §5 — Headline C: typed interfaces that cannot express what the normative layers require

### F-27.6 — PROVED — `CoverageEvaluator` receives `population + results` and never receives evidence, so it cannot establish coverage as v1.3 §842 defines it

```rust
trait CoverageEvaluator {
    fn evaluate(&self, population: &FixturePopulation, results: &[CheckResult]) -> CoverageRecord;
}
```

v1.3 §842 defines the two terms the lineage has used ever since: *"coverage = evidence exists;
conformance = required evidence establishes required result."* v1.5 §1260 requires evidence validity
to be established before coverage, and §1265 lists eleven coverage observables. The v1.6 signature
admits **no `EvidenceRecord`** and no way to reach one: an implementer writing `evaluate` against this
trait cannot check that evidence exists, only that a check result exists.

The contrast with v0.8 is instructive rather than exculpatory: v0.8 §307's gate went the *other* way,
naming its evidence input explicitly —

```rust
fn evaluate_gate(policy: &GatePolicy, checks: &[CheckResult],
                 evidence: &[EvidenceRecord], coverage: &CoverageRecord) -> GateResult;
```

— and v1.6 §1438 replaces that with an opaque `GateInput` that is never declared. So the lineage has
one trait that cannot see evidence (coverage) and one whose evidence visibility is now hidden behind a
name (gate).

Additionally, `CoverageEvaluator::evaluate` and `GateEvaluator::evaluate` return **no `Result`**: unlike
`Executor`, `Checker`, `EvidenceBuilder`, `FixtureLoader`, `ManifestLoader`, `Planner` and `Scheduler`
— all of which return `Result<_, _>` — the two evaluators can only return a record. If the evaluator
itself cannot run (population unreadable, store unavailable), that fact must be encoded inside
`CoverageRecord`/`GateResult`, and v1.6 never says which state encodes it. The lineage's own law is
*"A check that cannot run must never report success"*; at the type level, v1.6 gives these two checks
no failure channel at all.

**Classification: PROVED** (the signature and the two normative definitions are quoted verbatim).
**C-27.5:** pass the evidence population into `CoverageEvaluator`, and give both evaluators a `Result`
whose error variant maps to `BLOCKED`/`UNKNOWN` per v1.5 §1311 and v1.3 §802.

### F-27.7 — PROVED — `RunMode` is referenced once and declared nowhere, while the CLI exposes five mode switches and v1.5 defines a three-member mode vocabulary

| Source | Mode vocabulary |
|---|---|
| v1.5 §1364 | `EXPLORATORY`, `CONFORMANCE`, `RELEASE` |
| v1.6 §1455 | `mode: RunMode` — **the only occurrence of the name in the document** |
| v1.6 §1530–§1540 CLI | `--dry-run`, `--offline`, `--deterministic`, `--ci`, `--cache`/`--no-cache` (plus `--jobs`, `--seed`, `--retry`) |

So the type that is supposed to carry the run mode is undefined, five CLI switches are free-floating
with no stated composition, and the three normative modes of v1.5 §1364 are not among them.
`rfl run --ci --dry-run --offline --deterministic` is a legal command line whose semantics no
document defines.

**C-27.6:** declare `enum RunMode` with v1.5 §1364's three members, and map each CLI switch to a
field or a mode rather than leaving them as independent booleans.

### F-27.8 — PROVED — §1415's forward-compatibility rule is unsatisfiable for `RunStatus`

§1415: *"The semantic layer MUST represent them as an explicit unknown/unsupported state."*

| Enum | Has an explicit unknown member? |
|---|---|
| `CheckStatus` (§1414) | **yes** — `Unknown` |
| `RunStatus` (§1457) | **no** — `Completed`, `Partial`, `Blocked`, `Error`, `Cancelled` |
| `TransitionError` (§1452) | **no** |
| `DigestAlgorithm` (§1411) | not declared |

A serialized future `RunStatus` variant therefore has no legal representation, and the only remaining
options are the two §1415 forbids. **MINOR-to-substantive:** the rule is right, its application was
not checked against the enums the same document declares. **C-27.7:** state the unknown member for
every closed enum, or make §1415 apply only to enums that declare one.

---

## §6 — Headline D: the crate graph is stated three times and contradicts itself inside v1.6

### F-27.9 — PROVED — §1404's tree and §1405's chain disagree about two crates and nine

| §1404 tree (15 crates) | §1405 chain (8 nodes) |
|---|---|
| rfl-protocol, rfl-types, rfl-schema, rfl-canonical, rfl-authorization, rfl-executor, rfl-observation, rfl-check, rfl-evidence, rfl-coverage, rfl-conformance, rfl-gate, rfl-runner, rfl-persistence, rfl-cli | rfl-types → rfl-protocol → rfl-canonical → **rfl-validation** → **rfl-transition** → *domain engines* → rfl-runner → rfl-cli |

- In the chain but **not in the tree**: `rfl-validation`, `rfl-transition` (2).
- In the tree but **not in the chain**: `rfl-schema`, `rfl-authorization`, `rfl-executor`,
  `rfl-observation`, `rfl-check`, `rfl-evidence`, `rfl-coverage`, `rfl-conformance`,
  `rfl-persistence` (9).
- `domain engines` is not a crate name at all.

`rfl-persistence` is the omission that matters: §1439 declares `EvidenceStore`, §1442 requires an
atomic commit boundary, §1443 specifies a filesystem layout and §1589 requires locking — all four
requirements attach to a crate that §1405's acyclic dependency chain does not contain.

And the chain is itself a **reordering** of v0.8 §274's, whose chain begins at `rfl-protocol` (the
crate that owns the protocol types) and runs `rfl-protocol → rfl-validation → rfl-transition →
rfl-evidence → rfl-coverage → rfl-gate`. v1.6 §1405 inserts `rfl-types` ahead of `rfl-protocol`, keeps
the two crates its own tree deleted, inserts `domain engines` where three concrete crates belong, and
truncates before the gate.

```
v0.8 §273 tree:  8 crates  ──┐
v0.8 §274 chain: 6 nodes   ──┼── same object, now stated three times, three ways
v1.6 §1404 tree: 15 crates ──┤
v1.6 §1405 chain: 8 nodes  ──┘   (and the two v1.6 statements disagree with each other)
```

**C-27.8:** one tree, one chain, generated from it, with the crate count asserted so the two lists
cannot drift.

---

## §7 — Headline E: the release closure is now stated four times, in four different term sets

### F-27.10 — PROVED — the list of what a release claim closes over has been re-specified four times, and no two agree

| Source | Name | Terms | Has Manifest? | Has Policy? | Has Protocol? |
|---|---|---|---|---|---|
| v1.4 §1116 | `ReleaseClosure` | **9**: Protocol, Subject, Artifact, Population, Evidence, Coverage, Conformance, Gate, Manifest | **yes** | no | yes |
| v1.4 §1195 | `evaluate_release(...)` | **5** arguments: candidate, policy, coverage, conformance, evidence | no | yes | no |
| v1.5 §1388 | release-grade claim closure | **14**: Protocol, Subject, Source, Artifact, Build, Fixtures, Execution, Observation, Checks, Evidence, Coverage, Conformance, Gate, Policy | **no** | yes | yes |
| **v1.6 §1595** | `rfl release verify` items | **9**: manifest digest, subject, artifact, fixtures, evidence, coverage, conformance, gate, policy | **yes** (as digest) | **yes** | no |

Read as a matrix: v1.4 §1116 and v1.6 §1595 are both nine-term lists sharing seven terms, differing
by `Protocol` (v1.4 only) and `Policy` (v1.6 only). v1.5 §1388 is the fourteen-term superset that
drops `Manifest`. So the three nine-term-ish lists form a cycle of disagreements, and the tool that
is supposed to check them — `rfl release verify` — is defined by the one that omits `Protocol`.

This is Part XXVI's F-26.5 extended rather than repeated: the finding is no longer *"two closures
disagree about the manifest"* but **"the release closure has four specifications, and a verifier
implemented from §1595 will not check the protocol identity that v1.4 §1116 and v1.5 §1388 both
require."**

**C-27.9:** make §1595's verify list a *superset* of §1116's closure, or declare §1595 the
authoritative closure and mark §1116 and §1388 superseded by name.

### F-27.11 — PROVED — the store layout drifted from v1.5 §1398 in four ways, two of them renames

| v1.5 §1398 (12 outputs) | v1.6 §1443 (10 entries) |
|---|---|
| `run.json` | — **missing** |
| `plan.json` | `plan.json` |
| `schedule.json` | — **missing** |
| `execution/*.json` | `executions/` |
| `observation/*.json` | `observations/` |
| `check/*.json` | `checks/` |
| `evidence/*.json` | `evidence/` |
| `coverage.json` | `coverage.json` |
| `conformance.json` | `conformance.json` |
| `gate.json` | `gate.json` |
| `release-manifest.json` | `manifest.json` — **renamed** |
| `event-ledger.jsonl` | `events.jsonl` — **renamed** |

`rfl release verify <manifest>` (§1595) must locate a manifest file the two documents name
differently, and `run.json` — the artifact a resumed run (§1526) would read — is in the v1.5 set and
not the v1.6 set, while §1526 requires resume to "load persisted state". **C-27.10:** name the
on-disk artifact set once, as part of the persistence contract, and have §1398 and §1443 both point
at it.

---

## §8 — What v1.6 gets right that no predecessor did

### F-27.12 — PROVED (positive) — §1566 prohibits, by name, the exact class of defect this audit found in the toolchain at Part VII

> *"A panic or unhandled exception MUST NOT be interpreted as a semantic fixture failure. It is an
> implementation error unless explicitly converted by a declared boundary."*

Part VII §0.2 recorded a crash-as-pass path in the audited pipeline: a checker that died produced a
success-shaped result because nothing distinguished *the check failed* from *the check never ran*.
v1.6 §1566 states the prohibition, §1563 gives `InternalError` a class, and §1583 requires recovery to
distinguish `never started` from `started` from `completed` from `cancelled` from `unknown`. The one
residual gap is that no taxonomy member is `Panic` — §1566's "declared boundary" is left to the
implementer — so the discipline is stated and the type is not. **C-27.11** covers it.

### F-27.13 — PROVED (positive) — §1593 makes artifact presence non-evidentiary

> *"Presence of a file named `completed.json` MUST NOT establish completion by itself. Completion is
> derived from validated state."*

This is the audit's own `representation ≠ semantics ≠ evidence` law, stated for the store, and it sits
three sections after §1584 (*"a crash during persistence MUST NOT produce a record that validates as
complete evidence unless all required bytes were durably committed"*) and five sections after §1572's
near-invalid fixtures. Read with §1444 (*"filenames MUST NOT be the sole source of identity"*) and
§1587 (partial files rejected, not repaired), v1.6 is the first document in the lineage to specify a
persistence layer whose failure modes are named rather than assumed.

### F-27.14 — PROVED (positive) — the CLI's authorization preview separates what was decided from what was done

> §1533: *"It MUST distinguish `would_authorize` from `authorized_and_executed`."*

That pair is the operational form of `PROPOSED ≠ AUTHORIZED ≠ EXECUTED` — the distinction Part XII
required, Part XIII's `Precedence alone must never grant authority` implies, and v1.5 §1215 stated as
`NO AUTHORIZATION → NO DISPATCH`. §1532 adds *"A dry run MUST NOT create `EXECUTED` records"*, §1465
adds that a denied request may leave a decision record but not an execution record, and §1534 requires
the offline mode's network denial to be *enforced* rather than documented — which is the audit's
*"a check that cannot run must never report success"* in the shape of a network policy.

### F-27.15 — PROVED (positive) — coverage discovery is now prohibited by name, §1437

> *"Coverage MUST use the declared fixture population. The evaluator MUST NOT silently replace it with
> filesystem discovery."*

The audit's own C-1 defect — discovery treated as population, so the denominator of every conformance
claim was whatever happened to be on disk — is prohibited at the trait level and again at the CLI
level (the plan is built from `--manifest`, §1522). Twelve documents after the defect was recorded, it
is now unreachable by a conforming implementation, provided §1436's signature is fixed (F-27.6).

### F-27.16 — PROVED (positive) — the runner lifecycle is restated with zero drift, and the check vocabulary with zero drift

| Object | v1.5 | v1.6 | Drift |
|---|---|---|---|
| runner lifecycle (12 states) | §1208 `DECLARED … COMPLETED` | §1460 `DECLARED … COMPLETED` | **none** — member-for-member identical |
| check statuses (6) | §1250 | §1414 (as a Rust enum) | **none** |
| terminal run outcomes (3) | §1208 `BLOCKED`, `ERROR`, `CANCELLED` | §1457 `Blocked`, `Error`, `Cancelled` (+ `Completed`, `Partial`) | names carried; two additions |

Two documents, 400 sections apart, and the state machine reproduces exactly. That is the property the
lineage has failed to achieve for its status tokens, its record fields and its formal variables — and
it demonstrates that the failure is not inherent to additive specification but specific to the
vocabularies no document has been made responsible for. **C-27.12:** adopt the lifecycle's discipline
for the status and error vocabularies — one declarer, everyone else cites.

### F-27.17 — PROVED (positive) — the verification methodology is the first in the lineage to require generative testing of the runner itself

| Method | § | Targets |
|---|---|---|
| property tests | 1570 | 6 — canonicalization, digest stability, transition closure, coverage, gate evaluation, identifier parsing |
| fuzzing | 1571 | 6 — manifest parser, fixture parser, canonicalizer, schema decoder, CLI parser, event decoder |
| negative fixtures | 1572 | 5 classes incl. **near-invalid** and **identity mismatch** |
| mutation testing | 1573 | 10 mutations, of which two are the ones v1.5 could not detect |
| golden fixtures | 1575 | input / canonical output / digest |
| generator verification | 1579 | generator tested independently of generated output |
| CLI conformance | 1580–1581 | 5 categories, 5 injection classes |
| release gate | 1599 | 10 test classes as a precondition of being "conformance-grade" |

Across fifteen documents the lineage specified what evidence must exist; this is the first that
specifies **how to obtain it**, and the first to require that the runner be fuzzed, mutated and
independently generated against. Part XXVI's F-24.8/F-25.6 — the corpus missing the two near-miss
fixtures that make `BLOCKED→PASS` and `UNKNOWN→PASS` detectable — is hereby **closed at the
requirement level**: §1573 names exactly those two mutations, §1572 adds the near-invalid class, and
§1574+§1599 make detection a gate item. It remains open at the corpus level, which is a different
artifact.

---

## §9 — Remaining findings

### F-27.18 — MINOR — the exit codes merge four pairs of states that the same document requires be distinguished

| Code | Merged states | Distinguished at |
|---|---|---|
| 5 | `unknown` / `incomplete` | §1435 (`UNKNOWN` and `INCOMPLETE` are separate validation states) |
| 6 | `stale` / identity mismatch | §1431 (predicate mutation changes identity), §1477 (`INVALID` vs `STALE`) |
| 1 | failure / non-conformance | v1.5 §1250 (`FAIL` vs `BLOCKED` vs `ERROR`), §1273 (four non-conformant states) |
| 3 | blocked / precondition unavailable | v1.5 §1216–§1217 (*failed* vs *could not execute*) |

§1520 says exit codes "MUST represent CLI invocation state, not replace protocol result state" and
§1521 repeats it — so collapsing four pairs is defensible at the CLI. What is missing is the mapping
from exit code to the protocol states it summarises, which is what an automated caller needs in order
not to reconstruct §1520's intent from prose. **C-27.3** covers the table; this finding adds that the
mapping must be many-to-one *and declared*.

### F-27.19 — MINOR — "strict mode" is named once and defined nowhere

§1543: *"Unknown configuration keys SHOULD cause an error in strict mode. Silently ignoring a typo in
a security-relevant option is prohibited."* No section defines strict mode, and no CLI flag selects
it — §1535's `--deterministic` is the nearest candidate but is specified as freezing schedule policy,
randomness, canonicalization and environment inputs, not as rejecting unknown keys. §1542's precedence
chain also contains a level named *"explicit authorized CLI"*, which puts the word *authorized* inside
a precedence ordering; v0.2's law is *"Precedence alone must never grant authority"*, so the level's
name should say what it is — an operator-supplied override — and the authorization should be a
separate, earlier decision. **C-27.12** covers both.

### F-27.20 — MINOR — §1583's recovery states are prose phrases with no tokens, in a lineage where every other state set has tokens

`never started`, `started`, `completed`, `cancelled`, `unknown` — five recoverable distinctions, all
valuable (the first being the one Part VII found missing), none of them a declared token, none typed,
and no section says how they relate to `RunStatus` (§1457) or to the 12-state lifecycle (§1460) which
is optimized away in exactly this situation ("a state MAY be internally optimized away", §1461). An
implementer must invent five names, which is how the `UNKNOWN` census reached nine senses by v1.5.

### F-27.21 — NOTE — §1461's optimization clause and §1460's state machine interact with §1592 in a way worth stating explicitly

§1461 permits a state to be "internally optimized away" if all required semantic records are still
produced; §1592 requires that *if* intermediate states are persisted, each has a valid schema and an
explicit lifecycle state. So the two sections jointly define an implementation's freedom: skip the
state, keep the record; or keep the state, type it. This is coherent and is recorded as a positive
(NOTE) because it is the first place in the lineage where an optimisation's *evidence obligations* are
constrained rather than its performance.

---

## §10 — Carried items: the sixteenth-document census

| Item | First raised | Status in v1.6 |
|---|---|---|
| compiled-pack digest (v0.4 §86) | Part XV | `CompiledPack` = **0**, `compiled_digest` = **0** — sixteenth document |
| `EnvironmentAuthority` | Part XIII | **0** — sixteenth document |
| `evidence.schema.json` | Part XV | **0** — sixteenth document |
| `ClaimId` / `ReleaseId` as strong types | Part XX (supplied by v1.4 §1194) | **0** in v1.6 — the only document to have them is v1.4 |
| namespaced status tokens (C-23.3) | Part XXIII | not applied; `UNKNOWN` now also a validation state (§1435) and a forward-compatibility target (§1415) |
| v1.2 §749 inverted `⊇` | Part XXIII | superseded in substance (v1.4 §1071, v1.5 §1389); **not restated in v1.6** |
| v0.4 §86 exclusion rule for the compiled pack | Part XV | v1.6 §1446 restates the digest discipline without naming the artifact; **open** |
| `Covered(f)` state assignment | Part XXIV | v1.6 §1436 replaces the question with a trait signature that cannot reach evidence (F-27.6) |
| mutation detection of `BLOCKED→PASS`/`UNKNOWN→PASS` | Part XXIV | **closed at requirement level** (§1573, §1574, §1599); corpus still unspecified |
| v1.4 §1116 vs v1.5 §1388 closure | Part XXVI | extended: now four statements (§7) |

The three oldest items have now survived **sixteen** documents. Their status is no longer "carried" in
any meaningful sense: the compiled pack has no digest specification in any document after v0.4, and
the exclusion rule that v0.4 §86's defect called for is now stated four times for four *other*
artifacts (§5 of Part XXVI, §7 here) — which is the strongest available evidence that the rule was
learned and simply never applied to the artifact that prompted it.

---

## §11 — Corrections, in dependency order

| # | Correction | Depends on | Blocks |
|---|---|---|---|
| C-27.1 | **Add a fourth ledger bucket — *re-issued with loss*** — and record v0.8 §272–§361 ∩ v1.6 §1401–§1600 in it (14 shared headings, six narrowed contracts). | — | every later stage that assumes one implementation contract |
| C-27.2 | **Restore v0.8 §275's union of strong identifiers (13) and its prefix-validation law**; keep v1.6's additions (`FixtureId`, `AttemptId`, `PlanDigest`) and v1.4 §1194's `ClaimId`/`ReleaseId`. | — | F-27.2, §1600's first law |
| C-27.3 | **Freeze one exit-code table** (v1.6 §1520's), declare §318 superseded, and publish the exit-code→status mapping. | C-27.1 | F-27.3, F-27.18 |
| C-27.4 | **Publish one machine-code error registry**; make §1563's type names its projection; restore `SCHEMA_INVALID`/`SEMANTIC_INVALID` classes and place `TransitionError` in the taxonomy. | — | F-27.4, negative-test assertions |
| C-27.5 | **Give `CoverageEvaluator` the evidence population and both evaluators a failure channel.** | — | F-27.6, v1.3 §842 conformance |
| C-27.6 | **Declare `RunMode`** with v1.5 §1364's three members and map each CLI switch to a field or a mode. | — | F-27.7 |
| C-27.7 | **Give every closed enum an explicit unknown member**, or scope §1415 to enums that declare one. | — | F-27.8 |
| C-27.8 | **Generate §1405's chain from §1404's tree**; assert the crate count; name the persistence crate in the chain. | — | F-27.9 |
| C-27.9 | **Reconcile the four release-closure lists** — one authoritative closure, the others citing it. | — | F-27.10, `rfl release verify` |
| C-27.10 | **Name the run's on-disk artifact set once** and have v1.5 §1398 and v1.6 §1443 both cite it. | C-27.8 | F-27.11, resume and verify |
| C-27.11 | **Add a `Panic`/`InternalFailure` code** to the taxonomy so §1566's declared boundary has a type. | C-27.4 | F-27.12 residual |
| C-27.12 | **Define *strict mode* and rename §1542's *"explicit authorized CLI"* level** to an override the operator supplies; authorization stays a separate decision. | — | F-27.19 |
| C-27.13 | **Tokenise §1583's five recovery states** and relate them to `RunStatus` and the lifecycle. | C-27.6 | F-27.20 |

---

## §12 — Closing judgement

### §12.1 The stage, ninth

v1.6 appends a ninth stage to the declaration the lineage has been executing:

```
§151–§171   Part XI  PROMPT PACK PROTOCOL                NORMATIVE SPECIFICATION
§201–§271   v0.7     TYPES                                SCHEMA
§272–§361   v0.8     REFERENCE IMPLEMENTATION BLUEPRINT   REFERENCE IMPLEMENTATION  ◀── first
§362–§455   v0.9     EXECUTABLE PROTOCOL KERNEL            REFERENCE IMPLEMENTATION
§456–§555   v1.0     CONFORMANCE / EVIDENCE / RELEASE      TEST + EVIDENCE
§556–§655   v1.1     FIXTURE CORPUS + CONFORMANCE          TEST FIXTURES
§656–§800   v1.2     EVIDENCE + EXECUTION RECORD           EXECUTION EVIDENCE
§801–§997   v1.3     COVERAGE + CONFORMANCE AGGREGATION    DERIVATION
§998–§1200  v1.4     RELEASE GATE + MANIFEST               BOUNDARY
§1201–§1400 v1.5     CONFORMANCE RUNNER + ORCHESTRATION    EXECUTION
§1401–§1600 v1.6     RUNNER REFERENCE IMPL. + CLI          REFERENCE IMPLEMENTATION  ◀── second
```

Stage 4 is occupied twice, thirteen documents apart, by two documents that never cite each other, and
whose contracts differ in eleven measurable places. That is this Part's principal result: the lineage's
*stage* sequence is declared to be a progression, but its *implementation* layer is a re-entry, and no
mechanism in the lineage detects re-entry because nothing is responsible for the boundary between a
blueprint and its successor.

### §12.2 The pattern, stated plainly

Across Parts XXIV, XXV, XXVI and now XXVII the same structure has appeared in four different
vocabularies:

| Part | Artifact | Failure |
|---|---|---|
| XXIV | coverage states | a partition invariant with no state-assignment function |
| XXV | gate states | five enumerations of one object disagreeing |
| XXVI | status tokens | nine senses of `UNKNOWN` in one document; eight new vocabularies |
| **XXVII** | **implementation contracts** | **14 headings re-issued, six contracts narrowed, no ledger entry** |

Each Part's closing judgement has said the same thing in a different key: the lineage's **semantics
converge** while its **identifiers, vocabularies and contracts diverge**. v1.6 is the sharpest
instance yet, because the divergence is now *within one document* (§1404 vs §1405) and *across two
documents of the same stage* (§3–§4), and because the remedy is not a new concept but an existing one
used consistently — the lifecycle discipline of §1460, which reproduces v1.5 §1208 exactly.

### §12.3 What v1.6 gets right

1. **The verification methodology.** Property tests, fuzzing, near-invalid and stale fixtures,
   mutation detection, independent generator verification, CLI injection tests, and a ten-class
   implementation release gate (§1570–§1599). Nothing in fourteen prior documents specifies how to
   obtain evidence; this one does.
2. **The panic boundary** (§1566) and **no-fake-completion** (§1593) — the audit's Part VII and Part V
   findings, prohibited by name.
3. **Authorization preview** (§1533) and **enforced offline mode** (§1534).
4. **Coverage authority** (§1437) — the audit's C-1 defect, closed at the trait level.
5. **Zero drift in the lifecycle and check vocabularies** (§1460 = v1.5 §1208; §1414 = v0.8 §277),
   proving the lineage can hold a vocabulary stable when a document is made responsible for it.
6. **§1563's error taxonomy** is a genuine improvement in one respect: seventeen classes are more
   usefully typed than fifteen codes — the defect is dropping the codes rather than adding classes.

### §12.4 What v1.6 gets wrong

Two normative implementation contracts in force with no supersession (F-27.1); a strong-identifier set
that shrank from 13 to 8 and lost its validation rule (F-27.2); exit codes that swapped meanings
(F-27.3); an error taxonomy that lost its machine codes and lost the two validation classes the same
document requires (F-27.4); a `Digest` that changed representation and lost its length rule (F-27.5);
a coverage trait that cannot see evidence and two evaluators that cannot report failure (F-27.6); an
undeclared `RunMode` behind five CLI switches (F-27.7); a forward-compatibility rule unsatisfiable for
the enum that needs it (F-27.8); a crate graph that contradicts itself (F-27.9); four release-closure
lists (F-27.10); a store layout with two renames and two omissions (F-27.11).

### §12.5 The law sets

| Document | § | Laws | Character |
|---|---|---|---|
| v0.8 | 361 | 11 | implementation-shaped |
| v0.9 | 455 | 16 | type-and-engine-shaped |
| v1.2 | 800 | 12 | identity-required |
| v1.3 | 997 | 10 | derivation-required |
| v1.4 | 1200 | 15 | boundary-required |
| v1.5 | 1400 | 15 distinct / 17 lines | stage-required |
| **v1.6** | **1600** | **17, all distinct** | **layer-required: each law forbids a component from assuming another layer's authority** |

Measured against v1.5 §1400: **7 laws identical**, 4 new (`NO STRONG IDENTITY → NO SAFE CORE API`,
`NO FIXTURE DIGEST MATCH → NO VALID FIXTURE EXECUTION`, `NO BOOLEAN SUMMARY → NO AUTHORITATIVE
CONFORMANCE`, `NO RUNNER SELF-ASSERTION → NO INDEPENDENT ASSURANCE`), and the rest restated with
altered conditions or targets — `NO EVIDENCE BINDING → NO VERIFIED CLAIM` becomes `NO EVIDENCE CLOSURE
→ NO VERIFIED CLAIM`; `NO PLAN → NO CONFORMANCE EXECUTION` becomes `NO PLAN → NO RUN`. And the law
§1400 repeated **three times**, `NO EVIDENCE → NO VERIFIED CLAIM`, appears in §1600 **not at all**.
Seventeen documents' worth of law text, continuously reworded, with no ledger mapping one set to the
next. **C-27.1** is the correction that would end it.

### §12.6 Verdict

**v1.6 is the most implementable document in the lineage and the most duplicative.** Its typed
interfaces, CLI contract and test methodology are what a team would need in order to begin — and they
are issued on top of an existing blueprint whose identity sets, digest representation, exit codes and
error codes they silently replace. The document's own §1402 forbids exactly this: *"The reverse
direction is prohibited: Implementation → retroactive specification."* The reverse direction here is
not retroactive specification but its cousin — **duplicate specification**, with the earlier text left
standing.

**Classification of the document overall: PARTIALLY PROVED** — 21 findings (**17 PROVED, six of them
positive**, 3 MINOR, 1 NOTE), 13 corrections, of which **two are structural** (C-27.1 the ledger bucket, C-27.8 the crate
graph), **five are one-table reconciliations** (C-27.2, C-27.3, C-27.4, C-27.9, C-27.10) and the
remainder are single-sentence additions to types the document already declares.

---

*Part XXVII ends. The lineage now stands at **§0–§1600 across sixteen documents**, with one unassigned
number (§200) and one non-valid range (§1–§28, occupied twice with zero identical headings).*

*Open items, in priority order: the fourth ledger bucket and the v0.8/v1.6 implementation-contract
reconciliation (C-27.1), the strong-identifier union (C-27.2), the exit-code and error-code registries
(C-27.3, C-27.4), the coverage/gate evaluator signatures (C-27.5), the crate graph (C-27.8), the
release-closure reconciliation (C-27.9), the namespacing correction now four documents old (C-23.3),
and the three artifacts no document has touched in sixteen: the compiled-pack digest,
`EnvironmentAuthority`, and `evidence.schema.json`.*
