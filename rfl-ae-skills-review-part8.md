# RFL-AE `skills/` — Independent Review, Part VIII

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Parts I–VII:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md) · [`part5`](rfl-ae-skills-review-part5.md) · [`part6`](rfl-ae-skills-review-part6.md) · [`part7`](rfl-ae-skills-review-part7.md)
**Consolidated reference:** [`rfl-ae-skills-audit-consolidated.md`](rfl-ae-skills-audit-consolidated.md)
**Review date:** 2026-09-28

> **Method.** Verified by execution. §103 required a two-environment differential on the negative fixture; §104 required seeding stale state; §98 required the degraded-environment run.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection |
| PROVED — stronger | The direction is right; the actual defect is materially worse than stated |
| **RESTATED (n)** | Already established in n earlier parts |
| recommendation | Forward-looking design proposal |

---

## 0. Verification summary

| Part VIII § | Claim | Verified? |
|---|---|---|
| 97 / 97.1 | `$PY` inconsistency; two interpreters in one run | **RESTATED (4th)** — II §27, III §28, VII §3.8.2 |
| 98 | Dependency probe is advisory, not authoritative | **PROVED — and the advisory message is factually wrong** |
| 99 | `audit_file.py` docstring contradiction | **RESTATED (2nd)** — IV §35 |
| 100 | `audit_corpus` runs a subset | **RESTATED (2nd)** — III §21 |
| 101 | Stage title reveals coverage asymmetry | **RESTATED (3rd)** — III §21, III §23 |
| 102 | `neg()` proves only `rc != 0` | **RESTATED (2nd)** — VII §0.2, VII §90 |
| 103 | The findings count is informational, not asserted | **PROVED — DECISIVE RECEIPT, far stronger than claimed** |
| 104 | `/tmp` as mutable shared state | **PROVED** |
| 105 | `validate_skill.py` is structural | **RESTATED (3rd)** — III §26, VI §64 |
| 106 | Meta-validator trusts the skill's own Verification section | **RESTATED (2nd)** — VI §65 |
| 107 | `CheckManifest` | **RESTATED (2nd)** — VI §66 |
| 108 | Stable check IDs | **RESTATED (2nd)** — IV §38 |
| 109 | `CoverageRecord` | **RESTATED (2nd)** — III §22 |
| 110 | `ERROR` first-class | **RESTATED (2nd)** — IV §36 |
| 111 | State machine | **RESTATED (2nd)** — V §62 |
| 112 | Priority ordering | **P0 item 3 is the SIXTH appearance of a disproved claim** |

---

## 0.1 Decisive receipt: the negative test's sensitivity halves, and nothing notices

This is the strongest result since Part VII, and it confirms §103 with evidence far beyond what the finding claims.

**The finding says** the `8 planted defects` count is printed but never asserted, so `expected: 8 / actual: 1` or `actual: 0` would still `PASS`.

**Both are true, and the live system is already exhibiting the defect.**

Two `run_all.sh` runs of the *same* commit, differing only in whether the `markdown` package is installed:

```text
bare environment (system python3, no markdown):

  PASS  audit catches 8 planted defects  (exit 1, 6 findings)

full environment (venv, markdown 3.11):

  PASS  audit catches 8 planted defects  (exit 1, 8 findings)
```

**The same negative test reports PASS in both cases while detecting 25% fewer defects in the degraded environment.** The description asserts 8; the bare environment finds 6; both pass; nothing anywhere compares the two.

### Which two defects go undetected

Running `audit_file.py` on the fixture in both environments and diffing the findings:

| # | Finding | Bare | Full |
|---|---|---|---|
| 1 | ASCII art mixed into a box-drawing block | ✅ | ✅ |
| 2 | arrow lands on whitespace | ✅ | ✅ |
| 3 | broken `.md` links | ✅ | ✅ |
| 4 | broken internal anchors | ✅ | ✅ |
| 5 | provenance incomplete: 4/6 comments, 1 mapping error | ✅ | ✅ |
| 6 | rust block 0: `1{` vs `0}` | ✅ | ✅ |
| 7 | **`1 code block(s) render with no language class -- a placeholder escaped its ```text fence`** | ❌ | ✅ |
| 8 | **`HTML comment leaked into a rendered heading`** | ❌ | ✅ |

The two undetected defects are precisely the two that require the renderer.

### Why this is severe

**Finding 7 is the check for the defect that created the strict-mode rule.** `skills/README.md` states:

> That rule came from a concrete failure: **37 placeholders were spliced outside their fences**, every structural check still reported success, and the only symptom was a `<pre>` count that disagreed with the expected number of fence pairs.

The message the bare environment fails to emit — *"a placeholder escaped its ```` ```text ```` fence"* — describes that incident exactly. It is the check for the historical failure that motivated rule 2.

So, in the degraded environment:

> **The negative test passes even though the checker for the recorded historical incident's defect class did not run.**

This is a **mutation survival proof, obtained by accident**. Removing `check_render`'s unclassified-fence detection entirely would produce an identical result in that environment: the fixture's planted defect goes undetected, the harness sees exit 1 from the other six findings, and prints `PASS`.

That makes this a **fourth surviving mutant**, and the highest-value one of the four:

| Mutant | Survives | How known |
|---|---|---|
| corrupt source number | ✅ | Part IV §0.1 |
| change offset | ✅ | Part IV §0.1 |
| delete all provenance | ✅ | Part V §0.1 |
| **delete unclassified-fence detection** | ✅ | **Part VIII §0.1 (accidental)** |

### It also completes §98

The finding in §98 says the dependency probe is advisory rather than authoritative. The measured behaviour is worse than *advisory* — **the probe's message is factually wrong**:

```bash
markdown: NOT INSTALLED -- render checks will be SKIPPED
```

What actually happens to stage 3 under `--strict`:

```text
PROBLEMS (1):
  - render checks could not run
```

The render checks are not *skipped*; they are **fatal**. The advisory text predicts one outcome and the pipeline delivers the opposite.

And between those two facts — a probe that misdescribes the outcome, and a negative test whose sensitivity silently drops — **the degraded environment is reported as `FAIL` at stage 3 and `PASS` at stage 7 for the same missing dependency**, with the sensitivity loss recorded nowhere.

### The chain in full

```text
markdown not installed
        │
        ├──► probe prints "render checks will be SKIPPED"     (wrong: they FAIL)
        │
        ├──► stage 3 fails: "render checks could not run"      (correct, but late)
        │
        ├──► stage 5 fails: SKIPPED under --strict             (correct)
        │
        └──► stage 7 PASSES while detecting 6 of 8 defects     (undetected)
                 │
                 └── the 2 missed are render-dependent,
                     including the check for the incident
                     that created the rule
```

**Classification: PROVED — DECISIVE.** §103 is correct, and the live system already violates it in a way that hides the loss of the check for its own originating defect.

---

## 0.2 A correction to my Part IV

I had the evidence for §0.1 across two parts and did not compare it. Recording that plainly.

Part IV's N-1 section states:

> Observed in **Part III's stage 7**:
>
> ```text
> PASS  audit catches 8 planted defects  (exit 1, 6 findings)
> ```

But the receipt committed to this repository as `rfl-ae-runall-receipt.log` — produced by the successful venv run in Part III — reads:

```text
PASS  audit catches 8 planted defects  (exit 1, 8 findings)
```

The `6` I quoted came from an **earlier, non-committed bare-environment run**. So Part IV's citation is wrong in two ways: it attributes the line to "Part III's stage 7", and the committed Part III receipt shows a different number.

**I wrote the 6 and the 8 in two different documents and never compared them.** Part III §0 recorded the venv run; Part IV quoted the bare run; the discrepancy was visible from Part IV onward and I treated both as the same measurement.

This is precisely the failure the audit documents in the audited code:

| My error | Audited equivalent |
|---|---|
| quoted a run without recording its environment | Part V §57 — environment is a load-bearing input, recorded nowhere |
| transcribed two different measurements as one | Part V §0.1 — observation discarded, verdict retained |
| did not compare two numbers I held | Part IV §47 / Part V §63 — the 997/998 claim, five times |

It is also, with hindsight, **the receipt for §103** — sitting in my own notes. I classified the `0 findings` line as a MINOR format-coupling defect in Part IV while a 6-vs-8 divergence was on the line above it.

**Corrections applied:** this section, plus an amendment to the consolidated document's N-1 entry.

---

## 97. `run_all.sh` exposes a deeper problem: the pipeline is not actually one execution graph

**Classification: RESTATED (4th) for §97.1.** The framing in §97 is new and is the better statement of the issue.

### 97.1 Python interpreter identity is broken

Confirmed at `run_all.sh:12` and `:37`:

```bash
PY="${1:-python3}"
…
python3 skills/ascii-diagram-forge/scripts/examples.py > /tmp/_geo.log 2>&1
```

Every other stage uses `$PY`. So one run can use two interpreters, as the finding says. **Fourth appearance** — Part II §27, Part III §28, Part VII §3.8.2 all reported it, and Part III demonstrated it live (stage 1 ran under the system `python3` while the venv was the only interpreter with `markdown`).

It remains latent only because `geo.py` and `examples.py` import nothing external:

```python
from __future__ import annotations
from typing import Iterable, List, Sequence, Tuple
import os, sys
from geo import (...)
```

### The reframing in §97 is worth keeping

> The pipeline is not actually one execution graph.

This is more accurate than "the interpreter is inconsistent", and it generalizes. The seven stages are not a single graph with one environment, one subject set, and one scope; they are seven independent processes that happen to be invoked in sequence, sharing no execution context:

| Stage | Interpreter | Environment | Subject set | Scope |
|---|---|---|---|---|
| 1 | **`python3`** | ambient | geometry patterns | self-test |
| 2 | `$PY` | ambient | all skills | structural |
| 3 | `$PY` | ambient | 33 documents | 5 invariants |
| 4 | `$PY` | ambient | 50 files | rendered links |
| 5 | `$PY` | ambient | 1 document | 11 invariants |
| 6 | `$PY` | ambient | 2 documents | content only |
| 7 | `$PY` | ambient | fixtures | `rc != 0` |

Nothing is shared, so §0.1's sensitivity loss is structurally invisible: stage 3 and stage 7 see the same missing dependency and reach opposite conclusions without any component comparing them.

---

## 98. The first dependency probe is informational, not authoritative

**Classification: PROVED — and the probe's message is factually wrong.**

```bash
$PY -c "import markdown; print('markdown:', markdown.__version__)" 2>/dev/null \
  || echo "markdown: NOT INSTALLED -- render checks will be SKIPPED"
```

The finding correctly identifies the two authorities — an advisory probe and an authoritative strict checker — and the proposed resolution is right: a `DependencyRecord` with explicit scheduling so the runner can say

```text
CHECK RENDER-001  dependency: markdown  availability: MISSING  policy: REQUIRED  result: BLOCKED
```

rather than letting dependency state emerge indirectly from checker behaviour.

**What the receipt adds beyond the finding.** The probe does not merely fail to be authoritative — it predicts the wrong outcome. Measured, under `--strict`:

```text
$PY skills/.../audit_corpus.py --expect-gaps 108 --strict
…
PROBLEMS (1):
  - render checks could not run
```

The checks are not skipped. They are fatal. So the pipeline opens with a line that misdescribes its own behaviour, and in the bare environment the run ends `FAILURES ABOVE` / exit 1 — the opposite of *"will be SKIPPED"*.

This is the same defect class as `has_prov` (Part V §0.1) and `load_probes`' pre-fix `#` handling (Part I §28): a message about coverage that does not match what happens. Fixed by the finding's own recommendation — resolve dependencies before scheduling, and report the resolution as data.

---

## 99. `audit_file.py` has a documentation contradiction

**Classification: RESTATED (2nd) — Part IV §35.**

Confirmed verbatim. Docstring:

```text
Exit status: 0 clean, 1 problems found, 2 checks could not run (only fatal
under --strict for the render checks).
```

Implementation: three skip sites (render, provenance, probes) and

```python
if skipped and a.strict:
    return 2
```

The implementation is the safer behaviour and the docstring is wrong. Part IV §35 reached the same conclusion and recommended adopting the implementation's own message as the contract:

> `--strict`: any skipped check → exit 2. *"checks were skipped, so this run proved nothing."*

Nothing has changed. No new evidence.

---

## 100. But `audit_corpus.py` does not follow the same strict model completely

**Classification: RESTATED (2nd) — Part III §21.**

The finding's analysis of `audit_corpus.py`'s render handling is accurate:

```python
if rd["available"]:
    uncl, cmt, leak = rd["unclassified"], rd["cmthead"], rd["leak"]
else:
    uncl = cmt = leak = "-"
    skipped_any = True
…
if skipped_any and not A.HAVE_MARKDOWN:
    if a.strict:
        problems.append("render checks could not run")
```

Render is handled correctly under strict. The finding's conclusion is also correct and is the one Part III §21 established by measurement:

> `audit_corpus PASS` cannot mean *every document passes the full document audit*. It means *every document passes the subset implemented by `audit_corpus`*.

The difference, measured:

```text
audit_file    numbering · fences · rust · render · structure
              · provenance · links · probes           (11 checks)
audit_corpus  numbering · fences · render · raw links · provenance   (5 checks)
```

This is the third statement of the same finding across Parts III, VII and VIII. The recommendation — *make coverage machine-readable rather than relying on humans to remember the distinction* — is §109's `CoverageRecord`, and it is the right remedy.

---

## 101. `run_all.sh` therefore has a false-looking stage title

**Classification: RESTATED — the observation is accurate and belongs in the coverage record.**

```text
3/7 corpus audit (33 documents)      ← technically true
5/7 last document audited in full    ← implicitly reveals the asymmetry
```

The real graph is:

```text
33 documents ──► corpus-level subset (5 of 11 invariants)
1 document   ──► full audit (11 of 11)
```

and not `33 documents × full audit`.

The finding's proposed release-policy distinction is the correct generalization:

```text
document coverage:
    corpus-level checks   = 33/33
    full-document checks  =  1/33
```

so a gate can require `FULL_CORPUS` or `CORPUS_STRUCTURAL + LATEST_FULL` depending on policy — making the choice explicit rather than implicit in stage numbering.

---

## 102. The negative test harness has a more serious evidence problem

**Classification: RESTATED (2nd) — Part VII §0.2 and §90.**

Confirmed:

```bash
if [ $rc -ne 0 ]; then
```

with the finding's enumeration of everything that satisfies it being exactly right:

```text
expected checker failure       → nonzero   ✅ intended
unexpected checker failure     → nonzero   ❌ passes
Python exception               → nonzero   ❌ passes  (demonstrated Part VII §0.2)
missing dependency             → nonzero   ❌ passes  (demonstrated Part VII §0.2)
wrong command                  → nonzero   ❌ passes
permission failure             → nonzero   ❌ passes
```

> The test claims *"checker caught defect X"* but actually proves *"command did not return zero"*.

Correct, and this is the same finding Part VII proved with the `ModuleNotFoundError` receipt. No new evidence in this pass, but the enumeration is clearer than Part VII's.

---

## 103. The "8 planted defects" assertion is also not asserted

**Classification: PROVED — DECISIVE RECEIPT. See §0.1.**

The code:

```bash
echo "  PASS  $desc  (exit $rc, $(grep -c '^  - ' /tmp/_neg.log) findings)"
```

The count is interpolated into an informational string. There is no comparison. The finding's two scenarios are both valid:

```text
expected: 8   actual: 1   exit: 1   → PASS
expected: 8   actual: 0   (crashed)  exit: 1   → PASS
```

**And §0.1 shows the live system already diverging by two findings between environments while reporting PASS.** The finding's proposed contract is exactly right:

```text
expected:
    exit = 1
    findings = { RUST-BALANCE, UNCLASSIFIED-FENCE, COMMENT-IN-HEADING, ... }

observed: structured findings

PASS iff expected ⊆ observed
```

with exact equality for fitted fixtures.

**One addition the finding does not make.** This is where the repository's own doctrine applies most sharply. The negative test exists because *"a checker that has never been shown a defect is unverified"* (rule 3). An unasserted count means the test cannot distinguish *shown 8 defects* from *shown 6*. The mechanism that certifies the checkers is itself uncertified — and §0.1 shows it already failing, silently, in a documented degraded environment.

**Classification: PROVED.** Priority: this should rank with the P0 items.

---

## 104. `/tmp` is also being used as mutable shared test state

**Classification: PROVED.**

Fixed paths, confirmed:

```text
/tmp/_geo.log          /tmp/negtest/
/tmp/_neg.log          /tmp/negtest_splice/
```

The finding's two concerns are correct — interrupted-run persistence and parallel collision. Only the first is demonstrated here.

**Receipt.** Seed `/tmp/negtest_splice/diag/` with a stale diagram file, as an interrupted prior run would:

```text
$ printf 'STALE CONTENT FROM A PREVIOUS RUN\n' > /tmp/negtest_splice/diag/DSTALE.txt
$ # then run the splice negative test exactly as run_all.sh does
placeholders  : 1 occurrences, 1 unique
diagrams      : 3 in /tmp/negtest_splice/diag
  - diagram files never referenced: ['DSTALE', 'DUNUSED']
SPLICE ABORTED (nothing written)
```

Compared with the clean run's `['DUNUSED']`, the diagnostic changed. The **exit code is still 1**, so `neg()` still reports `PASS` — but the *content* of the finding changed based on leftover state.

### The compound with §103, which the finding does not note

Today §104 is masked by §103:

```text
stale state changes the diagnostic
        │
        └──► neg() does not check the diagnostic  → undetected
```

**Implement §103's fix and §104 becomes an active defect**: once the harness asserts an expected finding set, leftover `/tmp` state will break the test nondeterministically. So the two must be fixed together, in that order:

1. `mktemp -d` with the temp root in the execution context (per the finding)
2. *then* assert expected findings

Reversing the order produces a gate that fails intermittently for environmental reasons — more likely to be disabled than fixed.

**Classification: PROVED — MINOR alone, and a prerequisite-ordered dependency of §103.**

---

## 105. `validate_skill.py` is still structural validation, despite its name

**Classification: RESTATED (3rd) — Part III §26, Part VI §64.**

Confirmed: the source documents *"scripts run"* while implementing `py_compile.compile(...)`.

The finding's six-level hierarchy is the clearest statement of this in any pass, and is worth adopting as the vocabulary:

```text
LEVEL 0   file exists
LEVEL 1   Python parses / compiles
LEVEL 2   script executes
LEVEL 3   positive behavior verified
LEVEL 4   negative behavior verified
LEVEL 5   mutation resistance verified
LEVEL 6   execution evidence bound to artifact
```

> Current `validate_skill.py` establishes roughly Level 1.

Correct, and the mapping onto the accumulated receipts is exact:

| Level | Status | Receipt |
|---|---|---|
| 0 | ✅ | file existence checked |
| 1 | ✅ | `py_compile` |
| 2 | ❌ | §97.1 — stage 1 `python3`; no execution evidence |
| 3 | ❌ | no positive fixture executed |
| 4 | ⚠️ | negative tests exist but centralized and unasserted (§103) |
| 5 | ❌ | §4.8 — four surviving mutants |
| 6 | ❌ | Part V §56 — no verifier identity exists |

---

## 106. The meta-validator should not trust the skill's own Verification section

**Classification: RESTATED (2nd) — Part VI §65.**

The self-attestation boundary is confirmed by execution in Part VI §65: a skill whose Verification section reads *"This skill is verified. Trust me."* passes validation, exit 0.

The finding's framing of the remedy is right:

> The documentation becomes descriptive, not authoritative.

and §107 supplies the type. No new evidence in this pass.

---

## 107. This suggests a new object: `CheckManifest`

**Classification: RESTATED (2nd) — Part VI §66.**

```yaml
check:
  id: RFL-MD-RUST-001
  name: rust-delimiter-integrity
subject:
  class: specification-document
  scope: fenced-rust-blocks
implementation:
  verifier: auditlib.check_rust_balance
inputs:
  required: [markdown, source-file]
result:
  states: [PASS, FAIL, ERROR, SKIPPED]
strict:
  skipped: BLOCK
  error: BLOCK
fixtures:
  positive: [rust-balanced-001]
  negative: [rust-unbalanced-001]
```

Endorsed. Two observations from the receipts:

1. **The `strict` block is the resolution to §98.** Making `skipped: BLOCK` and `error: BLOCK` declared per check turns the advisory probe into a scheduling input, which is exactly what §98 asks for.
2. **The `fixtures` block is where §103's expected-finding sets belong.** A `negative` fixture should carry `expected_findings: [...]`, not merely a name — otherwise the manifest reintroduces the unasserted count in a new form.

The `id` convention (`RFL-MD-RUST-001`) also satisfies §108.

---

## 108. `Check IDs` solve several existing problems simultaneously

**Classification: RESTATED (2nd) — Part IV §38.**

Confirmed: stages are numbered `1/7 … 7/7`, which are display labels. Inserting a stage renumbers every subsequent label and invalidates the interpretation of historical evidence. Stable IDs (`GEO-001`, `SKILL-001`, `CORPUS-001`, `LINK-001`, `DOC-001`, `CLOSEOUT-001`, `NEG-001`) do not.

This becomes concrete in the receipt format this audit has been producing:

```text
check_id       = CORPUS-001
verifier_commit = 1090511...
subject_digest = sha256:...
result         = PASS
```

versus `stage 3 passed`. No new evidence.

---

## 109. The next major abstraction is `CoverageRecord`

**Classification: RESTATED (2nd) — Part III §22.**

The proposed object and the worked example are right:

```text
CORPUS-001:   total 33  checked 33  coverage 33/33  completeness COMPLETE
DOC-FULL-001: total 33  checked  1  coverage  1/33  completeness PARTIAL
```

> Now the release gate can distinguish `PASS + PARTIAL` from `PASS + COMPLETE`.

This directly implements `PARTIAL COVERAGE → NO FULL-CORPUS VERDICT`, and Part III §22 already established it. The `completeness` field is the part that matters — it is what makes stage 3's `ALL FILES OK` interpretable.

**One addition from the receipts.** `CoverageRecord` should also carry the **dependency** dimension, since §0.1 shows coverage degrading with the environment while remaining nominally 33/33:

```text
CORPUS-001   subjects 33/33   invariants 5/11   dependencies ALL_AVAILABLE
CORPUS-001   subjects 33/33   invariants 3/11   dependencies markdown=MISSING
```

Two runs, both `33/33`, with materially different strength.

---

## 110. ERROR must become first-class

**Classification: RESTATED (2nd) — Part IV §36.**

Confirmed: `audit_file.py` has zero `try`/`except`; an uncaught exception exits 1, indistinguishable from a content finding. Part V N-6 showed `verify_closeout.py` doing the same on a missing input.

The five-state model is right:

```text
PASS      invariant was established
FAIL      verifier executed successfully; observed invariant violation
ERROR     verifier could not establish a result
SKIPPED   verifier was deliberately not executed
UNKNOWN   execution happened but observation was insufficient
```

and the two examples are the correct disambiguations:

```text
markdown package missing   ≠  document contains invalid Markdown   →  SKIPPED/BLOCKED
IndexError inside checker  ≠  corpus invalid                       →  ERROR
```

Nothing new beyond Part IV §36 and Part V §62, but the examples are the clearest statement of it in the eight passes.

---

## 111. The resulting state machine becomes explicit

**Classification: RESTATED (2nd) — Part V §62.**

```text
DECLARED → SCHEDULED → EXECUTING → { OBSERVED, ERROR, SKIPPED }
                                         │
                              { PASS, FAIL, UNKNOWN }
                                         │
                    EVIDENCE_BOUND → COVERAGE_BOUND → GATEABLE → RELEASED
```

with:

```text
ERROR ≠ FAIL · SKIPPED ≠ PASS · UNKNOWN ≠ PASS
PARTIAL ≠ COMPLETE · OBSERVED ≠ VERIFIED · VERIFIED ≠ RELEASED
```

Part V §62 supplied a receipt for each distinction. This pass adds one to the table — `SKIPPED ≠ PASS` now has a second, stronger receipt from §0.1, where 2 of 8 checks were effectively skipped and the harness reported PASS:

| Distinction | Receipt |
|---|---|
| `ERROR ≠ FAIL` | Part IV §36, Part V N-6 |
| `UNKNOWN ≠ PASS` | Part V §0.1 (`n/a` in a clean cell) |
| **`SKIPPED ≠ PASS`** | **Part VIII §0.1 — 6 of 8 found, PASS reported** |
| `PARTIAL ≠ COMPLETE` | Part III §21; Part VIII §101 |
| `OBSERVED ≠ VERIFIED` | Part V §57 |
| `VERIFIED ≠ RELEASED` | Part I §25 |

`COVERAGE_BOUND` is the node the current pipeline lacks entirely, and it is what would have caught §0.1.

---

## 112. New priority ordering

**Classification: recommendation, with one correction — P0 item 3 is the SIXTH appearance of a disproved claim.**

The ordering is sound and the P0→P5 progression is right. Two amendments.

### Amendment 1 — P0 item 3

> **3. README expected values align with current corpus**

This is the `997 → 998` claim. Its history:

| Appearance | Form | Outcome |
|---|---|---|
| Part II §8 | "the skills README is stale" | DISPROVED — cardinality vs maximum |
| Part III §20 | "`run_all.sh` cannot produce the clean result" | DISPROVED by execution — exit 0, all numbers exact |
| Part III §32 | conceded: *"not a defect; nothing to fix"* | — |
| Part IV §47 | reinstated as risk item 1 | flagged RESTATED |
| Part V §63 | P0 item 3 | flagged RESTATED (5th) |
| Part VII §80 | **explicitly corrected** — *"997 sections and range 1–998 are not contradictory"* | closed |
| **Part VIII §112** | **P0 item 3 again** | **RESTATED (6th)** |

`skills/README.md` is accurate. `997` is a section count, `998` is the maximum section number, the difference is the deliberate `§108` gap preserved by design and enforced by `--expect-gaps 108`. Part VII explicitly closed this and explained the arithmetic; the item cannot be reinstated in the very next pass without new evidence, and none is offered.

**Replace P0 item 3 with §103** — asserting expected findings in the negative tests. That change is strictly more valuable than the item it replaces, and §0.1 shows it is already causing silent degradation.

### Amended P0

```text
P0 — correctness defects
  1. renumber.py preserves captured source numbers        (C-1, Part I §5)
  2. renumber.py becomes fence-aware                      (C-2, Part I §6)
  3. neg() asserts expected findings, not rc != 0         (C-4, Part VIII §103)  ← NEW
  4. provenance scope gate: remove has_prov               (C-3, Part V §0.1)
  5. negative-test fixtures use mktemp -d                 (Part VIII §104)
  6. run_all.sh uses $PY consistently                     (Part II §27)
```

with the ordering constraint from §104: **item 5 precedes item 3** if item 3 is implemented first, or the test becomes nondeterministic.

### Amendment 2 — P2 item 14

> **14. provenance as structured identity**

Correct, and it is the same remedy as Part V §50. Worth noting it is the precondition for the `T⁻¹` round-trip property (Part VII §84) that would test §2.1's fabrication.

The remainder of the P1–P5 ordering is endorsed without change.

---

## New findings from this pass

### N-14. The negative test's sensitivity is environment-dependent, and the loss is undetected

Established in §0.1 by two-environment differential: 8 findings with `markdown`, 6 without, `PASS` in both. The two missed defects are the render-dependent ones, including the unclassified-fence check for the historical incident that created the strict-mode rule.

This yields a **fourth surviving mutant** — removing that check entirely would be undetectable in a bare environment — and it is the highest-value of the four because it is the check guarding the recorded origin defect.

**Classification: PROVED — critical.**

### N-15. The dependency probe's message is factually wrong

`markdown: NOT INSTALLED -- render checks will be SKIPPED` precedes a run in which `--strict` makes the render checks **fatal**:

```text
PROBLEMS (1):
  - render checks could not run
```

The advisory predicts the opposite of the outcome. §98 classifies the probe as *advisory*; measured, it is *incorrect*.

**Classification: PROVED — MINOR.**

### N-16. §103 and §104 are prerequisite-ordered

Stale `/tmp` state changes the splice negative test's diagnostic without changing its exit code, so it is currently masked. Implementing §103 (assert expected findings) without §104 (`mktemp -d`) produces a test that fails intermittently for environmental reasons.

**Classification: PROVED — ordering constraint on the fix.**

### N-17. Self-review: quotation error in Part IV, and a missed comparison

Disclosed in §0.2. Part IV's N-1 quotes `(exit 1, 6 findings)` and attributes it to "Part III's stage 7", whose committed receipt shows `8`. The `6` came from an earlier non-committed bare-environment run.

The substantive point is not the citation: **I held both numbers across two parts and never compared them**, which is the receipt for §103 sitting in my own notes. I classified the neighbouring `0 findings` line as a MINOR format-coupling defect while a 6-vs-8 divergence was one line above it.

**Classification: PROVED — self-inflicted, corrected in §0.2 and in the consolidated document.**

---

## Bottom line

Part VIII restates thirteen sections already established and adds one decisive receipt.

**The decisive result is §0.1: the negative test's sensitivity halves in a documented degraded environment, and nothing notices.** Two runs of the same commit differ only in whether `markdown` is installed:

```text
bare:  PASS  audit catches 8 planted defects  (exit 1, 6 findings)
full:  PASS  audit catches 8 planted defects  (exit 1, 8 findings)
```

The description asserts 8; the bare environment finds 6; both pass. **The two undetected defects are the render-dependent ones — including the unclassified-fence check for the incident that created the strict-mode rule** (*"37 placeholders were spliced outside their fences"*). So in the degraded environment the negative test passes while the check guarding the project's own recorded origin defect does not run. That is a fourth surviving mutant, obtained by accident, and the highest-value of the four.

It also completes §98: the probe's message is not merely advisory but **wrong**. It says `render checks will be SKIPPED`; `--strict` makes them fatal.

Confirmed as stated: §97 (reframed better as *"not one execution graph"*), §98, §100–§104, §107, §109–§111. §99, §102, §105, §106, §108 are restatements of Part IV–VII findings with no new evidence, listed here only because the pass raised them.

**§112's P0 item 3 is the sixth appearance of the `997 → 998` claim** — disproved twice, conceded once, closed explicitly by Part VII §80 in the immediately preceding pass, and reinstated here without new evidence. Replaced above with the §103 fix.

Three new findings beyond §0.1: the probe's incorrect message (N-15), the §103/§104 ordering constraint (N-16), and a quotation error of my own from Part IV (N-17) — which is the receipt for §103, since I held both the 6 and the 8 without comparing them.

**Amended P0:**

```text
1. renumber.py preserves captured source numbers
2. renumber.py becomes fence-aware
3. neg() asserts expected findings, not rc != 0          ← NEW (was: README 997→998, withdrawn)
4. remove has_prov; require provenance for CORPUS_SPEC
5. negative-test fixtures use mktemp -d                  ← must precede 3 if 3 lands first
6. run_all.sh uses $PY consistently
```

Items 3 and 5 together are the smallest change that closes the highest-severity defect in this pass.

---

## Appendix — Part VIII finding index

| Part VIII § | Finding | Class | Verified by | Note |
|---|---|---|---|---|
| 0.1 | Negative test sensitivity env-dependent; 6 vs 8, both PASS | **PROVED (receipt)** | 2-env differential | strongest result |
| 0.2 | Self-correction: Part IV quotation error | PROVED | receipt log | see N-17 |
| 97.1 | `$PY` inconsistency | **RESTATED (4th)** | Parts II, III, VII | latent — geometry has no deps |
| 98 | Dependency probe advisory | PROVED — stronger | execution | message is **wrong**, not merely advisory |
| 99 | Docstring contradiction | **RESTATED (2nd)** | Part IV §35 | unchanged |
| 100 | `audit_corpus` subset | **RESTATED (2nd)** | Part III §21 | 5 of 11 |
| 101 | Stage title asymmetry | **RESTATED (3rd)** | Parts III, VII | coverage = 33/33 vs 1/33 |
| 102 | `neg()` proves only `rc != 0` | **RESTATED (2nd)** | Part VII §0.2 | enumeration clearer than Part VII |
| 103 | Findings count not asserted | **PROVED — DECISIVE** | §0.1 | see N-14 |
| 104 | `/tmp` mutable shared state | **PROVED** | seed test | prerequisite of §103 |
| 105 | Validator is structural | **RESTATED (3rd)** | Parts III, VI | 6-level hierarchy is the best statement |
| 106 | Verifier self-reference | **RESTATED (2nd)** | Part VI §65 | unchanged |
| 107 | `CheckManifest` | **RESTATED (2nd)** | Part VI §66 | `negative: expected_findings` should be explicit |
| 108 | Stable check IDs | **RESTATED (2nd)** | Part IV §38 | satisfies via §107's id convention |
| 109 | `CoverageRecord` | **RESTATED (2nd)** | Part III §22 | add a dependency dimension (§0.1) |
| 110 | ERROR first-class | **RESTATED (2nd)** | Part IV §36 | unchanged |
| 111 | State machine | **RESTATED (2nd)** | Part V §62 | `SKIPPED ≠ PASS` now has receipt #2 |
| 112 | Priority ordering | **P0 item 3 RESTATED (6th)** | Parts II–VII | replace with §103 |
| N-14 | Sensitivity loss undetected; 4th surviving mutant | **PROVED — critical** | §0.1 | highest-value mutant |
| N-15 | Probe message factually wrong | PROVED | execution | says SKIPPED, delivers fatal |
| N-16 | §103/§104 prerequisite ordering | PROVED | seed test | fix together, §104 first |
| N-17 | Part IV quotation error; missed comparison | PROVED | logs | self-inflicted, corrected |
