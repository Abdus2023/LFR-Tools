# RFL-AE Prompt Packs — Part XVI: Runtime & Execution Contract v0.5 — Test Matrix and Finding Closures

**Subject:** [`rfl-ae-prompt-instructions-v0.5.md`](rfl-ae-prompt-instructions-v0.5.md) (§106–§149), continuing [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md)
**Series:** [`part12`](rfl-ae-prompt-packs-part12.md) · [`part13`](rfl-ae-prompt-packs-part13.md) · [`part14`](rfl-ae-prompt-packs-part14.md) · [`part15`](rfl-ae-prompt-packs-part15.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — closures and test-matrix measurement
>
> **v0.5 closes two findings that three parts have carried.**
>
> - **`STALE` is resolved** — §126 makes staleness a state of the *evidence record*, which is exactly the form Part XII §5 recommended after rejecting the alternative. Parts XII, XIII and XIV all raised it; it is now closed.
> - **The two-level model is resolved** — §116 separates eight **execution** states from v0.2 §7's six **verification** states, and says so explicitly. Parts XII §5, XIII §5 and XIV §6.2 asked for this split; it is now specified.
>
> Both are credited in §2, before any finding.
>
> **New findings:**
>
> - **§3** — §131's non-monotonicity rule is the *correct* principle, and the measured defect is **the inverse of what it contemplates**: the audited system returned its strongest verdict at **zero evidence**, and adding evidence moved FAIL → PASS. §131's framing does not cover the zero case, which is the one that matters.
> - **§4** — §145's 15-row matrix is the most empirically testable artifact in the lineage. **Four of the fifteen rows have executed receipts showing the wrong answer**, and they map to the audit's critical findings.
> - **§5** — §125 defines evidence validity with 7 conjuncts; §126 declares 6 invalid states. **Four of the six are not expressible in §125's formula**, so a `STALE` record satisfying all seven conjuncts evaluates as *valid*.
> - **§6** — §112/§113 require tool identity and implementation digests. Measured: **zero `hashlib` occurrences and zero version strings** in the toolchain.
> - **§8** — v0.5's own release gate is downstream of v0.4's blocking defect.

---

## 1. Standing: §0–§149 across four documents

v0.5 continues v0.4's numbering, which continues v0.3's, which continues v0.2's. The effective specification is now **149 sections across four files**, and the dependency is again structural:

| v0.5 uses | Defined only in |
|---|---|
| §119's six-valued status, §122's FAIL, §123's UNKNOWN, §124's BLOCKED | **v0.2 §7** |
| §106's `CompiledPack`, §111's `AUTHORITY = CAPABILITY ∩ SCOPE` | **v0.4 §75–§77** |
| §121's *"required coverage"*, §129's PARTIAL | **v0.3 §52** |
| §134–§137's component boundaries | **v0.3 §41**, **v0.4 §97–§100** |
| §144's `UNTRUSTED DATA ✕ AUTHORITY ESCALATION` | **v0.3 §59** |

**§116 is explicit about this**: *"These are execution states. They must not be confused with verification states."* That sentence only makes sense if §7 of v0.2 is in force — and it is the cleanest statement of the layering problem the whole lineage has been circling.

**Part XIV §1.1's identity finding remains open, and is now four documents deep.** `api_version: rfl-ae.prompt-pack/v0.4` (v0.4 §72) versions the pack schema. **No document versions the protocol.** Four files, 149 sections, one `api_version` string, and it applies to the artifact rather than the specification.

---

## 2. Two findings closed

### 2.1 `STALE` — CLOSED, exactly as Part XII recommended

Part XII §5 found that v0.2 §7's six states are predicate-evaluation outcomes, that staleness is a different question, and that v0.1–v0.2 demanded staleness discipline in four sections while providing no state to record it. It offered two options:

```text
Option A — add a state
    status ∈ { PASS, FAIL, ERROR, UNKNOWN, SKIPPED, BLOCKED, STALE }

Option B — make staleness a property of evidence, not of a check result
    RuleResult.status    remains six-valued   (what the check found)
    EvidenceRecord.stale = true               (whether the record still binds)
```

Part XII recommended **Option B**, on the grounds that §7's six states are defined as outcomes of predicate evaluation, and §10 already separates *"WHAT happened"* from *"WHAT evidence records the observation."*

**v0.5 §126:**

```text
CREATED → BOUND → VALIDATED → ADMISSIBLE → USED_BY_GATE

Possible invalid states: UNBOUND · STALE · INCOMPLETE · CONFLICTED · CORRUPTED · MISMATCHED
```

**That is Option B, implemented.** §126 is an evidence-record lifecycle; `STALE` is one of its invalid states; §7's six states are untouched. §127 gives the default, §128 the reuse condition, §144 the enforcement (*"STALE EVIDENCE ✕ CURRENT GATE"*), and §131 the consequence (*"verification is revision-bound"*).

Four sections of scaffolding around a state that three prior parts said was missing. **CLOSED.**

One consequence worth naming: §126 is the first place in the lineage where a **claim-level invalid state** exists as a first-class object rather than as an absence. Part XIII §5 recommended exactly this separation — *"Split the verb in §28's header across these two objects"* — and §126/§116 together deliver it.

### 2.2 The two-level model — CLOSED, with a partial mapping

§116:

```text
SCHEDULED · RUNNING · COMPLETED · FAILED_TO_START
· TIMEOUT · CANCELLED · INTERRUPTED · CRASHED
```

with:

> *"These are execution states. They must not be confused with verification states. For example: `execution = COMPLETED, verification = FAIL` is perfectly valid. Likewise: `execution = CRASHED, verification = ERROR`."*

**The two sets are disjoint.** v0.2 §7 = `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED`; §116 = the eight above. Zero overlap, and §116 states the separation explicitly rather than implying it.

This closes the gap Parts XII §5, XIII §5 and XIV §6.2 each raised: that a single status field was being asked to carry both *what the machinery did* and *what the predicate evaluated to*.

**Residual — the mapping is 2 of 8.** §116 gives two examples and defines no general map:

| Execution state | Verification mapping given? |
|---|---|
| COMPLETED | ✓ example — can be FAIL |
| CRASHED | ✓ example — `ERROR` |
| SCHEDULED | — |
| RUNNING | — |
| FAILED_TO_START | — |
| TIMEOUT | — |
| CANCELLED | — |
| INTERRUPTED | — |

**The six unmapped states are the informative ones.** `TIMEOUT` is the clear case: it could be `ERROR` (machinery failed to produce a result), `UNKNOWN` (insufficient observation), or `BLOCKED` (a required resource was unavailable) — and §124's *"unavailable required resource"* arguably fits. §116 creates the right distinction but leaves its application undetermined, which means two implementations of §116 could classify one timeout differently and both satisfy the text.

For comparison, v0.1 §22 mapped 4 of 8 conditions, and v0.3 §41 mapped 6 of 13. **This is a third instance of a partial mapping, and the pattern is consistent: the distinction is drawn correctly and the mapping is left short.**

---

## 3. §131 is the right rule, and the measured defect is its inverse

§131:

> *"Adding evidence can change `UNKNOWN → PASS` or `UNKNOWN → FAIL`. Adding evidence must never be assumed to improve the result."*

This is correct, and it is the principle the audit needed. But the measured defect is **not the case §131 describes**, and the difference matters.

### 3.1 The measured non-monotonicity

Part V §0.1's five-state provenance experiment, on `has_prov`:

| Provenance comments remaining | Result |
|---|---|
| 30 (all) | **PASS** |
| 29 | FAIL |
| 15 | FAIL |
| 1 | FAIL |
| **0** | **PASS** |

Reading the transitions:

- **1 → 0 comments: FAIL → PASS.** *Removing* evidence improved the result. §131 addresses *adding* evidence; this is the opposite direction.
- **29 → 30 comments: FAIL → PASS.** *Adding* evidence improved the result — which §131 permits (it says improvement must not be *assumed*, not that it cannot occur).

**So the system is non-monotonic in both directions, and its strongest verdict is reached at zero evidence.**

The zero row is the finding. §131 contemplates non-monotonicity arising from **new information** — *"UNKNOWN → PASS"*, an unknown becoming known. The measured case is non-monotonicity arising from **evidence removal**, terminating in a PASS at the point where no evidence exists at all.

**That is the one case §131's framing does not cover, and it is the most dangerous one.** v0.2 §1 states `NO EVIDENCE → NO VERIFIED CLAIM` as a core invariant. The measured system returned PASS at zero evidence.

### 3.2 Why the gap is structural rather than editorial

§131 reasons about the *effect of adding* evidence to an existing evaluation. It therefore presumes an evaluation that exists and can be updated. The `has_prov` defect does not work that way:

```python
has_prov = "<!-- source: " in text        # audit_corpus.py:67
if has_prov and not prov["complete"]:     # audit_corpus.py:88
```

**The gate is conditional on the evidence being present.** When `has_prov` is false, the check does not run *at all* — so there is no evaluation for §131 to be about, and the document is reported `n/a` inside `ALL FILES OK`. The non-monotonicity is not in the evaluation; it is in **whether an evaluation occurs**.

§131 has no vocabulary for a predicate that does not execute. §121 (*"PASS is not established"*) and §123 (`UNKNOWN`) both describe outcomes of evaluation. **A check that is skipped by construction is neither** — it is the state Part XIV §6.2 found unreachable (`SKIPPED`), and it is the mechanism behind this defect.

**Correction.** §131 should state the stronger rule its own lineage needs:

```text
Adding evidence must never be assumed to improve the result.        ← §131, correct
Removing evidence must never improve the result.                     ← the missing half
Evidence absence must never satisfy a required-evidence predicate.   ← the mechanism
```

The third is `has_prov`'s defect stated as a rule, and it is v0.2 §1's `NO EVIDENCE → NO VERIFIED CLAIM` applied to a *gate condition* rather than to a *claim*. Part XV §3 found the third form missing from v0.4's gate; this is the same rule, and it is now missing from two documents for the same reason.

---

## 4. §145's test matrix, measured

§145 is the most empirically testable artifact in the lineage: fifteen rows, each a predicate with an expected outcome. `§145` says the runtime *"MUST eventually test at least"* these. So the useful question is not whether the runtime passes them — there is no runtime — but **which rows the audited toolchain can already be evaluated against, and what it does.**

| Row | Expected | Status against `vendor/rfl-ae/` | Receipt |
|---|---|---|---|
| `R05` | missing dependency → `BLOCK` | ❌ **VIOLATED** | Announced `SKIPPED`; stage 3 fatal; **stage 7 reports `PASS`** (Part VIII §98) |
| `R06` | tool crash → `ERROR` | ❌ **VIOLATED** | `ModuleNotFoundError` → `PASS (exit 1, 0 findings)` (Part VII §0.2) |
| `R09` | insufficient observation → `UNKNOWN` | ❌ **VIOLATED** | `n/a` inside a run reporting `ALL FILES OK` (Part V §0.1) |
| `R15` | surviving mutation → `DETECT GAP` | ❌ **VIOLATED** | 4 surviving mutants, none reported (Part VIII §0.1) |
| `R14` | deterministic compilation → same digest | ⛔ **BLOCKED** | v0.4 §86 defines the digest circularly — cannot run (Part XV §2) |
| `R07` | predicate violation → `FAIL` | ⚪ exercisable | The corpus audit does detect violations — the one thing it does |
| `R08` | predicate satisfied → `PASS` | ⚪ exercisable | As above |
| `R01` `R02` `R03` `R04` `R10` `R11` `R12` `R13` | allow / block / reject / data-only | ⬜ **no substrate** | No runtime, no authorization gate, no scope object, no evidence layer |

**4 violated · 1 blocked by a specification defect · 2 partially exercisable · 8 with no substrate.**

### 4.1 The four violations are not incidental

They are not four arbitrary rows. Each maps to a specific audit finding:

| Row | Audit finding | Severity |
|---|---|---|
| `R05` | C-7 — the markdown-absence differential; one probe, three classifications | **critical** |
| `R06` | crash-as-pass | high |
| `R09` | C-3 — non-monotonic provenance | **critical** |
| `R15` | 4 surviving mutants | high |

**Two of the audit's critical findings are rows in v0.5's own test matrix.** That is a stronger statement than "the toolchain is defective": it means the matrix is *calibrated* — §145 was written against the same failure modes the audit measured, independently and two documents earlier. Part XI §170.1 made the same observation about Part XI's gate (six of fifteen conditions coincided with measured defects). **§145 is the third independently-derived enumeration to converge on the same defect set**, which is reasonable evidence the set is complete.

### 4.2 `R05` deserves its own note

`R05` expects a missing dependency to yield `BLOCK`. Measured, in **one run** with `markdown` absent:

```text
run_all.sh:33   announced as  SKIPPED
stage 3         --strict → fatal: "render checks could not run"     DEFECT
stage 7         reports     PASS, with 6 findings instead of 8      DEFECT
```

Under v0.3 §41, *"missing dependency → `BLOCKED`"*. Under v0.2 §7, `BLOCKED = legitimate execution could not proceed because a required precondition, authority, dependency, artifact, or resource was unavailable`. **The dependency is named in the definition.** So `R05`'s expected outcome is not merely a design preference — it follows from §7, and the toolchain produces three different answers to it, none of which is `BLOCKED`.

---

## 5. §125's validity formula cannot express four of §126's invalid states

§125 defines evidence validity:

```text
EvidenceValid =
    SubjectBound ∧ CheckBound ∧ VerifierBound ∧ ExecutionBound
    ∧ ObservationBound ∧ ScopeBound ∧ ResultBound
```

**7 conjuncts.**

§126 declares six invalid states:

```text
UNBOUND · STALE · INCOMPLETE · CONFLICTED · CORRUPTED · MISMATCHED
```

**6 states. The two enumerations do not correspond:**

| Invalid state | Expressible as a §125 conjunct? |
|---|---|
| `UNBOUND` | ✓ — `¬(any Bound)` |
| `MISMATCHED` | ✓ — same negation |
| **`STALE`** | ❌ **no conjunct** |
| **`INCOMPLETE`** | ❌ **no conjunct** |
| **`CONFLICTED`** | ❌ **no conjunct** |
| **`CORRUPTED`** | ❌ **no conjunct** |

**Four of six invalid states lie outside §125's definition of validity.**

### 5.1 The conflict is direct, not merely incomplete

§125 says *"Evidence validity requires:"* — it is a **definition**, not a sufficient-condition test. §127 says stale evidence defaults to `STALE`; §144 says *"STALE EVIDENCE ✕ CURRENT GATE"*; §126 says *"Invalid evidence MUST NOT silently participate in a release gate."*

A record that is `STALE` but whose seven bindings all hold **satisfies §125** and would be admitted to a gate under §125's formula, while §126, §127 and §144 each forbid it. **Two sections of one document define the same predicate differently**, and the weaker definition is the one given the formula.

This is the recurring defect pattern one more time, and now with a measurable consequence: `CONFLICTED` is perhaps the most safety-relevant of the six — evidence that contradicts other evidence — and no part of §125's conjunction would detect it.

### 5.2 Correction

§125 should either extend the formula:

```text
EvidenceValid =
    SubjectBound ∧ CheckBound ∧ VerifierBound ∧ ExecutionBound
    ∧ ObservationBound ∧ ScopeBound ∧ ResultBound
    ∧ ¬Stale ∧ ¬Incomplete ∧ ¬Conflicted ∧ ¬Corrupted
```

or state that §126's six invalid states are checked **in addition to** §125's seven conjuncts, making the conjunction a necessary but not sufficient condition. §126's *"must not silently participate"* implies the latter; nothing says so.

---

## 6. Tool identity has no substrate

§112 requires:

```yaml
tool:
  id: rfl.corpus-audit
  version: 0.5.0
  implementation_digest: ...
```

and §113: *"`ToolID + ToolVersion + ToolDigest` should be captured where practical. If the implementation changes, old evidence must not silently become evidence for new implementation."*

**Measured against `vendor/rfl-ae/skills/`:**

```text
hashlib occurrences  →  0
version strings      →  0
```

**Zero.** No hashing of any kind, and no script declares a version. This is not a partial implementation — there is no substrate at all.

It is also the third independent finding of the same absence:

| Part | Finding |
|---|---|
| Part V §56 | searched the toolchain for commit, version, or hash binding — found nothing |
| Part XI §150 | *"Evidence: absent-as-structure"* |
| Part XV §5 | §90's check contract requires `subject_identity`, `execution_record`, `observation`, `coverage_record` — `audit_corpus` produces none |

§112's *"Two implementations with the same name are not necessarily the same verifier"* is precisely the condition Part V §56 could not distinguish, because nothing in the toolchain carries an implementation identity. And §113's requirement — that changed implementations not silently inherit old evidence — is the mechanism that would have prevented the `has_prov` and `neg()` discrepancies from being invisible.

---

## 7. Five gate enumerations now exist

| Where | Items | Scope |
|---|---|---|
| v0.2 §38 | 13 | final claim check (agent-side) |
| Part XI §170 | 15 | `PACK-001…015` (pack-side) |
| v0.4 §101 | 15 | `G01…G15` (pack-side, machine-checkable) |
| v0.5 §145 | 15 | `R01…R15` (runtime test matrix) |
| v0.5 §146 | 14 | runtime release gate |

**Five enumerations, none declared as a projection of another.**

This is not automatically a defect: §145 is a *test matrix* and §146 a *release gate*, so they legitimately differ; v0.4 §101 is pack-side and v0.5 §146 runtime-side. **But the overlaps are undeclared**, and they are substantial — authority appears in both §101 and §146; injection in both; status semantics in both; negative tests in both.

Part XV §4 built the correspondence table between §101 and Part XI §170 and found 10 one-to-one mappings, three v0.4-only items, and two Part XI items with no counterpart. **§146 has not been reconciled with either**, and §145 has not been reconciled with §146 despite both being in the same document and §146's last item being *"negative tests verified"* — which is `R11`–`R15`.

Part XI §163's own rule — *"the compiler must not silently choose one"* — applies to gates as much as rules. Five enumerations of what counts as "verified enough" with no declared relation is the pattern Parts XIII–XVI have now recorded eight times.

---

## 8. v0.5's release gate is downstream of v0.4's blocking defect

§146's minimum runtime gate contains:

```text
tool identity verified        ← needs §112's implementation_digest
```

and §145's matrix contains:

```text
R14  deterministic compilation → SAME DIGEST
```

Both depend on v0.4:

- **`R14` is v0.4 §101's `G08`/`G09`/`G15`** — canonicalization, digest, reproducibility — which Part XV §2 established **cannot be executed** while §86 defines `CompiledPackDigest = SHA256(CanonicalCompiledPack)` over an artifact that contains `compiled_digest`.
- **`tool identity verified`** requires a digest of the tool implementation (§112), and §113's `ToolDigest` is the same construction. The toolchain has zero `hashlib` (§6), and v0.4 §86's circularity would have to be resolved before any digest-based identity could be defined at all.

**So two items in v0.5's own release gate cannot be satisfied until v0.4's one-sentence defect is fixed.**

This sharpens Part XV §11's recommendation rather than changing it. Part XV said:

> *"The single most useful next action is therefore not a v0.5 and not another analysis."*

v0.5 has now arrived. **It does not change the answer, and it makes the dependency tighter**: the runtime whose purpose is to enforce the compiled policy cannot pass its own gate while the compiled policy's identity is undefined. The blocking defect is now on the critical path for two documents.

---

## 9. What v0.5 improves

Stated plainly, and the list is substantial:

| Change | Significance |
|---|---|
| **§106 the reason the runtime exists** | *"The runtime MUST NOT trust the model to enforce the policy that constrains the model."* This is the architectural claim the whole lineage has been building toward, stated in one sentence |
| **§116 execution vs verification states** | The two-level separation Parts XII–XIV asked for. Eight states, disjoint from §7, with the reason stated |
| **§126 evidence lifecycle** | `STALE` resolved in the form Part XII recommended, plus five more invalid states |
| **§118 observation must retain provenance** | *"No detached observation should be accepted as verification evidence"* — closes the observation-without-execution gap |
| **§120 result integrity** | `PASS` is not sufficient; seven conditions must hold including *"predicate executed"* — the direct fix for crash-as-pass |
| **§122 FAIL requires an evaluated predicate** | `ERROR ≠ FAIL` as a derivation rule rather than a slogan |
| **§129 coverage evaluated separately from result** | `10/100 files, predicate PASS` → *"at most PASS for executed scope"* — the sharpest statement of `PARTIAL ≠ COMPLETE` in the lineage, and Part XV §3's missing `PACK-010` finding addressed in prose |
| **§131 non-monotonicity** | Correct principle; §3 above proposes its missing half |
| **§135–§137 three component boundaries** | runtime/verifier, verifier/gate, gate/release — each with its own failure mode |
| **§138–§141 audit log and actor model** | Sixteen event types, seven actors, tamper-evident chain via `previous_event_digest` |
| **§142–§143 replay** | `MATCH / MISMATCH / INDETERMINATE`, and §143's causes list includes *"environment"* and *"dependency versions"* — **precisely the two variables in the measured 6-vs-8 differential** |
| **§145 the test matrix** | 15 rows, and §4 above shows 4 are already calibrated against measured defects |
| **§149** | *"THE AGENT IS NOT TRUSTED TO VERIFY ITSELF"* — and the six-invariant closure |

**§143 deserves a specific acknowledgement.** It lists the causes of nondeterminism as *time · randomness · network · filesystem ordering · environment · dependency versions · external services*. Part VIII §0.1's measured differential — same commit, same fixture, 6 versus 8 findings — was caused by an **environment** difference (the presence of `markdown`) and nothing else. §143 anticipates exactly the variable that was measured, and §142's `MISMATCH` verdict is the correct classification for it.

---

## 10. Standing

| Part XVI section | Standing |
|---|---|
| §1 Four documents; protocol identity still undefined | **PROVED** — by dependency analysis |
| **§2.1 `STALE`** | **CLOSED** — §126 is Part XII §5's Option B, implemented |
| **§2.2 Two-level model** | **CLOSED**; mapping 2 of 8 **PROVED** |
| §3.1 Measured non-monotonicity | **PROVED** — 30/29/15/1/0 → PASS/FAIL/FAIL/FAIL/PASS |
| §3.2 `has_prov` is a conditional gate | **PROVED** — `audit_corpus.py:67, 88` |
| §4 §145 matrix measured | **PROVED** — 4 violated, 1 blocked, 2 exercisable, 8 no substrate |
| §4.1 Calibration | **PROVED** — R05 and R09 are C-7 and C-3 |
| §5 §125 vs §126 | **PROVED by count** — 7 conjuncts, 6 states, 4 not expressible |
| §6 Tool identity absent | **PROVED** — 0 `hashlib`, 0 version strings |
| §7 Five gate enumerations | **PROVED**; reconciliation **OPEN** |
| §8 Downstream of v0.4 §86 | **PROVED** — §146 and R14 both depend on it |
| §9 §143 anticipates the measured cause | **PROVED** |

### Corrections, in dependency order

**1. v0.4 §86 — still first, and now blocking two documents.** One sentence defines the digest input boundary. §8 above shows v0.5's release gate depends on it.

**2. §3 — complete §131.** Add the two rules it is missing: *removing evidence must never improve the result*, and *evidence absence must never satisfy a required-evidence predicate*. The second is `has_prov` stated as a rule, and it is the same rule Part XV §3 found absent from v0.4's gate.

**3. §5 — extend §125.** Add `¬Stale ∧ ¬Incomplete ∧ ¬Conflicted ∧ ¬Corrupted`, or state that §126's states are checked in addition to the seven conjuncts.

**4. §2.2 — complete §116's mapping.** Six of eight execution states have no verification mapping, and `TIMEOUT` alone admits three defensible answers.

**5. §7 — reconcile the gates.** §146 against §101, and §145 against §146. Five enumerations with undeclared overlaps.

**6. §4.2 — adopt §145's rows as `PHASE 0` acceptance criteria.** `R05`, `R06`, `R09` and `R15` each fail today with an executed receipt. They are the pack system's own prerequisites and the cheapest work available.

### Closing judgement

**v0.5 is the strongest document in the lineage, and it is the first to close findings rather than add them.** Two long-carried gaps are resolved — `STALE` in exactly the recommended form, and the execution/verification split with its rationale stated. §145 is the most testable artifact produced so far, and four of its fifteen rows are already calibrated against defects the audit measured independently.

Its open items are of a different character from Parts XII–XV's. They are **completions** — a mapping 2 of 8, a formula needing four more conjuncts, five enumerations needing reconciliation — rather than contradictions. That is the correct kind of remaining work.

**And the critical path has not moved.** Four normative documents, 149 sections, five gate enumerations, and the substrate is still absent: no `packc`, no schema, no `import json` in the toolchain, no `hashlib`, no `check_id`, no `CoverageRecord`. Part XV §8 recorded that `evidence.schema.json` had been proposed three times and never built; v0.5's §126 now specifies its lifecycle, which makes it four.

Every part since XIII has ended at the same place, and this one is no exception: **v0.4 §86's one-sentence fix, then §102 step 1.** v0.5 raises the cost of delay — the runtime now depends on it too — but the next artifact is still a file.
