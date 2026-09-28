# RFL-AE `skills/` — Independent Review, Part IV

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Parts I–III:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md)
**Execution receipt (Part III):** [`rfl-ae-runall-receipt.log`](rfl-ae-runall-receipt.log)
**Review date:** 2026-09-28

> **Method.** Part IV was verified by execution. Every claim in §34–§38 is decidable by reading or running the toolchain, and the central finding (§41) is demonstrated below with a reproducible failure that propagates through the entire verification pipeline.

> **Numbering note.** These sections are numbered 34–47 as given in this pass. That range does not collide with Parts I–III.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection |
| PARTIALLY PROVED | The direction is right; a specific sub-claim is inaccurate |
| **RESTATED / DISPROVED** | Already refuted in an earlier part, appearing again |
| recommendation | Forward-looking design proposal |

---

## 0. Summary of verification

| Part IV § | Claim | Verified? |
|---|---|---|
| 34 | `run_all.sh` overstates execution scope | **PROVED** (comment is line 2, not line 1) |
| 35 | `--strict` docstring contradicts implementation | **PROVED** |
| 36 | No `ERROR` vs `FAIL` distinction | **PROVED** — zero `try`/`except` in `audit_file.py` |
| 37 | One `fail` bit collapses failure domains | **PARTIALLY PROVED** — exit code *is* printed per stage |
| 38 | Stage IDs are presentation, not semantic | PROVED — recommendation only |
| 39 | Negative fixtures are not mutation tests | **PROVED** |
| 40 | Mutation completeness as the invariant | recommendation — strongly endorsed |
| 41 | No test for `renumber.py`'s central invariant | **PROVED — worse than claimed** |
| 42 | `renumber.py` needs a precondition model | recommendation — endorsed, now with a receipt |
| 43 | Transform should be typed / idempotence-tested | recommendation |
| 44 | Provenance should be machine-readable | recommendation |
| 45 | Four-layer architecture | recommendation |
| 46 | Decisive release invariant `VERIFIED(P)` | recommendation — endorsed |
| 47 | Updated status | **item 1 RESTATED and disproved**; items 2–10 valid |

---

## 0.1 Decisive receipt: the verification loop certifies fabricated provenance

This is the strongest single result in the four-part review, and it subsumes §41 into something larger.

**Test.** A source with a deliberate numbering gap, exactly the shape §41 proposes:

```markdown
## 1. First
## 3. Third
## 7. Seventh
```

**Transform.**

```bash
python3 skills/corpus-provenance-numbering/scripts/renumber.py apply DRAFT.md \
    --source-name SRC.md --offset 100 --out OUT.md
```

**Output.** Exit 0.

```markdown
## 101. First
<!-- source: SRC.md §1 -->
## 102. Third
<!-- source: SRC.md §2 -->
## 103. Seventh
<!-- source: SRC.md §3 -->
```

**The provenance comments are false.** The source contains no §2 and no §3. The tool has not merely lost the gap — it has **fabricated source identities**, asserting that `## 3. Third` came from source §2 and `## 7. Seventh` came from source §3.

**Audit.** Now the decisive step. The same output is fed to the auditor:

```bash
audit_file.py OUT.md --range 101-103 --source-name SRC.md --offset 100
```

Result:

```text
provenance    : 3/3 comments, mapping errors=0 (offset 100)
```

**`mapping errors=0`.** The verifier certifies the fabricated mapping.

### Why this is the central result

The audit checks:

```text
corpus − source = constant offset
```

on its own ordinal model:

```text
101 − 1 = 100  ✓
102 − 2 = 100  ✓
103 − 3 = 100  ✓
```

The writer produced that mapping from the same ordinal model. **Writer and verifier share the blind spot, so the verifier confirms the writer's fiction.**

```text
SOURCE §1, §3, §7
        │
        ▼
   renumber.py           "source 1..3 -> corpus 101..103"
        │                 (ordinal model)
        ▼
   OUT.md                101←§1, 102←§2, 103←§3
        │                 (provenance comments assert §2, §3 exist)
        ▼
   auditlib              corpus − source = 100 everywhere
        │
        ▼
   "mapping errors=0"    ✅ CERTIFIED
```

This is not a bug in one script. It is the demonstration of Part III §21's coverage asymmetry, Part I §29's duplicated parsers, and §41's missing regression test **as a single failure**: the verification loop is internally consistent and externally wrong.

Detecting it requires an **external authority** — the actual source document — which no component of the current toolchain possesses. That is precisely the gap §46's `VERIFIED(P)` invariant would close, and it is why §42's precondition model is not a nicety.

### Second receipt: fence blindness corrupts fenced bodies

````markdown
## 1. Real section

```text
## 999. This is not a section
```
````

After `apply`:

````markdown
## 101. Real section
<!-- source: SRC.md §1 -->

```text
## 102. This is not a section
<!-- source: SRC.md §2 -->
```
````

Two distinct failures in one output:

1. **The fenced body was rewritten.** `## 999.` became `## 102.` — a code sample's contents were silently edited.
2. **A provenance comment was injected *inside* the code fence**, corrupting the fenced body with tooling metadata.

And the tool reported `renumbered 2 sections` when the document has **one** section.

This confirms Part II §12's writer/checker shared blind spot with a concrete artifact: the writer corrupts fences, and the fence-blind auditor cannot see it.

---

## 34. `run_all.sh` does not actually mean "execute every check"

**Classification: PROVED** — with one trivial precision note.

The header comment reads (`skills/run_all.sh`, **line 2**; line 1 is the shebang):

```bash
# run_all.sh — execute every check the skills provide, in dependency order.
```

Stage 3 invokes `audit_corpus.py`, whose implementation does not execute the full `audit_file.py` invariant set (Part III §21). Stage 5 executes the full suite only for `RFL-LEDGER-V01.md`.

So the actual semantics are:

```text
corpus
                   │
                   ▼
          ┌────────────────┐
          │ audit_corpus   │
          └───────┬────────┘
                  │
        subset of invariants
                  │
                  ▼
          whole corpus PASS
                  │
                  +
                  │
                  ▼
        newest document FULL
```

The headline says `every check`; the execution semantics are `every check available in the selected stage, with full checks only on the newest document`.

**Precision note:** the quoted text is on line 2, not line 1 — a shebang occupies line 1. Immaterial to the finding, noted for citation accuracy.

This should be corrected either by changing the description or, preferably, by changing the implementation (Part III §21–§22).

---

## 35. There is a potentially serious `--strict` semantic ambiguity

**Classification: PROVED — documentation/implementation mismatch.**

The docstring in `audit_file.py` reads:

```text
Exit status: 0 clean, 1 problems found, 2 checks could not run (only fatal
under --strict for the render checks).
```

The implementation collects skipped checks at **three** sites:

```python
line  81:  skipped.append("render (markdown package not installed)")
line 116:  skipped.append("provenance (--source-name/--offset not given)")
line 138:  skipped.append("probes (--probes not given)")
```

and then:

```python
line 148:  if skipped and a.strict:
line 149:      say("FAIL (--strict): checks were skipped, so this run proved nothing")
line 150:      return 2
```

Therefore **all** skipped checks become fatal under strict, not merely render checks.

That is the safer behavior, and the parenthetical in the docstring is simply wrong.

**Classification: PROVED — documentation/implementation mismatch.**

The proposed contract is better than the current wording:

```text
strict:
    any required-but-unexecuted check => exit 2
```

Then the semantics become clean:

```text
PASS    = executed + passed
FAIL    = executed + failed
SKIPPED = not executed
ERROR   = execution failure

strict gate:
    SKIPPED => failure
```

Worth noting the implementation's own message is already better than its docstring: `"checks were skipped, so this run proved nothing"` states the principle exactly. The docstring should adopt the implementation's language, not the reverse.

---

## 36. More importantly: `audit_file.py` does not distinguish ERROR from FAIL

**Classification: PROVED.**

Consider:

```python
r = A.check_rust_balance(text)
```

If the checker itself throws, the process terminates. **`audit_file.py` contains zero `try` blocks and zero `except` clauses** — verified by direct count. Every check call is unguarded.

That means:

```text
verification failure
```

and:

```text
verifier failure
```

are both outside the structured result model.

For a trustworthy verification framework these must differ:

```text
CHECK ERROR
    verifier couldn't establish the proposition

CHECK FAIL
    verifier established that the proposition is false
```

This is fundamental. For example:

```text
rust parser crashed
```

does not mean:

```text
Rust block is invalid
```

It means:

```text
verification unavailable
```

**A coupled observation.** In practice the process does not exit silently — an uncaught exception yields exit 1 under a shell driver, and `stage()` records `exit $rc`. So an ERROR is currently *reported as* a FAIL, exiting with the same code as a genuine content defect. That is exactly the conflation §36 identifies, and it is why the eventual Evidence model needs:

```text
PASS
FAIL
SKIPPED
ERROR
```

not merely `0 / 1 / 2`.

---

## 37. `run_all.sh` collapses different failure domains into one fail bit

**Classification: PARTIALLY PROVED — direction correct, one sub-claim inaccurate.**

The driver has:

```bash
fail=0

stage() {
  ...
  "$@"
  rc=$?
  if [ $rc -ne 0 ]; then
    echo ">>> stage FAILED (exit $rc)"
    fail=1
  fi
  return 0
}
```

and at the end:

```bash
if [ $fail -ne 0 ]; then
  echo "RESULT: FAILURES ABOVE"
  exit 1
fi
```

So the final exit code is a single bit. **But** the per-stage exit code *is* printed: `>>> stage FAILED (exit $rc)`. Observed in Part III's bare-environment run:

```text
>>> stage FAILED (exit 1)     ← corpus audit, content-level
>>> stage FAILED (exit 2)     ← full audit, skipped-under-strict
```

**So the information is preserved in stdout but not in the exit code.** The claim "this loses information" is true of the machine-readable result and false of the log.

That distinction matters, and it strengthens §37's own conclusion: the problem is not that the data is absent, it is that it is **unstructured**. Extracting it for evidence requires parsing log text, and Part III §0 was produced by exactly that manual correlation.

The execution record should retain:

```text
stage
status
exit_code
stdout_digest
stderr_digest
duration
tool
tool_version
inputs
```

**Classification: PARTIALLY PROVED.** Better stated as: *failure domains are distinguishable in the log but not in any structured or machine-readable artifact.*

---

## 38. Stage identity itself should become stable

**Classification: PROVED observation — recommendation.**

The stages are numbered `1/7 … 7/7`. These are presentation identifiers, not semantic identifiers. If a stage is inserted, all following numbers change.

Observed in Part III: the run printed `3/7 corpus audit (33 documents)`, `5/7 last document audited in full`. Inserting a stage would renumber every subsequent label.

Use stable IDs:

```text
GEO-001
SKILL-001
CORPUS-001
LINK-001
DOC-001
CLOSEOUT-001
NEG-001
```

Then the stage display number can change without invalidating historical evidence. This becomes important if RFL-AE eventually stores signed receipts.

---

## 39. The negative tests are good, but they are not yet mutation tests

**Classification: PROVED.**

The existing negative specimen is substantial. `run_all.sh` plants eight defects in `/tmp/negtest/BROKEN.md`:

```text
## 2. Bad heading <!-- source: BROKEN.md §2 -->   ← inline comment (heading/slug defect)
## 3. Missing provenance                          ← absent source comment
## 4. Wrong offset <!-- source: BROKEN.md §99 --> ← offset violation
```rust struct X { a: u8,                      ← unterminated Rust block
```text ┌────┼────┐ ▼ ▼ ▼  (off-centre fan)
```text ┌────┐ | ... | └────┘  (ASCII pipe in box border)
[nowhere](#4-does-not-exist)                     ← broken anchor
[nofile](NOPE.md)                                ← missing target
```

That is good. But it tests:

```text
known fixture → expected failure
```

not:

```text
valid corpus → mutation → expected failure
```

The second is much stronger, and §39's proposal is right:

```text
VALID.md
   │
   ├── mutate heading number
   ├── mutate provenance number
   ├── delete fence
   ├── move arrow
   ├── alter Rust delimiter
   ├── change link target
   └── remove probe phrase
          │
          ▼
       checker
          │
          ▼
      MUST FAIL
```

One nuance worth recording. The current fixtures and mutations are **not in competition**: the fixture tests the checker's *sensitivity* to a broad defect class; mutation tests its sensitivity *per invariant* against inputs that are otherwise valid. The second is what makes coverage claims meaningful (Part III §22).

---

## 40. The ideal invariant is mutation completeness

**Classification: recommendation — strongly endorsed.**

For each invariant `I`:

```text
M(I) = mutation that violates I

checker(M(I)) = FAIL
```

| Invariant | Mutation | Expected |
|---|---|---|
| numbering | §12 → §13 | FAIL |
| provenance | source §4 → §5 | FAIL |
| fence balance | delete closing fence | FAIL |
| Rust structure | delete `}` | FAIL |
| box width | remove one `─` | FAIL |
| orphan detection | remove target label | FAIL |
| anchor resolution | alter link fragment | FAIL |
| probe fidelity | alter/remove phrase | FAIL |

This produces a much stronger statement than `we have negative tests`. It establishes:

> each checker detects a representative violation of the property it claims to enforce.

**One row must be added**, from §0.1:

| Invariant | Mutation | Expected |
|---|---|---|
| **source-number fidelity** | **gapped source §1, §3, §7** | **FAIL — currently PASSES** |

This is the mutation that currently defeats the pipeline. Including it in the matrix would have surfaced the central defect immediately, and §41 explains why it is absent.

---

## 41. There is still no test for `renumber.py`'s central invariant

**Classification: PROVED — and stronger than claimed.**

The SKILL declares:

```text
corpus − offset == source
```

**`renumber.py` is referenced nowhere in any `.sh` or `.py` file in the repository.** A recursive search over `--include=*.sh --include=*.py` returns no matches outside `renumber.py` itself. The only references anywhere are documentation lines in `corpus-provenance-numbering/SKILL.md` (`:35`, `:47`, `:79`, `:87`, `:106`) showing usage examples.

So the position is stronger than "there is no test for the central invariant":

```text
renumber.py is not executed by run_all.sh
renumber.py is not referenced by any test script
renumber.py is not exercised by any negative test
renumber.py's contract is documented but never verified
```

It is the **only** skill script in the suite with zero automated exercise — while being the one script that **writes** corpus documents.

The §41 regression test is exactly right:

```text
source:  ## 1. A / ## 3. B / ## 7. C
offset:  100

must produce:  ## 101. A / ## 103. B / ## 107. C

and provenance must prove:  101−100=1, 103−100=3, 107−100=7
```

**This test was executed in §0.1 and the current implementation fails it**, producing `101, 102, 103` with fabricated comments `§1, §2, §3` — and, critically, the auditor then certifies that fabricated mapping with `mapping errors=0`.

The finding is correct, and the consequence is larger than the finding states: this is not a missing test around a working component. It is a missing test around a component that **fabricates provenance which the rest of the pipeline then endorses.**

---

## 42. `renumber.py` needs a precondition model

**Classification: recommendation — endorsed, now strongly evidenced.**

The design question is right. What should happen if the source contains:

```markdown
## 1. A
## 1. B
```

or:

```markdown
## 3. A
## 1. B
```

or:

```markdown
## 1. A
## 4. B
```

?

The skill implies source numbering is authoritative. Therefore the transform must not silently "repair" it.

Proposed model:

```text
SOURCE-NUMBERING STATES

VALID       unique, strictly increasing
GAPPED      increasing but non-contiguous
DUPLICATE   repeated source number
DECREASING  source number decreases
MISSING     heading without recognized source number
```

Behavior:

```text
VALID       → transform
GAPPED      → transform + preserve gap
DUPLICATE   → FAIL
DECREASING  → FAIL
MISSING     → FAIL
```

This is much better aligned with evidence preservation than ordinal renumbering.

**Strengthened by §0.1.** The `GAPPED → transform + preserve gap` row is exactly the case that currently fails, and it fails in the worst possible way: not by refusing, but by producing output that *looks* correct, carries *false* provenance comments, and passes the *full* audit. A precondition model that classifies `GAPPED` explicitly is the minimum viable fix.

Note also that `GAPPED` must not simply be permitted — it must be **verified against the source**, which is §46's requirement and the thing no current component can do.

---

## 43. The transform should be idempotence-tested

**Classification: recommendation — endorsed.**

A particularly useful property:

```text
normalize(normalize(x)) = normalize(x)
```

For `renumber.py` the exact operation is intentionally one-way. So the relevant property is:

```text
apply(source, offset)
```

must not reinterpret an already-renumbered corpus document as a new source.

Given §0.1, this is not hypothetical. Applying `apply` to an already-renumbered document would take its corpus numbers as source numbers and offset them again — and if the input has gaps (as corpus documents do, §108 being one), the numbers would be re-fabricated a second time. The transform is not merely non-idempotent; repeated application is **destructive in a way the audit cannot detect**, for the same shared-model reason.

A stronger API would separate:

```text
SourceDocument
CorpusDocument
```

rather than accepting arbitrary Markdown:

```text
SourceDocument
    │
    │ transform(offset)
    ▼
CorpusDocument
```

and never:

```text
Markdown
    │
    ▼
guess what this is
```

That is another place where a typed IR would help, and §0.1 shows the cost of not having one.

---

## 44. Provenance should eventually be machine-readable

**Classification: recommendation — endorsed.**

The current form:

```html
<!-- source: RFL-LEDGER-V01.md §12 -->
```

is human-readable and useful, but it is also a **string protocol**.

For stronger evidence the IR should represent:

```text
Provenance {
    source_name: "RFL-LEDGER-V01.md",
    source_section: 12,
    corpus_section: 981,
    offset: 969
}
```

with the Markdown comment as a **serialization**:

```html
<!-- source: RFL-LEDGER-V01.md §12 -->
```

rather than the underlying semantic object.

This follows the broader architectural distinction:

```text
representation ≠ semantics ≠ evidence
```

**Additional motivation from §0.1.** Because provenance is a string protocol, `renumber.py` can write `<!-- source: SRC.md §2 -->` for a source that has no §2 — the string is well-formed and the semantic claim is false. A `Provenance` object carrying `source_section` alongside a validated source document would make the fabrication a type-level impossibility rather than a checker omission.

---

## 45. The next-generation skills architecture

**Classification: recommendation — endorsed.**

Four layers:

```text
skills/
│
├── corpus-core/
│   ├── markdown_lexer
│   ├── corpus_ir
│   ├── headings
│   ├── fences
│   ├── anchors
│   └── provenance
│
├── corpus-transform/
│   ├── numbering
│   └── placeholder-splice
│
├── corpus-verify/
│   ├── structural
│   ├── semantic probes
│   ├── rendered links
│   └── coverage
│
└── verification-kernel/
    ├── manifests
    ├── execution records
    ├── evidence
    ├── mutation tests
    └── release gate
```

And the execution contract:

```text
INPUT DIGEST
                       │
                       ▼
                 CHECK MANIFEST
                       │
                       ▼
                 EXECUTION PLAN
                       │
                       ▼
                 ┌─────────────┐
                 │ CHECK ENGINE│
                 └──────┬──────┘
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
           PASS        FAIL       ERROR
             │          │          │
             └──────────┼──────────┘
                        ▼
                 EXECUTION RECORD
                        │
                        ▼
                  EVIDENCE RECORD
                        │
                        ▼
                 COVERAGE MATRIX
                        │
                        ▼
                   RELEASE GATE
```

The layering is correct, and §0.1 explains why the split between `corpus-transform` and `corpus-verify` is load-bearing rather than organizational: a transform that fabricates and a verifier that certifies are two halves of one failure, and they must not share a model.

---

## 46. The decisive release invariant

**Classification: recommendation — endorsed without qualification.**

```text
VERIFIED(P)
iff

  P is explicitly declared
  AND
  P has a defined checker
  AND
  checker actually executed
  AND
  required dependencies were available
  AND
  input identity is known
  AND
  checker returned PASS
  AND
  evidence is retained
  AND
  coverage scope contains the subject
```

Therefore:

```text
No checker              → UNVERIFIED
Checker skipped         → UNVERIFIED
Checker errored         → UNVERIFIED
Partial scope           → only scoped verification
Stale evidence          → UNVERIFIED
PASS without execution receipt → UNVERIFIED
```

This is considerably stronger than requiring all scripts to exit zero.

**This invariant correctly rejects the §0.1 output**, and it is worth stating exactly which conjuncts fail, because the current toolchain satisfies most of them:

| Conjunct | §0.1 output |
|---|---|
| P explicitly declared | ✅ source-number fidelity is the skill's stated invariant |
| P has a defined checker | ✅ `check_provenance` |
| checker actually executed | ✅ ran, `3/3 comments` |
| dependencies available | ✅ |
| input identity known | ✅ (offset supplied) |
| checker returned PASS | ✅ `mapping errors=0` |
| evidence retained | ✅ receipt captured |
| **coverage scope contains the subject** | ❌ **the subject is the SOURCE numbering, which the checker never reads** |

**The final conjunct is the one that fails** — and it is the only one that requires the checker to have access to an artifact it does not currently possess. This is the precise, formal statement of the §0.1 defect, and it shows why the invariant is correctly formulated rather than merely strict.

I would add one conjunct, from Part III §33:

```text
  AND
  the checker is not the same implementation that produced the subject
```

Independence of writer and verifier is not implied by any conjunct above, and §0.1 is its absence made concrete.

---

## 47. Current RFL-AE skills status

**Classification: items 2–10 valid; item 1 RESTATED and previously disproved.**

The assessment is sound in structure:

```text
CURRENT
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
   transformation    verification     orchestration
        │               │                │
     strong          strong-ish       emerging
        │               │                │
        └───────────────┼────────────────┘
                        ▼
              verification substrate
                        │
                 NOT YET CLOSED
```

**Item 1 — "Current release evidence does not match the current README state" — is not correct, and is now the fourth appearance of the same claim.**

It was:

- **raised** in Part II §8 — *"the skills README is stale (997 vs 998)"* → disproved: 997 is a cardinality, 998 a maximum, difference = the deliberate §108 gap
- **escalated** in Part III §20 — *"run_all.sh cannot produce the claimed clean result … the evidence was not actually generated"* → disproved by execution: all 7 stages, exit 0, every commit-message number reproduced exactly
- **conceded** in Part III §32, where the amended P0 states *"not a defect; nothing to fix"*, and Part III's maturity table **withdraws** the row
- **restated here** as the number-one remaining risk

Part IV's own preamble frames the pass as *"even after the README inconsistency is fixed"* — acknowledging the concession — and then item 1 reinstates it. These cannot both be current.

The claim should be removed from the risk list. Items 2–10 are valid and are supported by Parts III–IV.

### Revised risk list

Numbered as in the reviewed text, with item 1 replaced:

1. ~~Current release evidence does not match the current README state~~ — **withdrawn; verified reproducible in Part III §0**
2. Corpus audit coverage is narrower than per-document audit coverage **(Part III §21 — PROVED)**
3. Multiple Markdown interpreters can disagree **(Part I §29, Part II §12)**
4. Anchor semantics are duplicated **(Part II §16)**
5. Skill validation is structural rather than behavioral **(Part III §26–27)**
6. Execution results lack a structured evidence model **(Part IV §37)**
7. Negative fixtures are not yet mutation-derived **(Part IV §39–40)**
8. FAIL / ERROR / SKIPPED are not consistently separated **(Part IV §35–36)**
9. Provenance is encoded as text rather than a first-class semantic object **(Part IV §44)**
10. Remote publication is described as part of closeout but remains outside the verifier **(Part I §25)**

**New, and I would rank it first:**

0. **`renumber.py` fabricates source provenance, and the audit certifies it. `renumber.py` is exercised by nothing. (Part IV §0.1, §41)**

---

## New findings from this pass

Not present in the reviewed text, discovered while verifying it.

### N-1. The `neg()` findings counter is format-coupled and reports a spurious `0 findings`

`run_all.sh`'s negative-test harness counts findings with:

```bash
grep -c '^  - ' /tmp/_neg.log
```

That pattern matches `audit_file.py`'s `"  - "` problem-list format. `linkaudit.py` uses a different format (`BROKEN ANCHOR …`, `MISSING FILE …`), so the counter returns zero.

Observed in Part III's stage 7:

```text
PASS  audit catches 8 planted defects                (exit 1, 6 findings)
PASS  close-out fails with no forward link           (exit 1, 3 findings)
PASS  splice aborts on an unreferenced diagram       (exit 1, 1 findings)
PASS  link checker catches a broken anchor and a missing target  (exit 1, 0 findings)
```

The link checker **definitively found two problems** — it exits 1, and the earlier bare run showed both `BROKEN ANCHOR …` and `MISSING FILE …` — yet the harness reports **0 findings**.

This is a small instance of the review's own theme: a measurement that silently reports nothing, in the gate that exists to detect silent reporting failures. The `PASS` verdict is correct; the *count* is not, and a count that reads zero is exactly the signal a reader would use to decide a check was vacuous.

**Classification: MINOR — but thematically significant.**

### N-2. `renumber.py` has zero automated exercise — quantified

Established in §41 by recursive search. Worth stating as its own finding because it is a coverage fact rather than a code defect:

```text
skill script            referenced by run_all.sh    referenced by any test
────────────────────────────────────────────────────────────────────────
geo.py          examples.py    ✅ stage 1            ✅ self-test 12 cases
validate_skill.py             ✅ stage 2            ✅ (is the validator)
audit_corpus.py               ✅ stage 3            ✅ negative test
linkaudit.py                  ✅ stage 4            ✅ negative test
audit_file.py                 ✅ stage 5            ✅ negative test (BROKEN.md)
verify_closeout.py            ✅ stage 6            ✅ negative test
splice.py                     ✅ stage 7            ✅ negative test
renumber.py                   ❌ never               ❌ never
```

`renumber.py` is the only script in the suite with no automated exercise at any level — and it is the only one that **writes** corpus documents.

**Classification: PROVED — coverage gap.**

### N-3. `GAPPED` sources are not merely mishandled — they are misreported

`renumber.py` prints, for the §0.1 input:

```text
renumbered 3 sections: source 1..3 -> corpus 101..103
```

The source was `§1, §3, §7`. `source 1..3` is a false statement about the input, printed by the tool, in the run's own success message. A human reviewing the log would read `source 1..3` and conclude the source was contiguous.

This is a reporting-layer instance of the same defect: the tool's summary asserts a property of the input that the input does not have.

**Classification: PROVED.**

### N-4. Positive observation: `run_all.sh`'s negative-test inversion is correctly built

The `neg()` harness inverts the usual convention — success means *non-zero* exit — and reports `PASS` on failure as designed:

```bash
neg() {
  desc="$1"; shift
  "$@" > /tmp/_neg.log 2>&1
  rc=$?
  if [ $rc -ne 0 ]; then
    echo "  PASS  $desc  (exit $rc, ...)"
```

Both `run_all.sh` and `bash` semantics were exercised in Part III and behaved correctly. The defect in N-1 is in the *measurement*, not the inversion logic. Recording this because a review that only lists defects understates a component that is largely correct, and because N-1 would be easy to over-read as a failure of the harness itself.

**Classification: positive.**

---

## Bottom line

Part IV's architectural direction is correct, and §46's `VERIFIED(P)` invariant is the right formalization of it. The section-level findings §34, §35, §36, §39 and §41 are all confirmed by inspection or execution; §37 is directionally right but overstates what is lost.

**The single most consequential result is §0.1.** `renumber.py` does not merely lose source gaps — it fabricates provenance comments, and `auditlib` then certifies the fabricated mapping with `mapping errors=0`, because writer and verifier share one ordinal model. The pipeline is internally consistent and externally wrong, and no component of the current toolchain can detect it. §46's coverage-scope conjunct is exactly the conjunct that fails.

Two corrections to the reviewed text:

1. **§37 is PARTIALLY PROVED** — the per-stage exit code *is* printed (`>>> stage FAILED (exit $rc)`); what is missing is structure, not data.
2. **§47 item 1 is RESTATED and previously disproved** (Part II §8, Part III §20, conceded in Part III §32). It should be removed, and §0.1 should take its place at the top of the risk list.

Four new findings: the `neg()` findings counter reporting a spurious `0 findings` (N-1); `renumber.py`'s zero automated exercise, now quantified against every other script (N-2); its success message asserting a false property of its input (N-3); and one positive note on the negative-test inversion being correctly built (N-4).

**Recommended immediate action**, in one line: give `renumber.py` the §41 regression test. It fails today, it would have caught the central defect on the day it was written, and adding it is cheaper than any of the architectural work it justifies.

---

## Appendix — Part IV finding index

| Part IV § | Finding | Class | Verified by | Note |
|---|---|---|---|---|
| 0.1 | Fabricated provenance certified by audit | **PROVED (receipt)** | execution | strongest result of the four parts |
| 0.2 | Fence blindness corrupts fenced bodies | **PROVED (receipt)** | execution | comment injected inside a fence |
| 34 | `run_all.sh` overstates scope | PROVED | source | comment is line 2, not line 1 |
| 35 | `--strict` docstring contradicts code | PROVED | source | implementation is safer than documented |
| 36 | No ERROR vs FAIL distinction | PROVED | source | zero `try`/`except`; ERROR reads as FAIL, exit 1 |
| 37 | One `fail` bit collapses domains | PARTIALLY PROVED | execution | exit code *is* printed per stage |
| 38 | Stage IDs not semantic | PROVED | source | recommendation |
| 39 | Fixtures not mutations | PROVED | source | both are needed, not either |
| 40 | Mutation completeness | recommendation | — | add the source-number row |
| 41 | No test for `renumber.py` invariant | **PROVED — stronger** | search + execution | zero references in any `.sh`/`.py` |
| 42 | Precondition model | recommendation | — | endorsed; `GAPPED` row is the live case |
| 43 | Idempotence / typed transform | recommendation | — | repetition is destructively undetectable |
| 44 | Machine-readable provenance | recommendation | — | fabrication becomes type-impossible |
| 45 | Four-layer architecture | recommendation | — | transform/verify split is load-bearing |
| 46 | `VERIFIED(P)` | recommendation | — | coverage-scope conjunct is the failing one |
| 47 | Status | **item 1 restated/disproved** | Parts II–III | items 2–10 valid |
| N-1 | `neg()` counter reports spurious `0 findings` | MINOR | execution | format-coupled to `audit_file.py` |
| N-2 | `renumber.py` zero automated exercise | PROVED | search | only writer with no test |
| N-3 | Success message asserts false input property | PROVED | execution | `source 1..3` for `§1,§3,§7` |
| N-4 | Negative-test inversion correctly built | positive | execution | N-1 is a measurement defect, not a logic one |
