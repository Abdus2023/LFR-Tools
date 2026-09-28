# RFL-AE Prompt Packs — Part XIV: Operational Agent Protocol v0.3 — Additive Revision Analysis

**Subject:** [`rfl-ae-operational-agent-protocol-v0.3.md`](rfl-ae-operational-agent-protocol-v0.3.md) (§41–§70), continuing [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md) (§0–§40)
**Series:** [`part10`](rfl-ae-prompt-packs-part10.md) · [`part11`](rfl-ae-prompt-packs-part11.md) · [`part12`](rfl-ae-prompt-packs-part12.md) · [`part13`](rfl-ae-prompt-packs-part13.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — additive revision
>
> v0.3 begins at **§41** and uses v0.2's state definitions (§7), authority formula (§23), and evidence binding (§26) **without restating them**. It therefore *extends* the protocol rather than superseding it — the effective specification is now **§0–§70 across two documents** (§1).
>
> That has a consequence the documents do not declare, and which §57 of v0.3 makes load-bearing:
>
> ```text
> INSTRUCTION PRECEDENCE requires explicit provenance.
> The provenance of §41–§70 relative to §0–§40 is not stated.
> ```
>
> **Headline finding (§3):** v0.3 §46 defines the scope object and mandates `CLAIMED ⊆ EXECUTED ⊆ DECLARED` — but the object it defines **has no `claimed` field.** The containment cannot be evaluated from the structure that is supposed to carry it. This is Part XI §155's *highest-value* property, and Part XIII §3's pattern, appearing in a single section.
>
> **Seven sections analysed; four findings are the pattern established in Part XIII.**

---

## 1. v0.3 is additive — proved by dependency, not by assertion

v0.3's title differs from v0.2's (*"OPERATIONAL AGENT PROTOCOL"* vs *"MASTER PROMPT INSTRUCTIONS"*), and it starts numbering at §41. Two readings are possible in principle: a new document, or an extension of the old one.

**The dependency structure settles it.** v0.3 uses the following without defining them:

| v0.3 uses | Defined only in |
|---|---|
| `BLOCKED`, `UNKNOWN`, `ERROR`, `FAIL` (§41 transitions, §50) | **v0.2 §7** — six states and their semantics |
| §45's `EffectiveAuthority = … ∩ … ∩ …` | **v0.2 §23** — the formula; §45 changes one term (see §7.1) |
| §51's evidence components (`subject, check, verifier, execution, observation, scope, result`) | **v0.2 §26** — the binding list |
| §55's *"required evidence"*, §69's *"valid verifier"* | **v0.2 §13, §26** |
| §64's determinism property | **v0.2 §20, §22** — canonicalize → digest |

**If v0.3 superseded v0.2, none of these would be defined** — §41 would reference undefined states, and §45's formula would be the document's first mention of effective authority. v0.3 is not self-contained. **It requires v0.2 in force.**

### 1.1 What this means for identity

Under v0.2 §24 (*"track at minimum: PackDigest…"*) and §20 (*"a normative instruction pack has IDENTITY, VERSION…"*), the protocol's identity is now ambiguous in three ways:

1. **One pack or two?** `v0.2` and `v0.3` are separate files with separate titles, but v0.3 is not intelligible alone. Whether `PackDigest` covers §0–§40 or §0–§70 is undefined.
2. **Version numbering.** v0.3 carries no §0–§40; a reader receiving only v0.3 has a document whose first section is §41 and whose §7 is referenced but absent.
3. **Title change.** The document name changed at the same revision boundary. §152 of Part XI and v0.2 §24 both forbid filename-based identity — so the *name* change is irrelevant to identity, which makes the numbering the only signal, and the numbering says "continuation."

**Recommendation.** Rename to reflect what it is (e.g. `…-v0.3-addendum.md`), or merge §0–§70 into a single `v0.3` with a stated mapping. Then declare the resulting `PackDigest` coverage explicitly. This is the cheapest item in this part and it is a precondition for §57's provenance requirement and §64's `Compile(P)₁ == Compile(P)₂`.

---

## 2. Disposition of Part XIII's five findings

| Part XIII finding | Disposition |
|---|---|
| **XIII §3** — §26's binding (7) vs digest (6) | **RESTATED** — §51 restates the 7-item binding; no digest added, nothing reconciled |
| **XIII §4** — §11's two 8-field check views | **RESTATED, worsened** — §47 adds two more variants (§5) |
| **XIII §5** — §28's 13 conditions, 4 unrepresentable | **PARTIALLY ADDRESSED** — §41 supplies 6 transitions, §52 a separate coverage status; ~5 conditions still unmapped |
| **XIII §2.3** — no `STALE` state | **CLOSED behaviourally** — §61 defines the action (*reverify*), though not the state |
| **XIII §2.4** — §22's unlabelled rule class | **PARTIALLY CLOSED** — §58 defaults unresolved conflict to `BLOCKED` |

### 2.1 XIII §5 — the largest single improvement

v0.2 §28 listed 13 conditions under *"Stop **or downgrade the claim**"* and classified none. v0.3 §41 supplies **six explicit failure transitions**:

```text
missing authority ─────→ BLOCKED
missing dependency ────→ BLOCKED
ambiguous subject ─────→ UNKNOWN
verifier failure ──────→ ERROR
predicate violation ───→ FAIL
insufficient evidence ─→ UNKNOWN
```

And **§52 introduces a second, four-valued coverage status** — `COMPLETE / PARTIAL / ZERO / UNKNOWN` — separate from §50's six-valued result set.

**That is, in structure, the correction Part XIII §5 proposed**: split *stop* (a six-state rule result) from *downgrade the claim* (a coverage/claim-level judgement) rather than issuing both under one verb. §52 is the first place in the protocol where a claim-level status exists independently of a rule status. **This is the right shape**, and §52's closing rule — *"Do not treat `0 findings` as `complete coverage`"* — is the audit's own `NO COVERAGE ≠ PASS` principle stated normatively.

Two gaps remain, in §4 below.

### 2.2 XIII §2.3 — closed behaviourally

Part XIII found that §24 required dependent evidence be *invalidated*, with no state to transition to. v0.3 §61 resolves it by specifying the **action** rather than adding a status:

```text
Evidence(subject=X, execution=T1) does not apply to subject=Y
If subject identity changes: reverify
```

If reverification is mandatory on subject change, the `STALE` state question is largely moot — the remedy is a procedure, not a label. That is a better resolution than the one Part XII and Part XIII proposed.

**Residual, MINOR:** §61's escape clause — *"unless an explicit equivalence proof permits evidence reuse"* — has no definition. Nothing in §61, §63, or §64 states what constitutes an equivalence proof or who may issue one. As written it is an unbounded exception to a mandatory rule, which is the shape §55 warns against (*"a missing required component must not silently evaluate to true"*).

---

## 3. Headline: §46 mandates a containment it cannot evaluate

§46 says:

> *"The agent must maintain: `CLAIMED ⊆ EXECUTED ⊆ DECLARED`"*

and defines:

```yaml
scope:
  declared:
    subjects: []
    paths: []
    revisions: []
    checks: []
    operations: []
  executed: []
  excluded: []
  skipped: []
  unresolved: []
```

**Top-level fields: `declared`, `executed`, `excluded`, `skipped`, `unresolved`. There is no `claimed`.**

The containment has three terms. The structure carries two of them.

**Why this is the most consequential finding in v0.3.** Part XI §155 identified scope as *"the highest-value part of the protocol"*, and Part VI §78 found that of seven substantive audit defects, **four reduce to the single conjunct `SCOPE_COVERED`**. Part XI §155 states the danger precisely:

```text
CLAIMED > EXECUTED
    must be impossible to classify as VERIFIED
```

Making that impossibility enforceable requires `claimed` and `executed` to be **comparable**. A scope object without `claimed` cannot be checked: §53 generates claims from *evidence + coverage + predicate + policy*, but the claim is never placed back against the executed scope for the containment to hold. So the invariant is stated, mandated, and unenforceable from the structure defined beside it.

This is **Part XIII §3's pattern in its purest form** — a requirement stated in one form, represented in another, with no declared relation — and it is the same defect that `has_prov` exhibits in the audited toolchain: a rule whose scope is determined by something other than what the rule names.

**Correction.** Add `claimed` to the object, and add the containment as an executable predicate per Part XI §156:

```yaml
scope:
  declared: {...}
  executed: [...]
  claimed:  [...]      # ← the missing term
  excluded: [...]
  skipped:  [...]
  unresolved: [...]

# and, per §156:
rule: RFL-SCOPE-001
predicate: { operator: subset, left: claimed, right: executed }
on_violation: { status: FAIL }
```

---

## 4. Three coverage representations, none containing the invariant's terms

The protocol now represents coverage in three places:

| Where | Representation |
|---|---|
| **v0.2 §27** | `CoverageRecord`: declared, **discovered**, executed, excluded, skipped, **unexplored** (6) |
| **v0.3 §46** | scope object: declared{subjects,paths,revisions,checks,operations}, executed, excluded, skipped, **unresolved** (5 + 5) |
| **v0.3 §52** | report values: `COMPLETE / PARTIAL / ZERO / UNKNOWN` (4) |

**They do not agree, and nothing declares their relation.**

- v0.2 §27 has **`discovered`** and **`unexplored`**; v0.3 §46 has **`unresolved`** and no `discovered`. Are `unexplored` and `unresolved` the same concept? If so the rename is undeclared; if not, §46 lost a field and gained a different one.
- v0.3 §46 adds five sub-fields under `declared` (`subjects, paths, revisions, checks, operations`) that v0.2 §27 lacks — a genuine improvement, since §27's flat `declared scope` could not express *what* was declared.
- **Neither contains `claimed`**, which §46's own invariant requires (§3).
- v0.3 §52 introduces the four report values, but **§52 states coverage as a ratio** — `coverage = executed_scope / declared_scope` — and gives no mapping from the ratio to the symbols.

### 4.1 `ZERO` is ambiguous, and it is the case that matters most

```text
coverage = executed_scope / declared_scope
Report: COMPLETE / PARTIAL / ZERO / UNKNOWN
```

If `ZERO` means `0 / n` — declared something, executed nothing — that is the `NO COVERAGE ≠ PASS` case, and it is the one Part III §21 measured (5 of 11 invariants, reported `ALL FILES OK`).

If `ZERO` means `0 / 0` — a check with no declared scope — that is a **specification failure**, not a coverage outcome, and §46's *"Do not silently shrink scope"* and §55's *"a missing required component must not silently evaluate to true"* both bear on it. 0/0 is undefined as a ratio, so the formula does not produce it either.

**The two cases require different responses and share one symbol.** Under §68's discipline (*use the narrowest defensible claim*), reporting `ZERO` for a check that declared nothing would claim a coverage measurement where no denominator was ever established.

**Recommended correction:** define the four symbols against the ratio explicitly, and split `ZERO` into `ZERO` (0/n — executed none of a declared corpus) and `UNDECLARED` (0/0 — no denominator). Then state the mapping from v0.2 §27's six fields and v0.3 §46's five onto this vocabulary.

---

## 5. The check specification now has four variants

Part XIII §4 found that v0.2 §11 gave two 8-field views of a check that disagreed. v0.3 §47 adds **two more**, one of them internally inconsistent.

**§47 states a 7-field list and then gives a 7-field example that does not match it:**

| §47's list | §47's example |
|---|---|
| `check` | `CHECK C001` ✓ |
| `subject` | `Subject` ✓ |
| `scope` | `Scope` ✓ |
| `procedure` | `Procedure` ✓ |
| `expected` | `Expected` ✓ |
| **`observation`** | **— absent** |
| `evidence requirement` | `Evidence` ✓ |
| — | **`Predicate`** ← not in the list |

So the example supplies `predicate`, which the list omits, and omits `observation`, which the list requires. Same defect class as §3.

**Across all four variants:**

| Field | v0.2 §11 struct | v0.2 §11 instance | v0.3 §47 list | v0.3 §47 example |
|---|---|---|---|---|
| check_id | ✓ | ✓ | ✓ | ✓ |
| subject | ✓ | ✓ | ✓ | ✓ |
| scope | ✓ | ✓ | ✓ | ✓ |
| procedure | ✓ | ✓ | ✓ | ✓ |
| evidence | ✓ | ✓ | ✓ | ✓ |
| predicate | ✓ | ✓ | — | ✓ |
| expected | ✓ | — | ✓ | ✓ |
| observation | — | ✓ | ✓ | — |
| **verifier** | ✓ | — | — | — |
| **status** | — | ✓ | — | — |

**`verifier` appears in one of four. `status` appears in one of four.**

`verifier` is required by v0.2 §13 (*"a verifier must not certify properties it has not actually evaluated"*), §26 (binding list), §38 (*"what verifier produced the observation?"*) and v0.3 §51. **The check specification — the object every verification claim must map to, per §11 — drops the one field that four other sections require.**

This is the second consecutive part in which the check specification has got *less* consistent, not more: Part XIII recorded two variants, Part XIV records four. §11's requirement (*"every verification claim should map to an identifiable check"*) cannot be satisfied while the check's own definition varies by document.

**Correction.** Nominate one canonical field set — I suggest a superset of all four — distinguish schema from instance explicitly, and state that the other three forms are projections of it. Per Part XIII's closing observation, this is §16 applied above the parser layer, and it is the same remedy each time: **one object, declared projections.**

---

## 6. §41's state machine is not total over its own sections or its status model

### 6.1 Fourteen chain positions, fifteen defined states

§41's chain names **14** states:

```text
INTAKE · CLASSIFY · IDENTIFY · AUTHORIZE · SCOPE · PLAN · EXECUTE
· OBSERVE · EVALUATE · EVIDENCE · COVERAGE · CLAIM · GATE · REPORT
```

but §42–§56 define **15**:

```text
§42 INTAKE      §43 CLASSIFICATION   §44 IDENTIFICATION
§45 AUTHORIZATION §46 SCOPE          §47 PLAN
§48 EXECUTION   §49 OBSERVATION      §50 EVALUATION
§51 EVIDENCE    §52 COVERAGE         §53 CLAIM
§54 CLAIM STRENGTH                   §55 GATE      §56 REPORT
```

**`CLAIM STRENGTH` (§54) has no position in the chain.** It has its own section and its own eight-level hierarchy, but the state machine does not route through it.

This is minor in itself, and it is the same pattern a fourth time: the chain and the definitions are two representations of one state set with no declared relation.

### 6.2 `SKIPPED` is unreachable

§50 lists six possible results, including `SKIPPED`. §41's failure transitions are six entries, and **none terminates at `SKIPPED`.**

That is not merely an omission. §7 defines `SKIPPED = check was intentionally not executed` — a *deliberate* decision, not a failure. §41's table is titled *"Failure transitions"*, so a non-failure state cannot belong to it by construction. But then **no transition to `SKIPPED` exists anywhere**, and §1 makes `SKIPPED → NOT PASS` a core invariant. A state that carries a mandatory claim consequence is unreachable by the document's own state machine.

**This matters for a measured reason.** `run_all.sh:32-33` announces `SKIPPED` in the audited toolchain — *"render checks will be SKIPPED"* — and Part VIII §0.1 measured what follows: stage 3 treats the absence as fatal, while stage 7 reports `PASS` with 6 findings instead of 8. **§1's `SKIPPED → NOT PASS` is exactly the rule the measured behaviour breaks**, and §41 provides no route by which the agent reaches that state and applies the rule.

**Correction.** Add `SKIPPED` to the transition set (it is a decision point — *plan declares a check, policy excludes it*), or state where §52's coverage `skipped` field connects to §50's `SKIPPED` result. The two are presumably the same condition.

---

## 7. Two corrections v0.3 makes to its predecessors

Both are improvements, and one of them corrects **this series of parts**, not just the protocol.

### 7.1 §45 replaces `PackAuthority` with `RequestedAuthority` — and Part XIII missed why

v0.2 §23:

```text
EffectiveAuthority = PackAuthority ∩ GrantedAuthority ∩ EnvironmentAuthority
A pack cannot grant itself additional authority.
Therefore: PACK RULE ≠ AUTHORIZATION
```

v0.3 §45:

```text
EffectiveAuthority = RequestedAuthority ∩ GrantedAuthority ∩ EnvironmentAuthority
```

**The rename is a fix, not a cosmetic change.** `PackAuthority` names the pack as a *term in an authority intersection* — implying the pack is an authority source — while the sentence immediately beneath it denies exactly that (*"a pack cannot grant itself additional authority… PACK RULE ≠ AUTHORIZATION"*). The intersection made the naming harmless in practice, but the name asserted a relationship the next sentence forbade.

`RequestedAuthority` states it correctly: the pack **requests**, the environment and the grant **supply**. And §45 operationalizes it — *"if `RequestedAuthority > GrantedAuthority` the operation is `BLOCKED`"*.

**Part XIII §2 recorded v0.2 §23 as an improvement over v0.1 and did not examine the term.** It is a naming-level tension rather than a behavioural defect — the intersection bounds it either way — but it is the same class of defect Part XIII spent four sections on, and it went unexamined. Recorded here so the omission is visible rather than silently patched.

### 7.2 §41 corrects a contradiction in v0.1 §22 — and supersedes Part XII §4.1

**v0.1 §22** mapped:

```text
dependency unavailable → UNKNOWN
```

**v0.3 §41** maps:

```text
missing dependency → BLOCKED
```

These conflict. §7 decides it, and §7 is explicit:

> `BLOCKED = legitimate execution could not proceed because a required precondition, authority, **dependency**, **artifact**, or resource was unavailable.`

**A dependency is named in `BLOCKED`'s definition, so `BLOCKED` is correct and v0.1 §22 was wrong.** v0.3 §41 fixes it.

**This also supersedes Part XII §4.1.** Part XII proposed four mappings for v0.1 §22's unclassified stop conditions, two of which were:

| Part XII §4.1 proposed | Correct status per v0.2 §7 |
|---|---|
| required artifact is missing → `UNKNOWN` | **`BLOCKED`** — "artifact … was unavailable" |
| verification predicate is undefined → `ERROR` | `ERROR` ✓ (unchanged) |
| subject identity cannot be established → `UNKNOWN` | `UNKNOWN` ✓ (unchanged; v0.3 §41 concurs) |
| scope is ambiguous → `BLOCKED` | `BLOCKED` ✓ (unchanged) |

Two of the four were **wrong**, and I flagged them at the time as *"arguable"* — which was the correct hedge but not the correct answer. Part XII §4.1 was written against **v0.1 §9**, whose `BLOCKED` definition was generic (*"execution could not legitimately proceed"*) and did not enumerate dependencies or artifacts. **v0.2 §7 expanded that definition**, which silently invalidated two of Part XII's four proposals.

**The process failure is Part XIII's, not Part XII's:** Part XIII re-tested Part XII §4's *finding* (the non-correspondence) but did not re-test §4.1's *proposed mappings* against v0.2's revised §7. Under v0.2 §24's own rule, an expanded definition is a changed material input, and the dependent proposals should have been revalidated. They were not. Recorded as **N-18** in this series' pattern of self-corrections.

---

## 8. Live consistency: v0.3's new rules against the audited toolchain

| § | v0.3 rule | `vendor/rfl-ae/skills/` | Evidence |
|---|---|---|---|
| **§69** | never claim *"all checks passed"* / *"all files verified"* without demonstrated completeness | ❌ **violated** | `audit_corpus.py:122` prints **`ALL FILES OK`** while enforcing **5 of `auditlib.py`'s 11 invariants** |
| **§60** | a checker producing an invalid result → `ERROR`, not PASS | ❌ **violated** | `neg()` reports `PASS` on a `ModuleNotFoundError` (Part VII §0.2) |
| **§52** | *"do not treat `0 findings` as `complete coverage`"* | ❌ **violated** | `has_prov` yields `n/a` inside a run reporting `ALL FILES OK` (Part V §0.1) |
| **§63** | record irreproducibility rather than claiming determinism | ❌ **violated** | `ENVIRONMENT_DEPENDENT` — same commit, 6 vs 8 findings, both `PASS` (Part VIII §0.1) |
| **§65** | *"a mutation that survives is a verification gap"* | ❌ **4 survive** | Part VIII §0.1 |
| **§11** | every claim maps to an identifiable check | ❌ **no `check_id` exists** | zero JSON emissions; no structured result (§6.3 of Part XIII) |
| **§46** | `CLAIMED ⊆ EXECUTED ⊆ DECLARED` | ❌ **unrepresentable** | no declared scope object anywhere in the toolchain |

### 8.1 §69's violation is exact, and the count needs one clarification

`audit_corpus.py:122` prints `ALL FILES OK`. §69 prohibits *"all files verified"* unless completeness is demonstrated, and §69 requires for it: *declared scope + complete executed scope + required checks + required evidence + valid verifier.* The toolchain has **none of the five**.

The invariant count — **5 of 11**, as recorded in Part III §21 — I re-verified this session, and the measurement needed care:

```text
auditlib.py defines 11 invariants as `def check_*`
audit_corpus.py calls 4 of them:  check_fences, check_links, check_provenance, check_render
audit_file.py  calls all 11
audit_corpus.py additionally enforces 1 inline, unnamed: the cross-document
  numbering invariant — duplicates, gaps, expected-gaps (lines 98–107)
```

**So "5 of 11 invariants" is correct, and a call-site count yields "4 of 11".** Both are true measurements of the same code; they answer different questions (*how many invariants are enforced* vs *how many named functions are called*). Part III §21's figure stands.

The clarification is worth recording for a second reason. **Four of the five enforced invariants are named and addressable; one is anonymous inline index arithmetic** (`range(min, max + 1)` set difference, lines 99–100). §11 requires that *every verification claim map to an identifiable check* — so for the fifth invariant, that is impossible: there is no identifier to claim against. Of the eleven defined invariants, four are claimable, one is enforced but unnameable, and six are not enforced by the sweep at all. `ALL FILES OK` covers all of them equally.

---

## 9. Standing

| Part XIV section | Standing |
|---|---|
| §1 Additive, not superseding | **PROVED** — by dependency on v0.2 §7, §23, §26 |
| §1.1 Identity ambiguity | **PROVED** — three undeclared aspects |
| §2.1 §41 + §52 address XIII §5 | **PROVED, structurally** — first separate claim-level status |
| §2.2 §61 closes staleness behaviourally | **CLOSED**; equivalence-proof escape clause **OPEN** |
| §3 §46 has no `claimed` field | **PROVED** — object carries 2 of the invariant's 3 terms |
| §4 Three coverage representations | **PROVED**; `ZERO` ambiguity **PROVED** |
| §5 Four check-specification variants | **PROVED** — `verifier` in 1 of 4, `status` in 1 of 4 |
| §6.1 14 positions vs 15 states | **PROVED by count** |
| §6.2 `SKIPPED` unreachable | **PROVED** — no transition exists |
| §7.1 §45 corrects v0.2 §23 | **PROVED**; Part XIII's omission recorded |
| §7.2 §41 corrects v0.1 §22 | **PROVED**; Part XII §4.1 superseded — **N-18** |
| §8 Live consistency | **PROVED** — 7 rules, 7 violations |

### Corrections, in dependency order

**1. §3 (highest value).** Add `claimed` to §46's scope object and express the containment as an executable predicate. This is Part XI §155's highest-value property and the conjunct behind four audit findings; it is currently unenforceable.

**2. §5 (most repeated).** Nominate one canonical check field set with `verifier` and `expected` present; declare the other three as projections. Four variants is worse than the two Part XIII found.

**3. §4.** Define §52's four symbols against the ratio; split `ZERO` into `ZERO` (0/n) and `UNDECLARED` (0/0); map v0.2 §27 and v0.3 §46 onto one vocabulary.

**4. §1.1 (cheapest, and a precondition).** Declare whether `PackDigest` covers §0–§40 or §0–§70. Everything in §57 (provenance), §64 (determinism), and Part XI's `packc` depends on the answer.

**5. §6.** Add `CLAIM STRENGTH` to §41's chain or remove it as a state; add a transition to `SKIPPED` — which §1 makes consequential.

**6. §2.2, §7.2.** Bound §61's equivalence-proof clause; adopt `BLOCKED` for missing dependency/artifact (already done in §41) and retire v0.1 §22's mapping.

### The pattern, now confirmed across three revisions

Part XIII identified the recurring defect at v0.1–v0.2:

```text
a requirement stated in one form
and represented in another
with no declared relation between them
```

Part XIV finds it **four more times** in v0.3:

| § | Stated one way | Represented another |
|---|---|---|
| §46 | containment of `CLAIMED`, `EXECUTED`, `DECLARED` | object with no `claimed` |
| §47 | 7-field check list | 7-field example, different fields |
| §52 | coverage as a ratio | coverage as four symbols |
| §41/§50 | `SKIPPED` is a result state | no transition reaches it |

**And each revision has reduced the number of *instances* while preserving the *shape*.** v0.3 genuinely closed two of Part XIII's five findings and introduced a structurally correct claim-level status in §52 — that is real progress. But §46 now contains the same defect in its purest form, on the single property Part XI called highest-value.

The pattern is not a drafting problem. It is what happens when a specification grows by accretion across versions without a single canonical object per concept — **which is precisely what Part XI's `packc` exists to enforce, and why §1.1's identity question is a precondition rather than a formality.** A compiler cannot digest a document whose digest coverage is undefined.

Four parts have now been written against the same protocol lineage, and the same class of defect has survived each: **the specification has the right rules and no canonical objects to hold them.** That is the case for building the compiler, and it is now stronger than the case for another revision.
