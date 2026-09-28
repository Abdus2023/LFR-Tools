# RFL-AE `skills/` — Independent Review, Part III

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Part I:** [`rfl-ae-skills-review.md`](rfl-ae-skills-review.md) — sections 1–32
**Part II:** [`rfl-ae-skills-review-part2.md`](rfl-ae-skills-review-part2.md) — sections 8–19
**Review date:** 2026-09-28

> **Method change in this pass.** Parts I and II were conducted by reading remote sources. Part III was conducted by **cloning the branch and executing the pipeline**, because §20 alleges that the committed evidence is false. That allegation is only decidable by execution, and the doctrine quoted in §20 — `CLAIM → EXECUTION → RECEIPT → EVIDENCE → VERIFIED` — requires it.

> **Numbering note.** These sections are numbered 20–33 as given in this pass. That range collides with Part I's numbering (Part I §21 = slug implementations, §26 = `run_all.sh`, §28 = architectural observation, §32 = overall architecture). Part III finding numbers are **not** references to Part I sections.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection, with the code path identified |
| **DISPROVED** | Claimed in this pass, but contradicted by execution against the actual tree |
| LATENT | Real fragility, but currently precluded by corpus conventions or absent dependencies |
| OPEN | Contract ambiguity — behavior is not wrong, but the interface is underspecified |
| MINOR | Real but low-consequence |

---

## 0. Independent execution receipt

This is the receipt the pass relies on. It was produced by:

```bash
git clone --depth 1 -b arena/01a0e252-rfl-ae https://github.com/Abdus2023/RFL-AE.git
python3 -m venv /tmp/rflvenv && /tmp/rflvenv/bin/pip install markdown
bash skills/run_all.sh /tmp/rflvenv/bin/python
```

**Tool identity**

| Item | Value |
|---|---|
| Branch tip | `1090511a4080987168d1b17d48de88137ec1c27a` |
| Python | 3.11.2 |
| `markdown` | 3.11 |
| Documents | 33 |
| **`run_all.sh` exit code** | **0 — `RESULT: ALL STAGES PASSED`** |

**Commit-message claims vs measured values**

The tip commit message asserts specific numbers. Every one of them reproduced:

| Tip commit `1090511` claims | Measured in this pass | Verdict |
|---|---|---|
| `29/29 provenance, 0 mapping errors at offset 969` | `provenance : 29/29 comments, mapping errors=0 (offset 969)` | ✅ exact |
| `178` fences | `fences : 178 balanced langs={'text': 79, 'rust': 10}` | ✅ exact |
| `430/430 probes` | `probes : 430 phrases, missing=0` | ✅ exact |
| `linkaudit -> 50 files, 851 local links, 0 broken` | `files scanned : 50 / local links : 851 / broken : 0` | ✅ exact |
| `run_all.sh -> all stages pass, exit 0` | `RESULT: ALL STAGES PASSED`, exit 0 | ✅ exact |
| `4/4 negative tests fail as required` | `4/4 PASS`, `RESULT` clean | ✅ exact |

**Corpus totals, as reported by the corpus audit itself (stage 3):**

```text
documents     : 33
sections      : 997
range         : 1 .. 998
duplicates    : none
gaps          : [108]
```

**All seven stages:**

```text
1/7 geometry self-test          12/12 self-tests passed
2/7 skills are well-formed      6/6 skills ok
3/7 corpus audit (33 documents) ALL FILES OK  (997 sections, range 1..998, gaps [108])
4/7 internal links (rendered)   50 files, 851 links, 0 broken, 2 excused
5/7 last document full audit    RFL-LEDGER-V01.md OK
6/7 close-out ritual            CLOSE-OUT OK
7/7 negative tests (must fail)  4/4 PASS
```

**The claimed clean result is reproducible.** This does not settle §20 in the reviewer's favour — it refutes it. Detail in §20 below.

---

## 20. Correction: the claimed clean run reproduces exactly

**This finding does not hold. It is refuted by execution.**

The finding asserts:

> with the current files: derived max = 998, README max = 997 … which must produce `PROBLEMS: README says §1–§997, corpus max is §998` and exit 1.

Executed against the actual tree, stage 6 produces:

```text
====================================================================
CLOSE-OUT  RFL-TRANSITION-V01.md -> RFL-LEDGER-V01.md
====================================================================
prev sections : 23 (last 969)
new sections  : 29 (970..998)
documents     : 33  corpus max section: 998
forward link  : yes
README bullet : yes
README status : Thirty-three specification documents covering §1–§998
--------------------------------------------------------------------
CLOSE-OUT OK

EXIT CODE: 0
```

**CLOSE-OUT OK.** There is no `PROBLEMS` block and no exit 1.

### Two independent errors in the inference chain

**Error 1 — the wrong file.** `verify_closeout.py` reads the **root `README.md`**, not `skills/README.md`:

```python
ap.add_argument("--readme", default="README.md")
```

`run_all.sh:59-60` passes no `--readme` override:

```bash
$PY skills/spec-turn-closeout/scripts/verify_closeout.py \
    --new RFL-LEDGER-V01.md --prev RFL-TRANSITION-V01.md
```

So `skills/README.md` is **never read by the closeout checker at all**. It is not an input to that stage under any invocation present in the repository.

**Error 2 — the wrong number, in both files.** The checker extracts the status line with:

```python
m = re.search(r"([\w-]+) specification documents covering \u00a71\u2013\u00a7(\d+)", readme)
```

Root `README.md:459` contains:

> Thirty-three specification documents covering §1–§998 (§108 does not exist in the source; the gap is preserved)

yielding group(1) = `Thirty-three` and group(2) = `998`. The derived values are `documents = 33` and `max_sec = 998`. Both match.

And `skills/README.md`'s wording would not match that regex anyway — it reads *"thirty-three documents, 997 sections, 2122 text-fenced diagram blocks…"*, with no `specification documents covering §1–§` span. Pointed at that file, the checker would emit `README Status line not found or not in the expected form`, not the message this finding predicts. **The predicted error string is unreachable from the committed source.**

### The decisive evidence: the author's own tooling distinguishes count from max

Stage 3 of the very same run prints, in one block, three lines apart:

```text
sections      : 997
range         : 1 .. 998
gaps          : [108]
```

**`sections: 997` and `range: 1 .. 998` are printed simultaneously because both are true.** 997 is a **cardinality**; 998 is a **maximum**; the difference of 1 is the deliberate §108 gap, which the same block names explicitly. The toolchain models this correctly and has done all along.

`skills/README.md` saying "997 sections" is therefore not stale — it is the same cardinality, computed the same way, in the same repository that prints it.

**Classification: DISPROVED.**

### Why this matters beyond the finding itself

This is the **third consecutive pass** in which a single ambiguous number produced a false defect:

```text
Part II §8   "skills/README is stale (997 vs 998)"        → DISPROVED
Part III §20 "run_all.sh cannot produce the clean result" → DISPROVED
             … escalating to "the evidence was not actually generated"
```

The escalation is the serious part: an ambiguity in a README's phrasing was carried, twice refined but never re-grounded, into an allegation that the repository's committed verification evidence is fabricated. That is a strong claim to reach from a prose number, and it came within one step of being published as `PROVED — current artifact contradicts the claimed clean run_all.sh result`.

The remedy is the one already recommended in Part II §8r, now with a much better justification than "it is untidy":

> A number in verification documentation that a careful reader can misread three times is not a cosmetic problem. It is a defect in the evidence chain.

**The cheapest fix, and the one that closes this loop:** put the count and the max on the same line, adjacent and explicitly labelled, in every document that states either:

```text
corpus: §1–§998 (33 documents, 997 sections; §108 absent)
```

Then reading one number as the other requires ignoring the label immediately beside it.

**Classification of §20's residual concern: DISPROVED as stated; the underlying ambiguity remains MINOR and is now demonstrated to have real cost.**

### A residual observation on the commit message

The commit-message claims verified *exactly* in this pass, including the probe count. That is worth stating plainly, because it inverts the finding's premise: `430/430 probes` in the tip message is precisely `len(load_probes("probes/rfl-ledger-v01.txt")) == 430`.

(An earlier commit, `90660542`, claims `405/405` — and `rfl-transition-v01.txt` measures exactly **405**. Different document, different count, both accurate. There is no staleness here either.)

**The tip commit's evidence claims are reproducible under a matching environment.** Under `CLAIM → EXECUTION → RECEIPT → EVIDENCE → VERIFIED`, this commit reaches `VERIFIED` — independently, by a party that did not author it.

---

## 21. The corpus sweep is weaker than the per-file audit

**Classification: PROVED — coverage asymmetry. This is the strongest finding in this pass.**

Confirmed by direct source inspection and by execution output.

`audit_file.py` invokes:

```text
check_contiguity  check_fences        check_rust_balance
check_render      check_box_widths    check_orphans
check_offcentre   check_ascii_substitution
check_provenance  check_links         check_probes
```

`audit_corpus.py` invokes exactly five:

```python
secs   = [n for n, _t, _i in A.sections(text)]
fences = A.check_fences(text)
rd     = A.check_render(text)
links  = A.check_links(text, a.dir)
prov   = A.check_provenance(text, f)
```

It never calls:

```text
check_rust_balance()      check_box_widths()
check_orphans()           check_offcentre()
check_ascii_substitution()  check_probes()
```

The stage-3 output confirms the invariant set is literally five columns wide:

```text
FILE                   SECS        RANGE  FENCE  UNCL  CMT  LEAK  LINK         PROV
```

No Rust column. No geometry column. No probe column.

So:

```text
audit_file(document)      → 11 invariants
audit_corpus(all docs)    →  5 invariants
```

and `run_all.sh` applies the full 11 to **one** document — the newest.

This creates a false impression:

```text
stated meaning:  "whole corpus audited"
actual meaning:  "whole corpus audited for a subset of invariants"
```

A regression introduced into `ARCHITECTURE.md`, `SPECIFICATION.md`, `KSIR.md`, or `RFL-TRANSITION-V01.md` that affects **only** Rust delimiter structure, diagram geometry, orphan connectors, off-centre arrows, ASCII substitution, or probes **remains invisible to the whole-corpus sweep**.

The newest document getting a full audit does not retroactively protect the older ones. Each past document was audited in full exactly once — on the turn it was created — and never again.

This is arguably more important than any individual parser limitation in Parts I and II, because it is a *systematic* gap rather than a local one.

---

## 22. The correct model is a coverage matrix

**Classification: recommendation.**

The audit should explicitly expose:

| Invariant | Per-file | Whole corpus | Current release gate |
|---|---|---|---|
| Section continuity | Yes | Yes | Yes |
| Fence balance | Yes | Yes | Yes |
| Render availability | Yes | Yes | Yes |
| Rust lexical balance | Yes | **No** | Newest only |
| Box width | Yes | **No** | Newest only |
| Orphan connectors | Yes | **No** | Newest only |
| Off-centre arrows | Yes | **No** | Newest only |
| ASCII substitution | Yes | **No** | Newest only |
| Provenance | Yes | Yes | Yes |
| Raw links | Yes | Yes | Yes |
| Probe corpus | Yes | **No** | Newest only |
| Rendered links | separate | separate | Yes |

This should itself become machine-readable:

```text
AuditCoverage {
    invariant_id
    scope
    implementation
    executed
    strict
    evidence
}
```

Then:

```text
NO COVERAGE
    ≠
PASS
```

---

## 23. This exposes a more general principle: "scope" is missing from verdicts

**Classification: recommendation.**

RFL-AE already has sophisticated notions of authority, scope, epoch, evidence, freshness, and dependency. But the skills themselves don't consistently model verification **scope**.

A result such as:

```text
ALL FILES OK
```

really means:

```text
all files passed the invariants implemented by audit_corpus.py
```

Those are materially different statements. `ALL FILES OK` is what stage 3 actually printed.

The stronger result should be something like:

```text
AUDIT RESULT

scope:
    corpus/*.md

invariants:
    C001 section continuity        PASS
    C002 fence balance             PASS
    C003 render classification     PASS
    C004 provenance                PASS
    C005 source links              PASS
    C006 Rust lexical balance      PASS
    C007 diagram geometry          PASS
    ...

coverage:
    100%

execution:
    33 documents
    1,024 checks
    0 skipped
```

Now the verdict has an explicit domain.

---

## 24. Probe coverage has the same problem

**Classification: PROVED — corpus-level probe coverage is structurally absent.**

The probes are one of the more interesting parts of this system. They protect against a particularly dangerous failure:

```text
source
  ↓
transcription
  ↓
generated specification
```

where structural validity survives but semantic content changes. That is a very good mechanism.

But `check_probes()` only operates when a probe file is explicitly supplied:

```python
if a.probes:
    pb = A.check_probes(text, A.load_probes(a.probes))
    ...
else:
    skipped.append("probes (--probes not given)")
```

And `audit_corpus.py` has **no `--probes` flag at all**. Its entire CLI surface is:

```python
ap.add_argument("--dir", default=".")
ap.add_argument("--expect-gaps", default="")
ap.add_argument("--strict", action="store_true")
```

So this is not merely "the corpus sweep doesn't use probes" — the corpus sweep **cannot** use probes without modification.

`run_all.sh` supplies probes at exactly one place:

```bash
$PY skills/markdown-corpus-audit/scripts/audit_file.py RFL-LEDGER-V01.md \
    ... --probes skills/markdown-corpus-audit/probes/rfl-ledger-v01.txt
```

Meanwhile **20 probe files exist** in `probes/`, covering historical documents. So the corpus-level result cannot establish:

```text
source transcription fidelity
```

for the 19 documents whose probes are committed but unused by the gate.

**Classification: PROVED — corpus-level probe coverage absent.**

This is particularly important because the README itself says:

> read the checker's coverage before trusting its verdict.

The implementation should follow that rule mechanically.

---

## 25. Probe files themselves need provenance

**Classification: OPEN — trust-boundary gap. Recommendation is sound.**

The probe contract says:

> Probes must be copied from the SOURCE, not from the generated file.

Good. But what **proves** that the probe file actually came from the source?

The docstring in `auditlib.load_probes` is explicit that this is human-enforced:

> Probes must be copied from the SOURCE, not from the generated file -- a probe written from memory that disagrees with the source produces a phantom failure. Grep the source before treating a miss as a defect.

Currently:

```text
SOURCE
   ↓
human creates probe
   ↓
probe file
   ↓
check generated document
```

The probe file is therefore **itself an assertion**. It could contain a phrase that already matches the generated output even where the original source differed — and `check_probes()` would report `missing=0`.

`check_probes` verifies:

```text
phrase ∈ generated_text
```

It does not verify:

```text
phrase ∈ source_text
```

The architecture needs a stronger form:

```text
Source Artifact
      │
      ├── source digest
      └── extracted probe set
                 │
                 ▼
          ProbeManifest
                 │
                 ├── source digest
                 ├── source identity
                 ├── probe digest
                 └── extraction method
```

Then the provenance chain becomes:

```text
SOURCE
  ↓
EXTRACT
  ↓
PROBE MANIFEST
  ↓
GENERATED DOCUMENT
  ↓
PROBE VERIFICATION
```

This turns probes from manually trusted fixtures into evidence-bearing artifacts. Note that the recipe for the manifest already exists in the repository: the commit messages repeatedly state "diagrams/`X.py` regenerates all N byte for byte", which is exactly a digest-anchored reproducibility claim — applied to diagrams but not to probes.

---

## 26. `skill-creator` validates syntax, not executable behavior

**Classification: PROVED — validator is structural, not behavioral. The docstring overclaims against its own bullet list.**

The module docstring's first line reads:

```python
"""validate_skill.py — check a skill is well-formed and its scripts run.
```

Six lines below, the same docstring enumerates what it actually does:

```text
  * every scripts/*.py byte-compiles
```

The implementation:

```python
import py_compile
...
py_compile.compile(fp, cfile=os.path.join(td, "x.pyc"), ...)
```

That proves:

```text
Python parses
```

It does not prove:

```text
script executes correctly
```

The first docstring line and the bullet list disagree. The fix to the wording is one line: `its scripts compile`.

Current coverage:

```text
validate_skill.py
        ├── frontmatter
        ├── naming
        ├── description
        ├── Python compilation
        ├── referenced paths
        └── Verification section
```

Missing:

```text
        ├── positive execution
        ├── negative execution
        ├── exit-code contract
        ├── deterministic output
        ├── dependency behavior
        └── fixture isolation
```

Then introduce a separate `skill-test.py`, or `skill-creator/tests/`.

---

## 27. Shell scripts are completely outside `skill-creator` validation

**Classification: PROVED — coverage gap.**

```python
for f in sorted(os.listdir(scripts)):
    if not f.endswith(".py"):
        continue
```

Only `scripts/*.py` is scanned. `*.sh` is not validated.

The command-reference check has the same shape:

```python
for m in re.finditer(r"python3 (skills/[^\s`\"]+\.py)", text):
```

It can only ever match `.py` targets.

Therefore shell syntax, referenced paths, executable permission, and shell behavior are not covered by the meta-skill. This is more significant than it looks, because `run_all.sh` — the top-level orchestrator whose exit code *is* the release gate — lives at `skills/run_all.sh`, outside any skill's `scripts/` directory. It is not merely unvalidated; it is outside the validator's scan domain even in principle.

At minimum:

```bash
bash -n run_all.sh
```

should be part of validation. For stronger verification, `shellcheck` could be optional, while `bash -n` remains mandatory.

Worth noting for calibration: `run_all.sh` did execute correctly under `bash` and `dash`-free semantics in this pass, and its 7-stage sequencing, negative-test inversion, and exit-code propagation all behaved as documented. The gap is that this is currently *unverified by the toolchain*, not that it is broken.

---

## 28. `run_all.sh` violates its Python-interpreter abstraction

**Classification: PROVED — partial interpreter override, currently LATENT in effect.**

Confirmed at line 12 and line 37 of `skills/run_all.sh`:

```bash
PY="${1:-python3}"
...
python3 skills/ascii-diagram-forge/scripts/examples.py > /tmp/_geo.log 2>&1
```

Every other stage uses `$PY`. So:

```bash
./skills/run_all.sh .venv/bin/python
```

does not make the whole pipeline use that interpreter.

**Live demonstration.** This pass invoked `bash skills/run_all.sh /tmp/rflvenv/bin/python`, where the venv is the *only* interpreter with `markdown` installed. Stage 1 ran under the system `python3` regardless. It passed — because `geo.py` and `examples.py` import only `__future__`, `typing`, `os`, `sys`, and `geo`:

```python
# geo.py
from __future__ import annotations
from typing import Iterable, List, Sequence, Tuple

# examples.py
import os
import sys
from geo import (align, bar, box, ...)
```

No third-party dependencies. So the abstraction leak is **real but currently harmless** — the wrong interpreter happens to be an adequate one.

It becomes load-bearing the moment `examples.py` imports anything external, or the system `python3` diverges in version from the selected interpreter. The simple fix:

```bash
"$PY" skills/ascii-diagram-forge/scripts/examples.py
```

**Classification: PROVED minor defect; LATENT consequence.**

---

## 29. The "nothing exits 0 by default" statement is too broad

**Classification: OPEN — imprecise meta-documentation. The proposed replacement is better.**

`skills/README.md:52-53` reads:

> Every script here either verifies something or says it could not. Nothing in this directory exits 0 by default.

Read literally, the second sentence is false: scripts exit 0 on success, and this pass observed exit 0 from six of seven stages. The preceding sentence frames the intent, so the meaning is recoverable in context.

What the statement means is:

```text
no check silently returns success when it cannot perform
its required verification
```

That is much more precise. And imprecise meta-documentation is itself a verification hazard — it is a claim about the toolchain's behaviour that a reader cannot test.

The proposed rewrite is a genuinely better artifact:

> A check never returns PASS when a required verification step was not actually executed.

This is testable, and the toolchain already demonstrates it. Stage 5 in a bare environment:

```text
render        : SKIPPED -- 'markdown' package not installed
SKIPPED (1): render (markdown package not installed)
FAIL (--strict): checks were skipped, so this run proved nothing
>>> stage FAILED (exit 2)
```

That is the invariant in action, stated in one line the reader can check. Adopt the wording.

---

## 30. A major opportunity: turn the skills into a formal "Evidence Coverage" system

**Classification: recommendation.**

The pieces are already present. The missing object is essentially:

```rust
struct CheckResult {
    check_id: CheckId,
    subject: SubjectId,
    scope: Scope,
    status: CheckStatus,
    evidence: EvidenceRef,
    tool: ToolIdentity,
    tool_version: ToolVersion,
    inputs: Vec<Digest>,
    output: Digest,
}

enum CheckStatus {
    PASS,
    FAIL,
    SKIPPED,
    ERROR,
}
```

with a release rule:

```text
RELEASE-PASS
    iff
    every required check
    for every required subject
    has executable evidence
    and status = PASS
```

Then the skills stop merely printing text and start producing proof-carrying audit results.

Part III's §0 receipt is a hand-built instance of exactly this record. Producing it required manual correlation between a commit message and eight printed lines. `CheckResult` would make that correlation mechanical.

---

## 31. The emerging architecture

**Classification: recommendation.**

The current system:

```text
skills
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
 transformation   audit       closeout
       │            │            │
       └────────────┼────────────┘
                    ▼
                 strings
```

should evolve toward:

```text
SOURCE
                         │
                         ▼
                ┌─────────────────┐
                │ Markdown Corpus │
                │ Lexer / IR      │
                └────────┬────────┘
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
        Transform      Analyze     Extract
             │           │           │
             └───────────┼───────────┘
                         ▼
                  VerificationPlan
                         │
                         ▼
                    Check Engine
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
          PASS          FAIL       SKIPPED
             │           │           │
             └───────────┼───────────┘
                         ▼
                    EvidenceRecord
                         │
                         ▼
                   Coverage Matrix
                         │
                         ▼
                     Gate Engine
                         │
                         ▼
                     Closeout
```

This fits RFL-AE's larger architecture extremely well.

---

## 32. Revised priority order

**Classification: recommendation.** Amended by Part III's execution results.

### P0 — repair current false evidence

The original P0 was premised on §20. **§20 is withdrawn**, and the P0 as written does not apply: `skills/README.md` needs no correction from §997 to §998, and `run_all.sh` already exits 0.

Amended P0:

1. ~~Fix `skills/README.md` from §997 → §998~~ — **not a defect; nothing to fix**
2. Re-run the complete `run_all.sh` — **done in this pass; exit 0, all stages pass**
3. Preserve the resulting execution evidence — **§0 above is that receipt; retain it**
4. Do not treat the existing commit-message claim as current verification — **amended: the claim was independently reproduced in full, so it now *is* verified for this tree**

Replacement P0, addressing what the ambiguity actually cost:

1. **Disambiguate every count/max pair in prose.** `§1–§998 (997 sections; §108 absent)` wherever either number appears.

### P0 — fix provenance transformation

`renumber.py` must use the captured source section number and become fence-aware (Part I §5–§6, Part II §11–§12).

### P1 — unify Markdown interpretation

Create `markdown-corpus-core` with a shared lexical representation.

### P1 — unify anchors

One canonical anchor implementation + collision tests + golden vectors.

### P1 — make corpus audit complete

Either:

```text
audit_corpus → invoke every applicable per-file invariant
```

or explicitly define separate scopes. **This pass strengthens the case for the former**: §21 is a PROVED coverage asymmetry, and it is the highest-value fix in the list because it closes a gap affecting 32 of 33 documents.

### P1 — add coverage manifests

Every release audit should answer: WHAT was checked? WHERE? HOW? WITH WHAT tool/version? ON WHICH input? WITH WHAT result? WHERE is the evidence?

### P2 — behavioral skill validation

`skill-creator` should execute positive and negative fixtures (§26), and validate shell scripts (§27).

### P2 — strengthen lexical analysis

Rust delimiter stack and Markdown fence lexer.

### P2 — geometry hardening

Reject duplicate coordinates, negative coordinates, empty required structures, invalid joins (Part I §3, Part II §14–§15).

---

## 33. Most important conclusion so far

**Classification: assessment — endorsed, with one correction.**

The skills directory has crossed an important boundary. It is no longer merely:

```text
tools used to produce RFL-AE
```

It is becoming:

```text
a verification system that verifies the production of RFL-AE
```

That creates a new trust requirement:

```text
RFL-AE
  ↓
uses skills
  ↓
skills verify RFL-AE
  ↓
who verifies the skills?
```

The current answer is partly `negative tests` — but not yet sufficiently independent evidence, behavioral tests, coverage declaration, shared semantic substrate, and reproducible execution.

So the next architectural layer should not be another AI-agent skill. It should be the **Skills Verification Kernel**:

```text
SKILLS-VERIFICATION
│
├── SkillManifest
├── CheckManifest
├── CoverageManifest
├── FixtureManifest
├── ExecutionRecord
├── EvidenceRecord
├── DependencyRecord
├── ToolIdentity
└── VerificationGate
```

with the fundamental invariant:

```text
NO CHECK DEFINITION        ↓   NO CLAIMED COVERAGE
NO EXECUTION EVIDENCE      ↓   NO VERIFIED PASS
PARTIAL COVERAGE           ↓   NO FULL-CORPUS VERDICT
STALE INPUT DIGEST         ↓   EVIDENCE INVALID
CHECKER SELF-ASSERTION     ↓   NOT INDEPENDENT PROOF
```

**The correction.** This pass is itself an instance of the fifth line. The claim in §20 was a *checker self-assertion* — an inference from reading source, presented as `PROVED`. Executing the pipeline refuted it. That is precisely why the kernel needs `ExecutionRecord` as a first-class object rather than as a practice that reviewers are trusted to follow: reasoning about a checker is not the same as running it, and the difference is where this pass went wrong.

The fifth invariant should therefore be stated symmetrically. It applies to auditors as much as to checkers:

```text
READING THE CHECKER     ↓   NOT A VERIFIED CLAIM
```

---

## Bottom line

Part III's architectural argument is correct and, via §21, **better evidenced than Parts I or II**. The coverage asymmetry is the most consequential defect found across all three passes: 32 of 33 documents are verified for 5 invariants, and for 11 invariants only on the turn they were created.

The single most consequential correction: **§20 is refuted by execution.** The claimed clean `run_all.sh` result reproduces exactly — all 7 stages, exit 0, with every specific number in the tip commit message (`29/29` provenance, `178` fences, `430/430` probes, `50 files / 851 links / 0 broken`, `4/4` negative tests) matching measurement. The `§997 → §998` defect does not exist, and the predicted error string is unreachable from the committed source.

The confirmed new findings are:

1. **§21** — corpus sweep covers 5 of 11 invariants; **PROVED**, highest value
2. **§24** — no `--probes` flag on `audit_corpus.py` at all; probe coverage structurally impossible at corpus level
3. **§26** — `validate_skill.py` docstring says "scripts run" while the implementation byte-compiles
4. **§27** — `*.sh` outside validation entirely, including the release-gate orchestrator
5. **§28** — `$PY` override incomplete; **LATENT** because geometry has no third-party deps
6. **§29** — "nothing exits 0 by default" is literally false; the proposed replacement is testable
7. **§25** — probes are unprovenanced assertions; the manifest proposal is sound
8. **§22–23, §30–33** — recommendations, all endorsed

**New minor findings discovered in this pass** (not in the reviewed text):

- `verify_closeout.py:77,80` — `total_secs` is initialised and incremented but **never read**. The README count is derived from `len(all_md)`, not from section totals. Dead code that misleads a reader into thinking total-section agreement is being checked.
- Stage 1's geometry self-test covers 12 cases including `row() raises on overlap`, `row() raises on negative start`, and `hjoin() raises on overlap` — but has **no case for `marks()` or `bar()` duplicate coordinates**. This is direct confirmation of Part II §14: the asymmetry in the geometry guarantees is visible in the self-test's own coverage.
- `linkaudit.py` accepts `--allow` and explicitly excuses documented examples (`[excused: --allow]`, 2 links in `markdown-corpus-audit/SKILL.md`). Good design, and worth noting because the README's "0 broken" figures are only interpretable alongside the excuse list — another instance of §23's scope problem.

---

## Appendix — Part III finding index

| Part III § | Finding | Class | Verified by | Result |
|---|---|---|---|---|
| 0 | Independent execution receipt | evidence | execution | — |
| 20 | Clean `run_all.sh` claim is false | **DISPROVED** | execution | exit 0, all stages pass, all commit numbers exact |
| 20r | README count/max ambiguity has real cost (3 false findings) | MINOR | — | confirmed as the failure's root cause |
| 21 | Corpus sweep covers 5 of 11 invariants | **PROVED** | source + output | strongest finding of the pass |
| 22 | Coverage matrix | recommendation | — | — |
| 23 | Scope missing from verdicts | recommendation | — | `ALL FILES OK` observed verbatim |
| 24 | No corpus-level probe coverage | **PROVED** | source | no `--probes` flag exists |
| 25 | Probes are unprovenanced assertions | OPEN | source | recommendation endorsed |
| 26 | Validator docstring overclaims | **PROVED** | source | "scripts run" vs `py_compile` |
| 27 | `*.sh` outside validation | **PROVED** | source | `.py`-only scan |
| 28 | `$PY` override incomplete | PROVED / LATENT | execution | line 37 `python3`; no third-party deps in geometry |
| 29 | "Nothing exits 0 by default" imprecise | OPEN | — | proposed rewrite endorsed |
| 30 | Evidence Coverage system | recommendation | — | — |
| 31 | Emerging architecture | recommendation | — | — |
| 32 | Revised priority order | recommendation | — | P0 amended by §20 withdrawal |
| 33 | Skills Verification Kernel | assessment | — | endorsed; fifth invariant made symmetric |
| — | `total_secs` dead variable | MINOR | source | new in this pass |
| — | Geometry self-test omits `marks`/`bar` cases | MINOR | output | new in this pass; confirms Part II §14 |
| — | `--allow` excuse mechanism | positive | output | new in this pass |
