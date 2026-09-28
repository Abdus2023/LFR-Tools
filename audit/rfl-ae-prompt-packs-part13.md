# RFL-AE Prompt Packs — Part XIII: Master Instructions v0.1 → v0.2 — Finding Disposition

**Subject:** [`rfl-ae-master-prompt-instructions-v0.2.md`](rfl-ae-master-prompt-instructions-v0.2.md) (40 sections), superseding [`v0.1`](rfl-ae-master-agent-instructions-v0.1.md) (28 sections)
**Series:** [`part8`](rfl-ae-skills-review-part8.md) · [`part9`](rfl-ae-prompt-instruction-packs.md) · [`part10`](rfl-ae-prompt-packs-part10.md) · [`part11`](rfl-ae-prompt-packs-part11.md) · [`part12`](rfl-ae-prompt-packs-part12.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — version diff
>
> **v0.2 is a material change to a material input.** Its own §24 states:
>
> > *"Changing a material input should invalidate dependent evidence."*
>
> Part XII's findings are evidence dependent on v0.1. They are therefore **re-evaluated against v0.2 in §2, not carried forward.** Two are closed, one is partially closed, and one is restated in a sharper form.
>
> **New in this part:**
>
> - **§3** — §26's binding list and its own digest, six lines apart, do not agree.
> - **§4** — §11 gives two 8-field views of a check that disagree on two positions, and the instance form drops the verifier.
> - **§5** — §28's 13 conditions conflate *stop* with *downgrade the claim*; four of them fit no state in §7.
> - **§6** — v0.2 names six anti-patterns that exist in `vendor/rfl-ae/` today, with one — `SKIPPED ≠ PASS` — contradicted by the toolchain's own printed output.
>
> Applying §24 to this part itself: **Part XIII is evidence dependent on v0.2, and a v0.3 would invalidate it.** That is stated so the dependency is not assumed away later.

---

## 1. What changed

| | v0.1 | v0.2 |
|---|---|---|
| Sections | 28 | **40** |
| Core invariants (§1) | 9 | **12** |
| Stop conditions (§28) | 8, with a partial 4-item classification map | **13, with no map** |
| Rule classes | absent | **§21 — 5 strengths, 11 types** |
| Verifier sections | 0 | **§13, §14 — independence and self-verification** |
| Evidence structures | prose | **§25 ExecutionRecord (12 fields), §26 binding, §27 CoverageRecord (6 fields)** |
| Fence awareness / numbering | inside §17 | **§17, §18 — standalone mandatory sections** |
| Atomicity | absent | **§19** |
| Git / CI discipline | 1 line in §7 | **§31, §32 — separate sections** |
| Failure-mode analysis | absent | **§35 — 11 mandatory questions** |
| Anti-patterns | §27, 11 items | **§36 — 7 named constructions** |

**Three constructs were dropped or relocated:**

- §1's `SOURCE → … → PUBLICATION` pipeline is **removed** from the invariants; the provenance chain now appears only at §15 as `SOURCE ↓ TRANSFORMATION ↓ ARTIFACT`. The full `… → GATE → PUBLICATION` chain from v0.1 §2 no longer appears anywhere.
- v0.1 §11's *"The repository is verified" requires…* — the **6-item checklist — is gone.** §8 now classifies claims without stating an admissibility condition. This matters for §2.1 below.
- v0.1 §10's internal 8/7 list asymmetry is gone; §26 states one binding list.

---

## 2. Disposition of Part XII's four findings

| Part XII finding | Disposition | Basis |
|---|---|---|
| **XII §3** — admissibility specified five times (8/7/6/14/9); §11 weakest, omits the verifier | **CLOSED** | The competing definition was removed, not repaired |
| **XII §4** — §22's 8 stop conditions, 2 classified; 2 classifications not stop conditions | **RESTATED** | §28: 13 conditions, no classification — non-correspondence replaced by conflation |
| **XII §5** — no `STALE` state despite four sections demanding staleness discipline | **RESTATED, stronger** | Now referenced in §24, §33, §35, §38; §24 adds a *new* requirement the model cannot express |
| **XII §6** — §20's conflict outcome depends on an unassigned rule class | **PARTIALLY CLOSED** | §21 supplies the five strengths; §22's example still does not label its class |

### 2.1 XII §3 — CLOSED, by removal

Part XII's finding was that §11 — the section explicitly defining *"The repository is verified"* — was the weakest of five enumerations, omitting verifier, authority, and dependencies.

**v0.2 deletes that checklist.** §8 now classifies claims (`FACT / OBSERVED / DERIVED / INFERENCE / HYPOTHESIS / UNCERTAINTY`) without stating an admissibility condition, and §38 becomes the single final gate.

That is the correct repair. The defect was not that §11's six conditions were wrong — they were each correct — but that a *weaker* condition was presented as the definition while a stronger one existed elsewhere. Removing the weaker definition resolves it more cleanly than amending it would have, because amending would still have left two checklists.

**Residual check.** Is `verifier` now present where required?

| Section | Binds verifier? |
|---|---|
| §13 Verifier Independence | ✓ — the section exists |
| §26 Evidence | ✓ — `VERIFIER` in the binding list |
| §38 Final Claim Check | ✓ — *"What verifier produced the observation?"* |
| §25 ExecutionRecord | ~ — `tool` + `tool_version` serve as verifier identity for an execution |
| **§11 Check** | **✗ — see §4** |

So the omission is closed everywhere except §11, where a new variant of it appears.

### 2.2 XII §4 — RESTATED, in a form the earlier finding does not describe

v0.1 §22 listed 8 stop conditions and classified 2. Part XII's finding was that the two lists did not correspond, in either direction.

v0.2 §28 lists **13 conditions and classifies none.** The non-correspondence is gone because the map is gone.

But the underlying defect — *conflating two different kinds of condition* — survived the restructuring, and it is now visible in §28's own opening clause:

> *"Stop **or downgrade the claim** when:"*

**`Stop` and `downgrade the claim` are different responses with different statuses.** Some of §28's conditions mean *execution cannot legitimately proceed* (§7's `BLOCKED`/`UNKNOWN`/`ERROR`). Others mean *execution proceeded and the claim must be weakened* (`PARTIAL`). The section issues both under one verb. See §5.

### 2.3 XII §5 — RESTATED, and strengthened

Part XII found no `STALE` state, while four sections demanded staleness discipline. In v0.2:

| Section | Requirement |
|---|---|
| §24 | *"Changing a material input should invalidate dependent evidence."* |
| §33 | *"Do not use stale evidence as current evidence without identifying its time period."* |
| §35 | *"Could stale evidence appear current?"* |
| §38 | *"Could stale evidence explain the result?"* |

And §7 still defines exactly six states, none of which represents invalidation. **§24's requirement is new and is stronger than v0.1 had**: v0.1 asked that staleness be *noticed*; §24 requires that dependent evidence be *invalidated*. Invalidation is a state transition, and there is no state for it to transition to.

Part XII recommended Option B — staleness as a property of the evidence record rather than a seventh check status, since §9/§7's six states are predicate-evaluation outcomes and staleness is a different question. **v0.2 §26 makes that recommendation easier to implement**: evidence is now a digest-bound structure, so a `stale` flag has a defined home. The gap is unchanged; the fix is now cheaper.

### 2.4 XII §6 — PARTIALLY CLOSED

Part XII found that §20's conflict example (`repository.write = false` vs `true`) had an outcome contingent on a rule class the document never assigned.

**§21 now supplies it:**

```text
Rule strength: IMMUTABLE · REQUIRED · OVERRIDABLE · DEFAULT · ADVISORY
Precedence alone must never grant authority.
```

That is Part IX §116's system, and the added sentence closes the escalation path §23 formalizes.

**What remains:** §22's conflict example uses `unsafe_code = forbidden` vs `permitted` and still does not label the rule's strength. So the outcome is *determinable* now but not *determined* — a reader must infer that `unsafe_code` is `IMMUTABLE`-class to reach the stated `CONFLICT` result. The gap moved from "no class system exists" to "the example does not exercise it." **MINOR and closable in one line.**

---

## 3. New: §26's binding list and its own digest do not agree

§26 states the binding in two forms, six lines apart:

```text
Evidence should bind:              EvidenceDigest = H(
    SUBJECT                            SubjectDigest
    CHECK                           || CheckDigest
    VERIFIER                        || VerifierDigest
    EXECUTION                       || ExecutionDigest
    OBSERVATION                     || ObservationDigest
    SCOPE                           || CoverageDigest
    RESULT                          )
```

**7 items bound; 6 components digested.** Element by element:

| Concept | In binding list | In digest |
|---|---|---|
| SUBJECT | ✓ | ✓ |
| CHECK | ✓ | ✓ |
| VERIFIER | ✓ | ✓ |
| EXECUTION | ✓ | ✓ |
| OBSERVATION | ✓ | ✓ |
| **SCOPE** | **✓** | **✗** |
| **RESULT** | **✓** | **✗** |
| **COVERAGE** | **✗** | **✓** |

**SCOPE and RESULT appear in the binding requirement but not in the digest; COVERAGE appears in the digest but not the binding requirement.**

Why this matters more than a count difference: §26's own sentence says *"the binding principle must remain"* — the digest is how the binding is *enforced*. A concept required by the binding statement but absent from the digest is required in prose and unenforced in the construction. That is the same gap §151.1 of Part XI prohibits at the protocol level, and the same one Part V §0.1 measured in `has_prov`: **a requirement stated, and then not represented in the mechanism that decides.**

Two readings, both worth resolving explicitly:

1. **SCOPE and COVERAGE are the same concept.** Under this reading the digest is correct and the binding list should say `COVERAGE`. This is plausible — §27 uses `declared scope` as `CoverageRecord`'s first field — but then SCOPE is redundant in §26's list.
2. **They are distinct**, and the digest is incomplete.

**RESULT has no reading under which it is covered.** There is no `ResultDigest`, and RESULT is not obviously Observation (both appear as separate items in the same list). Under §26's own principle — *"Evidence from one subject must not silently migrate to another subject"* — the field that records *what the check found* is exactly the one that should be bound, and it is the one not digested.

**Recommended correction:** make the two forms structurally identical, and state whether SCOPE ≡ COVERAGE. Then §24's rule *"Changing a material input should invalidate dependent evidence"* has a defined set of inputs to check.

---

## 4. New: §11 gives two 8-field views of a check that do not agree

§11 defines a check twice, in the same section:

```text
Check                    A check is:
├── check_id             CHECK-ID
├── predicate            Predicate
├── subject              Subject
├── scope                Scope
├── procedure            Procedure
├── verifier             Observed Result        ← not in the struct
├── expected result      Status                 ← not in the struct
└── evidence requirements Evidence
```

**Both have 8 fields. Three positions differ.**

| Field | Struct | Instance form |
|---|---|---|
| check_id / CHECK-ID | ✓ | ✓ |
| predicate / Predicate | ✓ | ✓ |
| subject / Subject | ✓ | ✓ |
| scope / Scope | ✓ | ✓ |
| procedure / Procedure | ✓ | ✓ |
| evidence requirements / Evidence | ✓ | ~ |
| **verifier** | **✓** | **✗** |
| **expected result** | **✓** | **✗** |
| **Observed Result** | ✗ | **✓** |
| **Status** | ✗ | **✓** |

The plausible reading is that the struct is the **schema** (what a check *is*) and the line is an **instance** (a check *reported*). Under that reading the two field sets are legitimately different, and the instance simply adds `Observed Result` and `Status` because a reported check has results.

**But under that same reading, the instance drops `verifier` — and that is not legitimate.** A reported check with no verifier cannot satisfy:

- §13 — *"A verifier must not certify properties it has not actually evaluated"* (which verifier?)
- §26 — the binding list requires `VERIFIER`
- §38 — *"What verifier produced the observation?"*

`expected result` is also dropped, which matters because §12 requires the *expected* finding to be compared against the observed one:

```text
EXPECTED DEFECT → EXPECTED DETECTOR → EXPECTED FINDING → EXPECTED STATUS
```

An instance form carrying `Status` but not `expected result` has nothing to compare `Status` against — which is structurally why §12's requirement cannot be mechanised on this shape. **§12 and §11 are inconsistent with each other**, and the fix is to add `verifier` and `expected result` to the instance form.

This is a small finding with a direct consequence: it is the same defect that `neg()` exhibits at runtime (§6.1) — a status reported without an expectation to compare it to — appearing in the schema that is supposed to fix it.

---

## 5. New: §28's 13 conditions mix two responses, and four fit no state

§28 says *"Stop **or downgrade the claim** when:"* and lists 13 conditions. Under §7's definitions they partition into three classes:

**Class A — execution cannot legitimately proceed** (maps cleanly to §7):

| Condition | §7 state | Basis |
|---|---|---|
| missing authority | `BLOCKED` | §7: *"authority … was unavailable"* |
| ambiguous scope | `BLOCKED` | cannot establish whether proceeding is legitimate |
| unknown subject identity | `UNKNOWN` | §5 requires it before verification |
| missing artifact | `UNKNOWN` | §7's *"artifact … unavailable"* |
| unavailable dependency | `UNKNOWN` | §7, verbatim |
| undefined predicate | `ERROR` | machinery cannot produce a valid evaluation |
| verifier failure | `ERROR` | §7, verbatim |
| conflicting instructions | `BLOCKED` | no deterministic resolution (§22) |
| authority escalation | `BLOCKED` | §23 — *"must be rejected"* |

**Class B — execution proceeded, the claim must weaken** (no state exists):

| Condition | Required response | Representation |
|---|---|---|
| missing evidence | report narrower claim | none |
| coverage mismatch | `PARTIAL` claim | none |
| provenance loss | report narrower claim | none |

**Class C — invalidation** (no state exists):

| Condition | Required response | Representation |
|---|---|---|
| unexpected repository mutation | invalidate dependent evidence (§24) | none |

**So 9 of 13 map cleanly to §7, and 4 do not** — 3 claim-limiters and 1 invalidation condition.

Part XII §5 reached this exactly by a different route, from v0.1 §22's single unclassifiable condition (*"evidence cannot be captured"*). v0.2 reframed that as *"missing evidence"* and added three more of the same kind. **The two-level gap identified in Part XII is not closed; it now has four instances.**

The correction is the one Part XII proposed, now with more force:

```text
RuleResult.status   ∈ { PASS, FAIL, ERROR, UNKNOWN, SKIPPED, BLOCKED }
                      — what the check found. Six-valued. Unchanged.

ClaimRecord         = { admissibility, limiters[], invalidated }
                      — what may be claimed on the strength of it.
```

Splitting the verb in §28's header across these two objects resolves all four. `Stop` → the six states. `Downgrade the claim` → the claim record. And §24's invalidation becomes a transition on the claim record rather than a check status, which is the same conclusion Part XII §5 reached by way of `STALE`.

---

## 6. Live consistency: v0.2 names six anti-patterns that exist today

The new sections are not abstract. Measured against `vendor/rfl-ae/`, each names something present in the repository.

| § | v0.2 rule | Present in `vendor/rfl-ae/skills/` | Evidence |
|---|---|---|---|
| **§36** | *"Do not create: … nonzero-only negative tests"* | ❌ **exists** | `run_all.sh:120` — `neg()` gates on `[ $rc -ne 0 ]` |
| **§17** | fence awareness; fence content is not structure | ❌ **violated** | C-2; `sections()` counts fenced `## N.` |
| **§18** | preserve source numbering; `§1,§3,§7` + 100 → `§101,§103,§107` | ❌ **violated** | C-1, re-reproduced from `vendor/` in Part XI |
| **§19** | atomic rename, not `write()` | ❌ **violated** | `renumber.py:111` — plain `open(out, "w")`; no `tempfile`, `os.replace`, `flush`, or `fsync` anywhere in the file |
| **§25–§27** | `ExecutionRecord`, `EvidenceRecord`, `CoverageRecord` | ❌ **entirely absent** | **Zero** `json` emissions in the toolchain; `grep -rn "json\." skills/` → no matches |
| **§32** | *"Never state CI passed"* without a CI execution record | ❌ **nothing to state** | No `.github/`, no workflow, no `.yml`/`.yaml` in the tree at all |
| **§14** | a surviving mutation is a verification gap | ❌ **4 survive** | Part VIII §0.1 |
| **§31** | a commit does not establish that it was pushed | ❌ **unverifiable** | `verify_closeout.py` — 0 `git`/`subprocess` invocations |

**§18's correspondence is exact.** v0.2 states: *"If source contains `§1 §3 §7` and the operation adds offset 100, the semantic result is `§101 §103 §107`."* That is C-1, in the document's own notation, verified against `renumber.py` — which produces `§101 §102 §103` with source comments `§1 §2 §3`. **Part XI reproduced this from `vendor/` this session.**

### 6.1 `SKIPPED ≠ PASS` is contradicted by the toolchain's own printed output

This is the sharpest case, because the toolchain announces the state in words.

`run_all.sh:32-33`:

```sh
$PY -c "import markdown; print('markdown:', markdown.__version__)" 2>/dev/null \
  || echo "markdown: NOT INSTALLED -- render checks will be SKIPPED"
```

And v0.2 §1 states, as a mandatory invariant:

```text
SKIPPED → NOT PASS
```

With §7 defining `SKIPPED = check was intentionally not executed`.

**One condition — `markdown` absent — receives three different treatments in a single run:**

| Where | Treatment | Classification implied |
|---|---|---|
| `run_all.sh:33` | announced as `SKIPPED` | `SKIPPED` |
| stage 3 | fatal under `--strict`: `PROBLEMS (1): render checks could not run` | `FAIL` (or `ERROR`) |
| stage 7 | two render-dependent checks do not run; negative tests still report `PASS` with **6** findings instead of **8** | `PASS` |

The third row is the violation. §27 states:

> *"Never infer `no finding` from `not inspected`."*

Stage 7 does exactly that: two checks did not execute, and the harness reports `PASS`. Part VIII §0.1 measured the differential — **same commit, same fixture, 6 findings without `markdown`, 8 with it, both `PASS`.** v0.2 §1's `SKIPPED → NOT PASS` is not a hypothetical standard the toolchain fails; it is the exact rule that the measured behaviour breaks.

Note also the message is **factually wrong**, as Part VIII §98 established: it says the render checks "will be SKIPPED," and stage 3 treats their absence as fatal. So the announcement is wrong in one place and the classification is wrong in another, from one dependency probe.

### 6.2 §36's named anti-pattern exists at three sites

§36 prohibits *"nonzero-only negative tests."* `run_all.sh` contains three `rc -ne 0` gates:

```text
:23   stage runner            if [ $rc -ne 0 ]; then ... fail=1
:40   geometry self-test      [ $rc -ne 0 ] && { ... fail=1; }
:120  neg()                   if [ $rc -ne 0 ]; then echo "PASS ... findings"
```

Lines 23 and 40 gate *stages*, where exit-code-only is defensible. **Line 120 is the one §36 names**, and it is worse than "only checks the exit code" — as Part XII §2 established from the source, `neg()` **measures** the finding count via `grep -c` and then prints it without ever comparing it to an expectation. The measurement exists and is discarded.

### 6.3 §25–§27's evidence layer is absent, not partial

Worth stating precisely, because "partial" and "absent" are different claims:

```text
grep -rn "json\.\|json_export\|--format" vendor/rfl-ae/skills/   →  no matches
```

**No checker emits structured output of any kind.** Every result is human-readable text on stdout. So:

- §25's `ExecutionRecord` (12 fields) — no instance exists
- §26's evidence binding — no instance exists
- §27's `CoverageRecord` (6 fields) — no instance exists; the 21 occurrences of "coverage" in the toolchain are prose, e.g. `audit_corpus.py:9`, a docstring line

And §11's requirement that *"Every verification claim should map to an identifiable check"* has no mechanism: there is no `check_id` anywhere in the toolchain.

**This is the largest single gap between v0.2 and the repository.** §25–§27 specify the evidence layer; the repository has none of it. Part XI §150 reached the same conclusion from the architecture side — *"Evidence: absent-as-structure"* — and Part XIII confirms it from the document side.

---

## 7. Minor: §1's chain notation is ambiguous

§1 states the distinction chain as:

```text
representation ≠ semantics ≠ evidence ≠ ... ≠ durability ≠ release
```

In mathematical convention, `A ≠ B ≠ C` is read as **consecutive** inequalities — `A ≠ B` and `B ≠ C` — which do **not** entail `A ≠ C`. The intent here is plainly all-pairs: thirteen mutually distinct concepts.

This is trivial for a human reader and non-trivial for a compiler. §20 requires that *"the semantic source of truth should eventually be a structured pack,"* and a pack compiled from this notation would encode twelve consecutive inequalities where thirteen pairwise-distinct terms were meant. **MINOR** — a one-line clarification resolves it, and it belongs with §3's recommendation to make §26's two forms structurally identical: both are cases where a relation is stated in a form that does not survive mechanical interpretation.

---

## 8. What v0.2 improved

Stated separately from the findings, because the diff is substantial and mostly forward:

| Change | Significance |
|---|---|
| **v0.1 §11's weak checklist deleted** | XII §3 closed by removal rather than by amendment |
| **§21 rule strengths** | XII §6's underpinning supplied; *"precedence alone must never grant authority"* closes the escalation path |
| **§13, §14 verifier sections** | A verifier that certifies what it did not evaluate was the audit's most-repeated finding; these are the first normative statements against it |
| **§17, §18 standalone** | The two highest-leverage defects (C-2, C-1) now have dedicated mandatory sections with the exact example |
| **§25–§27 evidence layer** | ExecutionRecord / EvidenceDigest / CoverageRecord — the concrete structures the audit's P3 and P1 items called for |
| **§31, §32** | Git and CI state confusion — including the `git ls-remote` gap Part VII §80 found — now has separate normative statements |
| **§35's 11 questions** | Includes *"Could the verifier detect the verifier's failure?"* and *"Could missing evidence appear as PASS?"* — both directly measured |
| **§23's `∩ EnvironmentAuthority`** | Structurally prevents authorization from being read out of repository content, which §2 and §3 both forbid |

---

## 9. Standing

| Part XIII section | Standing |
|---|---|
| §2.1 XII §3 | **CLOSED** — competing definition removed; residual §11 variant recorded in §4 |
| §2.2 XII §4 | **RESTATED** — 13 conditions, no map; conflation in §28's header |
| §2.3 XII §5 | **RESTATED, stronger** — §24 adds an invalidation requirement with no state |
| §2.4 XII §6 | **PARTIALLY CLOSED** — §21 supplies the classes; §22's example unlabelled |
| §3 §26 list vs digest | **PROVED** — 7 vs 6; SCOPE and RESULT undisgested, COVERAGE unbound |
| §4 §11's two check views | **PROVED** — 8 vs 8, three positions differ; `verifier` dropped from the instance |
| §5 §28's 13 conditions | **PROVED by classification** — 9 map to §7, 4 do not |
| §6.1 `SKIPPED` | **PROVED** — `run_all.sh:32-33` plus Part VIII §0.1's measured 6/8 differential |
| §6.2 §36's anti-pattern | **PROVED** — three `rc -ne 0` sites; `:120` is the named one |
| §6.3 evidence layer absent | **PROVED** — zero JSON emissions |
| §7 chain notation | **MINOR** |

### Corrections, in dependency order

**1. §4 (cheapest).** Add `verifier` and `expected result` to §11's instance form. One line; makes §11 consistent with §12, §13, §26, §38.

**2. §3 (highest value).** Make §26's binding list and digest structurally identical; state whether `SCOPE ≡ COVERAGE`. This is the document's own §151.1 principle — a requirement must be represented in the mechanism that decides, not only in prose.

**3. §5 (largest).** Split §28's header: `Stop` → §7's six states; `downgrade the claim` → a claim record with `limiters[]` and `invalidated`. Resolves the four unrepresentable conditions and folds in §24's invalidation requirement and Part XII §5's staleness gap.

**4. §6 (prerequisite for anything else).** The six live violations are `PHASE 0` of Part XI §171. v0.2 §36 naming `nonzero-only negative tests` and §1 stating `SKIPPED → NOT PASS` does not change the toolchain; it changes what may be claimed about it.

**5. §2.4, §7 (one line each).** Label §22's example rule class; clarify §1's notation as pairwise.

### The pattern, stated once

Four of Part XIII's five findings are the same defect:

```text
a requirement stated in one form
and represented in another
with no declared relation between them
```

- §3 — bound in prose, not in the digest
- §4 — in the schema, not in the instance
- §5 — one verb for two responses
- §7 — one notation for two relations

This is **§16's principle** (*"Do not use independent regex interpretations of the same structured language when semantic consistency matters… If multiple parsers exist, establish their semantic relationship"*), and **§15's** (*"source identity, transformation identity, output identity must remain reconstructible"*), applied one layer up — at the specification rather than the parser.

Part XII found the same pattern at v0.1 and called it §17 applied above where §17 applies. **v0.2 fixed its instances and reproduced its shape.** That is the honest summary: the direction of travel is right, the class of defect is persistent, and it is exactly the class the document exists to eliminate.
