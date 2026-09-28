# RFL-AE Prompt Packs — Part XVII: Protocol Schemas v0.6 — Closures and the Reintroduced Collapse

**Subject:** [`rfl-ae-protocol-schemas-v0.6.md`](rfl-ae-protocol-schemas-v0.6.md) (§150–§199), continuing [`v0.5`](rfl-ae-prompt-instructions-v0.5.md), [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md)
**Series:** [`part13`](rfl-ae-prompt-packs-part13.md) · [`part14`](rfl-ae-prompt-packs-part14.md) · [`part15`](rfl-ae-prompt-packs-part15.md) · [`part16`](rfl-ae-prompt-packs-part16.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — closures, and one regression
>
> **v0.6 closes three findings that Parts XIII–XVI carried**, and two of the closures are on properties this series repeatedly called the most important in the protocol:
>
> - **Part XIV §3 CLOSED** — §154's `Scope` now carries **`claimed`**. Part XI §155 called scope *"the highest-value part of the protocol"*, and Part XIV §3 found the containment mandated over a structure with no `claimed` field. It is there now, and the containment is four terms.
> - **Parts XIII §4 / XIV §5 / XV §7.2 CLOSED at the object level** — §162's `CheckResult` carries `evaluator`, `evaluator_version`, `evaluator_digest`. The finding was that `verifier` appeared in one of five accumulated check variants; the newest one has it.
> - **Part XVI §5 PARTIALLY CLOSED** — §174 gives evidence validity **10 conjuncts** against §125's 7, and five of §126's six invalid states are now expressible. `CONFLICTED` still is not.
>
> **And it introduces one regression that undoes a closure from the immediately preceding part.**
>
> Part XVI §2.2 verified that v0.5 §116's eight execution states had **zero overlap** with v0.2 §7's six verification states — the two-level separation Parts XII–XIV had asked for, implemented. **v0.6 §160 replaces that state set, and it contains `BLOCKED`** — the same token as v0.2 §7. Measured: overlap goes from **0 → 1**.
>
> The separation v0.5 built is partially collapsed one document later, by a schema that did not reference it.

---

## 1. Standing: §0–§199 across five documents

v0.6 continues the numbering of v0.5, which continues v0.4's, which continues v0.3's, which continues v0.2's. The effective specification is now **199 sections across five files**.

The dependency is structural again — v0.6 uses §7's six statuses in §162 without defining them, §52's coverage states as §164's basis, and §41's chain verbatim as §167. And **§167 reproduces v0.3 §41's fourteen-chain exactly**, so the cross-document dependency is now four documents deep for a single state machine.

### 1.1 What is genuinely new

v0.6 is the first document in the lineage to define **object schemas** rather than contracts about objects. Sixteen schema sections (§152–§166), each with a field list. That is a category change, and it is what §196's implementation order requires: *"1. Typed identifiers, 2. Schema definitions, 3. Canonicalization…"* — v0.6 §152–§166 supply steps 1 and 2.

---

## 2. Closures

### 2.1 Part XIV §3 — `claimed` is present

Part XIV §3 found that v0.3 §46 mandated `CLAIMED ⊆ EXECUTED ⊆ DECLARED` while defining a scope object with five fields and **no `claimed`**. Verified then: zero occurrences.

**v0.6 §154:**

```yaml
Scope:
  scope_id:
  declared:
  permitted:
  excluded:
  executed:
  claimed:          # ← present
```

and the containment is now **four** terms:

```text
CLAIMED ⊆ EXECUTED ⊆ PERMITTED ⊆ DECLARED
```

**CLOSED**, and improved — the four-term form adds `PERMITTED`, which distinguishes *what was allowed* from *what was declared*. That is the term v0.5 §111's capability/scope intersection needed: `AUTHORITY = CAPABILITY ∩ SCOPE` produces a permission, and permissions now have a home.

Two consequences worth recording:

- **Part XI §155's danger condition is now expressible.** `CLAIMED > EXECUTED → must be impossible to classify as VERIFIED` requires the two terms to be comparable; they now are. Combined with §156 of Part XI's proposed `RFL-SCOPE-001` predicate (`operator: subset, left: claimed, right: executed`), the single conjunct behind four of seven substantive audit defects has a representation.
- **§154 states two prohibitions that Part XIV asked for in prose:** *"An exclusion MUST NOT silently disappear from the resulting scope"* and *"An empty scope MUST NOT be interpreted as unrestricted scope."* The second is the default-open failure mode, stated as a rule.

### 2.2 Parts XIII–XV — the check object now carries verifier identity

The finding was cumulative across three parts:

| Part | Form |
|---|---|
| XIII §4 | v0.2 §11 gave two 8-field check views; the instance form dropped `verifier` |
| XIV §5 | v0.3 §47 added two more; `verifier` in **1 of 4** |
| XV §7.2 | v0.4 §90 added a fifth; `verifier` in **1 of 5** — while §94 **required** `verifier_identity` |

**v0.6 §162:**

```yaml
CheckResult:
  check_result_id:
  check_id:
  observation_id:
  evaluator:
  evaluator_version:
  evaluator_digest:
  status:
  findings:
  timestamp:
```

**CLOSED at the object level.** The newest check object carries three verifier-identity fields, which is stronger than the original ask — `ToolID + ToolVersion + ToolDigest` is v0.5 §112–§113's minimum tool identity, applied to the evaluator.

**Residual, and it should be stated precisely:** the *proliferation* is not closed. Six check-shaped objects now exist across five documents (v0.2 §11 ×2, v0.3 §47 ×2, v0.4 §90, v0.6 §162), and the four older ones still lack the field. §162 is a sixth variant that happens to be right, not a reconciliation of the first five. Under the lineage's own recurrent principle — *one object with declared projections* — the correction is to nominate §162 as canonical and restate the others as projections.

### 2.3 Part XVI §5 — partially closed, by conjunct addition

Part XVI §5 found that v0.5 §125 defined evidence validity with 7 conjuncts and v0.5 §126 declared 6 invalid states, of which **4** were inexpressible — so a `STALE` record satisfying all seven bindings would evaluate as *valid*, directly conflicting with §126, §127 and §144 in the same document.

**v0.6 §174 gives 10 conjuncts:**

```text
SubjectResolvable · SubjectDigestMatches · ExecutionResolvable
· ObservationResolvable · CheckResultResolvable · VerifierResolvable
· ScopeBound · ProvenanceComplete · EvidenceDigestValid · ¬Expired
```

Mapped semantically against §126's six invalid states — **five are now covered**:

| Invalid state | Covering conjunct |
|---|---|
| `UNBOUND` | the four `…Resolvable` conjuncts |
| `MISMATCHED` | `SubjectDigestMatches` |
| `STALE` | `SubjectDigestMatches` |
| `INCOMPLETE` | `ProvenanceComplete` |
| `CORRUPTED` | `EvidenceDigestValid` |
| **`CONFLICTED`** | **none** |

**PARTIALLY CLOSED: 5 of 6.** The residual is specific and safety-relevant — `CONFLICTED` is evidence that contradicts other evidence, and §174 has no consistency or contradiction conjunct. §174 also adds `¬Expired(E)`, which is new and which pairs with §155's `Authority.expiry` and §156's `Capability.expires` — expiry is now a first-class concern in three schemas.

> **Method note.** My first check on this was a literal `grep` for each state's *name* inside §174's conjunct list, which returned "NOT-EXPRESSIBLE" for all six. That was the wrong test: it asked whether a string appears rather than whether a property is entailed. The corrected semantic mapping is above. The error is worth recording because it is **exactly the `has_prov` failure the audit is about** — a lexical test standing in for a semantic one, returning a confident wrong answer. It was caught before publication, which is the only reason it is a footnote rather than a finding.

---

## 3. The regression: §160 reintroduces `BLOCKED` into the execution state set

### 3.1 What v0.5 had established

Part XVI §2.2 verified v0.5 §116's separation and reported it as a **closure**:

```text
v0.2 §7  (verification):  PASS  FAIL  ERROR  UNKNOWN  SKIPPED  BLOCKED
v0.5 §116 (execution):    SCHEDULED  RUNNING  COMPLETED  FAILED_TO_START
                          TIMEOUT  CANCELLED  INTERRUPTED  CRASHED
```

Measured: **zero overlap**. The two sets are disjoint, and §116 states the rule explicitly — *"These are execution states. They must not be confused with verification states."*

### 3.2 What v0.6 §160 does

```text
CREATED
AUTHORIZED
STARTED
COMPLETED
FAILED
ERRORED
CANCELLED
TIMED_OUT
BLOCKED
```

**Measured:**

| | v0.5 §116 | v0.6 §160 |
|---|---|---|
| states | 8 | 9 |
| overlap with the other | **2** | **2** |
| overlap with v0.2 §7 | **0** | **1** |

Overlap between v0.5 §116 and v0.6 §160 is only `COMPLETED` and `CANCELLED`. **Six v0.5 states are gone; seven v0.6 states are new.** And the state that arrives carrying a name from the *verification* set is:

```text
v0.6 §160:  BLOCKED         ← execution state
v0.2 §7:    BLOCKED         ← verification state
             ↑ identical token
```

### 3.3 Three collisions, not one

The exact-string collision is `BLOCKED`. But two more are semantic:

| v0.6 §160 (execution) | v0.2 §7 (verification) | Relation |
|---|---|---|
| `BLOCKED` | `BLOCKED` | **identical token** — cannot be told apart by value |
| `FAILED` | `FAIL` | near-identical; one is a process outcome, one a predicate outcome |
| `ERRORED` | `ERROR` | near-identical, same distinction |

So a record carrying the value `BLOCKED` is ambiguous between the two layers by construction. Before v0.6, it was not: v0.5 §116 had no state in common with §7, and the layer was identifiable from the value alone.

### 3.4 And a spelling drift that would break canonicalization

```text
v0.5 §116:  TIMEOUT
v0.6 §160:  TIMED_OUT
```

Same state, two spellings, in adjacent documents of one protocol. Under v0.6 §185 (*canonicalization must define "list ordering… version representation"*) and §184's determinism contract, a canonicalizer receiving both would either treat them as distinct states or need an undeclared normalization rule. **This is precisely §17's prohibition** — *"Never maintain multiple subtly different interpretations of the same syntax without explicitly declaring their semantics"* — applied to the protocol's own vocabulary.

### 3.5 Why this is the most consequential finding in Part XVII

Part XVI §2.2 recorded the two-level separation as **closed**, and it was: verified 0 overlap, with the rationale stated in the document. **v0.6 §160 reopens it**, and the mechanism is the one the whole lineage is about — a schema that restates a concept without referencing the document that defined it.

The consequence is concrete. §169 forbids:

```text
EXECUTE   → PASS
OBSERVE   → VERIFIED
```

and the reason those transitions are forbidden is that the layers must not collapse. But §160 gives the execution layer a `BLOCKED` state, so an execution record can now read `BLOCKED` — and under v0.2 §7 a `BLOCKED` *verification* result is one that *"has not passed or failed."* **A reader of an execution record can no longer determine which claim the value supports.**

That is the collapse §169 forbids, arriving through the vocabulary rather than through a transition.

**Correction.** Either restore §116's eight states, or keep §160's nine and rename the collision:

```text
§160:  BLOCKED  →  NOT_STARTED_AUTHORIZED   (or fold into CREATED)
       FAILED   →  PROCESS_FAILED
       ERRORED  →  PROCESS_ERROR
       TIMED_OUT → TIMEOUT
```

The first option is cheaper and has the property §116 was written for: the two layers share no vocabulary, so no value is ambiguous.

---

## 4. `EnvironmentAuthority` is dropped from the intersection

v0.3 §45 and v0.4 §76 both define:

```text
EffectiveAuthority = RequestedAuthority ∩ GrantedAuthority ∩ EnvironmentAuthority
```

**v0.6 §155:**

```text
EffectiveAuthority =
    DeclaredAuthority ∩ GrantedAuthority ∩ ScopeAuthority
  ∩ CapabilityAuthority ∩ RuntimePolicy
```

Three changes:

| Change | Note |
|---|---|
| `RequestedAuthority` → `DeclaredAuthority` | third name for this term (Pack → Requested → Declared) |
| `ScopeAuthority`, `CapabilityAuthority` added | consistent with §154's `permitted` and §156's capabilities |
| **`EnvironmentAuthority` removed** | ← the significant one |

### 4.1 Why removing it matters here specifically

**Part VIII §0.1's decisive receipt was an environment difference.** Same commit, same fixture, same command — `bare: PASS (exit 1, 6 findings)` versus `full: PASS (exit 1, 8 findings)`. Two checks did not run because `markdown` was absent from the environment, and the harness reported `PASS` either way.

`EnvironmentAuthority` is the term that would have carried that distinction into the authority intersection. Removing it means the protocol's authority model no longer has a place to record *"this execution's environment differed, and therefore its result is not equivalent to the other run's."*

And v0.5 §143 already lists *"environment"* among its causes of nondeterminism, and v0.6 §179 has `STALE_INPUT` in its error taxonomy but no `ENVIRONMENT_MISMATCH`. **The concept survives in the diagnostics and has left the authority model.**

This is a **regression relative to v0.3 §45 and v0.4 §76**, and it is not merely a naming change — three terms became five, and the one dropped is the one with a measured defect behind it.

**Correction.** Restore `EnvironmentAuthority` as a sixth term, or state where environment binding is enforced instead. §160's `ExecutionRecord.environment_digest` exists, so the data is captured — the question is whether it constrains authority or only travels as metadata.

---

## 5. The v0.4 §86 blocking defect is unresolved in a third document

Part XV §2 established that v0.4 §86 defines `CompiledPackDigest = SHA256(CanonicalCompiledPack)` while §88 places `compiled_digest` **inside** `compiled_pack.identity` — circular. Part XV §11: *"Three of v0.4's fifteen release gates cannot be executed until it is resolved."* Part XVI §8: v0.5's own release gate is downstream of it too.

**v0.6 §185 lists eleven canonicalization requirements:**

```text
field ordering · omitted/default fields · Unicode normalization
· numeric representation · boolean representation · null handling
· list ordering · map ordering · whitespace rules · encoding
· version representation
```

**Measured: zero exclusion rules** — the same count as v0.4 §84. None of the eleven is of the form *"fields excluded from the digest input."*

And v0.6's nearest statement points the wrong way, as §85 did in v0.4:

> §184: *"Timestamps, environment identifiers, random IDs, and execution metadata MUST NOT contaminate the semantic digest unless intentionally included."*

That is the correct *principle* — and `compiled_digest` is not metadata. It is a field inside `identity`, which §185's *"field ordering"* and *"omitted/default fields"* requirements explicitly bring inside the canonical form. So §184 and §185 together still leave the circular artifact circular.

**The blocking defect now spans three documents** (v0.4, v0.5, v0.6), and v0.6's §198 requires a v0.6 release to bind *"schema digest, implementation digest, test-suite digest, fixture-set digest"* — **four digests, in a document that still has not defined what a digest is computed over.**

That is worth stating plainly, because §196 and §199 both instruct the reader to implement: §196 step 2 is *"Schema definitions"* and step 3 *"Canonicalization"*, and v0.6 supplies step 2 while leaving step 3's boundary undefined. **Following v0.6's own implementation order reaches the same wall.**

---

## 6. `SKIPPED` remains unreachable — third occurrence

Part XIV §6.2 found that v0.3 §41's failure transitions contained no path to `SKIPPED`, while v0.2 §1 makes `SKIPPED → NOT PASS` mandatory and v0.3 §50 lists `SKIPPED` among possible results. Part XVI §7.3 recorded it as *"deferred to G14, not solved."*

**v0.6 §168:**

```text
ANY → ERROR
ANY → BLOCKED
ANY → CANCELLED
ANY → UNKNOWN
```

**Measured: zero transitions to `SKIPPED`** — while §162 lists `SKIPPED` among `CheckResult`'s six allowed statuses, and §195 repeats the invariant `SKIPPED ≠ PASS`.

So the state is:

- a legal `CheckResult` status (§162)
- bound by a mandatory claim rule (§195)
- assigned a position in the status lattice (§178)
- and **unreachable by every transition the state machine defines (§168)**

§168 says *"a failure transition MUST preserve the reason"* — and `SKIPPED` is precisely a reason that is **not** a failure. It is excluded from §168 by that section's own framing, exactly as it was from v0.3 §41. **Three documents, three state machines, and the reachability gap has survived all three.**

This matters beyond tidiness because of what Part VIII §98 measured: `run_all.sh:33` literally announces *"render checks will be SKIPPED"* — the protocol's own vocabulary appearing in the audited toolchain — and the observed behaviour is `PASS` with two checks not run. **§195's `SKIPPED ≠ PASS` is the exact rule that behaviour breaks**, and §168 provides no route by which the protocol reaches the state and applies the rule.

**Correction.** `SKIPPED` is a decision, not a failure: add a transition of the form *PLAN → SKIPPED* with the check declared but excluded by policy, and bind it to §164's `CoverageRecord.excluded_scope`. §154's `excluded` field already exists for this.

---

## 7. Gate enumerations: now six

| Where | Items | Kind |
|---|---|---|
| v0.2 §38 | 13 | agent final claim check |
| Part XI §170 | 15 | `PACK-001…015` |
| v0.4 §101 | 15 | `G01…G15` |
| v0.5 §145 | 15 | `R01…R15` test matrix |
| v0.5 §146 | 14 | runtime release gate |
| **v0.6 §197** | **15** | conformance gate |

Part XVI §7 found five; v0.6 §197 is the sixth. **§197 does include coverage** (*"Coverage tests PASS"*), which addresses Part XV §3's finding that v0.4 §101 omitted the partial-coverage gate item — **PARTIALLY CLOSED**, in the sense that the item is now present, though §193 is what states its semantics and §197's row does not reference it.

And a new matrix joins them. **§189 specifies 16 conformance areas × 2 (positive, negative) = 32 cells, every one of which reads `yes`.** Verified: 16 rows.

That is legitimate as a *specification* — §189 says the suite *"MUST verify at least"* these areas. But it is the first conformance matrix in this lineage with **no current-state column**: Part XI §145's 12-row matrix had 2 rows already failing, and Part XVI §4 measured 4 of v0.5 §145's 15 rows failing against real receipts. §189's uniform `yes/yes` conveys which areas are *in scope*, and nothing about which are *hard*, *absent*, or *currently failing* — and the substrate for all 32 cells is what Parts XIII–XVII have each found missing.

---

## 8. What v0.6 improves

The list is long, and several items close findings this series raised:

| Change | Significance |
|---|---|
| **§152–§166 sixteen object schemas** | First typed object model in the lineage; supplies §196's implementation steps 1–2 |
| **§152 identifier classes + the identifier/digest rule** | *"An identifier MUST NOT be treated as cryptographic integrity evidence unless it contains or is explicitly bound to a cryptographic digest"* — closes a confusion Part XI §165 left implicit |
| **§153 subject: `locator != identity`, `identity != integrity`** | Three distinct concerns named; `content_digest` is what evidence binds to |
| **§154 `claimed` present, four-term containment** | **XIV §3 CLOSED**; adds `permitted`, forbids silent exclusion loss, forbids empty-as-unrestricted |
| **§161 observation states, 7-valued, and `NOT_OBSERVABLE` ≠ `FALSE`** | Absence made explicit — the single most repeated corrective in this series |
| **§162 evaluator triple** | **XIII §4 / XIV §5 / XV §7.2 CLOSED at the object level** |
| **§162 `PASS != execution success`, `ERROR != FAIL`** | The four semantic rules stated as inequalities rather than slogans |
| **§163 evidence questions** | Nine questions, including *"Against which exact artifact/content identity?"* |
| **§164 `80/80 PASS → Coverage = PARTIAL`** | A worked numeric example, not a principle. The clearest statement of `PARTIAL ≠ COMPLETE` in the lineage |
| **§165–§166 gate and event schemas** | `derived_status` vs `decision`; append-only events with `payload_digest` |
| **§169 ten forbidden transitions** | Includes `MODEL → AUTHORITY` and `MODEL → EVIDENCE` as *named rejections* |
| **§170–§171 pre/postconditions per transition** | *"If a precondition is unresolved: `transition = BLOCKED`"* — and *"MUST NOT be silently treated as satisfied"* |
| **§172 referential integrity** | Nine reference examples; *"Dangling references MUST invalidate the dependent artifact"* |
| **§174 EvidenceValid, 10 conjuncts** | **XVI §5 PARTIALLY CLOSED** (5 of 6) |
| **§176 *"a gate MUST NOT accept `passed: true` as authoritative input"*** | The direct anti-verification-theater rule. *"If such a field exists, it MUST be treated as an untrusted claim."* This is the clearest instance in the lineage of the principle the audit spent eight parts establishing |
| **§179 error taxonomy, 15 classes** | Includes `STALE_INPUT` and `INTEGRITY_ERROR` |
| **§180 `checker_crash ≠ checked_subject_failure`** | With a worked expected/actual example |
| **§181 `self_test = PASS` is not independent verification** | Names seven admissible methods; closes the self-certifying-verifier route |
| **§182 thirteen negative fixture classes; §183 eight positive ones** | **§183 is the first normative requirement for positive fixtures in the lineage** — Part VIII found the toolchain tested only against broken artifacts |
| **§190 seventeen-case transition matrix** | Includes `checker crash`, `partial observation`, `stale evidence`, `coverage gap`, `digest mismatch` |
| **§192–§194 worked failure/partial/stale examples** | Each with the correct result *and* the prohibited one |
| **§195 nineteen protocol invariants** | The most complete enumeration in the lineage |
| **§196 twenty-step implementation order** | Explicitly: *"Do not implement release claims before the lower layers exist."* |
| **§199** | *"RFL-AE v0.6 therefore treats the protocol itself as a verifiable artifact."* |

---

## 9. Standing

| Part XVII section | Standing |
|---|---|
| §2.1 `claimed` present, 4-term containment | **CLOSED** — verified: 0 → 1 occurrence |
| §2.2 evaluator triple in §162 | **CLOSED at object level**; proliferation residual |
| §2.3 §174 10 conjuncts, 5 of 6 states | **PARTIALLY CLOSED**; `CONFLICTED` OPEN |
| §3 §160 state set replaces §116's | **PROVED** — 8 vs 9, overlap 2 |
| §3.2 §160 vs §7 overlap 0 → 1 | **PROVED by set difference** |
| §3.3 three collisions | **PROVED** — `BLOCKED` exact; `FAILED`/`ERRORED` semantic |
| §3.4 `TIMEOUT` → `TIMED_OUT` | **PROVED** |
| §4 `EnvironmentAuthority` dropped | **PROVED** — 3 terms → 5, the environment term absent |
| §5 §185 no exclusion rule | **PROVED** — same count as v0.4 §84; blocking defect now 3 documents old |
| §6 `SKIPPED` unreachable | **PROVED** — 0 transitions; 3rd occurrence |
| §7 Six gate enumerations; §189 all-`yes` | **PROVED by count** — 16 rows, 32 cells |
| §7 §197 includes coverage | **PARTIALLY CLOSED** — Part XV §3 |

### Corrections, in dependency order

**1. §3 — restore disjointness.** Either v0.5 §116's eight states, or rename `BLOCKED`/`FAILED`/`ERRORED` in §160 and normalize `TIMED_OUT`. This is the only regression in v0.6; it undoes a verified closure from the previous document.

**2. §5 — the v0.4 §86 digest boundary.** Still first on the critical path, now spanning three documents. §198 requires four digests and §185 still does not define what they are computed over.

**3. §4 — restore `EnvironmentAuthority`,** or state where environment binding constrains rather than travels.

**4. §6 — make `SKIPPED` reachable.** One transition, bound to §164's `excluded_scope`.

**5. §2.3 — add a consistency conjunct to §174** covering `CONFLICTED`.

**6. §7 — reconcile seven enumerations** (§38, §170, §101, §145, §146, §197, §189) and give §189 a current-state column.

**7. §2.2 — nominate §162 canonical** and restate the other five check objects as projections.

### Closing judgement

**v0.6 closes more than it opens, and two of the closures land on properties this series repeatedly called the most important.** Part XIV §3's missing `claimed` — the conjunct behind four of seven substantive defects, and Part XI §155's highest-value object — is now represented, and improved to four terms. The verifier-identity omission that accumulated across Parts XIII, XIV and XV is closed in the newest object. §174's conjunct list grew by three and now expresses five of six invalid states.

**The regression is the notable part, and it is instructive.** v0.5 §116 achieved something this lineage had been trying to do for four documents — a verified 0-overlap separation between execution and verification states. **v0.6 §160 replaced the state set without referencing it**, and `BLOCKED` returned to both layers. The document that did this is the one that added sixteen schemas, a nineteen-item invariant list, and §169's ten forbidden transitions.

That is the pattern Parts XIII–XVII have now recorded nine times, in its most consequential instance: **each document specifies its objects correctly and in isolation, and the cross-document invariants — the ones a previous document had to compute to establish — are not carried forward.** A specifier reading only §160 would have no way to know that `BLOCKED` had been deliberately removed from that layer one document earlier.

**And the critical path has not moved.** Five documents, 199 sections, sixteen schemas, seven gate enumerations — and §196's step 3, *canonicalization*, still cannot be implemented because v0.4 §86's digest boundary is undefined. `evidence.schema.json` has now been specified in prose five times.

§199 says the next target is *"SCHEMA → REFERENCE IMPLEMENTATION → CONFORMANCE FIXTURES → EXECUTION RECORDS → EVIDENCE → REPLAY → RELEASE GATE"*. **The schema layer now largely exists, in prose.** The obstacle is unchanged: one sentence about what a digest is computed over, and then a file.
