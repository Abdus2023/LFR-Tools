# RFL-AE Prompt Packs — Audit Part XXVI

**Subject:** `RFL-AE v1.5 — Conformance Runner & Orchestration Specification`, §1201–§1400 (200 sections)
**Recorded verbatim at:** `audit/rfl-ae-conformance-runner-orchestration-v1.5.md`
**Predecessor:** Part XXV (v1.4, §998–§1200)
**Date of analysis:** 2026-09-28
**Method:** all counts below were produced by executed measurement scripts against the recorded
verbatim file. Where two enumerations differ in spelling but not in intent, the mapping is shown
rather than the raw set difference. Where a finding rests on a lexical test rather than a semantic
one, that is stated in the finding.

---

## §1 — Scope, method, verification, and the anomaly that had to be recorded first

### §1.1 What was measured

| Measurement | Method | Result |
|---|---|---|
| Section range | regex over `^#{1,2}\s*§N` | **§1201–§1400** |
| Section count | count of matches | **200** |
| Contiguity | `nums == range(1201, 1401)` | **True** |
| Separators | count of `^---$` | **200** (one per section) |
| Continuity vs v1.4 | v1.4 ends §1200 → v1.5 opens §1201 | **adjacent, no gap** |
| Heading format | detected before counting | `##` at section level, as in v1.2–v1.4 |
| Restored blocks | block-index census | **§1201–§1400, 110+ blocks** |
| Union across fifteen documents | set union of all ranges | **§0–§1400, one gap: §200** |

### §1.2 The one anomaly recorded before anything else

§1400's law list contains **seventeen lines and fifteen distinct laws**. The final law —

```text
NO EVIDENCE → NO VERIFIED CLAIM
```

— appears **three times consecutively**. Measured: 17 lines, 15 distinct, one law repeated 3×.

This is recorded verbatim and is not a transcription error on my side: the triplication is in the
supplied text, and the recording preserves it. Two things follow, and they must be kept apart:

1. **As a document fact:** a normative law list that states one law three times inflates the count of
   governing laws without adding a governing law. A reader who counts "how many rules must I satisfy"
   gets 17; a checker that dedupes gets 15. F-26.2 classifies this MINOR and gives the fix.
2. **As a coincidence to disambiguate:** *"NO EVIDENCE → NO VERIFIED CLAIM"* is also one of **this
   audit's own** load-bearing rules, carried since Part XI. The specification author arriving at the
   same sentence is convergence, not derivation, and the audit is in no position to claim otherwise:
   the phrase is the natural statement of the property, and the audit has never had access to the
   author's sources. It is recorded as a coincidence, and the three-fold repetition is recorded as a
   document fact, and the two are not connected.

### §1.3 The fifteen-document union

| Document | Range | Sections | Supplied |
|---|---|---|---|
| v0.1–v1.4 (fourteen documents) | §1–§1200 | 1201 numbers | 1st–14th |
| **v1.5** | **§1201–§1400** | **200** | **15th** |
| **union** | **§0–§1400** | **1400 numbers, §200 unassigned** | |

### §1.4 A declared dependency set, for the first time

v1.5 is the first document to state its own dependencies:

> *"Depends on: Protocol v0.7+, Reference Implementation Blueprint v0.8, Conformance/Evidence/Release
> Specification v1.0, Fixture Corpus Specification v1.1, Release Gate Specification v1.4"*

Five documents, named by number. Measured against what v1.5 actually restates:

| Named as a dependency | v1.5 restates its vocabulary? |
|---|---|
| v0.7 | no (v0.7's types are not quoted) |
| v0.8 | no |
| v1.0 | **yes** — §1273 restates the conformance verdicts of §506–§510 |
| v1.1 | **yes** — §1213's D/X/E/V/P sets are §557's four populations with a fifth added |
| **v1.2** | **not named, but §1239 restates and rewrites §669's execution vocabulary** |
| **v1.3** | **not named, but §1265–§1268 and §1392 restate §810/§841/§869's coverage model** |
| v1.4 | **yes** — §1367 requires "all v1.4 release predicates" |

**F-26.15 (MINOR):** the declared dependency set omits the two documents v1.5 quotes most
consequentially. §1239's execution statuses are a rewrite of v1.2 §669's, and §1392's reconciliation
identity is a second attempt at v1.3 §869's. A reader trusting the dependency list would not open
either. **Fix:** add v1.2 and v1.3, or state why they are excluded.

---

## §2 — Structural census of §1201–§1400

| § | Function | Count |
|---|---|---|
| 1202 | runner boundary roles | 8 |
| 1203 | non-equivalence roles | 8 |
| 1204 | authoritative inputs | **10** |
| 1205 | execution-plan components | **11** |
| 1206 | plan-digest terms | **7** |
| 1208 | lifecycle states | **12** |
| 1208 | terminal exceptional states | 3 |
| 1210 | preflight checks | **14** |
| 1211 | preflight failure mappings | **6** |
| 1213 | population sets / inequalities | **5 / 4** |
| 1221 | isolation items | **10** |
| 1222 | resource limits | 8 |
| 1224 | cancellation fields | 4 |
| 1227 | scope items | **9** |
| 1229 | tool identity fields | 5 |
| 1231 | environment fields | **11** |
| 1233 | network policies | 4 |
| 1235 | model-execution fields | **8** |
| 1239 | execution statuses | **9** |
| 1243 | artifact fields | 5 |
| 1250 | check statuses | **6** |
| 1258 | evidence bindings | **10** |
| 1259 | closure nodes | 6 |
| 1260 | evidence-validity conjuncts | **8** |
| 1265 | coverage observables | **11** |
| 1273 | conformance states | **8** |
| 1278 | flakiness states | 4 |
| 1279 | cache identity terms | **7** |
| 1284 | delivery semantics | 4 |
| 1286 | ledger events | **13** |
| 1287 | event fields | **7** |
| 1290 | worker identity fields | 6 |
| 1292 | controller verification checks | 6 |
| 1296 | CI identity bindings | **9** |
| 1300 | build identity fields | 6 |
| 1301 | mutation record fields | **7** |
| 1304 | metamorphic fields | 6 |
| 1305 | property traceability terms | 5 |
| 1312 | configuration classes | 3 |
| 1320 | gate-result fields | 6 |
| 1323 | freeze-check identities | **8** |
| 1326 | manifest references | **8** |
| 1329 | replay inputs | 6 |
| 1330 | replay modes | 3 |
| 1338 | run identity fields | 5 |
| 1339 | runner identity fields | 5 |
| 1341 | bootstrap validation items | **8** |
| 1344 | adversarial inputs | 6 |
| 1350 | timestamp kinds | 4 |
| 1361 | external dependency examples | 4 |
| 1364 | execution modes | 3 |
| 1366 | conformance-mode requirements | 5 |
| 1388 | release evidence closure items | **14** |
| 1391 | summary counters | **11** |
| 1392 | reconciliation partition terms | **7** |
| 1398 | runner output artifacts | **12** |
| 1400 | governing laws (distinct / lines) | **15 / 17** |
| 1400 | stage chain | **12** |
| 1400 | non-equivalence statements | **9** |

**Shape.** v1.5 is the **execution document**: it takes the release boundary v1.4 defined and specifies
the machine that has to reach it. §1201–§1207 plan and freeze; §1208–§1214 lifecycle, preflight and
population; §1215–§1224 dependency, scheduling and resources; §1225–§1235 authorization, scope, tool
and environment identity, injection and model execution; §1236–§1243 attempts, execution records,
exit and output; §1244–§1256 observation, checking and result semantics; §1257–§1264 evidence
construction, binding, closure, validity and persistence; §1265–§1274 coverage and conformance;
§1275–§1285 retries, flakiness, cache, recovery and delivery semantics; §1286–§1293 events, causality
and workers; §1294–§1300 CI and build binding; §1301–§1308 mutation, metamorphic, property and
cross-language execution; §1309–§1316 schema validation, configuration and projection; §1317–§1328
gate invocation and manifest generation; §1329–§1337 replay; §1338–§1343 run identity and
self-verification; §1344–§1369 security, time, isolation, dependencies and modes; §1370–§1379
mutation propagation, event integrity and partial runs; §1380–§1399 the seven separations, publication,
claim scope and the output set; §1400 the invariant.

---

## §3 — Headline A: the coverage partition becomes arithmetic — and reconciles against the counters with one spelling drift

### F-26.1 — PARTIALLY PROVED — §1392 supplies the first explicit arithmetic partition of a declared fixture population, three Parts after the audit found the missing assignment function

Part XXIV's F-24.5 recorded that v1.3 §869 states `sum(state_counts) = declared_count` without any
state-assignment function behind it, so the invariant is unfalsifiable as written. v1.5 §1392 states
a partition in explicit arithmetic:

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

Seven outcome terms, one equation, no unnamed residue — with §1392's own qualifier *"subject to
explicitly documented multi-attempt semantics"*, which is the correct place for the retry caveat
(§1393's attempt counting and §1394's aggregate policy).

This is a genuine improvement over v1.3 in the specific respect the audit criticised: the terms are
**counters**, so each one is independently observable, and the equation is checkable by arithmetic
rather than by trusting a state assignment. §1391 exposes the same categories as counters, and §1265
requires them *"separately observable"*.

**Classification: PARTIALLY PROVED.** It closes the *outcome* partition; v1.3 §809's ten coverage
**states** (`NOT_DECLARED DECLARED SCHEDULED EXECUTED EVIDENCED COVERED UNCOVERED PARTIAL BLOCKED
INVALID`) remain unassigned, and v1.5 does not restate them. The two partitions are different objects:
§1392 partitions by result, §809 by coverage state. So the lineage now has a correct partition
(F-26.1) next to an unassigned one (F-24.5, still open) — which is itself the clearest evidence that
the v1.3 gap is a drafting omission rather than a conceptual difficulty.

**One drift inside the equation's own neighbourhood, MINOR:** §1265 and §1391 both spell the seventh
counter `errored`; §1392 spells it `error`. The three lists are separated by 126 and 1 sections
respectively, and a reconciliation check built from §1392's equation will not match a counter named
from §1391 without a rename. Measured: `errored` = 2 occurrences (`§1265`, `§1391`), `error` in §1392's
block. **Fix:** one spelling in both places.

---

## §4 — Headline B: the execution vocabulary changes for the seventh time, and now overlaps the check vocabulary

### F-26.3 — PROVED — §1239's nine execution statuses share six members with v1.2 §669's eleven, drop five, add three — and two of the additions are the check vocabulary's `BLOCKED` and `ERROR`

| Document | § | Execution statuses | Count |
|---|---|---|---|
| v0.8 | 278 | `Created Authorized Running Succeeded Failed Error Cancelled TimedOut Blocked` | 9 |
| v1.2 | 669 | `REQUESTED AUTHORIZED STARTED RUNNING COMPLETED FAILED_TO_START INTERRUPTED TIMEOUT CANCELLED CRASHED UNKNOWN` | 11 |
| **v1.5** | **1239** | **`COMPLETED FAILED_TO_START TIMEOUT CANCELLED CRASHED RESOURCE_EXHAUSTED BLOCKED ERROR UNKNOWN`** | **9** |

Measured overlap with v1.2 §669: **6 shared** (`CANCELLED COMPLETED CRASHED FAILED_TO_START TIMEOUT
UNKNOWN`). Dropped: `AUTHORIZED INTERRUPTED REQUESTED RUNNING STARTED`. Added: `BLOCKED ERROR
RESOURCE_EXHAUSTED`.

The third addition is an unambiguous improvement — `RESOURCE_EXHAUSTED` is a mechanical outcome that
neither previous vocabulary could express, and §1222 requires *"Resource exhaustion MUST produce a
structured execution status"*, a requirement that was unsatisfiable before this document.

The first two additions are the defect. `BLOCKED` and `ERROR` were placed in the **check** vocabulary
by v1.2 §677 and deliberately kept **out** of the execution vocabulary by v1.2 §669, whose whole
purpose was the sentence *"Execution status is distinct from verification status."* Measured in v1.5:

```
§1239 execution statuses : COMPLETED FAILED_TO_START TIMEOUT CANCELLED CRASHED
                          RESOURCE_EXHAUSTED BLOCKED ERROR UNKNOWN
§1250 check statuses     : PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED
                                          └─────┴─────────┴────── shared: 3
```

Three tokens now belong to both vocabularies in the same document, four documents after the
distinctness rule was stated. §1223 shows the document knows how to keep them apart when it wants to:

```text
execution_status = TIMEOUT
check_status = UNKNOWN
```

— a two-field representation in which the mechanical outcome and the verification outcome are named
separately. That is precisely the shape §1239's `BLOCKED`/`ERROR` should have used: an execution that
could not proceed is `FAILED_TO_START` or `BLOCKED` *as an execution fact* and `BLOCKED` *as a check
fact*, and the two are the same token.

**Classification: PROVED.** The comparison is verbatim; the distinctness rule is measured in v1.2 §669.
**Fix:** remove `BLOCKED`/`ERROR` from §1239 (a blocked execution is `FAILED_TO_START` with an
authorization or dependency cause), or adopt the C-23.3 namespace and make the two fields' domains
disjoint by construction.

---

## §5 — Headline C: the conformance verdict vocabulary is extended, in a document that forbids redefinition

### F-26.4 — PROVED — §1273 lists eight conformance states where v1.0 §506–§510 defines four, and §1201's first rule is that the runner MUST NOT redefine protocol semantics

| Source | Vocabulary | Count |
|---|---|---|
| v1.0 §506–§510 (headings: *Conformance Verdict*, *Conformant Definition*, *Incomplete Definition*, *Non-Conformant Definition*, *Blocked Definition*) | `CONFORMANT`, `INCOMPLETE`, `NON_CONFORMANT`, `BLOCKED` — each separately defined | **4** |
| **v1.5 §1273** | `CONFORMANT NON_CONFORMANT INCOMPLETE BLOCKED ERROR UNKNOWN STALE INVALID` | **8** |

The four new members are `ERROR`, `UNKNOWN`, `STALE`, `INVALID`. Three of them are load-bearing in
v1.5's own text: §1211 maps preflight failures to `BLOCKED`/`ERROR`/`CANCELLED`, §1311 requires
`STALE` or `BLOCKED` on schema drift, §1319 requires `STALE` for a stale gate input, and §1322 says a
required predicate whose evidence is unavailable *"MUST produce `BLOCKED` or `UNKNOWN`"* — so the
extension is not gratuitous; the document needs the extra states.

The problem is the framing. §1201 states:

> "The runner MUST NOT redefine protocol semantics. The runner executes semantics already defined by
> the normative protocol."

and §1273 presents its eight states as *"Recommended aggregate states"* — a new, weaker-levelled
vocabulary that **overlaps and extends** a normative vocabulary defined by v1.0 without a supersession
statement. A reader has no way to know whether `INCOMPLETE` in §1273 is v1.0 §508's `INCOMPLETE`, or
a new recommended state that happens to share its name; and since §1273 is a SHOULD-level list and
v1.0 §506–§510 are definitions, the two texts are in genuine competition for the same four names.

**Classification: PROVED.** The heading set of v1.0 §505–§511 was read verbatim; §1273's block was
extracted and counted. **Fix:** either add `ERROR`, `UNKNOWN`, `STALE`, `INVALID` to v1.0 §506–§510 as
a fifth-through-eighth *definition*, or state in §1273 that it is a runner-level projection of the
v1.0 verdicts with four additional blocking states, and rename the four inherited ones in the
projection if their semantics differ.

---

## §6 — Headline D: two release-closure predicates, nine terms and fourteen, disagreeing about the manifest

### F-26.5 — PROVED — v1.4 §1116's `ReleaseClosure` and v1.5 §1388's "release-grade claim" closure are different sets over the same concept, and only one contains the manifest

| Source | Title | Terms | Count |
|---|---|---|---|
| v1.4 §1116 | *"A release is evidentially closed only when:"* `ReleaseClosure =` | Protocol, Subject, Artifact, Population, Evidence, Coverage, Conformance, Gate, **Manifest** | **9** |
| v1.5 §1388 | *"A release-grade claim requires closure across:"* | Protocol, Subject, **Source**, Artifact, **Build**, Fixtures, **Execution**, **Observation**, **Checks**, Evidence, Coverage, Conformance, Gate, **Policy** | **14** |

The five terms §1388 adds — Source, Build, Execution, Observation, Checks — are genuine additions that
v1.4's closure lacks and that v1.4 §1118's *evidence bundle* list does contain, so §1388 is closer to
the bundle than to the closure. The term §1388 **drops** is `ManifestValid`, which v1.4 §1116 has.

That asymmetry matters because §1400's own law is `NO RELEASE MANIFEST → NO IMMUTABLE RELEASE
IDENTITY`, and §1325 places manifest generation **after** the gate:

```text
Conformance Run → Evidence → Coverage → Conformance → Gate → Release Manifest
```

So the manifest is downstream of everything §1388 closes over, and its absence from §1388 is
defensible. But v1.4 §1116 includes it, and both are titled as release closure — which means the two
documents disagree about whether "the release is evidentially closed" includes the artifact that
establishes what was released.

**Classification: PROVED.** Both blocks were extracted and compared as sets. **Fix:** state which
closure is authoritative for a release claim, and either add `Manifest` to §1388 or remove it from
v1.4 §1116 with a note that the manifest is the closure's *output* rather than its input.

---

## §7 — Headline E: the formal variables collide across documents

### F-26.6 — PROVED — `D`, `E` and `P` carry different meanings in three formal predicates of the same lineage

The lineage now writes formal predicates with single letters, and the letters are not namespaced:

| Identifier | Meaning in v1.1 §607 | Meaning in v1.3 §927 | Meaning in v1.5 §1213 |
|---|---|---|---|
| `D` | a fixture name (`declared = {A,B,C,D}`) | — | **declared fixtures** |
| `E` | — | **Evidence** (`EvidenceValid(E)`, `CoverageComplete(P,E,R)`) | **executed fixtures** |
| `P` | — | **Population** (`PopulationValid(P)`) | **passed fixtures** |
| `V` | — | — | evaluated fixtures |
| `X` | — | — | discovered fixtures |

So `E` means *evidence* in the coverage specification and *executed* in the runner specification; `P`
means *population* in the former and *passed* in the latter; and `D` has already been used as a
fixture's own name. In three documents that are all normative and all in force.

This is the formal-notation instance of the labelling defect Part XXIII recorded for status tokens and
Part XXV recorded for record fields. It is the most consequential of the three, because these letters
appear inside equations that a reader is expected to evaluate: `CoverageComplete(P,E,R)` and
`D ≠ X, D ≠ E, E ≠ V, V ≠ P` are one document apart in reading order and mean different things by `P`
and `E`.

**Classification: PROVED.** All three usages were extracted verbatim. **Fix:** qualify the letters at
first use in each document — `P_pop`, `E_ev`, `E_exec` — or state in the preamble of each specification
that single-letter identifiers are document-local. The second option is one sentence and costs nothing.

---

## §8 — The error-code taxonomy gains a fourth space and an ad-hoc member

### F-26.7 — PROVED — §1252's `INVALID_IDENTIFIER` appears in no enumerated error space, and v1.5 introduces zero `E_`-prefixed codes

| Space | Document | § | Members |
|---|---|---|---|
| check error codes | v1.2 | 679 | 9 (`E_`-prefixed) |
| evidence validation errors | v1.2 | 719 | 21 (bare) |
| release transition errors | v1.4 | 1198 | 14 (`E_`-prefixed) |
| blocking reason | v1.4 | 1179 | 1 (bare, `REQUIRED_CI_EVIDENCE_MISSING`) |
| **negative-fixture expectation** | **v1.5** | **1252** | **1 — `INVALID_IDENTIFIER`** |

Measured: `E_`-prefixed codes in v1.5 = **0**. Measured: `INVALID_IDENTIFIER` occurs in neither v1.2
§679/§719 nor v1.4 §1198.

So the example that §1252 uses to explain the negative-fixture law — *"Receiving any arbitrary
nonzero exit code is insufficient evidence"* — is an error code that exists in no taxonomy the
specification has defined. The example is doing normative work: a fixture author reading §1252 will
write `expected: error_code = INVALID_IDENTIFIER` and produce an expectation no checker can match
against a declared code set.

Also measured: v1.5 coins eight one-off tokens with no enumeration behind them —
`CI_RECORD_NOT_OBSERVED` (§1295), `EVIDENCE_ERROR` (§1264), `CACHE_REUSED` (§1280),
`RESOURCE_EXHAUSTED` (§1239), `STABLE_PASS`/`INCONSISTENT` (§1278),
`AT_MOST_ONCE`/`EFFECTIVELY_ONCE` (§1284). Each appears exactly once, several inside *"Suggested"* or
*"Possible"* lists. Individually the SHOULD-level framing protects them; collectively they mean the
lineage has coined roughly forty stratified tokens across five documents with no registry.

**Fix:** name the space §1252 draws from, or make the example use a code from v1.2 §679.

---

## §9 — Vocabulary expansion: the largest in the lineage, against an unapplied rule

### F-26.8 — PROVED — v1.5 introduces at least eight new enumerated vocabularies while the namespacing correction has gone unapplied for four documents

| New vocabulary | § | Count |
|---|---|---|
| runner lifecycle (+ terminal exceptional) | 1208 | 12 + 3 |
| execution status | 1239 | 9 |
| conformance states | 1273 | 8 |
| flakiness states | 1278 | 4 |
| delivery semantics | 1284 | 4 |
| replay modes | 1330 | 3 |
| execution modes | 1364 | 3 |
| network policies | 1233 | 4 |

Plus two restatements (check status §1250, six) and one relation (§1223). Eight new sets in one
document is the largest vocabulary expansion in the lineage — larger than v1.2's status lattice, which
introduced six.

Every one of those eight is built from the same token pool that already carries five senses of
`UNKNOWN` and six of `BLOCKED`. Measured in v1.5: `UNKNOWN` appears as an **execution** status
(§1239), a **check** status (§1250), a **coercion target** (§1271), a **conformance state** (§1273), a
**delivery semantic** (§1284), a **result-precedence member** (§1315), a **gate outcome** (§1322), a
**replay condition** (§1306) and a **reproducibility state** (§1306) — nine senses in one document,
against five in v1.2 and seven in v1.3.

C-23.3 (namespace the status tokens) is now four documents old. The count of senses has risen at every
document since it was proposed:

| Document | Senses of `UNKNOWN` | Documents since C-23.3 |
|---|---|---|
| v1.2 | 5 | 0 |
| v1.3 | 7 | 1 |
| v1.4 | 7 | 2 |
| **v1.5** | **9** | **3** |

**Classification: PROVED** (the sense inventory is a reading of nine sections; the token counts are
measured). This is the audit's clearest case for treating namespacing as a precondition rather than a
correction: the rule has been unapplied for long enough that the problem has grown monotonically.

---

## §10 — What v1.5 gets right that no predecessor did

### F-26.9 — PROVED (positive) — §1223 is the lineage's first explicit cross-vocabulary mapping rule

```text
execution_status = TIMEOUT
check_status   = UNKNOWN
```

Every prior document either defined one vocabulary or asserted that two vocabularies must remain
distinct (v1.2 §669; §717). §1223 is the first to state **what the value in one field is when a
specific condition holds in the other** — and it adds the correct exception (*"unless the fixture's
normative expected result explicitly defines timeout as the semantic expected behavior"*), which is
exactly the case v1.1 §646 made a conformance requirement (a negative fixture that expects a timeout
is a pass).

### F-26.10 — PROVED (positive) — §1284 admits delivery ambiguity instead of assuming it away

```text
AT_MOST_ONCE
AT_LEAST_ONCE
EFFECTIVELY_ONCE
UNKNOWN
```

with *"The runner MUST NOT claim exactly-once execution unless the underlying dispatch mechanism
proves it."* Fifteen documents in which execution was modelled as a sequence of attempts, and this is
the first that says what the delivery guarantee actually is — and the first to make `UNKNOWN` a
legitimate answer to "how many times did this run?". §1377 then makes at-least-once transport a
tolerated condition rather than an error (*"Duplicate event delivery MUST be tolerated"*), and §1285
requires non-idempotent fixtures to declare themselves.

### F-26.11 — PROVED (positive) — §1340–§1343 close the independence question at the runner boundary

| § | Statement |
|---|---|
| 1340 | the runner *"MUST NOT be the sole authority proving its own conformance"* |
| 1341 | bootstrap verification must validate the eight artifact classes *"without depending entirely on the implementation being verified"* |
| 1342 | critical predicates SHOULD have an independently implemented checker |
| 1343 | *"If runner and checker share the same defective semantic implementation, agreement does not establish correctness"* → **`agreement ≠ independence`** |

§1343 is the most valuable sentence in v1.5 and the direct descendant of this audit's Part VII finding,
where two environments produced `6 findings` and `8 findings` from the same commit and both reported
PASS: agreement between two runs of the same flawed checker established nothing, and the audit's own
remedy — independent checkers — is now normative. Read with v1.4 §1159 (two independent gate
evaluators disagreeing blocks release) and v1.4 §1189 (circular trust prohibited), the lineage now
covers the runner, the gate and the system.

### F-26.12 — PROVED (positive) — sixteen new guards, twelve of them protecting a non-PASS state through composition

| § | Guard |
|---|---|
| 1216 | a dependency failure MUST NOT be converted into `FAIL` |
| 1217 | *"fixture failed"* and *"fixture could not execute because prerequisite failed"* must stay distinct |
| 1224 | a cancelled fixture MUST NOT become `PASS` |
| 1269 | blocked fixtures MUST NOT disappear from denominators |
| 1270 | a skipped fixture MUST NOT count as passed |
| 1271 | `UNKNOWN → FAIL` and `UNKNOWN → PASS` are both prohibited |
| 1274 | unresolved required fixtures → `INCOMPLETE`, never complete conformance |
| 1276 | a retry MUST NOT occur *"merely because the runner wants a PASS"* |
| 1277 | disagreeing attempts: preserve both, never select the favourable result |
| 1280 | `CACHE_REUSED` MUST remain distinct from `EXECUTED` |
| 1282 | completion MUST NOT be inferred *"merely because a process previously existed"* |
| 1295 | absence of an observed CI record is not proof CI never ran → `CI_RECORD_NOT_OBSERVED` |
| 1314 | MUST NOT report *"all tests passed"* when records contain unresolved required fixtures |
| 1316 | MUST NOT expose `success: true/false` as the authoritative result |
| 1335 | a replay mismatch MUST NOT overwrite the original result |
| 1349 | truncation ⇒ `complete = false`, and a check needing the full output becomes `UNKNOWN` or `FAIL`, *"not silently PASS"* |

Running total: **at least 61** (21 through Part XXIII, +12 v1.3, +12 v1.4, +16 v1.5), still with no
index. §1295 and §1282 are the two most notable: the first is the lineage's second statement of the
absence-of-observation rule (after v1.4 §1051) and the first to give it a **named state**; the second
is the crash-recovery version of the same error — a process that existed is not a run that completed.

### F-26.13 — PROVED (positive) — §1395 and §1396 prohibit both directions of retry bias

| § | Bias | Prohibition |
|---|---|---|
| 1395 | first-pass bias | MUST NOT discard later contradictory evidence *"merely because an earlier attempt passed"* |
| 1396 | last-pass bias | MUST NOT replace earlier failures *"merely because the latest attempt passed"* |

Both directions named, both prohibited, one section apart. Together with §1322 (*"It MUST NOT become
PASS"*) and v1.3 §874 (*"Flakiness MUST NOT be hidden by selecting PASS"*), the lineage now forbids
every route by which a flaky fixture becomes a stable pass. This is the class of defect the audit
found in the toolchain's non-monotonic provenance check (Part V §0.1: deleting 29 of 30 comments →
FAIL; deleting all 30 → PASS), and it is now prohibited by name in both of its forms.

---

## §11 — The supersession ledger, extended

Part XXV introduced three statuses for carried findings. Applying them to v1.5's arrivals:

| Finding | Was | Now | Authority |
|---|---|---|---|
| coverage partition without assignment function | OPEN (Part XXIV F-24.5) | **PARTIALLY CLOSED** — §1392 partitions outcomes arithmetically; v1.3 §809's state assignment still absent | F-26.1 |
| execution vocabulary drift | OPEN (7 documents) | **EXTENDED** — 7th variant, now overlapping the check vocabulary | F-26.3 |
| status-token collisions | OPEN — worsened | **WORSENED** — 9 senses of `UNKNOWN` in one document, 8 new vocabularies | F-26.8 |
| two release-closure predicates | not previously recorded | **NEW — OPEN** | F-26.5 |
| formal-variable collisions | not previously recorded | **NEW — OPEN** | F-26.6 |
| error-code taxonomy | OPEN — 2 conventions | **EXTENDED** — 4th space plus an ad-hoc member | F-26.7 |
| v1.2 §749 inverted scope symbol | SUPERSEDED in substance (v1.4 §1071) | **SUPERSEDED further** — §1389 restates it with the same identifier, correct direction | §12 |
| absence-of-observation confusion | SUPERSEDED/CLOSED via v1.4 §1051 | **CLOSED** — §1295 gives it a state name | F-26.12 |
| compiled-pack digest (v0.4 §86) | OPEN since Part XV | **OPEN** — `CompiledPack` = 0, `compiled_digest` = 0, fifteenth document | §11 |
| `EnvironmentAuthority` | OPEN since Part XIII | **OPEN** — 0, fifteenth document | §11 |
| `evidence.schema.json` | OPEN since Part XV | **OPEN** — 0, fifteenth document | §11 |
| `ClaimId` / `ReleaseId` as types | CLOSED (v1.4 §1194) | **CLOSED** — but v1.5 §1389 discusses claim scope without naming either | §11 |

The three oldest open items have now survived fifteen documents. The compiled-pack digest is the
sharpest: the self-reference exclusion rule — the rule that v0.4 §86's defect is an instance of — is
now stated in v1.2 §682 (evidence), v1.3 §847 (coverage), v1.4 §1064 and §1112 (manifest), v1.4 §1102
(gate evaluation) and v1.5 §1327, which restates it in eight words:

> *"The manifest digest MUST be computed over canonical manifest content. The digest MUST NOT include
> itself."*

Seven statements of the rule, six of them written after the defect was identified, and the artifact
that prompted it still has none.

---

## §12 — The scope relation, three Parts later

### F-26.14 — PROVED — §1389 uses the same identifier v1.2 §749 inverted, with the correct operator

| Document | § | Statement | Operator |
|---|---|---|---|
| v1.2 | 749 | `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` | **⊇ — inverted** |
| v1.4 | 1071 | `ClaimScope ⊆ EvidenceScope` / `ClaimedPopulation ⊆ CoveredPopulation` | ⊆ ×2 |
| **v1.5** | **1389** | **`CLAIMED_SCOPE ⊆ EVIDENCE_SCOPE`** | **⊆** |

Measured in v1.5: `⊆` = 1, `⊇` = 0, `CLAIMED_SCOPE` = 1. §1390 supplies the worked consequence —
*"A run over 1,000 fixtures MUST NOT produce a claim about 10,000 fixtures unless the additional 9,000
are independently evidenced"* — which is the overclaim rule in operational form.

Running symbol count across the lineage: **three correct `⊆` statements (v1.4 ×2, v1.5 ×1) and one
inverted `⊇` (v1.2)**. §1389's use of the exact identifier `CLAIMED_SCOPE` makes the contrast
unavoidable: the same name, the same document number style, opposite operators, and the later usage is
the one a runner is bound by. **C-23.1 and C-24.10 remain corrective-only.**

---

## §13 — Remaining findings

### F-26.2 — MINOR — §1400 states the same law three times, inflating the count from 15 to 17

Measured: seventeen lines, fifteen distinct laws, `NO EVIDENCE → NO VERIFIED CLAIM` ×3 consecutively.
If the triplication is emphasis, the list should say so — a bracketed note, or the law set apart from
the numbered ones. If it is duplication, one instance was intended. Either way the artifact a
conformance checker reads should have 15 laws or 15 laws plus a marked coda, not 17 lines.

Note that the triplication is **not** the most-repeated statement in the lineage: *"NO LAYER MAY
PRETEND TO BE THE NEXT LAYER"* appears in v0.4, v0.8, v1.0, v1.2, v1.3, v1.4 and — in nine separate
inequalities — v1.5 §1400. That repetition is spread across documents as a closing cadence; §1400's is
inside one list, which is what makes it a counting hazard.

### F-26.16 — MINOR — §1210 and §1221 are the only enumerations in the document not presented as fenced blocks

Every other enumeration in v1.5 — including the ten authoritative inputs of §1204, which *is* a fenced
block — is presented as a code block. §1210's fourteen preflight checks and §1221's ten isolation
items are Markdown bullet lists. The content is equivalent; the presentation means a reader who
extracts enumerations mechanically (as any fixture generator will) sees 10 inputs and misses 24 items.

Recorded as MINOR because it is a formatting inconsistency rather than a semantic defect, and recorded
at all because this audit has itself been misled three times by presentation differences across
documents (Part XXVI §1 of each Part's method section).

### F-26.17 — NOTE — §1398's twelve runner outputs and v0.7 §269's twelve artifacts are different sets

Both are twelve. v0.7 §269 enumerates *build artifacts* (Rust modules); §1398 enumerates *run outputs*
(`run.json`, `plan.json`, `schedule.json`, `execution/*.json`, `observation/*.json`, `check/*.json`,
`evidence/*.json`, `coverage.json`, `conformance.json`, `gate.json`, `release-manifest.json`,
`event-ledger.jsonl`). No defect; recorded because two twelves in one lineage is the kind of
coincidence that a future summary will compress into one number.

---

## §14 — Carried items: the fifteenth-document census

| Item | First raised | Status in v1.5 |
|---|---|---|
| compiled-pack digest (v0.4 §86) | Part XV | `CompiledPack` = 0, `compiled_digest` = 0 — **open, fifteenth document** |
| `EnvironmentAuthority` | Part XIII | **0** — fifteenth document |
| `evidence.schema.json` | Part XV | **0** — fifteenth document |
| `ClaimId` / `ReleaseId` | Part XX | 0 and 0 in v1.5; the names were supplied by v1.4 §1194 and are not carried forward |
| `ResultStatus` / `CheckStatus` | Part XX | 0, 0 |
| `FLAKY` | Part XXIV | 0 — v1.5 §1278 replaces it with a four-state scale (`STABLE_PASS/STABLE_FAIL/INCONSISTENT/UNRESOLVED`), which is an improvement |
| README/997 repository claim | Part II | **CLOSED, cosmetic** |
| coverage partition | Part XXIV | **PARTIALLY CLOSED** (§1392) |
| absence-of-observation | Part V | **CLOSED** (§1295) |
| v1.2 §749 inversion | Part XXIII | **SUPERSEDED** (v1.4 §1071, v1.5 §1389) |

---

## §15 — Corrections, in dependency order

| # | Correction | Depends on | Blocks |
|---|---|---|---|
| C-26.1 | **Namespace the status tokens** (C-23.3/C-24.7/C-25.5) — nine senses of `UNKNOWN` in this document alone. | — | every generated enum in the runner |
| C-26.2 | **Remove `BLOCKED`/`ERROR` from §1239**, or make §1239 and §1250 disjoint by construction. | C-26.1 | execution-record typing; F-26.3 |
| C-26.3 | **Reconcile §1273 with v1.0 §506–§510** — define the four new states, or declare §1273 a projection. | — | conformance verdict typing; F-26.4 |
| C-26.4 | **Reconcile v1.4 §1116 and v1.5 §1388** — decide whether the manifest is inside the release closure. | — | release-grade closure checking; F-26.5 |
| C-26.5 | **Qualify single-letter identifiers** as document-local, or rename them. | — | every formal predicate; F-26.6 |
| C-26.6 | **Name the error-code space §1252 draws from**, or use a v1.2 §679 code in the example. | C-23.9/C-24.11/C-25.10 | negative-fixture expectations; F-26.7 |
| C-26.7 | **Fix `errored` vs `error`** between §1265/§1391 and §1392. | — | the reconciliation check; F-26.1 |
| C-26.8 | **Deduplicate §1400's law list** (17 lines → 15 laws, with any emphasised law marked). | — | law counting; F-26.2 |
| C-26.9 | **Add v1.2 and v1.3 to the declared dependency set**, or state the exclusion. | — | dependency traceability; F-26.15 |
| C-26.10 | **Apply the compiled-pack exclusion rule** (C-23.14, C-25.11) — v1.5 §1327 has the wording. | — | the lineage's oldest open defect |
| C-26.11 | **Restate v1.3 §809's coverage states** with an assignment function, or drop them in favour of §1392's partition. | — | coverage-state typing; F-26.1 |
| C-26.12 | **Promote §1210 and §1221 to fenced blocks** so mechanical extraction sees all 24 items. | — | fixture generation; F-26.16 |

---

## §16 — Closing judgement

### §16.1 The stage

v1.5 is the **execution document**, and it arrives as the lineage's first document written *for an
implementer rather than a reader*. Eight of its sections are pure mechanics that no prior document
attempts — §1219's ban on hash-map iteration order in deterministic mode, §1282's crash-recovery rule,
§1346's path-traversal rejection, §1349's truncation flag, §1351's clock-trust warning, §1377's
at-least-once deduplication. These are the sentences one writes after having been burned by the thing
they forbid, and they are the strongest evidence in fifteen documents that the specification is
descending from principle toward practice.

```
§151–§171  Part XI  PROMPT PACK PROTOCOL                 NORMATIVE SPECIFICATION
§201–§271  v0.7     TYPES                                SCHEMA
§272–§361  v0.8     REFERENCE IMPLEMENTATION BLUEPRINT   REFERENCE IMPLEMENTATION
§362–§455  v0.9     EXECUTABLE PROTOCOL KERNEL            REFERENCE IMPLEMENTATION
§456–§555  v1.0     CONFORMANCE / EVIDENCE / RELEASE      TEST + EVIDENCE
§556–§655  v1.1     FIXTURE CORPUS + CONFORMANCE          TEST FIXTURES
§656–§800  v1.2     EVIDENCE + EXECUTION RECORD           EXECUTION EVIDENCE
§801–§997  v1.3     COVERAGE + CONFORMANCE AGGREGATION    DERIVATION
§998–§1200 v1.4     RELEASE GATE + MANIFEST               BOUNDARY
§1201–§1400 v1.5    CONFORMANCE RUNNER + ORCHESTRATION    EXECUTION
```

Nothing in §0–§1400 has been built. The audited repository still contains only the upstream toolchain
and its five measured defects, all in the verification layer.

### §16.2 What v1.5 gets right that no predecessor did

1. **The coverage partition becomes an equation with named terms** (§1392) — the first arithmetic
   partition in the lineage, after three Parts of the audit asking for one.
2. **A cross-vocabulary mapping rule** (§1223) — the first statement of what one field's value is when
   another field's condition holds.
3. **Delivery semantics** (§1284) with `UNKNOWN` as a legitimate answer.
4. **`agreement ≠ independence`** (§1343), with bootstrap and independent-checker requirements
   (§1340–§1342).
5. **Sixteen new guards**, including both directions of retry bias (§1395, §1396) and the named absence
   state `CI_RECORD_NOT_OBSERVED` (§1295).
6. **The dependency-cause distinction** (§1216, §1217) — *failed* versus *could not execute*, which the
   audit has recorded as a class of error since Part XII.

### §16.3 What v1.5 gets wrong

One arithmetic partition that does not reconcile with its own counter lists by one letter (F-26.1);
one execution vocabulary that has now crossed into the check vocabulary's domain (F-26.3); one
conformance verdict list that extends a normative vocabulary inside a document that forbids
redefinition (F-26.4); two release-closure predicates that disagree about the manifest (F-26.5);
formal variables that mean different things in adjacent documents (F-26.6); and eight new vocabularies
built from a token pool the audit asked to namespace three documents ago (F-26.8).

### §16.4 The laws of §1400

| Document | § | Laws | Character |
|---|---|---|---|
| v0.8 | 361 | 11 | implementation-shaped |
| v0.9 | 455 | 16 | type-and-engine-shaped |
| v1.2 | 800 | 12 | identity-required |
| v1.3 | 997 | 10 | derivation-required |
| v1.4 | 1200 | 15 | boundary-required |
| **v1.5** | **1400** | **15 distinct (17 lines)** | **stage-required: every law forbids one stage from standing in for the next** |

§1400 is the first law set in the lineage built entirely from the *stage* vocabulary rather than the
identity or derivation vocabulary: `NO PLAN → NO CONFORMANCE EXECUTION`, `NO AUTHORIZATION → NO
DISPATCH`, `NO SEMANTIC CHECK → NO VERIFICATION RESULT`. Every one of the fifteen is decidable by
looking for one artifact at one boundary, which makes it the most mechanically checkable set since
v1.4's — and the only one whose closing coda is stated twice over, once as nine inequalities
(`execution success ≠ observation` … `publication ≠ deployment`) and once as the six-line chain
DECLARE → FREEZE → AUTHORIZE → SCHEDULE → EXECUTE → OBSERVE → CHECK → EVIDENCE → COVERAGE →
CONFORMANCE → GATE → RELEASE.

### §16.5 Verdict

**v1.5 is the most operationally useful document in the lineage and the most vocabulary-generative.**
Its mechanical rules are of a kind no predecessor contains and could only have been written from
experience: the cache-hit/execution distinction (§1280), the crash-recovery rule (§1282), the two
retry-bias prohibitions (§1395, §1396), the truncation flag (§1349), the path-traversal rejection
(§1346). Read against the audit's own history, three of those rules name defects this audit measured
in the toolchain — inference-from-process-existence (Part VII's crash-as-pass), favourable-result
selection (Part V's non-monotonic provenance), and summary-over-records (the C-3 receipt).

Against that, v1.5 introduces eight enumerated vocabularies while the namespacing correction sits
unapplied, and its formal notation reuses letters that mean different things one document away. The
pattern from Parts XXIV and XXV holds and sharpens: **the lineage's semantics are converging while its
labels are diverging.** Each document gets more precise about what happens and less careful about what
to call it.

**Classification of the document overall: PARTIALLY PROVED** — a runner specification that is
implementable in its mechanics, ambiguous in its vocabulary, and whose nineteen findings (two MINOR
structural, one NOTE, sixteen substantive) reduce to five corrections that are each one table or one
sentence long.

---

*Part XXVI ends. The lineage now stands at **§0–§1400 across fifteen documents**, with one unassigned
number (§200) and one non-valid range (§1–§28, occupied twice with zero identical headings).*

*Open items, in priority order: the status namespace (C-26.1, four documents old and growing
monotonically), the execution/check vocabulary overlap (C-26.2), the conformance verdict
reconciliation (C-26.3), the release-closure disagreement (C-26.4), the formal-variable qualification
(C-26.5), the error-code taxonomy (C-26.6), and the three items no document has touched in fifteen:
the compiled-pack digest, `EnvironmentAuthority`, and `evidence.schema.json`.*
