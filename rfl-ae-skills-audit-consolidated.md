# RFL-AE `skills/` — Consolidated Audit

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at audit:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Audit date:** 2026-09-28
**Method:** remote source inspection, plus independent execution of the pipeline on a local clone (Python 3.11.2, Python-Markdown 3.11).

> **What this document is.** A single consolidated record of a seven-pass audit. Findings are organised by **component and severity**, not by the order they were discovered. Duplicates are merged; repeated observations appear once, with all their evidence attached. The seven per-pass files remain in this repository as the chronological record — this document is the de-duplicated result.

> **What this document is not.** It is not a claim that the audited software is unsound. The `skills/` directory is substantially real engineering with unusually good failure-driven invariants (§5). It is a statement that the suite has not yet become a self-verifying verification system (§4), together with the specific defects that prevent it.

---

## Contents

- [1. Verification status](#1-verification-status)
- [2. The four P0 defects](#2-the-four-p0-defects)
- [3. Remaining defects by component](#3-remaining-defects-by-component)
- [4. Architectural conclusions](#4-architectural-conclusions)
- [5. What is verified correct](#5-what-is-verified-correct)
- [6. Ranked priority list](#6-ranked-priority-list)
- [Appendix A — Unified findings index](#appendix-a--unified-findings-index)
- [Appendix B — Closed and withdrawn findings](#appendix-b--closed-and-withdrawn-findings)
- [Appendix C — Execution receipts](#appendix-c--execution-receipts)

---

## 1. Verification status

### 1.1 What was independently executed

The pipeline was cloned and run. This is the receipt the audit rests on:

```bash
git clone --depth 1 -b arena/01a0e252-rfl-ae https://github.com/Abdus2023/RFL-AE.git
python3 -m venv /tmp/rflvenv && /tmp/rflvenv/bin/pip install markdown
bash skills/run_all.sh /tmp/rflvenv/bin/python
```

| Item | Value |
|---|---|
| Branch tip | `1090511a4080987168d1b17d48de88137ec1c27a` |
| Python / Python-Markdown | 3.11.2 / 3.11 |
| Documents | 33 |
| **`run_all.sh` exit code** | **0 — `RESULT: ALL STAGES PASSED`** |

All seven stages passed, and every specific number asserted in the tip commit message reproduced exactly:

| Tip commit `1090511` claims | Measured | Verdict |
|---|---|---|
| `29/29 provenance, 0 mapping errors at offset 969` | `provenance : 29/29 comments, mapping errors=0 (offset 969)` | ✅ |
| `178` fences | `fences : 178 balanced langs={'text': 79, 'rust': 10}` | ✅ |
| `430/430 probes` | `probes : 430 phrases, missing=0` | ✅ |
| `linkaudit -> 50 files, 851 local links, 0 broken` | `files scanned : 50 / local links : 851 / broken : 0` | ✅ |
| `all stages pass, exit 0` | exit 0 | ✅ |
| `4/4 negative tests fail as required` | 4/4 PASS | ✅ |

**The claimed clean result is reproducible.** Under `CLAIM → EXECUTION → RECEIPT → EVIDENCE → VERIFIED`, the tip commit reaches `VERIFIED` — independently, by a party that did not author it.

### 1.2 Corpus state, as the corpus audit itself reports it

```text
documents     : 33
sections      : 997
range         : 1 .. 998
duplicates    : none
gaps          : [108]
```

`997` is a **cardinality**; `998` is a **maximum**; the difference is the deliberate `§108` gap, which `run_all.sh:47` enforces via `--expect-gaps 108`. Both numbers are correct and mutually consistent — see [Appendix B](#appendix-b--closed-and-withdrawn-findings) for why this is stated at the top.

### 1.3 Evidence planes

| Plane | Status | Basis |
|---|---|---|
| **A — Source** | strongest | repo cloned at a pinned SHA; every asserted number reproduced |
| **B — Execution** | partial | a real receipt exists, but it is hand-assembled from logs; no structured record |
| **C — Publication** | **absent** | no `git` invocation in the closeout verifier; no CI exists at all |

### 1.4 Classification scheme

| Class | Meaning |
|---|---|
| **PROVED** | Established by execution or by source inspection with the code path identified |
| **PROVED — stronger** | The concern is valid; the actual defect is materially worse than the stated form |
| **LATENT** | Real and demonstrated, but the current corpus does not exhibit it |
| **MINOR** | Real, low-consequence |
| **OPEN** | Contract ambiguity — behaviour is not wrong, the interface is underspecified |
| **CLOSED** | Investigated and withdrawn; recorded so it is not re-raised |

---

## 2. The four P0 defects

These four are the only findings with severe consequences, and each is established by a reproducible receipt rather than by inference.

### 2.1 `renumber.py` fabricates source provenance — and the auditor certifies it

**Severity: critical. Status: PROVED, currently live.**

`renumber.py`'s `apply()` captures the source heading number and discards it:

```python
m = re.match(r"^## (?:§)?(\d+)\. (.*)$", line)
if m:
    n += 1
    corpus = n + offset
```

`m.group(1)` is **never used**. The transformation is therefore:

```text
documented:  corpus = source_section + offset
implemented: corpus = ordinal_of_heading + offset
```

Those are different functions. They agree only when the source is contiguous.

**Receipt.** Source with a deliberate gap — the shape RFL-AE's own corpus contains (`§108`):

```markdown
## 1. First
## 3. Third
## 7. Seventh
```

Run `apply --offset 100`:

```text
renumbered 3 sections: source 1..3 -> corpus 101..103
```

```markdown
## 101. First
<!-- source: SRC.md §1 -->
## 102. Third
<!-- source: SRC.md §2 -->
## 103. Seventh
<!-- source: SRC.md §3 -->
```

**The provenance comments are false.** The source has no §2 and no §3. The tool has not merely lost the gap — it has **fabricated source identities**. Its own success message, `source 1..3`, is also a false statement about its input.

**The compounding failure.** The output is then fed to the auditor:

```text
provenance    : 3/3 comments, mapping errors=0 (offset 100)
```

`mapping errors=0`. The verifier **certifies the fabricated mapping**, because writer and verifier share one ordinal model:

```text
SOURCE §1, §3, §7
        │
        ▼
   renumber.py      ordinal model  →  101←§1, 102←§2, 103←§3
        │
        ▼
   OUT.md           comments assert §2 and §3 exist
        │
        ▼
   auditlib         corpus − source = 100 everywhere
        │
        ▼
   "mapping errors=0"    ✅ CERTIFIED
```

**This is not a bug in one script.** It is the demonstration that the verification loop is internally consistent and externally wrong. Detecting it requires an **external authority** — the actual source document — which no component of the current toolchain possesses.

**Aggravating facts:**

- `renumber.py` is referenced by **no** `.sh` or `.py` file in the repository. Its only references anywhere are documentation lines in `corpus-provenance-numbering/SKILL.md`. It has **zero automated exercise**, and it is the only script that *writes* corpus documents.
- It is the only component in the suite rated *defective* rather than *partial*.

**Minimum fix:**

```python
source_n = int(m.group(1))
corpus = source_n + offset
```

plus rejection of duplicate and decreasing source numbers, and an explicit `GAPPED` state that preserves the gap rather than repairing it. See §4.1 for the round-trip property that would test it.

---

### 2.2 `renumber.py` is fence-blind — and corrupts fenced bodies

**Severity: critical. Status: PROVED, currently live.**

`apply()` scans every line with no lexical state. It cannot distinguish a Markdown heading from a line that resembles one inside a fenced code block.

**Receipt.** Source:

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
2. **A provenance comment was injected inside the code fence**, corrupting the fenced body with tooling metadata.

And the tool reported `renumbered 2 sections` for a document containing **one** section.

This is particularly serious because `renumber.py` is part of the **transformation authority**. The correct abstraction is a lexical scan separating prose, headings, fenced code, and other constructs — then transforming only actual headings.

---

### 2.3 The provenance invariant is non-monotonic

**Severity: critical. Status: PROVED, currently live.**

The corpus sweep gates the entire per-section provenance invariant on a whole-file substring test:

```python
has_prov = "<!-- source: " in text
...
if has_prov and not prov["complete"]:
    errs.append("provenance incomplete")
```

**Receipt.** A controlled five-state test on a real corpus document (`RFL-LEDGER-V01.md`, 29 sections, 30 provenance comments, offset +969), holding the filename constant so `source_name` matching is unaffected, varying only the number of comments retained:

| comments kept | of 30 | PROV cell | verdict |
|---|---|---|---|
| 30 | 30 | +969 | PASS |
| 29 | 29 | +969 | FAIL: provenance incomplete |
| 15 | 15 | +969 | FAIL: provenance incomplete |
| 1 | 1 | ?off | FAIL: provenance incomplete |
| **0** | **0** | **n/a** | **PASS — provenance not checked** |

**Deleting 29 of 30 comments fails the audit. Deleting all 30 passes it.** Removing more evidence produces a better result. The invariant is not an invariant — it is a property that holds only for documents that opt in.

**This is already live.** In the real corpus, **10 of 33 documents** report `PROV = n/a`:

```text
ARCHITECTURE.md   CONTRACTS.md       DESIGN-IR.md       FORMAL-CORE.md
KSIR.md           PROTOCOL.md        RECONSTRUCTION.md  RUST-CORE.md
SPECIFICATION.md  TRANSITIONS.md
```

All ten are permanently outside the provenance invariant, and the sweep reports `ALL FILES OK` over them.

**Symmetric false positive.** The same one-line test is fence-blind and fires on text that merely *mentions* the syntax. A document with **no** provenance, containing only a fenced example:

````markdown
## 1. Real section
## 2. Another section

Referencing the syntax in a fenced example:

```text
<!-- source: DOC.md §1 -->
```
````

is flagged `provenance incomplete`. So the test cuts three ways, all wrong:

```text
substring in real provenance    → enforced        ✅ correct
substring inside a code fence   → enforced        ❌ false positive
substring absent entirely       → not enforced    ❌ false negative (severe)
```

**Why this outranks §2.1 in likelihood.** §2.1 requires `renumber.py` to fabricate data. This requires only that someone **delete** data — a common operation an author might perform for innocent reasons.

**Minimum fix:** delete `has_prov`. A corpus document is a `CORPUS_SPEC` (§4.4) and requires complete provenance unconditionally.

---

### 2.4 The negative-test harness accepts a crash as a pass

**Severity: critical. Status: PROVED, currently latent (no CI exists).**

The harness in `run_all.sh` gates on a single bit:

```bash
neg() {
  desc="$1"; shift
  "$@" > /tmp/_neg.log 2>&1
  rc=$?
  if [ $rc -ne 0 ]; then
    echo "  PASS  $desc  (exit $rc, $(grep -c '^  - ' /tmp/_neg.log) findings)"
  else
    fail=1
  fi
}
```

**Any** non-zero exit passes — including an interpreter crash before the checker's first line runs.

**Receipt.** Run the linkaudit negative test's command in an environment without the `markdown` package:

```text
Traceback (most recent call last):
  File ".../linkaudit.py", line 9, in <module>
    import markdown
ModuleNotFoundError: No module named 'markdown'
exit code: 1
```

What `neg()` reports:

```text
  PASS  link checker catches a broken anchor and a missing target  (exit 1, 0 findings)
```

**The negative test passes while the checker never ran.**

**Why this compounds.** It is two earlier findings combining:

```text
linkaudit.py has no dependency guard (bare `import markdown`)
                    +
neg() accepts any non-zero exit as a pass
                    =
the linkaudit negative test is vacuously satisfied
whenever the dependency is missing
```

In such an environment, a single `run_all.sh` run reports:

```text
stage 4  → FAIL (exit 1)    ← linkaudit crashes; stage correctly fails
stage 7  → PASS             ← SAME crash; negative test passes
```

**The same crash is a failure at stage 4 and a success at stage 7.** No stage compares the two.

**A field with two meanings.** The harness prints `grep -c '^  - '` as a findings count. That pattern matches `audit_file.py`'s output format but not `linkaudit.py`'s, so `0 findings` has two indistinguishable causes:

```text
1. checker ran, found problems, output format didn't match the counter  → 0
2. checker never ran at all (import crash)                              → 0
```

Case 1 is cosmetic. Case 2 is a vacuous pass. Both render as the same string in the same field of the same line — and the field that would distinguish them is the one reporting `0`.

> A measurement that reports zero is indistinguishable from a measurement that did not occur.

**Minimum fix:** require structured findings rather than a non-zero exit. See §4.5.

**Latency note:** this is currently latent because the repository has no CI (see §3.6). Adding CI before fixing it would convert a latent defect into an active one.

---

## 3. Remaining defects by component

### 3.1 `markdown-corpus-audit` — scope and coverage

#### 3.1.1 The corpus sweep covers 5 of 11 invariants — **PROVED**

`audit_file.py` invokes eleven checks:

```text
check_contiguity  check_fences       check_rust_balance
check_render      check_box_widths   check_orphans
check_offcentre   check_ascii_substitution
check_provenance  check_links        check_probes
```

`audit_corpus.py` invokes exactly five (`sections`, `check_fences`, `check_render`, `check_links`, `check_provenance`). It never calls `check_rust_balance`, `check_box_widths`, `check_orphans`, `check_offcentre`, `check_ascii_substitution`, or `check_probes`. The stage-3 header confirms the invariant set is literally five columns wide:

```text
FILE                   SECS        RANGE  FENCE  UNCL  CMT  LEAK  LINK         PROV
```

`run_all.sh` applies the full eleven to **one** document — the newest.

> stated meaning: `whole corpus audited`
> actual meaning: `whole corpus audited for 5 of 11 invariants`

A regression in any of the six unchecked invariants, introduced into any of the 32 older documents, is **invisible to the whole-corpus sweep**. Each past document was audited in full exactly once — on the turn it was created — and never again. This is the most consequential structural finding: it affects 32 of 33 documents.

#### 3.1.2 Whole-document deletion is undetected — **PROVED**

**Receipt.** Delete `RFL-LEDGER-V01.md` (§970–998, 29 sections) and run stage 3:

```text
documents     : 32          ← was 33
sections      : 968         ← was 997
range         : 1 .. 969    ← was 1 .. 998
duplicates    : none
gaps          : [108]       ← equals --expect-gaps 108  ✅
```

The corpus audit **does not flag the deletion.** Removing the highest-numbered document creates no interior gap, so the one assertion that compares against declared intent is satisfied.

**Root cause:** `documents` and `sections` are printed and never asserted:

```python
print(f"documents     : {len(files)}")
print(f"sections      : {len(all_secs)}")
```

Both values appear in every run and participate in no check. They are precisely the numbers a human reader uses to decide the corpus is intact.

**Fairness:** the full pipeline does catch this at stage 6, via the README status line. But it is caught **incidentally** — by a prose-number check on a manually-maintained file rather than by corpus-identity verification — and stage 6 catches it by **crashing** (see §3.5.2), not by diagnosing. Had the deleted document been last in the link chain, stage 3 would have passed cleanly.

#### 3.1.3 No corpus-level probe coverage, structurally — **PROVED**

`check_probes()` runs only when a probe file is supplied. `audit_corpus.py` has **no `--probes` flag at all**:

```python
ap.add_argument("--dir", default=".")
ap.add_argument("--expect-gaps", default="")
ap.add_argument("--strict", action="store_true")
```

**20 probe files exist**; 19 are committed but unused by the release gate. So the corpus-level result cannot establish source-transcription fidelity for any historical document.

#### 3.1.4 Scope universes differ between tools — **PROVED**

`audit_corpus.py` is non-recursive (`os.listdir`, `X/*.md`); `linkaudit.py` walks the tree recursively. Measured:

```text
audit_corpus   33 files   (root *.md except README.md)
linkaudit      50 files   (recursive)
```

A document added under `docs/` would be included by the link checker and **invisible** to the corpus audit. If the intended corpus is root-level specification documents, that must be declared: `CorpusScope = root_markdown_files_except(README.md)`.

#### 3.1.5 `README.md` exclusion is an unstated authority rule — **PROVED**

```python
EXCLUDE = {"README.md"}
```

Sensible, but an authority decision encoded as a set literal with no stated rationale and no constraint on growth. The remedy is §4.4's `DocumentClass`.

#### 3.1.6 Fence counting is substring counting — **PROVED limitation**

`check_fences()` uses `text.count("```")`. It counts literal backticks in prose and misses other valid fence forms. Adequate within the frozen dialect; not a fence parser. This is one of five independent fence-blind parsers (§4.3).

#### 3.1.7 `check_links()` regex is dialect-narrow — **OPEN limitation**

```python
md_links = re.findall(r"\]\(([^)#]+\.md)\)", text, re.M)
```

Handles simple links only. No titles, escaped parentheses, reference links, query strings, or nested constructs. Adequate for the controlled dialect; should be **described as such**. `linkaudit.py`'s rendered approach is the stronger of the two (see §3.2.3).

#### 3.1.8 Rust "balance" is delimiter counting — **PROVED limitation**

```python
body.count("{") == body.count("}")
body.count("(") == body.count(")")
body.count("[") == body.count("]")
```

Equal counts do not establish balanced nesting: `{ [ } ]` passes this check and is invalid. Delimiters inside strings and comments are counted as code. The check proves **cardinality**, not syntax. Rename to `check_rust_delimiter_counts()`, or add a small lexical scanner handling strings, raw strings, char literals, and both comment forms.

#### 3.1.9 Structural diagram checks are deliberately narrow — **OPEN, documented**

`check_box_widths()` recognises top-border-plus-`│`-rows only; it does not establish corner matching, junction validity, or box collision. `check_orphans()` is conservatively adjacency-based, so a dangling endpoint can pass. `check_offcentre()` checks whether `▼` lands on a label character, which is the right semantic choice. `check_ascii_substitution()` correctly permits ASCII-only diagrams and flags only accidental `|` insertion.

These are **partial invariants**, and the README's own warning applies: *read the checker's coverage before trusting its verdict.*

#### 3.1.10 Provenance layout is an unstated dialect — **OPEN**

`check_provenance()` accepts two layouts only: comment on the next line, or after exactly one blank line. The docstring documents this, so it is intentional — but it is not declared **normative**. Across all seven passes, the audit repeatedly had to infer case by case whether a restriction was a frozen dialect or an implementation accident. Each documented restriction should carry a `NORMATIVE` vs `IMPLEMENTATION_LIMIT` marker.

#### 3.1.11 Source identity is a filename plus an integer — **PROVED**

```python
pat = re.compile(r"^<!-- source: " + re.escape(source_name) + r" \u00a7(\d+) -->$")
```

Nothing binds the claim to a revision of that file. §2.1 produced real output asserting `SRC.md §2` for a source with no §2 — the string is well-formed, the filename correct, the number an integer, and the audit certified it. A filename-plus-integer identity cannot express "this claim is false" because there is no artifact to check against.

Minimum identity for evidence-grade provenance:

```text
SourceIdentity { repository, commit, path, section, content_digest }
```

#### 3.1.12 The renderer is an untrusted observation source — **PROVED**

`auditlib.slug`'s docstring asserts:

> GitHub (gfm) anchor slug. **Must match GitHub exactly** …

The implementation is a locally-constructed approximation — carefully reverse-engineered, and its own docstring documents the reasoning (the double-hyphen rule for stripped glyphs) — but not validated against the target. The evidence chain is therefore:

```text
Markdown source → Python-Markdown → custom anchor model → checker result
```

and not:

```text
Markdown source → GitHub → checker result
```

Never emit `github_verified = true` unless actual GitHub behaviour has been established. Record instead: `RendererObservation { renderer, renderer_version, extensions }`.

---

### 3.2 Anchors and links

#### 3.2.1 Three slug implementations, one algorithmically different — **PROVED**

```text
renumber.py   github_slug()
auditlib.py   slug()
linkaudit.py  slug()
```

`renumber.github_slug` and `auditlib.slug` appear equivalent. `linkaudit.slug` is **algorithmically different**: it strips HTML tags first and uses per-character `ch.isalnum()`, where `auditlib` uses `re.sub(r"[^\w\- ]", "", s)`. They agree on today's corpus by coincidence of its character set, not by construction.

Anchor identity is externally observable semantics. There should be **one** implementation with golden vectors:

```text
simple heading · apostrophe · Unicode · punctuation · multiple spaces
em dash · en dash · symbols · HTML inline content · duplicate headings
heading beginning with number · heading containing §
```

**Open problem:** golden vectors must be captured *from GitHub*, and there is no mechanism to do so. Until then the vectors pin current behaviour and prevent drift, but do not establish correctness against the target.

#### 3.2.2 Duplicate anchors are under-modelled — **LATENT**

`auditlib.py:281` collects anchors as a set:

```python
anchors = {slug(h) for h in headings}
```

A set answers *does this slug occur*, not *what anchor does GitHub assign on repetition*. GitHub appends `-1`, `-2`.

**Latency:** duplicate heading titles were searched for across the corpus and **none exist**, because every heading carries a unique `§NNN` prefix. Collision is structurally precluded by the numbering convention. This becomes live the moment any unnumbered or advisory heading is added — which the corpus already does at document boundaries.

#### 3.2.3 Two different link checks with different evidence domains — **OPEN, good architecture**

`check_links()` operates on source syntax; `linkaudit.py` renders Markdown and examines HTML hrefs. That is a genuine strength: rendering avoids interpreting links inside code fences as real links, and `linkaudit.py` correctly resolves `OTHER.md#fragment` relative to the containing document.

The defect is descriptive. The repository tends to call both "link checks", risking overclaim:

```text
SOURCE-LINK-CHECK    source-level reference structure
RENDER-LINK-CHECK    Markdown-rendered href structure
GITHUB-LINK-CHECK    GitHub-specific anchor semantics   ← does not exist
```

---

### 3.3 `corpus-provenance-numbering`

Covered fully in §2.1 and §2.2. Two additional contract issues:

#### 3.3.1 No precondition model for source numbering — **OPEN**

What should happen for `## 1. A / ## 1. B` (duplicate), `## 3. A / ## 1. B` (decreasing), or `## 1. A / ## 4. B` (gapped)? The skill implies source numbering is authoritative, so the transform must not silently repair it:

```text
VALID       unique, strictly increasing   → transform
GAPPED      increasing, non-contiguous    → transform + preserve gap
DUPLICATE   repeated source number        → FAIL
DECREASING  source number decreases       → FAIL
MISSING     no recognised source number   → FAIL
```

The `GAPPED` row is the live case, and it fails in the worst way: not by refusing, but by producing output that looks correct, carries false provenance, and passes the full audit.

#### 3.3.2 No round-trip property — **OPEN**

The intended transformation has a formal inverse:

```text
T :  S ↦ C = S + O
T⁻¹: C ↦ S = C − O
```

`T⁻¹` is not currently implementable in a meaningful sense, because `C` is written to the document while `S` is written only into a comment — and §2.1 showed those can disagree. **Making `T⁻¹` a first-class function is the design move that forces the comment to be the sole carrier of source identity**, and makes any disagreement with a real source document detectable.

---

### 3.4 `placeholder-splice`

This skill has the strongest local design in the suite — its trust boundary is verification against the actual failure mode, not against a proxy.

#### 3.4.1 Fence grammar is narrower than the documentation implies — **OPEN, dialect limitation**

```python
blocks = re.findall(r"^```(\w*)\n(.*?)\n^```", out, re.S | re.M)
```

Does not cover `` ```text {.class} ``, longer fences, or indentation variants. Two defensible resolutions:

```text
Option A — deliberately freeze the dialect (normative, not implicit):
    fences MUST be exactly  ```LANG ... ```
Option B — make the parser Markdown-aware:
    fence length, indentation, info strings, opening/closing matching
```

For a controlled corpus, Option A is sufficient **if it is normative**.

**Positive note:** this skill has already replaced a weaker check with a stricter one — the `--fence-lang` substring test was superseded by exact-fenced-body matching, and the deprecated flag is documented with the defect that motivated the change. That is the right trajectory for the rest of the suite.

#### 3.4.2 The write is not crash-durable — **MINOR**

```python
with open(a.target, "w", encoding="utf-8") as fh:
    fh.write(out)
```

No temporary file, `flush`, `fsync`, or atomic rename. Distinguish:

```text
semantic correctness  ≠  atomic update  ≠  crash durability
```

The skill establishes the first only. Bounded risk today because `splice.py` runs during authoring, not as a release-gate transform. It becomes material if splicing ever runs unattended: `open(..., "w")` truncates before writing, so an interruption leaves a partially written target with no indication.

---

### 3.5 `spec-turn-closeout`

#### 3.5.1 Content closeout is verified; publication closeout is not — **PROVED**

`verify_closeout.py` implements four content checks plus numbering continuity:

```text
previous → new forward link        CHECKED  (substring — see §3.5.3)
README → new document              CHECKED  (substring)
README count/max                   CHECKED  (regex)
new → previous provenance          CHECKED  (substring)
previous.last + 1 == new.first     CHECKED
commit message                     NOT CHECKED
local commit identity              NOT CHECKED
push                               NOT CHECKED
remote branch SHA                  NOT CHECKED
```

**This is stronger than a documentation gap.** The SKILL's trap section **prescribes the exact command** by which the push is to be verified:

> **Assuming a push succeeded.** `git push` can fail on auth or a non-fast-forward … `git ls-remote origin refs/heads/<branch>`.

and its frontmatter claims the skill covers *"commit, push, and verification that all of it actually happened."*

So the repository contains a skill that names the command closing its own gap, a trap recording that the failure has occurred before, and a verifier that cannot invoke `git` at all. This is the cleanest example in the audit of **a contract stated in prose that no executable artifact attempts to satisfy**.

**Minimum fix:** either rename to `spec-content-closeout`, or implement the `git ls-remote` check. See §4.6.

#### 3.5.2 Reports `ERROR` as `FAIL` — **PROVED**

Run against a tree missing its `--new` document:

```text
Traceback (most recent call last):
  File ".../verify_closeout.py", line 58, in main
    new_t = A.read(p(a.new))
FileNotFoundError: [Errno 2] No such file or directory: './RFL-LEDGER-V01.md'
```

Exit 1 — **the same exit code as a content finding**, with no `PROBLEMS` block. A driver cannot distinguish *"the closeout check found problems"* from *"the closeout checker crashed."*

#### 3.5.3 Substring evidence where structural evidence is required — **PROVED**

Three of the five checks are substring tests:

```python
if f"]({a.new})" not in prev_t:        # forward link
if f"]({a.new})" not in readme:        # README entry
if a.prev not in new_t:                # provenance blockquote
```

The third is weakest — a bare filename containment test with no link syntax, no blockquote syntax, and no position requirement. Its diagnostic reads *"provenance blockquote should state what it continues from"*, naming a structure the check cannot observe. All three are also fence-blind: a fenced example containing `](RFL-LEDGER-V01.md)` would satisfy the README check.

```text
"string exists"  ≠  "required semantic structure exists"
```

#### 3.5.4 `total_secs` is dead — **MINOR**

`verify_closeout.py:77,80` initialises and increments `total_secs` and never reads it. The README count is derived from `len(all_md)`. Dead code that implies a total-section agreement check which does not exist.

---

### 3.6 Skills verification and the bootstrap layer

#### 3.6.1 The negative-test rule is declared, unenforced, and unmet — **PROVED**

`skill-creator/SKILL.md` rule 3:

> **Ship a negative test.** A checker that has never been shown a defect is unverified. Every audit skill here is demonstrated against a deliberately broken fixture … When you add a check, add the input that trips it.

The rule also appears in the skill's **frontmatter `description`** — the machine-read discovery surface an agent matches against:

> … and the rule that **every skill must ship a negative test**.

Three measurements:

```text
1. Does validate_skill.py enforce it?
   → 7 assertions total; NONE checks for a negative test.

2. Does any skill ship a fixture in its own directory?
   → find skills/*/ -iname "*neg*" -o -iname "*fixture*" -o -iname "*test*"
     all six skills: NONE

3. Is the rule met in substance?
   → partially — 4 of 6, and not the two that matter.
```

The complete assertion set:

```text
:47  SKILL.md has no YAML frontmatter
:50  frontmatter name != directory
:53  description too short
:55  description never says WHEN to use it
:57  no '## Verification' section          ← existence only, see §3.6.2
:70  scripts/{f} does not compile          ← see §3.6.3
:76  SKILL.md references missing {rel}
```

**In substance**, negative tests exist but are centralized in `run_all.sh` stage 7:

| Skill | Negative test | Where |
|---|---|---|
| `markdown-corpus-audit` | ✅ | `run_all.sh` stage 7 (`BROKEN.md`, 8 planted defects) |
| `placeholder-splice` | ✅ | `run_all.sh` stage 7 |
| `spec-turn-closeout` | ✅ | `run_all.sh` stage 7 |
| `ascii-diagram-forge` | ✅ | 12 self-tests incl. `row() raises on overlap` |
| **`corpus-provenance-numbering`** | ❌ **none anywhere** | — |
| **`skill-creator`** | ❌ **none anywhere** | — |

**The two untested components are the two load-bearing ones:**

| Component | Tests | Role |
|---|---|---|
| `renumber.py` | ❌ none | **writes** corpus documents |
| `validate_skill.py` | ❌ none | **certifies** all skills |

Every other script is exercised by at least one `run_all.sh` stage or by `examples.py`'s self-tests. These two are executed but never verified, and they are precisely the write path and the trust path. This is not coincidence — it is the structural shape of the bootstrap problem: the components closest to the root of the trust chain have the least verification, because verifying them requires the machinery under construction.

#### 3.6.2 `validate_skill.py` validates structure, not behaviour — **PROVED**

The module docstring's first line:

```python
"""validate_skill.py — check a skill is well-formed and its scripts run.
```

Six lines later, its own bullet list:

```text
  * every scripts/*.py byte-compiles
```

The implementation is `py_compile.compile(...)`. `COMPILES` is being reported as `RUNS`. The overclaim also lives in the frontmatter `description` (*"checking that a skill's scripts actually run and fail loudly"*), so it misleads every agent and reader who never opens the file.

**`## Verification` is checked for existence only:**

```python
if not re.search(r"^## Verification", text, re.M):
```

A skill containing `## Verification` followed by *"This skill is verified. Trust me."* validates cleanly, exit 0. **Latent** — all six corpus Verification sections are genuinely substantive. The real cost is that they carry load-bearing prose numbers that nothing verifies, including `Expected: 33 documents, 997 sections, range 1 .. 998` — the numbers whose ambiguity produced four false findings across this audit.

#### 3.6.3 Discovery assumes a flat topology — **PROVED, MINOR**

```python
targets = [os.path.join(target, d) for d in sorted(os.listdir(target))
           if os.path.isdir(os.path.join(target, d))]
```

One level, no recursion, no manifest. Constructing `skills/experimental/skill-X/` (a fully valid skill) and running the validator:

```text
FAIL  experimental
        - experimental: no SKILL.md
…
1 problem(s)
EXIT: 1
```

Three wrong behaviours: `experimental` is treated as a skill; `skill-X` is never discovered; and a legitimate reorganisation breaks stage 2. **MINOR** because it fails loudly and safely — in contrast to §2.3's silent bypass.

#### 3.6.4 Frontmatter parser silently drops keys — **MINOR**

The parser is a key/value splitter, not YAML. Six cases probed through the real function:

| Case | Parsed result | Verdict |
|---|---|---|
| simple | `{'name': 'a', 'description': 'short'}` | ✅ |
| `description: "something: with colon"` | `{'description': '"something: with colon"'}` | ✅ |
| block scalar `>` + indented lines | `{'description': '>'}` | ❌ |
| nested `meta:` + indented key | `{'meta': ''}` — nested key **lost** | ❌ |
| list value | `{'tags': ''}` — items **lost** | ❌ |
| comment line | not captured | ✅ |

`str.partition(":")` splits at the first colon, so embedded colons survive. The real defects are block scalars (yielding the literal `'>'`) and nested structures/lists, which are **silently discarded** by design. Either declare a `YAML subset V1` and **reject** unsupported constructs loudly, or use a real parser. Silent key-dropping inside a validator's parser is the same defect class as §2.3.

#### 3.6.5 The command-reference check is pattern-limited but currently exhaustive — **LATENT**

```python
for m in re.finditer(r"python3 (skills/[^\s`\"]+\.py)", text):
```

Measured across all six SKILL.md files: **all 27 invocations are `python3 skills/...`**. No `$PY`, no bare `python`, no `./`, no `python3 -m`. The regex is currently exhaustive, so this is latent rather than live — and it is one copy-paste from `run_all.sh`, which uses `$PY` in 15 places.

#### 3.6.6 `*.sh` is outside validation entirely — **PROVED**

```python
for f in sorted(os.listdir(scripts)):
    if not f.endswith(".py"):
        continue
```

Shell scripts are not validated. This matters more than it appears: `run_all.sh` — the orchestrator whose exit code *is* the release gate — lives at `skills/run_all.sh`, outside any skill's `scripts/` directory and hence outside the validator's scan domain even in principle. `bash -n run_all.sh` should be mandatory; `shellcheck` optional.

#### 3.6.7 The validator has no tests — **PROVED**

A recursive search for `validate_skill.py` across all `.sh` and `.py` returns only its own invocation at `run_all.sh:43` and a usage hint printed by `new_skill.py:78`. It is executed by the release gate, never verified by anything.

The recursion:

```text
skill-creator declares:   every skill must ship a negative test
        ↓
skill-creator itself:     ships no negative test
        ↓
who would catch that?     validate_skill.py
        ↓
validate_skill.py:        does not check for negative tests
        ↓
who would catch that?     mutation testing
        ↓
validate_skill.py:        has no tests at all
```

---

### 3.7 `ascii-diagram-forge`

The strongest component conceptually: geometry is calculated rather than typed, and the failure mode is correct — `incorrect geometry → exception → no artifact`, rather than best-effort rendering.

#### 3.7.1 `marks()` and `bar()` accept duplicate coordinates — **PROVED**

`row()` and `hjoin()` both raise on overlap. `marks()` and `bar()` do not:

```python
def marks(glyphs):
    out = ""
    prev = None
    for col, ch in sorted(glyphs):
        out = (" " * col + ch) if prev is None else out + " " * (col - prev - 1) + ch
        prev = col
    return out
```

With duplicate columns, `col - prev - 1 == -1` and `" " * -1 == ""`, so the second glyph is emitted immediately after the first rather than at the same absolute coordinate. Asymmetric guarantees weaken the foundational invariant that **all positions are absolute columns**.

**Additional detail:** `sorted()` breaks ties on the glyph's **codepoint**, so the wrong output is silently *deterministic* — it renders identically every time and reads as intentional. Negative first coordinates also render at column 0 via `" " * -1 == ""`.

**Corroboration:** the geometry self-test has 12 cases including `row() raises on overlap`, `row() raises on negative start`, and `hjoin() raises on overlap` — but **no `marks()` or `bar()` duplicate-coordinate case**. The asymmetry is visible in the test suite's own coverage.

#### 3.7.2 `dchain([])` and `bw([])` fail indirectly — **MINOR**

```python
def dchain(items, c=4, g="↓"):
    out = [items[0]]        # IndexError on empty
def bw(lines):
    return max(len(l) for l in lines) + 4    # ValueError on empty
```

An exception is raised, but it does not name the violated contract. For a library whose purpose is legible failure, explicit domain errors are preferable.

#### 3.7.3 `hjoin()` ordering precondition is undocumented — **OPEN**

`hjoin()` consumes segments in caller order without sorting, unlike `marks()` and `bar()`. That may be intentional, but the contract does not state *segments must be supplied in increasing column order*. For a library whose purpose is preventing silent layout drift, this ambiguity is undesirable.

---

### 3.8 `run_all.sh` — orchestration

#### 3.8.1 The header overstates execution scope — **PROVED**

Line 2 reads:

```bash
# run_all.sh — execute every check the skills provide, in dependency order.
```

Actual semantics: every check available in the selected stage, with full checks only on the newest document (§3.1.1). Either fix the description or fix the implementation; the latter is preferred.

#### 3.8.2 The interpreter override is incomplete — **PROVED, LATENT**

```bash
PY="${1:-python3}"
…
python3 skills/ascii-diagram-forge/scripts/examples.py     # line 37 — hardcoded
```

Every other stage uses `$PY`. **Live demonstration:** this audit invoked `bash skills/run_all.sh /tmp/rflvenv/bin/python`, where the venv is the only interpreter with `markdown` installed. Stage 1 ran under the system `python3` regardless — and passed, because `geo.py` and `examples.py` import only `__future__`, `typing`, `os`, `sys`, and `geo`. No third-party dependencies, so the wrong interpreter is currently an adequate one. The leak becomes load-bearing the moment geometry gains a dependency. Fix: `"$PY"`.

#### 3.8.3 Failure domains collapse to one bit — **PARTIALLY PROVED**

The driver has `fail=0` and every failed stage sets `fail=1`; the final exit is 1. **But** the per-stage exit code *is* printed (`>>> stage FAILED (exit $rc)`) — observed as both `exit 1` (content) and `exit 2` (skipped-under-strict) in a bare-environment run.

So the information is preserved in stdout but not in the exit code. The accurate statement: *failure domains are distinguishable in the log but not in any structured or machine-readable artifact.* The fix is `ExecutionRecord` (§4.7), not a richer exit code.

#### 3.8.4 Stage identifiers are presentational — **OPEN**

`1/7 … 7/7` are display labels. Inserting a stage renumbers every subsequent label. Stable IDs (`GEO-001`, `CORPUS-001`, …) would let the display change without invalidating historical evidence.

#### 3.8.5 `--strict` docstring contradicts the implementation — **PROVED, MINOR**

The docstring says exit 2 is *"only fatal under --strict for the render checks"*. The implementation collects skipped checks at three sites (render, provenance, probes) and fails on any of them under `--strict`. **The implementation is the safer behaviour**; the docstring is simply wrong. Adopt the implementation's own message as the contract: *checks were skipped, so this run proved nothing.*

#### 3.8.6 `ERROR` is not distinguished from `FAIL` — **PROVED**

`audit_file.py` contains **zero `try` blocks and zero `except` clauses**; every check call is unguarded. An uncaught exception yields exit 1 under a shell driver — indistinguishable from a content finding.

```text
CHECK ERROR   verifier couldn't establish the proposition
CHECK FAIL    verifier established that the proposition is false
```

A crashed Rust checker does not mean the Rust block is invalid; it means verification was unavailable. Needs the five-valued result set of §4.7.

#### 3.8.7 The "nothing exits 0 by default" invariant is imprecise — **OPEN, MINOR**

`skills/README.md` reads:

> Every script here either verifies something or says it could not. Nothing in this directory exits 0 by default.

Read literally the second sentence is false — scripts exit 0 on success. The intent is recoverable from the first sentence, but imprecise meta-documentation is itself a hazard. The testable replacement:

> A check never returns PASS when a required verification step was not actually executed.

---

### 3.9 Repository-level: no CI

**PROVED.** The repository has **no CI configuration at all**:

```text
gh api repos/Abdus2023/RFL-AE/actions/workflows  → total_count: 0
gh api repos/Abdus2023/RFL-AE/actions/runs       → total_count: 0
gh api repos/Abdus2023/RFL-AE/contents/.github   → 404
```

Zero workflows, zero runs, no `.github` directory — the repository has never had a CI system. This is stronger than *"no run attached to this commit"*.

Two consequences:

1. **Commit-message claims have no machine evidence.** `run_all.sh -> all stages pass, exit 0` is a claim about a local execution. The receipt in §1.1 is the first **third-party** execution evidence for this repository.
2. **§2.4 is latent.** If CI existed and ran `run_all.sh`, the crash-as-pass defect would be an active exposure in an automated gate. Adding CI before fixing it would convert a latent defect into an active one.

---

## 4. Architectural conclusions

The individual checker defects are now well-characterised. What follows is the structural conclusion, consolidated from all seven passes. **These were established by Part V and every subsequent pass confirmed them without adding to them.**

### 4.1 One normative Markdown lexical layer

The same fence-blindness appears in **five** independent parsers:

| Component | Blind spot | Consequence |
|---|---|---|
| `renumber.py` | rewrites fenced bodies | **corrupts documents** (§2.2) |
| `auditlib.sections()` | counts fenced `## N.` as sections | corrupts numbering, provenance, counts, anchors |
| `has_prov` | fence text satisfies the gate | **entire invariant bypassed** (§2.3) |
| `splice.py` | narrow fence regex | dialect limitation (§3.4.1) |
| `verify_closeout.py` | substring tests | false positives (§3.5.3) |

The correct abstraction is a shared lexical layer:

```text
Markdown source
       │
       ▼
┌─────────────────┐
│ Markdown Lexer  │
│ / Corpus IR     │
└────────┬────────┘
         │
   ┌─────┼─────┬──────────┐
   ▼     ▼     ▼          ▼
headings fences links  provenance
   │     │     │          │
   └─────┴─────┴──────────┘
         ▼
   common semantics
         ▼
  numbering · audit · splice · closeout
```

This is the highest-leverage single component in the plan: it is the prerequisite for four separate P0-class findings.

### 4.2 One canonical anchor implementation

Replace three implementations with one `AnchorTable`, with collision handling and golden vectors captured from the target renderer.

### 4.3 Scope must become a first-class field

This is the finding that repeats most often, in the most forms:

| Finding | Scope defect |
|---|---|
| §3.1.1 | 5 of 11 invariants corpus-wide |
| §2.3 | 10 of 33 documents outside the invariant entirely |
| §3.1.4 | 33 files vs 50 files between two tools |
| §3.1.5 | `README.md` excluded by an unstated rule |
| §3.5.1 | content scope verified, publication scope not |

`ALL FILES OK` is dangerous unless its scope is attached. Measured, the honest form is two statements:

```text
CORPUS AUDIT RESULT
scope:    root specification documents
checks:   numbering, fences, render, raw links, provenance      (5)
coverage: 33 / 33 documents
result:   PASS

LATEST DOCUMENT AUDIT
scope:    RFL-LEDGER-V01.md
checks:   numbering, fences, Rust, rendering, structure,
          provenance, links, probes                             (11)
coverage: 1 / 1
result:   PASS
```

### 4.4 A `CorpusManifest` and `DocumentClass`

The verifier currently asks *"what numbers exist?"* rather than *"what numbers are supposed to exist?"* A manifest makes corpus identity **declared** rather than derived:

```yaml
corpus:
  name: RFL-AE
  version: "0.1"
documents:
  - path: ARCHITECTURE.md
    class: CORPUS_SPEC
    corpus_range: 1-51
  - path: VERIFICATION.md
    class: CORPUS_SPEC
    corpus_range: 357-386
gaps:
  - 108
```

enabling four assertions the sweep cannot currently make:

```text
expected documents == observed documents
expected sections  == observed sections
expected ranges    == observed ranges
expected gaps      == observed gaps
```

with a document-class taxonomy:

```text
CORPUS_SPEC · REPOSITORY_DOC · SKILL_SPEC · FIXTURE · GENERATED_ARTIFACT
```

and per-checker scopes (`CORPUS-001 → CORPUS_SPEC`, `LINK-001 → ALL_MARKDOWN`, `SKILL-001 → SKILL_SPEC`). `EXCLUDE = {"README.md"}` disappears: exclusion becomes a consequence of classification rather than a hand-maintained exception.

### 4.5 Structured findings instead of exit codes

The negative-test contract should be:

```text
NegativeFixture → ExpectedFinding { check_id, subject, category, severity }
                → ObservedFinding
                → exact/structural match
```

rather than `returncode != 0`. This single change closes §2.4 and subsumes §3.8.6.

### 4.6 Content closeout and publication closeout are different claims

```text
CONTENT CLOSEOUT                    PUBLICATION CLOSEOUT
├── forward edge                    ├── HEAD identified
├── README entry                    ├── commit exists
├── README status                   ├── commit message valid
├── provenance                      ├── branch identified
└── numbering continuity            ├── remote ref queried
                                    └── remote SHA == expected SHA
```

> Neither implies the other. A locally perfect working tree does not prove publication. A remote commit does not prove the content was audited before it was pushed.

The second direction is the less obvious one and requires evidence binding to the same SHA — §4.7, not a shell script.

### 4.7 `ExecutionRecord` and the evidence model

The end state is a chain with identity binding at every arrow:

```text
SOURCE → DIGEST → CHECK DEFINITION → VERIFIER → EXECUTION
       → OBSERVATION → EVIDENCE → COVERAGE → GATE → COMMIT → REMOTE REF
```

with:

```text
ExecutionRecord
  record_id · check_id · subject_id · subject_digest
  verifier_id · verifier_digest · toolchain_id · dependency_set · environment_id
  started_at · finished_at · exit_code · stdout_digest · stderr_digest
  result · scope

result ∈ { PASS, FAIL, ERROR, SKIPPED, UNKNOWN }
```

**Two of the five result values currently have no representation, and two are collapsed into one bit:**

| Value | Current representation | Receipt |
|---|---|---|
| `PASS` | exit 0 | — |
| `FAIL` | exit 1 | — |
| `ERROR` | **exit 1** — same as FAIL | §3.8.6, §3.5.2 |
| `SKIPPED` | `n/a` in a **PASS** cell | §2.3 |
| `UNKNOWN` | **rendered as PASS** | §2.3 |

The kernel axiom:

```text
EVIDENCE(P) ⇒
    DECLARED(P) ∧ EXECUTED(P) ∧ SUBJECT_BOUND(P)
    ∧ VERIFIER_BOUND(P) ∧ SCOPE_COVERED(P) ∧ RESULT_OBSERVED(P)

VERIFIED(P) ⇔ EVIDENCE(P) ∧ RESULT(P) = PASS
RELEASED    ⇔ ∀ P ∈ RequiredProperties: VERIFIED(P)
```

**Cross-check.** Applying these six conjuncts to the seven substantive defects found across all passes:

| Defect | Conjunct that fails |
|---|---|
| §3.1.1 corpus coverage asymmetry | `SCOPE_COVERED` |
| §2.1 fabricated provenance certified | `SCOPE_COVERED` |
| §2.3 non-monotonic invariant | `SCOPE_COVERED` |
| §3.1.2 document deletion undetected | `DECLARED` |
| §3.6.1 negative-test rule unenforced | `DECLARED` |
| no verifier identity anywhere | `VERIFIER_BOUND` |
| §3.8.6 `ERROR` as `FAIL` | `RESULT_OBSERVED` |

Seven defects reduce to three conjuncts — `SCOPE_COVERED` four times, `DECLARED` twice, `VERIFIER_BOUND` once — with `EXECUTED` and `SUBJECT_BOUND` never failing. That concentration is evidence the axiom is correctly formulated rather than over-fitted, and it identifies **`SCOPE_COVERED` as the conjunct that does the work**. No component of the current toolchain models scope at all.

### 4.8 Mutation testing, with three results already available

For each invariant `I`, `M(I)` is a mutation violating it; `checker(M(I))` must fail. **Three of the eight proposed operators have already been executed against the real checkers, and all three mutants survive:**

| Operator | Result | Evidence |
|---|---|---|
| corrupt source number | **SURVIVES** | §2.1 — `§3` → `§2`, certified |
| change offset | **SURVIVES** | §2.1 — `mapping errors=0` |
| delete all provenance | **SURVIVES** | §2.3 — `n/a`, passes |

The framework does not need to be built before it produces results.

### 4.9 Bootstrap trust

> Who verifies the verifier?

The chain must ground in a deliberately small trusted layer:

```text
bootstrap/
├── file_digest      ├── process_runner
├── canonical_json   ├── exit_status
├── filesystem_scope └── evidence_serializer
```

Two of these — `process_runner` and `exit_status` — are precisely where the current toolchain is weakest (§3.8.3, §3.8.6, §2.4), which supports keeping them tiny and separately verified. `canonical_json` is a prerequisite for §4.4's manifest.

### 4.10 The recurring defect pattern

Every major finding in this audit has the same form:

> **A check that reports success when it did not run.**

| Finding | Form |
|---|---|
| §2.3 | provenance invariant disappears when provenance is absent |
| §2.4 | negative test passes on an import crash |
| §3.1.1 | corpus sweep silent on 6 of 11 invariants |
| §3.1.3 | probe coverage structurally impossible corpus-wide |
| §3.5.1 | publication never checked, skill claims it is |
| §3.6.1 | negative-test rule declared, unenforceable |
| §3.6.4 | validator's parser silently drops keys |
| §2.1 | fabricated provenance certified as correct |

The seven parts of this audit found these by execution. The architectural recommendations above exist to make the class detectable by the toolchain instead.

---

## 5. What is verified correct

A review that lists only defects misrepresents a largely-correct component. The following were verified as working as documented.

**Pipeline execution.** All seven stages pass, exit 0, at a pinned SHA. Every specific claim in the tip commit message reproduces exactly (§1.1).

**Geometry failure mode.** The central design is right: geometry is calculated, not typed, and overlap raises rather than rendering best-effort. 12 self-tests pass, including `row() raises on overlap`, `row() raises on negative start`, and `hjoin() raises on overlap`.

**`placeholder-splice` trust boundary.** The strongest local design in the suite. It does not check `placeholder count == diagram count`; it checks that each resulting body is the **exact content of a classified fenced block** — verification against the actual failure mode rather than a proxy. It has also already deprecated a weaker check (`--fence-lang`) and documented the defect that motivated the change.

**Strict-mode failure semantics.** A missing dependency produces `SKIPPED`, and `--strict` promotes it to failure with the message *"checks were skipped, so this run proved nothing."* Observed working: a bare environment produced `FAIL (--strict): checks were skipped` and exit 2. This is the clearest implementation of the project's own `NO EVIDENCE → NO VERIFIED CLAIM` principle.

**Gap assertion.** `--expect-gaps` requires equality with the **declared** set, not merely the absence of unexpected gaps, so both a missing expected gap and an extra one fail. It is the one place the sweep compares observed state against declared intent — the pattern §4.4 generalises.

**Provenance mapping (when it runs).** `check_provenance()` tests `corpus − source = constant offset` directly, rather than merely counting comments against headings. That is substantially stronger than a count check, and it is only undermined by the scope gate in §2.3.

**Negative-test inversion.** The `neg()` harness correctly inverts the usual convention — success means non-zero exit — and reports `PASS` on failure as designed. Its defect (§2.4) is in what it *accepts*, not in the inversion logic.

**Deliberately conservative checks.** `check_orphans()` avoids false positives by considering neighbouring rows (17 false orphan reports preceded it). `check_offcentre()` checks whether `▼` lands on a label character rather than requiring mathematical centring of every token. `check_ascii_substitution()` permits ASCII-only diagrams and flags only accidental substitution. All three trade coverage for precision where precision is the right choice.

**Documentation quality.** The trap sections are unusually good. They record symptoms, not rules — *"the original helper used `out.ljust(col) + text`, which silently concatenated on overlap and produced the corrupt line `acceptquarantine`"* — which is what makes a defect recognisable on recurrence.

---

## 6. Ranked priority list

### P0 — correctness defects

| # | Action | Closes |
|---|---|---|
| 1 | **`renumber.py`: use `m.group(1)`; reject duplicate/decreasing source numbers; add explicit `GAPPED` handling** | §2.1 |
| 2 | **`renumber.py`: add lexical fence awareness** | §2.2 |
| 3 | **Delete `has_prov`; require complete provenance for `CORPUS_SPEC` documents** | §2.3 |
| 4 | **`neg()`: require structured findings, not a non-zero exit** | §2.4 |
| 5 | Give `renumber.py` the gap-preservation regression test (§2.1's receipt, as a fixture) | §2.1, §4.8 |

Item 5 is a one-file change that fails today and would have caught §2.1 on the day it was written.

### P1 — scope correctness

| # | Action | Closes |
|---|---|---|
| 6 | Shared Markdown lexer/IR (`markdown-corpus-core`) | §4.1 (four findings) |
| 7 | `CorpusManifest` + `DocumentClass`; assert documents, sections, ranges, gaps | §4.4, §3.1.2 |
| 8 | Make `audit_corpus` invoke every applicable per-file invariant | §3.1.1 |
| 9 | Declare checker scopes explicitly | §4.3 |
| 10 | One canonical anchor implementation + golden vectors | §4.2 |

### P2 — evidence correctness

| # | Action | Closes |
|---|---|---|
| 11 | `ExecutionRecord` with the five-valued result set | §4.7 |
| 12 | Verifier identity (commit SHA, version) bound into results | §4.7 |
| 13 | Subject digests; environment and dependency identity | §4.7 |
| 14 | Split content closeout from publication closeout | §4.6, §3.5.1 |

### P3 — self-verification

| # | Action | Closes |
|---|---|---|
| 15 | Mutation framework, seeded with the three known surviving mutants | §4.8 |
| 16 | Enforce negative-test rule in `validate_skill.py` | §3.6.1 |
| 17 | Give `validate_skill.py` a test | §3.6.7 |
| 18 | `bash -n` for shell scripts | §3.6.6 |
| 19 | Behavioural validation (execute declared fixtures) | §3.6.2 |

### P4 — publication authority

| # | Action | Closes |
|---|---|---|
| 20 | CI workflow running `run_all.sh` — **after item 4** | §3.9 |
| 21 | Commit SHA binding in evidence | §4.7 |
| 22 | Remote-ref verification (`git ls-remote`) | §3.5.1 |

> **Ordering constraint:** item 20 must follow item 4. Adding CI while `neg()` accepts crashes would convert a latent defect into an active one in an automated gate.

---

## Appendix A — Unified findings index

Every finding, de-duplicated, with the parts in which it was established. `I` = Part I (original), `II`–`VII` = subsequent passes.

### Critical

| ID | Finding | Class | Parts | Confirmed by |
|---|---|---|---|---|
| C-1 | `renumber.py` fabricates source provenance; auditor certifies it | PROVED | I §5, II §11, IV §0.1, IV §41, VII §82 | execution |
| C-2 | `renumber.py` fence-blind; corrupts fenced bodies | PROVED | I §6, II §12, IV §0.2, VII §83 | execution |
| C-3 | Provenance invariant non-monotonic; 10/33 docs bypassed | PROVED | V §0.1 | 5-state experiment |
| C-4 | Negative-test harness accepts a crash as a pass | PROVED | VII §0.2, VII §90 | execution |
| C-5 | Corpus sweep covers 5 of 11 invariants | PROVED | III §21 | source + output |
| C-6 | Closeout verifies content, not publication; SKILL prescribes `git ls-remote` | PROVED | I §25, VII §80 | source |

### High

| ID | Finding | Class | Parts | Confirmed by |
|---|---|---|---|---|
| H-1 | `linkaudit.py` has no dependency guard and no `--strict` | PROVED | II §9, VII §0.2 | source + execution |
| H-2 | No verifier identity — no SHA, version, or hash anywhere | PROVED | V §56 | search |
| H-3 | `documents`/`sections` printed, never asserted; deletion undetected | PROVED | V §53, V §55, V N-5 | mutation |
| H-4 | No corpus-level probe coverage; no `--probes` flag exists | PROVED | III §24 | source |
| H-5 | Negative-test rule declared, unenforced, unmet by 2 of 6 skills | PROVED | VI §0.1 | 3 measurements |
| H-6 | Three slug implementations; one algorithmically different | PROVED | I §21, II §16, VII §88 | source |
| H-7 | `ERROR` not distinguished from `FAIL` | PROVED | IV §36, V N-6, VII §3.8.6 | source + execution |
| H-8 | Substring evidence where structure required (closeout) | PROVED | VII §80.1 | source |
| H-9 | Source identity is filename + integer | PROVED | V §50 | source |
| H-10 | Renderer is an untrusted observation source | PROVED | V §58 | source |
| H-11 | `validate_skill.py` validates structure, not behaviour | PROVED | III §26, VI §64 | source |
| H-12 | No CI exists at all | PROVED | VII §92 | `gh api` |

### Medium

| ID | Finding | Class | Parts |
|---|---|---|---|
| M-1 | Scope universes differ: 33 vs 50 files | PROVED | V §51 |
| M-2 | `README.md` exclusion is unstated authority | PROVED | V §52 |
| M-3 | `*.sh` outside validation entirely | PROVED | III §27 |
| M-4 | Validator has no tests (incl. `renumber.py`) | PROVED | III §26, IV §41, IV N-2, VI §73 |
| M-5 | Discovery assumes flat topology | PROVED | VI §68 |
| M-6 | Rust "balance" is delimiter counting | PROVED | I §11, II §13 |
| M-7 | `marks()`/`bar()` accept duplicate coordinates | PROVED | I §3 (G-1), II §14 |
| M-8 | Fence counting via `text.count("```")` | PROVED | I §12 |
| M-9 | Frontmatter parser silently drops keys | PROVED | VI §69 |
| M-10 | Fixtures are not mutation tests | PROVED | IV §39, IV §40, VI §73, VII §91 |
| M-11 | `splice.py` write not crash-durable | PROVED | VII §86 |
| M-12 | Stage identifiers presentational | OPEN | IV §38 |
| M-13 | `$PY` override incomplete | PROVED/LATENT | II §27, III §28 |
| M-14 | `run_all.sh` header overstates scope | PROVED | IV §34 |
| M-15 | `--strict` docstring contradicts implementation | PROVED | IV §35 |
| M-16 | `dchain([])`/`bw([])` fail indirectly | MINOR | I §3 (G-3), II §15 |
| M-17 | `total_secs` dead variable | MINOR | IV N-1 |
| M-18 | `check_links()` regex dialect-narrow | OPEN | I §19 |
| M-19 | `splice.py` fence grammar narrow | OPEN | I §8, VII §85 |
| M-20 | Provenance layout dialect unnamed | OPEN | V §49 |
| M-21 | No precondition model for source numbering | OPEN | VII §42 |
| M-22 | No round-trip property | OPEN | VII §84 |
| M-23 | Duplicate anchors under-modelled | LATENT | II §16b, VII §89 |
| M-24 | Command-reference check pattern-limited | LATENT | VI §70 |
| M-25 | `hjoin()` ordering precondition undocumented | OPEN | I §3 (G-2) |
| M-26 | "Nothing exits 0 by default" imprecise | OPEN | III §29 |
| M-27 | Diagram checks deliberately narrow | OPEN | I §13, I §14, I §15, I §16 |
| M-28 | `## Verification` checked for existence only | LATENT | VI §65 |
| M-29 | `ALL FILES OK` lacks scope | OPEN | III §23, VII §95 |

### Positive

| ID | Finding | Parts |
|---|---|---|
| P-1 | Pipeline reproduces fully; every commit number exact | III §0 |
| P-2 | Geometry failure mode correct (raise, not render) | I §2 |
| P-3 | `splice.py` exact-fenced-body verification is the strongest local design | I §7 |
| P-4 | Strict-mode `SKIPPED → FAIL` works as documented | III §10 |
| P-5 | `--expect-gaps` compares against declared intent | V N-8 |
| P-6 | `check_provenance` tests the mapping, not a count | I §17 |
| P-7 | `neg()` inversion logic correct | IV N-4 |
| P-8 | `splice.py` already deprecated a weaker check | VII N-13 |
| P-9 | Tightening trajectory on `load_probes` `#` handling | I §28 |
| P-10 | Trap sections record symptoms, not rules | I §28 |

---

## Appendix B — Closed and withdrawn findings

Recorded so they are not re-raised.

### B.1 `skills/README.md` "997 sections" vs "§1–§998" — **CLOSED, NOT A DEFECT**

This claim appeared **five times across four consecutive passes** and was disproved twice before being self-corrected.

| Pass | Claim | Outcome |
|---|---|---|
| II §8 | "the skills README is stale (997 vs 998)" | DISPROVED — 997 is a cardinality, 998 a maximum |
| III §20 | "`run_all.sh` cannot produce the claimed clean result … the evidence was not actually generated" | DISPROVED by execution — all 7 stages, exit 0, every number reproduced |
| III §32 | conceded: *"not a defect; nothing to fix"* | — |
| IV §47 | reinstated as risk item 1 | flagged RESTATED |
| V §63 | P0 item 3 | flagged RESTATED (5th) |
| VII §80 | **explicitly corrected**: *"997 sections and range 1–998 are not contradictory"* | ✅ closed |

**The facts.** `997` is a section **count**; `998` is the maximum section **number**; the difference is the deliberate `§108` gap, preserved by design and enforced by `run_all.sh:47` (`--expect-gaps 108`). Root `README.md:459` states it directly:

> Thirty-three specification documents covering §1–§998 (§108 does not exist in the source; the gap is preserved)

The corpus audit prints all three consistent facts in a single block:

```text
sections      : 997
range         : 1 .. 998
gaps          : [108]
```

**Residual recommendation (MINOR, still worth doing).** Reading `997 sections` next to `--range 970-998` invites the misreading — five passes are the evidence. Disambiguate in place:

```text
corpus: §1–§998 (33 documents, 997 sections; §108 absent)
```

**What this episode demonstrates.** An observation printed three lines apart in one block was repeatedly reinterpreted as a contradiction, and the error escalated from "a README number is stale" to "the committed verification evidence is fabricated." Under the `ExecutionRecord` model of §4.7, the observation and the verdict would travel together and the contradiction would resolve on first contact. This is the strongest available argument for binding evidence to observations rather than to conclusions.

### B.2 Other withdrawn items

| Claim | Part | Outcome |
|---|---|---|
| `README 997 → 998` as a P0 fix | V §63 | withdrawn — nothing to fix |
| Corpus README synchronization as a maturity deficiency | II §19 | withdrawn — README is accurate |
| `description: "something: with colon"` breaks the frontmatter parser | VI §69 | incorrect — `partition(":")` splits at the first colon; it works |
| Duplicate anchors are a live defect | II §16b | downgraded to LATENT — no duplicates exist in the corpus |
| Route-deviation / alternate-path concern | — | not raised |

### B.3 Historical incidents (already fixed, retained for context)

These are recorded in the skills' own documentation as traps and are **not** open defects:

```text
acceptquarantine        overlap concatenation in geometry
37 unfenced diagrams    placeholders spliced outside fences
17 false orphans        over-aggressive orphan detection
2 phantom off-centre     token-centring false positives
52 silently skipped      `#`-prefixed probes treated as comments
hyphenated README number `\w+` stopping at the hyphen in "Twenty-one"
wrong relative links     `#fragment` resolved against the wrong base
/tmp toolchain loss      ephemeral environment
```

---

## Appendix C — Execution receipts

### C.1 Pipeline run

```text
════════ 1/7 geometry self-test ════════      12/12 self-tests passed
════════ 2/7 skills are well-formed ════════  6/6 skills ok
════════ 3/7 corpus audit (33 documents) ════════  ALL FILES OK
════════ 4/7 every internal link resolves ════════  50 files, 851 links, 0 broken, 2 excused
════════ 5/7 last document audited in full ════════  RFL-LEDGER-V01.md OK
════════ 6/7 close-out ritual verified ════════  CLOSE-OUT OK
════════ 7/7 negative tests (each MUST fail) ════════  4/4 PASS
RESULT: ALL STAGES PASSED
```

Stage 3 totals:

```text
documents     : 33
sections      : 997
range         : 1 .. 998
duplicates    : none
gaps          : [108]
```

Stage 5 (full per-file audit of the newest document):

```text
sections      : 29  first=970  last=998  expected=970..998
fences        : 178  balanced  langs={'text': 79, 'rust': 10}
rust blocks   : 10  delimiter problems=0
render        : tables=2 h2=30 h3=8 code=89 unclassified=0 stray_pipes=0 cmthead=0 leak=0
structure     : text_blocks=79 box_width=0 orphans=0 offcentre=0 ascii_sub=0
provenance    : 29/29 comments, mapping errors=0 (offset 969)
links         : anchors=38 internal=29 broken_anchors=0 md=2 broken_md=0
probes        : 430 phrases, missing=0
```

### C.2 Degraded-environment run (no `markdown`)

```text
markdown: NOT INSTALLED -- render checks will be SKIPPED
════════ 3/7 corpus audit (33 documents) ════════
NOTE: render checks SKIPPED -- 'markdown' is not installed, so
>>> stage FAILED (exit 1)
════════ 4/7 every internal link resolves ════════
>>> stage FAILED (exit 1)
════════ 5/7 last document audited in full ════════
render        : SKIPPED -- 'markdown' package not installed
SKIPPED (1): render (markdown package not installed)
FAIL (--strict): checks were skipped, so this run proved nothing
>>> stage FAILED (exit 2)
RESULT: FAILURES ABOVE
```

The environment changes the outcome with identical corpus content, and is recorded nowhere in the result — Part V §57.

### C.3 Fabricated provenance (§2.1)

Input `## 1. First / ## 3. Third / ## 7. Seventh`, offset 100:

```text
renumbered 3 sections: source 1..3 -> corpus 101..103

## 101. First      <!-- source: SRC.md §1 -->
## 102. Third      <!-- source: SRC.md §2 -->     ← §2 does not exist
## 103. Seventh    <!-- source: SRC.md §3 -->     ← §3 does not exist
```

Audit of that output:

```text
provenance    : 3/3 comments, mapping errors=0 (offset 100)
```

### C.4 Non-monotonic invariant (§2.3)

| comments kept | of 30 | PROV cell | verdict |
|---|---|---|---|
| 30 | 30 | +969 | PASS |
| 29 | 29 | +969 | FAIL: provenance incomplete |
| 15 | 15 | +969 | FAIL: provenance incomplete |
| 1 | 1 | ?off | FAIL: provenance incomplete |
| 0 | 0 | n/a | PASS — not checked |

### C.5 Crash-as-pass (§2.4)

```text
$ python3 skills/markdown-corpus-audit/scripts/linkaudit.py --root /tmp/negtest2
Traceback (most recent call last):
  File ".../linkaudit.py", line 9, in <module>
    import markdown
ModuleNotFoundError: No module named 'markdown'
exit code: 1

  PASS  link checker catches a broken anchor and a missing target  (exit 1, 0 findings)
```

### C.6 Document deletion (§3.1.2)

Delete `RFL-LEDGER-V01.md`, run stage 3:

```text
documents     : 32          ← was 33
sections      : 968         ← was 997
range         : 1 .. 969    ← was 1 .. 998
gaps          : [108]       ✅ equals --expect-gaps 108
```

### C.7 Environment and identity

```text
branch tip         1090511a4080987168d1b17d48de88137ec1c27a
python             3.11.2
python-markdown    3.11
documents          33
workflows          0
workflow runs      0
.github            absent (404)
```

---

*End of consolidated audit. The seven per-pass files in this repository remain the chronological record; this document supersedes them as the reference.*
