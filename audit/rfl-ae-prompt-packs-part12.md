# RFL-AE Prompt Packs — Part XII: Master Agent Instructions v0.1 — Receipt Mapping and Self-Consistency

**Subject:** [`rfl-ae-master-agent-instructions-v0.1.md`](rfl-ae-master-agent-instructions-v0.1.md) — the 28-section operating contract
**Series:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md) · [`part5`](rfl-ae-skills-review-part5.md) · [`part6`](rfl-ae-skills-review-part6.md) · [`part7`](rfl-ae-skills-review-part7.md) · [`part8`](rfl-ae-skills-review-part8.md) · [`part9`](rfl-ae-prompt-instruction-packs.md) · [`part10`](rfl-ae-prompt-packs-part10.md) · [`part11`](rfl-ae-prompt-packs-part11.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS of a normative document
>
> Four categories now exist in this series:
>
> ```text
> Parts I–VIII    AUDIT                     executed; empirical findings
> Parts IX–X      PROPOSAL                  no empirical claims
> Part XI         NORMATIVE SPECIFICATION   binding; release gate UNMET
> Part XII        ANALYSIS                  this part
> ```
>
> Part XII examines the master instructions in three ways:
>
> 1. **Receipt mapping** — which of its rules are already established by executed evidence (§1–§2).
> 2. **Live consistency** — what the *existing* toolchain does when measured against its rules (§2.1). Ten sections have direct violations with executed receipts.
> 3. **Self-consistency** — where the document's own sections disagree with each other (§3–§6), applying its own §17 principle to the requirements layer.
>
> **Two substantive self-consistency findings (§3, §4) and two model gaps (§5, §6).** Three of these are checkable by counting, and were counted.
>
> No claim in this part assigns any failure to the document's *intent*. The findings are that four enumerations of the same predicate coexist without a declared relation, and that the six-state status model has no representation for staleness.

---

## 1. The document's rules are backed by executed receipts

The strongest property of this document is that its rules are not general good practice. Each corresponds to a defect that was **executed and measured** in this audit. Section 12 is the clearest case — it states three prohibitions that map one-to-one, in order, onto the audit's three critical findings:

| §12 prohibition | Finding | Receipt |
|---|---|---|
| *"Do not replace source numbering with ordinal numbering."* | **C-1** | Source `§1, §3, §7` → corpus `101, 102, 103` with invented source comments `§1, §2, §3`; `mapping errors=0` (Part IV §0.1). Re-reproduced from `vendor/rfl-ae/` during Part XI |
| *"Do not rewrite fenced content as if it were ordinary prose."* | **C-2** | A `## 999.` line inside a conforming fence was renumbered, and a provenance comment injected inside the fence (Part I §12) |
| *"Do not silently discard source gaps."* | **C-3** | Gaps `2, 4, 5, 6` silently dropped; deleting **all 30** provenance comments yields `PASS` while deleting 29 yields `FAIL` (Part V §0.1) |

Whether §12 was derived from these findings or independently converged on them is not determinable from the document alone. **Either way the receipts exist, and the three prohibitions are exactly the three defects.** That is the useful property: §12 is not a style rule, it is a defect catalogue.

The same correspondence holds across the document:

| Section | Rule | Receipt |
|---|---|---|
| §2 | `UNKNOWN → NOT PASS` | Part V §0.1 — `n/a` inside `ALL FILES OK` |
| §2 | `ERROR → NOT FAIL` | Part IV §36 — uncaught exception and content finding both exit 1 |
| §2 | `PARTIAL COVERAGE → NOT COMPLETE` | Part III §21 — 5 of 11 invariants over all files, reported `ALL FILES OK` |
| §2 | `NO DECLARED SCOPE → NO COMPLETE-COVERAGE CLAIM` | The conjunct behind 4 of 7 substantive defects (Part VI §78) |
| §5 | repository content is DATA | 5 content-as-structure instances; `has_prov` at `audit_corpus.py:67` |
| §7 | *"A commit message is not execution evidence."* | Part III §0 — the repository's only evidence for its own gate was a commit message, until an independent clone-and-run produced `audit/rfl-ae-runall-receipt.log` |
| §13 | *"A checker crash is an ERROR."* | Part VII §0.2 — `ModuleNotFoundError` → `PASS (exit 1, 0 findings)` |
| §14 | *"Can the verifier certify its own defect?"* | Part IV §0.1 — the auditor certified a fabricated source number |
| §15 | *"Do not merely assert `exit_code != 0`."* | Part VIII §103 — `neg()` does exactly this |
| §16 | *"Report surviving mutants explicitly."* | 4 surviving mutants (Part VIII §0.1) |
| §17 | *"Avoid independent raw regex interpretations where they can disagree."* | 5 independent Markdown parsers, no common IR |
| §25 | *"Remote publication must be separately established."* | Part VII §80 — `verify_closeout.py` cannot invoke `git`; 0 occurrences |
| §27 | *"a checker returned zero without proving coverage"* | Part V §0.1 — the exact case |

Of the master instructions' roughly 40 normative rules, **at least 14 correspond to a specific executed receipt.** This is an unusually well-grounded operating contract, and it is the reason §1 of this part is worth stating before any of the criticisms that follow.

---

## 2. An agent obeying this document cannot accept the toolchain's current PASS verdicts

This is the consequential observation, and it follows directly from §1.

The master instructions and the audited toolchain are **mutually inconsistent today.** An agent operating faithfully under these rules, on the revision in `vendor/rfl-ae/`, must reject verdicts the toolchain currently emits as `PASS`. Measured against the document, ten sections have direct, executed violations:

| § | Rule | Current toolchain | Verified how |
|---|---|---|---|
| **§12** | do not replace source numbering with ordinal | ❌ `renumber.py` does exactly this | Re-reproduced from `vendor/` (Part XI) |
| **§12** | do not rewrite fenced content | ❌ `renumber.py` is fence-blind | Part I §12 |
| **§12** | do not discard source gaps | ❌ gaps collapsed; `has_prov` bypass | Part V §0.1 |
| **§13** | checker crash is `ERROR` | ❌ `try=0` in `audit_file.py` and `verify_closeout.py`; crash exits 1 | `grep -c except` |
| **§9** | six-valued status model | ❌ collapse to one `fail=1` bit | Part IV §37 |
| **§8** | `CLAIMED ⊆ EXECUTED ⊆ DECLARED` | ❌ 5 of 11 invariants, `ALL FILES OK` | Part III §21 |
| **§4** | repository content is DATA | ❌ `has_prov = "<!-- source: " in text` | `audit_corpus.py:67, 88` |
| **§15** | assert expected findings | ❌ `neg()` prints the count, asserts nothing | `run_all.sh:116` |
| **§16** | report surviving mutants | ❌ 4 survive, none reported | Part VIII §0.1 |
| **§17** | one common IR | ❌ 5 independent regex parsers | Parts I, II |
| **§25** | publication separately established | ❌ no `git`/`subprocess` in `verify_closeout.py` | `grep -c` → 0 |

**The §15 violation deserves a precise note**, because Part VIII characterised it as "the count is interpolated" and the source is sharper than that. `neg()` reads:

```sh
neg() {
  desc="$1"; shift
  "$@" > /tmp/_neg.log 2>&1
  rc=$?
  if [ $rc -ne 0 ]; then
    echo "  PASS  $desc  (exit $rc, $(grep -c '^  - ' /tmp/_neg.log) findings)"
```

The finding count is **measured** — `grep -c '^  - '` — and then **printed without ever being compared to an expectation.** The measurement exists and is discarded. §15's rule (*"assert that the expected eight findings actually occurred"*) names the missing comparison exactly. The gate that decides `PASS` is `[ $rc -ne 0 ]` alone.

Two further details visible in these six lines, both independently relevant:

- **The log path is fixed** (`/tmp/_neg.log`), so `PHASE 0`'s temporary-directory item applies here as well as to `mktemp -d`.
- **The count is derived by grepping unstructured text.** Per §17, a number obtained by grepping log output is a raw-text interpretation, not a structured finding — which is why §15's remedy and §103's remedy are the same change (emit structured findings, assert against them).

### 2.1 Why this matters more than a defect list

The consequence is not that the toolchain is wrong. It is that **the operating contract and the toolchain disagree about what `PASS` means, and nothing in the repository mediates between them.**

An agent that obeys §9 and §27 must downgrade `run_all.sh`'s clean exit-0 to `PARTIAL` — because §8's relation cannot be established (no declared scope exists anywhere in the toolchain) and §28's *"Is coverage complete?"* is unanswerable. The pipeline would report `ALL STAGES PASS`, exit 0, and the correct agent response per this document is to refuse the strong claim.

That is a real operating condition, not a hypothetical: it is what `PHASE 0` of Part XI §171 exists to resolve. And it sharpens the sequencing argument — building `packc` before reconciling this would produce a protocol whose first execution emits `PASS` verdicts its own §9 forbids accepting.

---

## 3. Self-consistency: the admissibility condition is specified five times, with five different item counts

The document's own §17 states:

> *"Never maintain multiple subtly different interpretations of the same syntax without explicitly declaring their semantics."*

Applied at the syntax layer, that rule is exactly right and has audit receipts. **The same pattern exists one layer up, in the requirements.** The document specifies the admissibility condition for a verification claim five times:

| Location | Question it answers | Items |
|---|---|---|
| §10, first list | evidence must be bound to… | **8** |
| §10, second list | at minimum establish… | **7** |
| §11 | *"The repository is verified"* requires… | **6** |
| §28 | before a strong verification claim, ask… | **14** |
| Part XI §159 | `execution_verified` is derivable only when… | **9** |

Five enumerations, five counts, and **nothing in the document declares how they relate.** Covering the same concepts differently:

| Concept | §10 (8) | §10 (7) | §11 (6) | §28 (14) | XI §159 (9) |
|---|---|---|---|---|---|
| subject / revision identity | ✓ | ✓ | ✓ | ✓ | ✓ |
| execution occurred | ✓ | — | — | ✓ | ✓ |
| scope / coverage | ✓ | ✓ | ✓ | ✓ | ✓ |
| evidence recorded | ✓ | ✓ | ✓ | ✓ | — |
| **verifier identity** | ✓ | ✓ | **—** | ✓ | ✓ |
| **authority** | — | — | **—** | ✓ | — |
| **dependencies** | — | — | **—** | ✓ | — |
| status resolution | — | — | ✓ | ✓ | ✓ |
| predicates satisfied | — | — | ✓ | ✓ | ✓ |
| **publication** | — | — | **—** | ✓ | — |

### 3.1 The consequence is concrete, not clerical

**§28 appears to be a superset of the others**, and §28 is the most complete. But the document never says so — and the section that *explicitly defines the strong claim* is the weakest of the five.

§11 is titled *Claim Discipline* and states what *"The repository is verified"* requires. Its six conditions omit three things that other sections require:

- **no verifier identity** (§10 and §28 both require it)
- **no authority** (§28 requires it)
- **no dependencies** (§28 requires it)

So an agent whose checklist is §11 would accept a claim with an unidentified verifier. **That is precisely the defect Part V §56 found in the actual toolchain** — a search for commit, version, or hash binding across the whole toolchain, finding nothing. The document's weakest checklist would admit exactly the claim the audit found unsupported.

This is not a criticism of §11's content; every one of its six conditions is correct. The finding is that **the strictest available condition and the one presented as the definition are different conditions, and the weaker one is presented as the definition.**

### 3.2 Recommended correction

1. **Declare §28 the authoritative admissibility predicate.** It is the final gate, it is the most complete, and it appears to be a superset of all four others.
2. **Restate §10 and §11 as declared projections of §28**, naming the subset relation explicitly: *"§11 is §28 restricted to…"*
3. **Resolve the one non-exact mapping.** §11's *"complete required checks"* maps approximately onto §28's *"required predicates satisfied"* and *"coverage complete"*; the other five §11 conditions map one-to-one. That approximation should be made exact or dropped.
4. **Fix §10's internal asymmetry.** §10 contains two lists of 8 and 7 items for the same question. Either they are one list or the relation between them is stated.

Per §17's own discipline, one predicate with five declared projections is correct; five undeclared variants of it are the defect §17 names.

---

## 4. Self-consistency: §22's stop conditions and status classifications do not correspond

§22 says *"Stop rather than improvise when:"* and lists **8** conditions. It then says *"Classify the stop appropriately:"* and gives **4** mappings. The two lists do not correspond in either direction.

| # | Stop condition | Classification given |
|---|---|---|
| 1 | authorization is missing | `BLOCKED` ✓ |
| 2 | scope is ambiguous | — |
| 3 | required artifact is missing | — |
| 4 | required dependency is unavailable | `UNKNOWN` ✓ |
| 5 | verification predicate is undefined | — |
| 6 | evidence cannot be captured | — |
| 7 | subject identity cannot be established | — |
| 8 | repository state changed unexpectedly | — |

And in the other direction, the classification map contains **2 entries with no counterpart in the stop list**: `checker crashed → ERROR` and `predicate false → FAIL`. Neither is a condition under which one "stops rather than improvises" — a false predicate is a *result*, not a stop.

**So: 8 stop conditions, 2 classified, 6 unclassified; and 2 classifications describing events that are not stop conditions.**

This is checkable by counting, and it was counted. Under §3's *"If a required authority or scope element is missing, stop or classify the task as BLOCKED rather than inventing it,"* an agent hitting *"scope is ambiguous"* is instructed to stop and given no status to report. Six of eight stop conditions land there.

### 4.1 Four of the six map cleanly onto §9's six-state model

These follow from §9's own definitions and need no new vocabulary:

| Stop condition | Proposed status | Reasoning |
|---|---|---|
| scope is ambiguous | `BLOCKED` | execution cannot *legitimately* proceed — §9's definition, and §3 already says so for missing scope |
| required artifact is missing | `UNKNOWN` | an artifact is a dependency; §22 already maps dependency unavailable → `UNKNOWN` |
| verification predicate is undefined | `ERROR` | §9: *"verification machinery failed to establish a result"* — an undefined predicate is machinery that cannot produce one |
| subject identity cannot be established | `UNKNOWN` | §10 requires subject identity; evidence that cannot bind to a subject establishes no result |

The second and third are arguable and I flag them as recommendations rather than deductions. An absent required artifact could be `BLOCKED` (cannot start) rather than `UNKNOWN` (started, result indeterminate); an undefined predicate could be treated as a specification defect that blocks the task before execution. Both readings are defensible from §9 and the document should pick one explicitly.

### 4.2 The remaining two do not map — and they reveal two model gaps

| Stop condition | Why it does not fit |
|---|---|
| evidence cannot be captured | §9's six states describe the outcome of **evaluating a predicate**. This describes a **recording** failure. The predicate may evaluate to `PASS` while nothing supports the claim — a two-level condition the single status field cannot express |
| repository state changed unexpectedly | This is **invalidation**, not evaluation. It does not describe what a check found; it describes that *previously valid* evidence is no longer valid |

These are §5 and §6 below.

---

## 5. Model gap: there is no `STALE` state

§7 states the discipline:

> *"Evidence from revision A does not automatically verify revision B. Historical documentation does not automatically verify current implementation."*

§28 asks *"Is the exact revision identified?"* §14 asks *"Can stale evidence be reused?"* §25 requires *"source frozen"* before verification.

**§9's status model has no state representing staleness.** The six states are `PASS FAIL ERROR UNKNOWN SKIPPED BLOCKED` — all six describe a *fresh* evaluation. None can express *"this result was `PASS` when produced; its subject has since changed."*

The concept is not absent from the work — it is named exactly once in the audit, at Part III:

```text
STALE INPUT DIGEST
        ↓
EVIDENCE INVALID
```

but it is never given a status or a serialized representation. So the document demands staleness discipline in four separate sections while its status vocabulary cannot record the condition.

Two corrections are possible, and the choice matters:

```text
Option A — add a state
    status ∈ { PASS, FAIL, ERROR, UNKNOWN, SKIPPED, BLOCKED, STALE }

Option B — make staleness a property of evidence, not of a check result
    RuleResult.status  remains six-valued (what the check found)
    EvidenceRecord.stale = true  (whether the record still binds its subject)
```

**Option B is the better fit and follows the document's own structure.** §9's six states are defined as outcomes of predicate evaluation; adding a seventh mixes two different questions into one field. §10 already separates *"WHAT happened"* from *"WHAT evidence records the observation"* — so staleness belongs on the evidence record, where it invalidates the claim without altering what the check observed.

This also connects to §159 of Part XI: `subject.bound` is already one of the nine conditions for `execution_verified`. A `stale` flag makes the failed condition nameable — the claim fails because `subject.bound` no longer holds, not because the check changed its mind.

**Note.** The audit itself has this defect in a live form. §7's rule has direct bearing on `audit/rfl-ae-runall-receipt.log`: that receipt binds revision `1090511a…` specifically, and it is the only third-party execution evidence for the audited revision. Any future use of it against a different revision is stale reuse — which is why `vendor/PROVENANCE-rfl-ae.md` pins the revision and records the no-silent-sync policy.

---

## 6. Model gap: §20 depends on a rule-class system the document does not carry

§20 states the composition case:

```text
BASE:  repository.write = false
TASK:  repository.write = true
```

Result:

```text
CONFLICT
```

*"unless an explicit higher-authority rule permits the override."*

The escape clause makes the outcome depend on a classification that the document never assigns. The same example is resolved two different ways depending on an unstated fact:

| Rule class of `repository.write = false` | Correct outcome |
|---|---|
| `IMMUTABLE` | `CONFLICT` — no override is possible |
| `OVERRIDABLE` | TASK wins, if the override rule is explicit |
| `DEFAULT` | TASK wins by default |

Part IX §116 already defines exactly this system — `IMMUTABLE · REQUIRED · OVERRIDABLE · DEFAULT · ADVISORY` — and Part XI §153 relies on it:

```text
TASK    ✗ cannot override BASE / IMMUTABLE
DOMAIN  ✗ cannot grant authority
```

**The master instructions state the composition rule without stating the class system the rule operates on**, while §20's own example is unresolvable without it. This is a genuine under-specification, and it is the cheapest of the four self-consistency items to close: import §116's five classes into §20, and label the example rule.

Note the example was chosen well — `repository.write` is the one field where a wrong resolution *grants mutation authority*, which §4 forbids by default. A rule-class system is what makes §4's default hold under composition.

---

## 7. The document is PACK SOURCE — stage 1 of the 8-stage pipeline its own §19 defines

§19 requires of a pack:

```text
PACK SOURCE → PARSE → PACK IR → RESOLVE → CANONICALIZE → DIGEST → COMPILE → EXECUTE
```

and:

> *"Pack semantics must not depend on an LLM improvising their meaning."*

Measured against §19's own field list, this document is:

| §19 requires | Present? |
|---|---|
| `pack_id` | ✗ — "v0.1" is a version string in a title, and §152/XI forbids using a filename as identity |
| `version` | ~ present as prose |
| `digest` | ✗ |
| `kind` | ✗ — it is a governance pack, but does not say so |
| `authority` | ~ §4 states a **default**; there is no declared field or intersection with `GrantedAuthority` (XI §154) |
| `scope` | ~ §8 names `DECLARED/EXECUTED/CLAIMED`; no declared scope object exists |
| `dependencies` | ✗ |
| `rules` | ✓ — 28 sections, prose |
| `procedures` | ✓ — §14, §24 chains |
| `claims` | ~ §11 states the claim condition |
| `stop conditions` | ✓ — §22, with §4's gap |
| `evidence requirements` | ✓ — §10 |

**This is not a defect in the document.** It is stage 1 of 8. *PACK SOURCE* is the correct input; the other seven stages have not been performed. The document is the artifact a compiler consumes.

**But it is the ideal first input to build that compiler against**, and this is the actionable conclusion. Of §168's five reference packs, the master instructions are the **governance pack** — and they are:

- fully specified in prose, needing no new authoring
- backed by 14 audit receipts (§1), so their rules have known ground truth
- the only pack whose *content* is already known to be correct

A `packc` that compiles this document into `compiled-pack.json` + an execution projection, and whose output can be checked against §1's receipt table, exercises §168's end-to-end path on real content rather than a synthetic fixture. It is also the highest-stakes pack in the system: if the governance pack compiles wrongly, every pack that depends on it inherits the error — which is Part IX §118.1's point about the base pack, one layer up.

---

## 8. Self-application of §28

§28 is a 14-item gate to be applied *before making a strong verification claim*. Its obvious application is to this document.

**The document makes no verification claim about itself, so §28 does not apply to it.** It states requirements, not findings. No item of §28 is violated by its existence, and none should be marked so. Stating this plainly is the correct result; manufacturing a finding here would be exactly the *"produce a PASS"* inversion the document's closing rule rejects.

What *is* worth noting is the form of §28 itself. Its 14 items are the document's release gate, expressed as a human checklist — while §19 requires pack semantics to be machine-interpretable, Part XI §170 defines a machine-checkable gate (`PACK-001 … 015`), and §27 enumerates twelve conditions. **Three gate specifications now exist in three forms**, and only Part XI §170's is executable. §28 is the most complete (§3) and the least mechanisable.

The consolidation implied by §3 is therefore also a mechanisation: **adopt §28's 14 items as the authoritative admissibility predicate, express them in §170's machine-checkable form, and add the §170 items §28 lacks** — `PACK-004` canonicalization determinism, `PACK-005` digest determinism, and `PACK-014` repeat-compilation identity have no counterpart in §28's list.

---

## 9. Standing

| Part XII section | Standing |
|---|---|
| §1 Receipt mapping | **PROVED** — 14 rules mapped to executed receipts; §12's three prohibitions verified one-to-one against C-1/C-2/C-3 |
| §2 Live inconsistency | **PROVED** — 10 sections violated by the current toolchain, each with an executed receipt or a `grep -c` count |
| §2 §15 sharpening | **PROVED** — `run_all.sh:116`; count measured then discarded, gate is `[ $rc -ne 0 ]` alone |
| §3 Five enumerations | **PROVED by count** — 8/7/6/14/9; §11 omits verifier, authority, dependencies |
| §3.2 Corrections | **PROPOSED** |
| §4 §22 non-correspondence | **PROVED by count** — 8 stop conditions, 2 classified, 6 not; 2 classifications not stop conditions |
| §4.1 Four mappings | **PROPOSED** — 2 of the 4 arguable, flagged |
| §5 No `STALE` state | **PROVED** — named once (Part III), defined nowhere; Option B **PROPOSED** |
| §6 §20 rule classes | **PROVED** — under-specified; §116's system already exists and applies |
| §7 PACK SOURCE | **PROVED** — field-by-field against §19's own list |
| §8 §28 self-application | **Passes, correctly** — no verification claim is made by the document |

### The four corrections, in dependency order

**1. §22 (cheapest).** Classify the six unclassified stop conditions; move `checker crashed` and `predicate false` out of a list about stopping. Four mappings proposed, two flagged as choices.

**2. §28 §3 (highest value).** Declare §28 authoritative; restate §10 and §11 as declared projections. This closes the gap where the section defining *"The repository is verified"* omits the verifier.

**3. §20 §6.** Import Part IX §116's rule classes so composition outcomes are determined rather than contingent.

**4. §9/§28 §5.** Decide staleness: Option B (evidence property) preferred. Then express §28 in `PACK-0xx` form and add the three determinism items it lacks.

Then compile the document — it is the first real input for `packc`.

### The governing observation

This document is the most empirically grounded artifact in the series: **14 of its rules name a specific defect that was executed and measured**, and its three provenance prohibitions are exactly the audit's three critical findings. Its self-consistency findings are all of one kind — *the same predicate specified in more than one place without a declared relation* — which is **its own §17 principle applied one layer up from where §17 applies it.**

That is a coherent document with four closable gaps, not a flawed one. The audit's findings were about a toolchain whose verifiers could not establish what they reported. The master instructions are about an agent that must not repeat that — and with §2's reconciliation, and §171's `PHASE 0` before `PHASE 1`, it does not have to.
