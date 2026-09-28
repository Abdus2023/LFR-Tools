# RFL-AE Prompt Packs — Part XXII: Fixture Corpus & Conformance Manifest v1.1 — The Coverage Derivation Arrives, and One Fixture Class Goes

**Subject:** [`rfl-ae-fixture-corpus-conformance-manifest-v1.1.md`](rfl-ae-fixture-corpus-conformance-manifest-v1.1.md) (§556–§655), continuing [`v1.0`](rfl-ae-conformance-evidence-release-v1.0.md), [`v0.9`](rfl-ae-executable-protocol-kernel-v0.9.md), [`v0.8`](rfl-ae-reference-implementation-blueprint-v0.8.md), [`v0.7`](rfl-ae-executable-protocol-types-v0.7.md), [`v0.6`](rfl-ae-protocol-schemas-v0.6.md), [`v0.5`](rfl-ae-prompt-instructions-v0.5.md), [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md), [`v0.1`](rfl-ae-master-agent-instructions-v0.1.md)
**Series:** [`part18`](rfl-ae-prompt-packs-part18.md) · [`part19`](rfl-ae-prompt-packs-part19.md) · [`part20`](rfl-ae-prompt-packs-part20.md) · [`part21`](rfl-ae-prompt-packs-part21.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — the four-part-old coverage finding closes by example, and the declared-population defect closes outright
>
> **§607 supplies the first worked coverage derivation in the lineage.**
>
> ```text
> declared = {A,B,C,D}
> checked  = {A,B}
> →
> missing = {C,D}
> unexpected = {}
> status = PARTIAL
> ```
>
> Parts XIX §3, XX §4 and XXI §5.1 each recorded that a coverage status was **required but underived** — v0.8 §305 gave an ambiguous branch, v0.9 dropped the algorithm, v1.0 re-required the field without a rule. Measured: `PARTIAL` as a protocol token is **0 in v1.0 and 1 in v1.1** — §607 is the first place a coverage status appears as an *output derived from given sets*.
>
> **And §557 + §572 + §638 close the audited toolchain's central structural defect outright.**
>
> `DECLARED FIXTURES ≠ DISCOVERED FILES ≠ EXECUTED FIXTURES ≠ PASSED FIXTURES` (§557), *"manifest ≠ directory listing"* with filesystem discovery *"diagnostic only"* (§572), and corpus drift *"SHALL block complete conformance"* (§638). Part V §55 recorded the audited corpus reporting a self-consistent **32 documents** after one was deleted — because it discovered whatever was on disk and reported on that. **v1.1 makes the declared population authoritative and makes drift blocking.**
>
> **But §606 drops a fixture class that v1.0 §484 listed.** `unobservable member`: v1.0 = 1, v1.1 = 0.

---

## 1. Standing

**The transition is clean.** v1.0 ends at §555; v1.1 opens at §556. Measured: **100 sections, §556–§655, contiguous.**

| Measure | Value |
|---|---|
| Documents supplied | **11** |
| Union of section numbers | **§0–§655** |
| Gaps | **one: §200** |
| Clean transitions in the last four documents | **4 of 4** (§271→§272, §361→§362, §455→§456, §555→§556) |

---

## 2. HEADLINE A: §607 is the first coverage derivation in the lineage

### 2.1 The measurement

| Token | v0.9 | v1.0 | **v1.1** |
|---|---|---|---|
| `PARTIAL` (protocol token) | **0** | **0** | **1** |
| `status = PARTIAL` | 0 | 0 | **1** |
| `status: COMPLETE` | 0 | 1 | 0 |

### 2.2 What v0.9, v1.0 and v1.1 each had

| Document | Coverage status |
|---|---|
| **v0.8 §305** | A derivation that is **ambiguous** — *"else if unsupported exists: `PARTIAL or UNKNOWN`"* — one input, two legal outputs, contradicting §308's determinism rule 53 lines later |
| **v0.9 §394/§395/§425** | `status: CoverageStatus` **required as a field**, set algebra given, **no derivation** |
| **v1.0 §504** | *"`COMPLETE` SHALL be derived, not supplied by the caller"* — the requirement restated, still **no rule** |
| **v1.1 §607** | **A worked derivation with concrete inputs and an output** |

### 2.3 Why §607 constitutes progress rather than an example

**It is a required fixture, not prose.** §606 lists `partial` in the coverage fixture family, §607 supplies the arithmetic, and §563 makes `REQUIRED` the class that *"determine[s] complete release conformance"*. So the mapping is now pinned by a fixture that a conformance run must reproduce.

**And §608 completes the pair:**

```text
checked = {A,A,B}  →  logical checked population = {A,B}
"The duplicate SHALL not increase coverage."
```

That is v0.9 §395's *"Duplicates SHALL be eliminated before set operations"* given an executable form, matching §306 and §485.

**Disposition: Parts XIX §3, XX §4 and XXI §5.1 are PARTIALLY CLOSED.** The mapping exists **by example**; it does not exist as a general rule. §607 covers exactly one input pair, and the three statuses are still not defined by a predicate over the sets. A corpus that satisfies §607 has not demonstrated it handles `unresolved member` or `empty`.

**Correction.** §607's structure generalises in one paragraph, and §606's own nine classes supply the cases:

```text
declared unresolved            → UNKNOWN
missing ≠ ∅                    → PARTIAL
unexpected ≠ ∅                 → policy decision (§598)
missing = ∅ ∧ unexpected = ∅   → COMPLETE
```

---

## 3. HEADLINE B: §606 drops `unobservable member`

### 3.1 The drift

| | v1.0 §484 | v1.1 §606 |
|---|---|---|
| count | 9 | 9 |
| shared | complete · partial · empty · duplicate declared · duplicate checked · unresolved member · skipped member | ← same |
| renamed | `unexpected member` | `unexpected checked` |
| added | — | **`missing member`** |
| **dropped** | **`unobservable member`** | **—** |

### 3.2 Two of the three changes are improvements

- **`unexpected member` → `unexpected checked`** is a precision gain. v0.9 §395 defines `unexpected = checked − declared`, so "unexpected checked" says which side of the subtraction it comes from.
- **`missing member`** is genuinely new and correct: `missing = declared − checked`, the counterpart to `unexpected`. The pair is now complete where v1.0 §484 had only one of the two named.

### 3.3 The third is a deletion

**`unobservable member` occurs once in v1.0 and zero times in v1.1.** Measured across both documents: `unobservable` = 1 in v1.0, 0 in v1.1. Meanwhile v1.1 §600's observation family **retains** `not observable`.

So the coverage fixture minimum no longer exercises the case where a member **exists in the declared scope and cannot be observed** — which is precisely the condition v0.9 §386 distinguishes as `NOT_OBSERVABLE` among its five completeness states, and which §418 requires not be rewritten as empty content.

**This matters more than a missing test.** The audited toolchain's failure mode was exactly this: `audit_corpus.py:67/88` gated on `has_prov` conditionally, so removing evidence produced `PASS` rather than a distinguishable absence (Part XVI §3, measured 30/29/15/1/0 → PASS/FAIL/FAIL/FAIL/**PASS**). An unobservable member is the coverage-layer expression of that condition, and it is the one class that left the list.

**Fifth dropped token in this series**, after:

| # | Token | Dropped | Restored |
|---|---|---|---|
| 1 | v0.5 §116's eight execution states | v0.6 §160 | — |
| 2 | `claimed` | v0.7 | **v1.0 §545** |
| 3 | `NONE` coverage state | v0.7 | — |
| 4 | v0.4's `CompiledPackDigest` | v0.7–v1.0 | — (v1.0 §541 re-enters by relocation) |
| 5 | **`unobservable member`** | **v1.1 §606** | — |

**Correction.** Restore `unobservable member` to §606's minimum. §606 says *"Minimum"*, so the list is a floor — but a floor that omits the class is a floor that does not require it.

---

## 4. The declared-population defect, closed outright

### 4.1 What the audit measured

Part V §55: deleting a corpus file left the audited toolchain reporting a **self-consistent 32 documents** with no anomaly — because the corpus was whatever the filesystem contained, and the report described only that. There was no declared population against which the observed one could be compared.

The consequence is the whole class of findings in Parts II–VIII: *"a check that cannot run must never report success"*, `NO COVERAGE ≠ PASS`, and the README/997 count that could not be falsified because nothing declared what the count should be.

### 4.2 What v1.1 establishes

| § | Rule |
|---|---|
| **§557** | *"`DECLARED FIXTURES ≠ DISCOVERED FILES ≠ EXECUTED FIXTURES ≠ PASSED FIXTURES`… All four populations SHALL be independently representable."* |
| **§572** | *"The fixture manifest SHALL be authoritative for required conformance population. Filesystem discovery SHALL be diagnostic only."* — *"A fixture existing on disk but absent from the manifest SHALL not silently become required."* |
| **§573** | The runner SHALL detect five discrepancy classes, including both directions |
| **§574** | Every required fixture has a five-edge closure: `fixture_id → manifest entry → fixture file → content digest → valid fixture` — *"Any broken edge SHALL prevent complete corpus conformance"* |
| **§575** | `corpus_digest = H(canonical_manifest)`, covering six properties |
| **§636** | Discovery produces `DISCOVERED` — *"not `REQUIRED` — until reconciled with the manifest"* |
| **§637** | Reconciliation computes `manifest_only · filesystem_only · both · invalid · duplicates` |
| **§638** | *"Required corpus drift SHALL block complete conformance."* |

**Four populations, independently representable, with the declared one authoritative and drift blocking.** That is the structural remedy for Part V §55, and it is the cleanest closure in the series: the audit described a corpus with no declared population, and v1.1 specifies one.

**§557's four-way inequality is the lineage's doctrine applied to its own test corpus.** It is `representation ≠ semantics ≠ evidence` and `CLAIMED ⊆ EXECUTED ⊆ DECLARED` applied to fixtures — the same shape, at the layer where the audited toolchain actually failed.

---

## 5. Other closures aimed at the audit's findings

### 5.1 §634 forbids deleting a failing fixture — the C-3 defect

> *"A required fixture SHALL NOT be deleted merely because it currently fails. Deletion or demotion SHALL require change-control evidence."*

**Consolidated finding C-3:** *"The provenance invariant is non-monotonic; 10 of 33 documents sit outside it — Deleting 29 of 30 comments → FAIL; deleting **all 30** → PASS."*

§634 names the temptation that produced C-3 and forbids it. §626 reinforces it — *"Any required mutation that survives SHALL produce `CONFORMANCE = FAIL`. It SHALL not be hidden by aggregate mutation scores"* — which is §537 of v1.0 restated and aimed at the same behaviour.

### 5.2 §584 makes identifier category separation executable

```text
Required negative fixture:
    TaskId("execution:...")
Expected:
    REJECT
    WRONG_IDENTIFIER_KIND
"The test SHALL prove that category separation is executable."
```

**Part XIX §9.1 and XX §8.1 recorded that the newtype wrapper cannot enforce the prefix rule** — `TaskId(TypedId { kind: IdKind::Execution, … })` is well-typed, and v0.9 §369's `TypedId.kind` is a public field.

§584 makes the *parser* requirement testable. **PARTIALLY CLOSED:** the test proves the parser rejects the wrong kind, and the construction-time gap remains open, because a fixture exercises the parse path and not the constructor. v0.8 §280's *"An object SHALL NOT become protocol-valid merely because it is syntactically constructible"* is the rule that still has no fixture.

### 5.3 §602 gives `ERROR ≠ FAIL` its own fixture pair

> *"A checker infrastructure failure… SHALL produce `ERROR` when the checker did not evaluate the predicate. A predicate evaluation returning false SHALL produce `FAIL`. **These cases SHALL be distinct fixtures.**"*

Parts VII §0.2, XVI §7.1 and XVIII §2.4 carried `ERROR ≠ FAIL` — and Part VII §0.2's receipt is a crash producing `PASS (exit 1, 0 findings)`. §602 is the first statement that requires the distinction to be *tested* rather than merely asserted, and §601 requires each of the six statuses to have at least one explicit fixture.

### 5.4 §646 — rejection for the wrong reason is not a pass

> *"expected `EVIDENCE_STALE` / actual `SCHEMA_INVALID` … is not necessarily a PASS."*

**This is the returncode finding, at the comparison layer.** The audited `neg()` asserted `[ $rc -ne 0 ]` and nothing else, so any failure satisfied it — the fixture equivalent of passing for the wrong reason. v0.7 §258, v0.8 §318/§319, v0.9 §443 and v1.0 §474 each prohibited the practice; **§646 states the acceptance criterion that makes the prohibition decisive.**

### 5.5 §647 — the inverse defect, named for the first time

> *"The corpus SHOULD contain valid fixtures near negative boundaries. This detects implementations that appear secure merely by rejecting too much."*

**Every prior finding in this series is an under-detection defect** — a check that passed when it should have failed. §647 addresses the opposite: an implementation that rejects everything satisfies every negative fixture and fails only the positives. **§611 supplies the corresponding gate case** (`all declared requirements satisfied → PASS`, *"This protects against over-restrictive implementation"*), and §592's structural/semantic separation fixture prevents conflation at the layer below.

**This is a genuinely new axis for the lineage**, and it is the correct complement: a corpus of negatives alone is satisfiable by a program that always says no.

### 5.6 Three more

| § | Rule | Finding |
|---|---|---|
| **§633** | *"Every confirmed protocol defect SHOULD result in a regression fixture."* | The audit's ranked P0 item 5 — a gap-preservation regression test — generalised into a corpus rule, with the chain `defect → reproduction → fixture → fix → conformance evidence` |
| **§631** | *"A random failure without its reproducing seed SHALL be classified as incomplete diagnostic evidence."* | Stage-7's environment-dependent finding count (6 vs 8) |
| **§566** | `{ "expected": true }` *"SHALL NOT be sufficient"* | The Boolean-summary defect |

**§631's phrase is worth singling out.** *"Incomplete diagnostic evidence"* is a fourth category alongside pass/fail/blocked — a failure that is real but **not yet usable**, because the record of it cannot be reproduced. That is `verification failure vs verifier failure` given a name.

---

## 6. Nine rules preventing a non-PASS state from collapsing into PASS

| Document | § | Rule |
|---|---|---|
| v1.0 | §500 | skipped required test prevents complete conformance |
| v1.0 | §501 | blocked not counted as PASS |
| v1.0 | §502 | unknown not converted to PASS |
| v1.0 | §517 | missing matrix member is `MISSING`, not PASS |
| **v1.1** | §569 | unresolved required dependency → `BLOCKED`/`UNKNOWN`, *"SHALL NOT produce PASS"* |
| **v1.1** | §641 | preflight failure → `execution = BLOCKED`, *"SHALL not fabricate per-fixture PASS results"* |
| **v1.1** | §644 | comparison `ERROR` *"shall not be interpreted as a fixture PASS"* |
| **v1.1** | §653 | optional failures reported, *"SHALL not silently disappear"* |
| **v1.1** | §646 | rejection for the wrong reason is not a PASS |

**Nine rules, five of them new in v1.1.** The audit carried `SKIPPED ≠ PASS`, `BLOCKED ≠ PASS`, `UNKNOWN ≠ PASS`, `NO COVERAGE ≠ PASS` and `ERROR ≠ FAIL` as separate observations across Parts XII–XX. **The lineage now has a rule for each, and v1.1 adds the two the audit never reached: a comparison error, and a rejection for the wrong reason.**

---

## 7. Smaller findings

### 7.1 §655's laws contain the lineage's only typographical error

Measured: `FILSYSTEM` occurs **once**, in §655's seventh law — `NO MANIFEST/FILSYSTEM RECONCILIATION → NO COMPLETE CORPUS CLAIM`. `FILESYSTEM` occurs **zero** times in v1.1.

Recorded verbatim, as supplied. It is **MINOR** — the law is unambiguous in meaning and §637's reconciliation section names the correct concept. Noted because it is the only such error in fourteen law blocks across four documents, and because a normative law text is the one place a reader may quote verbatim.

### 7.2 §650's coverage record and v1.0 §504's are different shapes

| | Fields |
|---|---|
| v1.0 §504 | `declared · executed · missing · skipped · failed · status` — **6**, ending in a status |
| v1.1 §650 | `declared · executed · passed · failed · errored · skipped · blocked · unknown · missing` — **9**, with **no status field** |

v1.1's record is the richer and more honest one — nine counts, one per status, which is v1.0 §503's summary list applied to the corpus. But it **omits the `status` field** that v1.0 §504 requires and v1.1 §607 then computes.

So the same object has two shapes in two consecutive documents, and §650's lacks the field §607 derives. **MINOR**, and it is the same enumeration-versus-its-own-expansion pattern Parts XIX §7 and XX §3 recorded.

### 7.3 §567's example pair is well-chosen

`REJECT + WRONG_SUBJECT` is *"stronger than"* `REJECT` alone — and §474 of v1.0 already made `expected: nonzero` invalid. **§567 is the positive statement of the same rule**: not only must the expected result be structured, it must be as specific as the protocol permits. *"When exact error identity is protocol-defined, it SHALL be tested."*

### 7.4 §588 orders the comparison correctly

> *"The runner SHALL compare canonical bytes before comparing the digest."*

This is v1.0 §475–§476 (*"A matching digest with different bytes SHALL fail"*; *"Both comparisons are required"*) given an ordering. **Bytes first, digest second** — the digest is a summary and cannot detect what the bytes comparison detects.

---

## 8. Carried items that did not move

| Item | v1.0 | v1.1 | Status |
|---|---|---|---|
| `ClaimId` | 0 | **0** | **5th document** |
| `CompiledPack` / `compiled_digest` | 0 | **0** | untouched |
| `ResultStatus` / `CheckStatus` | 0 | **0** | v0.9's split unfixed |
| `EnvironmentAuthority` | 0 | **0** | Still absent from the authority intersection |
| `evidence.schema.json` | not named | **not named** | 10 audit files name it; **0 on disk** |

**v1.1 is a fixture specification and is not obliged to repair a type declaration.** What is notable is that the two items requiring *an artifact* rather than a rule have now survived eleven documents:

- **`ClaimId`** — five documents have referenced `Claim` and none has identified it.
- **`evidence.schema.json`** — ten documents have specified it; §591 of v1.1 defines the schema *fixture family* for schemas that do not exist.

**§591 is the sharpest instance.** It enumerates eight fixture cases — `minimum valid object · complete valid object · missing required field · wrong type · unknown enum · invalid array element · unexpected property · malformed nested object` — for a schema that has been named in ten documents and written in none.

---

## 9. Standing

| Part XXII section | Standing |
|---|---|
| §1 §556–§655 contiguous, 100 sections | **PROVED** |
| **§2 §607 first coverage derivation** | **PARTIALLY CLOSED** — by example, not by rule; `PARTIAL` 0→1 |
| §2.3 §608 duplicate rule executable | **CLOSED** |
| **§3 §606 drops `unobservable member`** | **PROVED** — 5th dropped token |
| §4 §557/§572/§574/§638 declared population | **CLOSED** — Part V §55 |
| §5.1 §634 no deletion of failing fixtures | **CLOSED** — C-3 |
| §5.2 §584 identifier kind fixture | **PARTIALLY CLOSED** — parser tested, constructor not |
| §5.3 §602 ERROR ≠ FAIL fixture pair | **CLOSED** |
| §5.4 §646 wrong-reason rejection | **CLOSED** |
| §5.5 §647 over-broad rejection | **NEW** — first inverse-defect rule |
| §5.6 §633/§631/§566 | **CLOSED** |
| §6 Nine non-PASS rules | **PROVED** — five new in v1.1 |
| §7.1 §655 `FILSYSTEM` | **MINOR** |
| §7.2 §650 vs §504 shape mismatch | **MINOR** |
| §8 Carried items unmoved | **PROVED** — `ClaimId` 5th document |

### Corrections, in dependency order

**1. §2 — state the coverage mapping as a rule, not only as §607's example.** The four cases are derivable from §606's own nine classes and §395's two sets.

**2. §3 — restore `unobservable member`** to §606's minimum. It is the coverage-layer expression of the condition the audit measured as `PASS` at zero evidence.

**3. §7.1 — `FILSYSTEM`** in §655's seventh law.

**4. §7.2 — give §650's coverage record the `status` field** that §504 requires and §607 derives, or state that §650 supersedes §504.

**5. — still open across documents:** `ClaimId` (5th), the pack exclusion rule, v0.9's `ResultStatus`/`CheckStatus` split, §522's missing evidence reference, and the authority term set.

### Closing judgement

**v1.1 closes the structural defect the audit spent its first eight parts describing.**

Part V §55 measured a corpus that reported a self-consistent population after a file was deleted, because the corpus was whatever the filesystem held. **§557 separates four populations; §572 makes the manifest authoritative and discovery diagnostic; §574 requires five-edge closure per required fixture; §638 makes drift block conformance.** That is the remedy, specified at the layer where the failure actually occurred.

**And §607 closes, by example, the finding Part XIX raised and Parts XX and XXI carried.** The coverage status is now derived for at least one input pair, with a required fixture pinning it, and §608 completes the duplicate rule beside it.

**The one regression is small and precise:** `unobservable member` left the minimum list, and the condition it names — a declared member that cannot be observed — is the coverage-layer form of the exact defect (`PASS` at zero evidence) that motivated this series.

**The pattern across eleven documents is now stable enough to state plainly.** Properties that are *computed* — a set difference, a status derivation, an occurrence count — survive when they are pinned by something executable: a fixture, a law, a required closure. Properties that are only *stated* do not: `claimed` needed three documents and a release-critical invariant to come back; `ClaimId` has been referenced for five documents and never declared; `evidence.schema.json` has been specified for ten.

**v1.1 is the first document whose principal instrument is a fixture corpus rather than a rule set** — and it is the document after which the coverage derivation, the duplicate rule, the status distinctions and the declared population all became *executable*. The lineage has spent eleven documents learning that its own doctrine is correct: **a rule stated is not a rule tested, and a corpus whose population is not declared cannot support a conformance claim.**

§655 ends: *"THE TEST POPULATION MUST ITSELF BE VERIFIED BEFORE ITS RESULTS CAN SUPPORT A CONFORMANCE CLAIM."*

The corpus now has a manifest, a digest, an identity scheme, a reconciliation procedure and thirteen laws. **The schemas it would test still have zero files.**
