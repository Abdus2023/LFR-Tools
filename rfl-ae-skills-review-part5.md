# RFL-AE `skills/` — Independent Review, Part V

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Parts I–IV:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md)
**Review date:** 2026-09-28

> **Method.** Verified by execution. §48 required a three-way controlled experiment (later extended to five states), §51 and §55 required corpus mutation, and §56 required a repository-wide identity search.

> **Numbering note.** These sections are numbered 48–63 as given in this pass.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection |
| PROVED — stronger | The direction is right; the actual defect is materially worse than stated |
| PARTIALLY PROVED | Direction correct; a specific sub-claim is inaccurate |
| **RESTATED** | Already disproved in an earlier part, appearing again |
| recommendation | Forward-looking design proposal |

---

## 0. Verification summary

| Part V § | Claim | Verified? |
|---|---|---|
| 48 | `check_provenance()` cannot distinguish MISSING from MALFORMED | **PROVED — much stronger: the invariant is non-monotonic** |
| 49 | Only a specific physical layout is accepted | PROVED — the normative/implementation framing is correct |
| 50 | Source identity is only a filename | PROVED — and Part IV §0.1 shows it already misfires |
| 51 | `audit_corpus` and `linkaudit` cover different universes | **PROVED** |
| 52 | `README.md` exclusion is implicit authority | PROVED (`EXCLUDE = {"README.md"}`) |
| 53 | `set(all_secs)` rebuilt in the loop | PROVED — and the *semantic* half is the real finding |
| 54 | A `CorpusManifest` is needed | recommendation — endorsed, with a receipt |
| 55 | Whole-document deletion may go undetected | **PROVED — stage 3 does not detect it** |
| 56 | Verifier identity absent | **PROVED** — no SHA anywhere in the toolchain |
| 57 | Dependency identity thin | PARTIALLY — values are printed, never bound |
| 58 | Renderer is an untrusted observation source | PROVED — docstring claims exactness |
| 59–62 | Three-level render status; evidence-producing kernel | recommendation — endorsed |
| 63 | Revised priority | **P0 item 3 is the fifth appearance of a disproved claim** |

---

## 0.1 Decisive receipt: the provenance invariant is non-monotonic

This is the strongest result in the five-part review, and it supersedes Part IV §0.1 as the single most important finding.

**Experiment.** A controlled five-state test on a real corpus document (`RFL-LEDGER-V01.md`, 29 sections, 30 provenance comments, offset +969), holding the filename constant so `source_name` matching is unaffected, varying only the number of provenance comments retained:

```text
comments kept   of 30   PROV cell   verdict
────────────────────────────────────────────────────────────
30              30      +969        PASS
29              29      +969        FAIL: provenance incomplete
15              15      +969        FAIL: provenance incomplete
 1               1      ?off        FAIL: provenance incomplete
 0               0      n/a         PASS  (provenance not checked)
```

**Deleting 29 of 30 comments fails the audit. Deleting all 30 passes it.**

Removing *more* evidence produces a *better* result. The invariant is non-monotonic, which means it is not an invariant at all — it is a property that holds only for documents that happen to opt in.

### The root cause, in one line

`audit_corpus.py`:

```python
has_prov = "<!-- source: " in text
...
if has_prov and not prov["complete"]:
    errs.append("provenance incomplete")
```

A **whole-file substring test** gates the entire per-section invariant. So:

```text
"<!-- source: " in text ?
        │
        ├── YES ──► every section must carry valid provenance  → enforced
        │
        └── NO  ──► invariant not applied at all               → printed as "n/a"
```

### Why §48 understates this

The finding describes a **section-level** representational gap:

> the representation does not distinguish "the other 8 sections have no provenance" from "the other 8 sections have provenance in an unrecognized form"

That is accurate, and the finding correctly notes `complete=False` is computed properly. At section granularity the concern is benign.

The **file-level** defect is the severe one, and it inverts the finding's reassuring conclusion:

> So the implementation correctly prevents a false complete result.

True for a partially-provenanced file. **False for a wholly-unprovenanced one**, where the check vanishes and the verdict reverts to PASS.

### This is already live in the corpus

From Part III's execution receipt, the real corpus has **10 of 33 documents** with `PROV = n/a`:

```text
ARCHITECTURE.md     CONTRACTS.md      DESIGN-IR.md      FORMAL-CORE.md
KSIR.md             PROTOCOL.md       RECONSTRUCTION.md RUST-CORE.md
SPECIFICATION.md    TRANSITIONS.md
```

All ten are **permanently outside the provenance invariant**, and the sweep reports `ALL FILES OK` over them. §48's principle —

> Absence of an observation must not be confused with absence of the object being observed.

— is exactly right, and it is violated at file granularity across roughly a third of the corpus.

### Symmetric false positive: the test is also fence-blind

The same substring test fires on text that merely *mentions* the syntax. A document with **no provenance at all**, containing only a fenced example:

````markdown
## 1. Real section
## 2. Another section

Referencing the syntax in a fenced example:

```text
<!-- source: DOC.md §1 -->
```

That is all. No real provenance above.
````

is audited as:

```text
PROBLEMS (1):
  - DOC.md: provenance incomplete
```

So the one-line test cuts three ways, all wrong:

```text
substring in real provenance        → enforced          ✅ correct
substring inside a code fence       → enforced          ❌ false positive
substring absent entirely           → not enforced      ❌ false negative (the severe case)
```

This is the **same fence-blindness** established in Part II §12 and demonstrated as corruption in Part IV §0.2 — now shown to control whether an entire invariant is applied. `has_prov` is a fourth fence-blind parser, alongside `renumber.py`, `auditlib.sections()`, and `splice.py`.

**Classification: PROVED — the invariant is non-monotonic and is already bypassed by 10 of 33 documents.**

### Why this is worse than Part IV §0.1

Part IV §0.1 required `renumber.py` to **fabricate** data, and the audit to certify the fabrication. This requires only that someone **delete** data — a far more common operation, and one an author might perform for entirely innocent reasons (rewriting a document, removing a stale block, normalising prose).

```text
Part IV §0.1   fabricated evidence → certified      (requires a defective transform)
Part V  §0.1   absent evidence     → certified      (requires only a deletion)
```

And it is the purest possible instance of the review's recurring theme: a check that reports success when it did not run.

---

## 48. `check_provenance()` has an important soundness weakness

**Classification: PROVED — and materially stronger than stated. See §0.1 above for the decisive receipt.**

The section-level analysis in the finding is correct as far as it goes. `check_provenance()` builds:

```python
pairs: List[Tuple[int, int]] = []
for n, _t, i in secs:
    ...
    if src is not None:
        pairs.append((n, src))
```

so `pairs` contains only what was successfully parsed, and both `MISSING` and `MALFORMED` sections vanish from it identically. The proposed representation is the right fix:

```text
SectionProvenanceResult {
    section: 981
    state: PRESENT | MISSING | MALFORMED | MISMATCH
    source_section: ...
}
```

and the principle it cites is correct and important:

> Absence of an observation must not be confused with absence of the object being observed.

The reason this needs the file-level generalization: the same conflation occurs one level up, where it is not merely a representational gap but a **verification bypass**. The `MISSING` state must be representable at the *document* level too — and today, a document in that state is reported as `n/a` rather than as a violation.

**Minimum fix:** replace the substring gate with a declared document property. A corpus document in `CORPUS_SPEC` class (see §52) should require complete provenance unconditionally; `has_prov` should not exist.

---

## 49. Provenance currently accepts only one very specific physical layout

**Classification: PROVED.** The framing — *implementation limit* vs *normative format restriction* — is the correct one.

The accepted layouts are determined by:

```python
for j in (i + 1, i + 2):          # next line, or past one blank line
    if j >= len(lines):
        break
    nxt = lines[j].strip()
    if not nxt:
        continue
    m = pat.match(nxt)
    src = int(m.group(1)) if m else None
    break
```

Only these two are accepted:

```markdown
## 981. Foo
<!-- source: X §12 -->
```

```markdown
## 981. Foo

<!-- source: X §12 -->
```

A non-blank, non-matching line in either position terminates the search, so an intervening metadata line is rejected. The docstring is explicit that this is intentional:

> The comment may sit on the line directly beneath the heading OR after a single blank line -- both layouts occur in the corpus (VERIFICATION/GATES use a blank line, EXECUTION/ORCHESTRATION do not), and both are correct.

So it is **documented**, which is more than most of the limitations in this review. The finding's recommendation still stands: name it.

```text
PROVENANCE_DIALECT_V1:
    heading
    [optional one blank line]
    provenance comment
```

The value of naming it is that the distinction becomes propagable. Across all five parts, this review has had to infer, case by case, whether a restriction was a frozen dialect or an implementation accident — the `splice.py` fence regex (Part I §8), the `check_links()` regex (Part I §19), the two-layout rule here, and the `has_prov` substring test (§0.1) are all the same question. A `NORMATIVE` vs `IMPLEMENTATION_LIMIT` marker on each documented restriction would remove that inference entirely.

**Classification: PROVED — limitation is documented; the normative/implementation distinction should be made explicit.**

---

## 50. `check_provenance()` has a more important issue: source identity is only a filename

**Classification: PROVED — and Part IV §0.1 shows the consequence is already live.**

Identity is:

```python
pat = re.compile(r"^<!-- source: " + re.escape(source_name) + r" \u00a7(\d+) -->$")
```

i.e. a filename and an integer. Nothing binds the claim to a particular revision of that file.

The proposed minimum identity is right:

```text
SourceIdentity {
    repository
    commit
    path
    section
    content_digest
}
```

with the visible comment remaining compact:

```html
<!-- source: RFL-LEDGER-V01.md §12 -->
```

while machine evidence binds to the immutable artifact.

**Why this is already urgent rather than forward-looking.** Part IV §0.1 produced real output containing:

```markdown
## 102. Third
<!-- source: SRC.md §2 -->
```

where `SRC.md` has no §2. The string is well-formed, the filename is correct, the section number is an integer, and the audit certified it with `mapping errors=0`. A filename-plus-integer identity cannot express "this claim is false" — there is no artifact to check it against. §50's remedy is precisely what makes fabrication detectable.

---

## 51. The corpus checker has an important scope bug

**Classification: PROVED.**

`audit_corpus.py`:

```python
files = sorted(f for f in os.listdir(a.dir)
               if f.endswith(".md") and f not in EXCLUDE)
```

Non-recursive: `X/*.md` only.

`linkaudit.py`:

```python
def walk(d):
    for e in sorted(os.listdir(d)):
        ...
        yield from walk(p)
```

Recursive: the whole tree.

Measured in Part III, the two universes differ in size exactly as predicted:

```text
audit_corpus   33 files   (root *.md except README.md)
linkaudit      50 files   (recursive)
```

The 17-file difference includes `skills/*/SKILL.md` and `audit/*.md`. So a document added under `docs/` would be:

```text
linkaudit:      included
corpus audit:   invisible
```

The finding's recommended declaration is right:

```text
CorpusScope = root_markdown_files_except(README.md)
```

Note this is the same **structural** defect as §0.1 in a different register: §0.1 is a scope gate that silently disables an invariant; §51 is a scope definition that is never stated. Both are instances of `PARTIAL ≠ COMPLETE` (§62) — the verdict's domain is undefined, so "all corpus files" is ambiguous.

---

## 52. `README.md` exclusion is another implicit authority rule

**Classification: PROVED.**

```python
EXCLUDE = {"README.md"}
```

Confirmed at `audit_corpus.py:23`. The exclusion is sensible — `README.md` is repository documentation, not corpus — but it is an **authority decision encoded as a set literal**, with no stated rationale and no mechanism preventing `EXCLUDE` from growing.

The proposed `DocumentClass` taxonomy is the right remedy:

```text
DocumentClass:
    CORPUS_SPEC
    REPOSITORY_DOC
    SKILL_SPEC
    FIXTURE
    GENERATED_ARTIFACT
```

with each verifier declaring its scope:

```text
CORPUS-001    scope = CORPUS_SPEC
LINK-001      scope = ALL_MARKDOWN
SKILL-001     scope = SKILL_SPEC
```

This directly resolves §51's ambiguity and makes `EXCLUDE` unnecessary: exclusion becomes a consequence of classification rather than a hand-maintained exception list.

Worth noting the classification is already real — the corpus *behaves* as if it had these classes (33 numbered specs with monotonic sections, `skills/*/SKILL.md` with frontmatter, `audit/*.md` as prose reports). The taxonomy formalizes an existing structure rather than imposing a new one.

---

## 53. The `set(all_secs)` computation is unnecessarily rebuilt inside the loop

**Classification: PROVED — the literal observation is correct; the semantic half is the real finding.**

```python
gaps = [i for i in range(min(all_secs), max(all_secs) + 1) if i not in set(all_secs)]
```

`set(all_secs)` is reconstructed for every candidate `i`. Confirmed at line 99. As the finding says, performance is irrelevant at ~1,000 sections.

The important part is the second half, which this pass **confirmed and extended by execution**:

> The code is calculating the gap set from the observed headings, not from a declared corpus manifest. That means the verifier asks "What numbers exist?" rather than "What numbers are supposed to exist?"

That is exactly right, and the code makes it concrete. `audit_corpus.py` lines 94–95:

```python
print(f"documents     : {len(files)}")
print(f"sections      : {len(all_secs)}")
```

Both values are **printed and never asserted**. They appear in every run, including the Part III receipt:

```text
documents     : 33
sections      : 997
range         : 1 .. 998
```

and they double as the numbers a human reader uses to decide the corpus is intact. Nothing checks them.

By contrast, the two quantities that *are* asserted are `dupes` (added to `problems` at line 103) and `gaps` (compared against `--expect-gaps`). So the sweep enforces *internal consistency* — no duplicate section numbers, gaps exactly as declared — and never *corpus identity*.

**Classification: PROVED.** See §54 and §55 for the consequence.

---

## 54. This exposes the need for a `CorpusManifest`

**Classification: recommendation — endorsed, now with a demonstration.**

The proposed manifest:

```yaml
corpus:
  name: RFL-AE
  version: "0.1"

documents:
  - path: ARCHITECTURE.md
    class: CORPUS_SPEC
    source_name: ARCHITECTURE.md
    source_range: 1-51
    corpus_range: 1-51

  - path: VERIFICATION.md
    class: CORPUS_SPEC
    source_range: ...
    corpus_range: ...

gaps:
  - 108
```

enables the four assertions the current sweep cannot make:

```text
expected documents == observed documents
expected sections  == observed sections
expected ranges    == observed ranges
expected gaps      == observed gaps
```

§55 provides the evidence that this matters.

---

## 55. Otherwise the verifier cannot detect deletion of a whole document

**Classification: PROVED — stage 3 does not detect it.**

The finding reasons about this but does not execute it. Executed here.

**Experiment.** Delete `RFL-LEDGER-V01.md` — the newest document, corpus §970–998, 29 sections — from a full clone, then run stage 3 alone:

```bash
python3 skills/markdown-corpus-audit/scripts/audit_corpus.py --expect-gaps 108 --strict
```

**Result:**

```text
documents     : 32          ← was 33
sections      : 968         ← was 997
range         : 1 .. 969    ← was 1 .. 998
duplicates    : none
gaps          : [108]       ← equals --expect-gaps 108  ✅

PROBLEMS (1):
  - RFL-TRANSITION-V01.md: 1 broken link(s)
```

**The corpus audit does not flag the deletion.** The document count (33→32), the section count (997→968), and the §998 ceiling are all reported as facts and none is asserted as a requirement (§53). The gap set is unchanged, because removing the *highest*-numbered document creates no interior gap.

The deletion was caught **incidentally** — `RFL-TRANSITION-V01.md` happens to carry a forward link to the deleted document. Had the deleted document been the last in the link chain, or had the forward link been absent, stage 3 would have reported `gaps: [108]` and passed cleanly.

This is precisely the finding's point, and it is stronger in execution than in the reasoning: the verifier cannot answer *"was `RFL-LEDGER-V01.md` supposed to exist?"* because it has no independent expected-document set.

### One fairness correction

The finding's closing sentence is worth qualifying. The full pipeline **does** catch this deletion — at stage 6, via the README status-line check (README says §1–§998; corpus max is §969). So RFL-AE is not currently publishing a corpus with a silently missing document.

But the catch is **accidental in two ways**:

1. It depends on the README's status line being accurate and manually maintained — the same manual-synchronization surface that produced three false findings in Parts II–IV.
2. It is not a corpus-identity check. It is a prose-number check that happens to constrain corpus identity.

And stage 6's behaviour on the missing document is itself instructive — see N-6 below: it does not report a structured problem, it **crashes**.

So the honest statement is: *the deletion is caught, but not by the component whose job it is to detect it, and the catching component fails by exception rather than by diagnosis.* §54's manifest converts this from an accident into an assertion.

---

## 56. The verifier currently knows nothing about its own version

**Classification: PROVED.**

A repository-wide search across `skills/*.sh` and `skills/*/*/scripts/*.py` for commit binding, version identity, or hash capture:

```text
grep -rn "rev-parse\|commit\|sha\|SHA\|__version__\|version" skills/...
  (excluding python/markdown matches)
  → no matches
```

**No component of the toolchain records a commit SHA, a version, or any verifier identity.** `run_all.sh` prints:

```text
python : $PY
repo   : $ROOT
markdown: 3.11
```

to stdout at startup — but these are **not bound into any result**, and `$ROOT` is a filesystem path, not an identity.

So the finding's question has no answer in the current system:

> Evidence: corpus PASS — **what version of the checker generated it?**

`auditlib.py` at revision A, B, or C could produce materially different results, and the evidence cannot distinguish them. This is especially pointed given that the branch under review has been **changing the verification implementation across the very commits being audited** — commit `90660542` modified `load_probes` to stop silently skipping 52 probes, which changes results for seven documents. Evidence produced before that commit is not comparable to evidence produced after it, and nothing in the output records which side of the change a result came from.

Proposed:

```text
VerifierIdentity {
    repository
    commit_sha
    path
    version
}
```

**Classification: PROVED — verifier identity is entirely absent, not merely incomplete.**

---

## 57. Dependency identity is equally important

**Classification: PARTIALLY PROVED — the values are printed, but nothing binds them.**

`run_all.sh` does capture and print:

```bash
echo "python : $PY"
$PY -c "import markdown; print('markdown:', markdown.__version__)" 2>/dev/null \
  || echo "markdown: NOT INSTALLED -- render checks will be SKIPPED"
```

Observed in Part III:

```text
python : /tmp/rflvenv/bin/python
repo   : /tmp/rflae
markdown: 3.11
```

So the finding's characterization — *"the evidence currently records essentially `markdown: 3.x`"* — is accurate in substance and slightly generous: what exists is a **startup banner**, not a record. It is not attached to any check result, not captured in any exit code, and not emitted on failure paths.

Genuinely absent, confirming the finding:

```text
✗ Python version bound to results   (printed as a path, not a version)
✗ OS / platform
✗ enabled extensions               (auditlib hardcodes ["tables", "fenced_code"])
✗ dependency lock or hash
✗ any binding between environment and verdict
```

The finding's conclusion holds:

> PASS @ environment A doesn't establish that PASS @ environment B would occur.

This is not academic, and Part III demonstrated why: the `markdown` package's absence changed the pipeline's outcome from `ALL STAGES PASSED` (exit 0) to `FAILURES ABOVE` (exit 1) with identical corpus content. The environment is a load-bearing input, and it is recorded nowhere in the result.

**Classification: PARTIALLY PROVED** — env values are surfaced; the defect is the absence of binding, not of capture.

---

## 58. The markdown renderer is itself an untrusted observation source

**Classification: PROVED.**

`auditlib.py:262–270` carries the claim:

```python
def slug(heading: str) -> str:
    """GitHub (gfm) anchor slug.

    Must match GitHub exactly: downcase, drop apostrophes, strip punctuation
    (anything that is not a word char, hyphen, or space), then replace EACH
    space with a hyphen.
```

The docstring's **own next paragraph** demonstrates that this is a locally-constructed approximation, not a verified implementation:

> `519. Command ≠ Event` loses the `≠` and keeps the two surrounding spaces, so its slug is `519-command--event` with a DOUBLE hyphen. Collapsing produces `519-command-event`, which does not resolve on GitHub.

That is careful, well-reasoned reverse-engineering of GitHub's behaviour. It is also — as the finding says — an assertion about GitHub that GitHub has not confirmed. The double-hyphen rule is justified by reasoning about the algorithm, and the claim `Must match GitHub exactly` has no test vector captured from GitHub.

The evidence chain is therefore:

```text
Markdown source
       │
       ▼
Python-Markdown interpretation      (renderer approximation)
       │
       ▼
custom anchor model                 (slug approximation)
       │
       ▼
checker result
```

and not:

```text
Markdown source → GitHub interpretation → checker result
```

The recommended vocabulary is right:

```text
RendererObservation {
    renderer = python-markdown
    renderer_version = ...
    extensions = ["tables", "fenced_code"]
}
```

and the guidance is important: never emit `github_verified = true` unless actual GitHub behaviour has been independently established.

This closes the loop with Part I §21 and Part II §16: three slug implementations, none validated against a golden vector captured from the target platform. §58 explains *why* that matters — the target platform is an **unverified oracle**, and the local approximation is being reported as if it were the oracle.

---

## 59. This suggests a three-level rendering status

**Classification: recommendation — endorsed.**

```text
SOURCE_VALIDATED
    Markdown structural checks passed

REFERENCE_RENDER_VALIDATED
    designated renderer produced expected structure

TARGET_RENDER_VALIDATED
    actual target platform behavior observed
```

For RFL-AE, Python-Markdown PASS establishes the second. It cannot establish the third.

This is a clean decomposition and it maps directly onto the repository's existing evidence architecture. It also gives an honest vocabulary for the current state: the corpus sits at `REFERENCE_RENDER_VALIDATED`, and the `slug` docstring's `Must match GitHub exactly` is an implicit claim of `TARGET_RENDER_VALIDATED` that has not been earned.

---

## 60. The verification kernel should therefore be evidence-producing, not verdict-producing

**Classification: recommendation — endorsed.**

The proposed shift:

```text
checker → Observation → EvidenceRecord → Proposition evaluation → Gate decision
```

with `PASS` as a derived interpretation:

```text
EvidenceRecord {
    subject_digest: sha256:...
    verifier: { name: markdown-corpus-audit, commit: ... }
    check:    { id: CORPUS.SECTION.CONTIGUITY, version: 1 }
    scope:    { documents: 33, sections: 998 }
    environment: { python: ..., markdown: ... }
    execution:   { exit_code: 0 }
    observation: { duplicates: [], gaps: [108] }
    result:      PASS
}
```

> Now PASS is merely a derived interpretation of an observation. That is much harder to fake accidentally.

Worth making explicit how this would have changed the last five passes. §0.1's bypass is invisible because the *observation* (`has_prov = False`) is discarded — only the derived verdict (`n/a`) survives. Under this model the observation would be recorded, and `n/a` would be visibly a **non-observation**, not a clean result.

Part III §0's receipt was assembled by manually correlating a commit message against eight printed lines. That is exactly the work `EvidenceRecord` automates.

---

## 61. The release gate can then become formally simple

**Classification: recommendation — endorsed.**

```text
Gate(P,E) = ACCEPT
```

only if all ten conditions hold:

```text
 1. P is declared
 2. E identifies P
 3. E identifies its subject
 4. E identifies the verifier
 5. E identifies the execution
 6. execution actually occurred
 7. execution was successful
 8. scope covers P
 9. evidence is fresh for the subject
10. all required checks passed
```

Everything else becomes `UNVERIFIED` rather than being stretched into a green/red binary.

**Applying it to §0.1**, the gate correctly refuses:

| Condition | The zero-provenance document |
|---|---|
| 1. P declared | ✅ complete provenance required |
| 2. E identifies P | ✅ |
| 3. E identifies subject | ✅ |
| 4. E identifies verifier | ❌ no verifier identity exists (§56) |
| 5. E identifies execution | ⚠️ printed, not bound (§57) |
| 6. execution occurred | ✅ |
| 7. execution successful | ✅ |
| **8. scope covers P** | ❌ **§0.1 — the document fell outside scope entirely** |
| 9. evidence fresh | ❌ no digest binding |
| 10. all required checks passed | ❌ provenance never ran |

Conditions 4, 8, 9 and 10 all fail. This is a useful cross-check: the gate as formulated **does** reject the §0.1 bypass, on four independent grounds rather than one. That is evidence the formulation is well-designed rather than over-fitted to any single defect.

---

## 62. This gives RFL-AE a much stronger state machine

**Classification: recommendation — endorsed.**

```text
DECLARED → SCHEDULED → EXECUTING
                          ├──► OBSERVED ──┬──► PASS
                          │               ├──► FAIL
                          │               └──► UNKNOWN
                          └──► ERROR
                                            PASS → EVIDENCE_BOUND → GATEABLE → RELEASED
```

with the distinctions:

```text
ERROR   ≠ FAIL
UNKNOWN ≠ PASS
PARTIAL ≠ COMPLETE
OBSERVED ≠ VERIFIED
VERIFIED ≠ RELEASED
```

These are already implicit in RFL-AE's architecture, and Parts III–V supply receipts for each:

| Distinction | Receipt |
|---|---|
| `ERROR ≠ FAIL` | §0.1/N-6 — stage 6 raises an uncaught `FileNotFoundError`; exit 1 is indistinguishable from a content finding |
| `UNKNOWN ≠ PASS` | §0.1 — `n/a` renders as a clean cell |
| `PARTIAL ≠ COMPLETE` | Part III §21 — 5 of 11 invariants corpus-wide |
| `OBSERVED ≠ VERIFIED` | §57 — environment printed but unbound |
| `VERIFIED ≠ RELEASED` | Part I §25 — closeout certifies content, not publication |

The last row is the only one without a direct execution receipt in this pass, and it is the one Part I §25 already documents.

---

## 63. Revised implementation priority

**Classification: recommendation — with one correction.**

The ordering is sound, and the P0→P4 progression from correctness through scope, evidence, self-verification, to publication authority is the right shape.

**P0 item 3 — `README 997 → 998` — is the fifth appearance of a claim disproved twice.**

It was:

| Appearance | Form | Outcome |
|---|---|---|
| Part II §8 | "the skills README is stale" | DISPROVED — 997 is a cardinality, 998 a maximum, difference = the §108 gap |
| Part III §20 | "run_all.sh cannot produce the claimed clean result" | DISPROVED by execution — all 7 stages, exit 0, every number reproduced |
| Part III §32 | conceded: *"not a defect; nothing to fix"* | — |
| Part IV §47 | reinstated as risk item 1 | flagged as RESTATED |
| Part V §63 | **P0 item 3** | **flagged again** |

The amended P0 in Part III §32 correctly removed it. It has now returned twice.

This matters beyond bookkeeping. It is the clearest available demonstration of §60's thesis: a verdict (`README is wrong`) has propagated through five passes while the underlying **observation** (`sections: 997, range: 1..998, gaps: [108]`, printed together in a single block) was never re-examined. Under an `EvidenceRecord` model, the observation would have been carried alongside the verdict and the contradiction resolved on first contact.

**Recommended P0, corrected:**

```text
P0 — Correctness defects
  1. renumber.py source-number preservation        (Part I §5, Part IV §0.1)
  2. renumber.py fence awareness                   (Part I §6, Part IV §0.2)
  3. provenance scope gate: remove has_prov        (Part V §0.1)  ← NEW, highest severity
  4. rerun current release gate
```

**Item 3 is the new entry and should rank first among P0 items.** `has_prov` currently excludes 10 of 33 documents from the provenance invariant and converts total-evidence-deletion into PASS. It is a one-line change — replace the substring test with a document-class requirement (§52) — and it closes the highest-severity defect found across five passes.

The rest of the P1–P4 ordering is endorsed without change, with one note: P1 item 9 (*canonical Markdown lexer/IR*) is the prerequisite for four separate findings (§0.1's fence-blind gate, Part II §12, Part IV §0.2, Part II §16's slug duplication). It is the highest-leverage single component in the whole plan.

---

## New findings from this pass

### N-5. `documents` and `sections` are printed but never asserted

`audit_corpus.py:94–95`:

```python
print(f"documents     : {len(files)}")
print(f"sections      : {len(all_secs)}")
```

Neither value participates in `problems`. They are the two numbers a human reader uses to confirm the corpus is intact, and the sweep guarantees neither. Directly supports §53/§54 and is the mechanism behind §55's receipt.

**Classification: PROVED.**

### N-6. Stage 6 fails by uncaught exception, so `ERROR` is reported as `FAIL`

Running `verify_closeout.py` against a tree missing its `--new` document:

```text
Traceback (most recent call last):
  File ".../verify_closeout.py", line 126, in <module>
    sys.exit(main())
  File ".../verify_closeout.py", line 58, in main
    new_t = A.read(p(a.new))
  File ".../auditlib.py", line 46, in read
    with open(path, encoding="utf-8") as fh:
FileNotFoundError: [Errno 2] No such file or directory: './RFL-LEDGER-V01.md'
```

Exit 1 — **the same exit code as a content finding**, and no `PROBLEMS` block. A driver cannot distinguish *"the closeout check found problems"* from *"the closeout checker crashed"*.

This is §36's thesis made concrete in a second script: `audit_file.py` has zero `try`/`except` (Part IV §36), and `verify_closeout.py` crashes on a missing input rather than reporting it. Both conflate verifier failure with verification failure, and `run_all.sh`'s `fail=1` collapses them further (Part IV §37).

**Classification: PROVED.**

### N-7. The `has_prov` substring test is fence-blind in the opposite direction

Demonstrated in §0.1: a document with **no** real provenance, containing the literal string only inside a fenced code example, is flagged `provenance incomplete`.

So the gate is both a false negative (zero real occurrences → no enforcement) and a false positive (occurrence inside a fence → enforcement). Same one-line root cause.

**Classification: PROVED.**

### N-8. Positive observation: the gap assertion is precisely implemented

Worth recording, because the surrounding findings concern what is *not* checked. The deliberate-gap mechanism is exactly right:

```python
expected_gaps = [int(x) for x in a.expect_gaps.split(",") if x.strip()]
if a.expect_gaps and gaps != expected_gaps:
    problems.append(f"gaps {gaps} != expected {expected_gaps}")
```

It requires equality with the **declared** set, not merely the absence of *unexpected* gaps — so both a missing expected gap and an extra one fail. It is checked only when `--expect-gaps` is supplied, which `run_all.sh` does (`--expect-gaps 108`), and it is the single assertion in the sweep that compares observed state against declared intent. It is the pattern §54's manifest would generalize to documents, sections, and ranges.

**Classification: positive.**

---

## Bottom line

Part V's architectural direction is correct, and the two recommendations that matter most — §54's `CorpusManifest` and §60's evidence-producing kernel — are now supported by execution rather than by reasoning alone.

**The single most consequential result is §0.1: the provenance invariant is non-monotonic.** Deleting 29 of 30 provenance comments from a real corpus document fails the audit; deleting all 30 passes it, because a one-line whole-file substring test (`has_prov = "<!-- source: " in text`) gates the entire invariant. This is already live: **10 of 33 corpus documents** carry `PROV = n/a` and sit permanently outside the check while the sweep reports `ALL FILES OK`. It is more severe than Part IV §0.1 because it requires only a *deletion*, and it is the purest instance of the review's recurring theme — a check that reports success when it did not run.

Confirmed as stated: §49 (limitation is documented; the normative/implementation distinction should be named), §50, §51 (33 vs 50 files measured), §52, §53, §56 (no SHA anywhere), §58 (`Must match GitHub exactly` at `auditlib.py:265`), §59–§62.

Two corrections:

1. **§57 is PARTIALLY PROVED** — `python`, `repo`, and `markdown` are printed at startup; the defect is the absence of *binding* to results, not of capture.
2. **§63 P0 item 3 is the fifth appearance** of the `README 997 → 998` claim, disproved twice and conceded in Part III §32. Replace it with the `has_prov` fix, which should rank first among P0 items.

§55 was verified by execution and is stronger in practice than in the reasoning: deleting the newest document leaves stage 3 reporting `documents : 32`, `sections : 968`, `gaps : [108]` and passing, with the deletion caught only incidentally by another document's forward link. Stage 6 does catch it eventually, via the manually-maintained README status line — and does so by crashing rather than diagnosing (N-6).

Four new findings: N-5 (`documents`/`sections` printed, never asserted), N-6 (stage 6 reports `ERROR` as `FAIL`), N-7 (`has_prov` is also a false positive inside fences), and N-8, a positive note that the `--expect-gaps` assertion is the one place the sweep compares observed state against declared intent — the exact pattern §54 generalizes.

**Recommended immediate action**, unchanged in spirit from Part IV but now different in content: remove `has_prov`. It is a one-line change that closes the highest-severity defect in the review, and it should precede the manifest work it motivates.

---

## Appendix — Part V finding index

| Part V § | Finding | Class | Verified by | Note |
|---|---|---|---|---|
| 0.1 | Provenance invariant is non-monotonic | **PROVED (receipt)** | 5-state experiment | strongest result of the review |
| 0.1b | 10 of 33 documents outside the invariant | **PROVED** | corpus receipt | already live |
| 0.1c | `has_prov` fence false positive | **PROVED (receipt)** | execution | see N-7 |
| 48 | MISSING vs MALFORMED indistinguishable | PROVED — stronger | execution | benign at section level, severe at file level |
| 49 | Only two layouts accepted | PROVED | source | documented, not named as normative |
| 50 | Identity is filename + integer | PROVED | source | Part IV §0.1 shows it misfires |
| 51 | `audit_corpus` vs `linkaudit` universes | PROVED | execution | 33 files vs 50 |
| 52 | `README.md` exclusion is implicit authority | PROVED | source | `EXCLUDE` set literal |
| 53 | `set(all_secs)` rebuilt in loop | PROVED | source | semantic half is the real finding |
| 54 | `CorpusManifest` needed | recommendation | §55 receipt | endorsed |
| 55 | Whole-document deletion undetected | **PROVED (receipt)** | mutation | stage 3 passes; stage 6 catches by crash |
| 56 | Verifier identity absent | PROVED | search | no SHA anywhere |
| 57 | Dependency identity thin | PARTIALLY PROVED | execution | printed, never bound |
| 58 | Renderer is untrusted oracle | PROVED | source | `Must match GitHub exactly` |
| 59 | Three-level render status | recommendation | — | endorsed |
| 60 | Evidence-producing kernel | recommendation | — | governs §0.1's invisibility |
| 61 | `Gate(P,E)` | recommendation | §0.1 cross-check | rejects §0.1 on 4 grounds |
| 62 | State machine | recommendation | Parts III–V | each distinction has a receipt |
| 63 | Priority order | **P0 item 3 RESTATED (5th)** | Parts II–IV | replace with `has_prov` fix |
| N-5 | `documents`/`sections` never asserted | PROVED | source | mechanism behind §55 |
| N-6 | Stage 6 reports `ERROR` as `FAIL` | PROVED | execution | traceback, exit 1 |
| N-7 | `has_prov` fence false positive | PROVED | execution | same root cause as §0.1 |
| N-8 | `--expect-gaps` correctly compares against declared intent | positive | source | pattern §54 generalizes |
