# RFL-AE `skills/` — Independent Review, Part II

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Part I:** [`rfl-ae-skills-review.md`](rfl-ae-skills-review.md) — sections 1–32
**Review date:** 2026-09-28
**Method:** reading the actual remote tree and sources on the branch, and re-executing the checks each finding depends on.

> **Numbering note.** These sections are numbered 8–19 as given in this pass. That range collides with Part I's numbering (§8 = `splice.py` fence regex, §17 = provenance checking, §18 = provenance parser boundary, §19 = `check_links()`). Part II finding numbers are **not** references to Part I sections. Where a Part II finding restates a Part I finding, the Part I section is cited explicitly.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established directly from source inspection, with the offending code path identified |
| PARTIALLY VERIFIED | Defect observed, but full impact depends on inputs not exercised here |
| LATENT | Real fragility, but structurally precluded by current corpus conventions |
| OPEN | Contract ambiguity — behavior is not wrong, but the interface is underspecified |
| MINOR | Real but low-consequence |
| **DISPROVED** | Claimed in this pass, but contradicted by the evidence on the branch |

---

## Verification summary

Everything below was re-checked against the branch tip, because the claims in this pass are stronger than Part I's and several are load-bearing for the architectural conclusion.

| Part II § | Claim | Verified? |
|---|---|---|
| §8 | `skills/README.md` is stale (997 vs 998) | **NO — DISPROVED** |
| §9 | `linkaudit.py` has unguarded `import markdown`, no `--strict` | **YES** |
| §10 | Four independent Markdown interpretations | YES, with one correction (§16) |
| §11 | `renumber.py` uses ordinal position, ignores `m.group(1)` | **YES** |
| §12 | `renumber.py` and `auditlib.py` share the fence-blind root | **YES** |
| §13 | Rust check proves cardinality, not balance | **YES** |
| §14 | `marks()` / `bar()` accept duplicate coordinates | **YES** |
| §15 | `dchain([])` and `bw([])` fail indirectly | **YES** |
| §16 | Three slug implementations; set-collapse of anchors | **YES** (collision risk is LATENT) |
| §17 | `check_links()` and `linkaudit.py` occupy different evidence domains | YES — framing, not a defect |

---

## 8. Correction: the skills README is **not** stale

This finding was checked first, because it is the only claim in this pass that is stated as a plain factual defect.

**It does not hold.** `skills/README.md` is accurate and self-consistent with the corpus.

`skills/README.md` says:

> thirty-three documents, 997 sections, 2122 text-fenced diagram blocks (995 of them reproducible from the committed generators in `diagrams/`) — across thirty-three consecutive specification pastes.

The corpus at tip `1090511a`:

| Quantity | Value | Evidence |
|---|---|---|
| Root corpus documents | **33** | 34 root `.md` blobs minus the root `README.md` |
| Max corpus section | **§998** | `RFL-LEDGER-V01.md` last heading is `## 998.`; `run_all.sh:54-55` passes `--range 970-998` |
| Deliberate gap | **§108** | root `README.md:459`; `run_all.sh:47` passes `--expect-gaps 108` |
| **Section count** | **997** | 998 − 1 (the §108 gap) |

Root `README.md:459` states it outright:

> Thirty-three specification documents covering §1–§998 (§108 does not exist in the source; the gap is preserved)

So `thirty-three documents, 997 sections` is **exactly right**. The reviewer compared a **section count** (997) against a **maximum section number** (998) and read the difference of 1 as staleness. The difference of 1 *is* the §108 gap — the very gap `run_all.sh --expect-gaps 108` exists to enforce.

```text
§1 ─────────────────────────► §998          max section number   = 998
    (§108 absent)
§1 ─────────────────────────► §998
    minus 1 missing section  ──────────────► section count        = 997

997 ≠ stale.  997 = 998 − the gap the audit already knows about.
```

**Classification: DISPROVED.**

### The residual, weaker finding that *is* real

The original concern does have a legitimate kernel — just not the one claimed.

Reading `997 sections` next to `--range 970-998` **invites** the misreading above. An unqualified cardinal in verification documentation, sitting one line away from numbers that are ordinal, is genuinely ambiguous. The fact that a careful reader made exactly this error is the evidence.

The distinction:

```text
section COUNT  → 997      (cardinality, gap-aware)
section MAX    → 998      (ordinal, gap-blind)
```

Recommendation — same as the original, but justified differently. Not "the README is stale," but "the README's numbers are not self-describing":

1. **Disambiguate in place.** Write `thirty-three documents, §§1–998 with §108 absent (997 sections)` so both quantities and their relationship are explicit.
2. **Make it checkable.** A `verify_readme_claims.py` that derives count, max, and gap from the corpus and compares against the stated numbers. This is a real gap — `run_all.sh` verifies its own `--range`/`--offset` against the corpus, but nothing verifies `skills/README.md`'s prose.

**Classification of the residual finding: MINOR — ambiguous numerical claim in verification documentation.**

Point 2 is still worth building. The Part I closeout finding (§25) is the same shape: a document makes a claim that no executable check confirms.

---

## 9. `linkaudit.py` violates its own dependency contract

**Classification: PROVED — contract inconsistency**

The other audit scripts deliberately handle a missing `markdown` package. `auditlib.py`:

```python
try:
    import markdown as _markdown
    HAVE_MARKDOWN = True
except ImportError:
    _markdown = None
    HAVE_MARKDOWN = False
```

and at line 196:

```python
if not HAVE_MARKDOWN:
    ...
```

`linkaudit.py` begins:

```python
import argparse, os, re, sys, collections
import markdown
```

with **no controlled dependency handling**.

Therefore:

```bash
python3 skills/markdown-corpus-audit/scripts/linkaudit.py
```

on an environment without `markdown` produces an **import failure** rather than `SKIPPED`.

This conflicts directly with the skill-creator rule:

> A check that cannot run must never report success.

The script does fail, so it does not create a false green result. But it does not implement the project's stated `SKIPPED` / dependency-aware contract. Confirmed at the tip: `linkaudit.py` accepts only `--root` and `--allow`, and has **no `--strict` flag at all**.

**Fix**

Use the same dependency model as `auditlib`:

```text
dependency available
    → execute

dependency unavailable
    → SKIPPED
    → explicit installation instruction
    → non-zero under strict
```

Make `linkaudit.py --strict` explicit rather than relying on unconditional failure. Note the asymmetry this creates today: `audit_file.py` encodes "2 = checks could not run" and lets `--strict` promote it (`run_all.sh:47` uses `--strict`), while `linkaudit.py` — invoked at `run_all.sh:50` — has no such channel. Two scripts in the same skill disagree about what an unrunnable check means.

---

## 10. The Markdown parser problem is now clearly systemic

**Classification: PROVED — architectural**

The deeper issue is larger than the individual `renumber.py` bug.

There are currently multiple interpretations of Markdown:

```text
                            Markdown source
                                  │
        ┌─────────────┬───────────┼───────────┬──────────────┐
        ▼             ▼           ▼           ▼              ▼
   renumber.py   auditlib.py  splice.py  linkaudit.py   GitHub GFM
   regex parser  regex parser regex parser  AST          (ground truth)
        │             │           │           │              │
        └─────────────┴─────┬─────┴───────────┘              │
                            ▼                                │
                  four interpretations                       │
                            │                                │
                            ▼                                ▼
                    semantic split-brain  ◄───────────────────┘
```

Two of those four are *not* independent — see the correction in §16.

This creates a semantic split-brain. A document can theoretically be:

```text
valid according to auditlib
invalid according to GitHub
```

or:

```text
valid according to splice
invalid according to auditlib
```

without either checker being internally inconsistent.

This is precisely the kind of problem that becomes dangerous once an AI agent starts using the skills autonomously: an agent that trusts "the audit passed" is trusting one interpretation, not the document.

---

## 11. `renumber.py` is worse than merely "not gap preserving"

**Classification: PROVED** (Part I §5 stands; this adds the general form)

Current implementation:

```python
n = 0

for line in lines:
    m = re.match(r"^## (?:§)?(\d+)\. (.*)$", line)
    if m:
        n += 1
        corpus = n + offset
```

The captured input number `m.group(1)` is **never used**.

Therefore the implementation doesn't actually perform:

```text
corpus = source + offset
```

It performs:

```text
corpus = ordinal_position + offset
```

Those are different functions.

For example, source:

```markdown
## 1. A
## 3. B
## 7. C
```

Current result:

```markdown
## 101. A
## 102. B
## 103. C
```

Claimed result:

```markdown
## 101. A
## 103. B
## 107. C
```

The current implementation therefore **cannot satisfy its own lossless provenance model for a source with gaps**.

More importantly, it can silently transform an abnormal source into an apparently normal one. The output is always contiguous. That is exactly the wrong direction for an evidence-preserving pipeline:

```text
input has a gap  →  output has no gap  →  the gap is now unrecoverable
```

Contrast with the corpus itself, where §108 is *deliberately preserved* and `run_all.sh --expect-gaps 108` enforces its preservation. The tooling enforces gap preservation at the audit layer while destroying it at the transformation layer.

---

## 12. `renumber.py` and `auditlib.py` share the same root defect

**Classification: PROVED**

Both operate on `^## ...` without Markdown lexical state. So this:

````text
```text
## 999. This is not a section
```

## 1. Real section
````

can be interpreted differently by the tooling. The fenced `## 999.` is text inside a code block, but `renumber.py` will rewrite it and `auditlib.sections()` will count it.

The correct abstraction is not:

```text
regex over lines
```

but:

```text
Markdown lexer
    │
    ├── prose
    ├── heading
    ├── fenced block
    ├── fence body
    ├── link
    ├── HTML comment
    └── other
```

Then all downstream tools consume that representation.

This is the same conclusion as Part I §18 and §29, now with the transformation layer included. Note that `renumber.py` is the script that **writes** documents while `auditlib.py` **reads** them — so the two fence-blind parsers are not merely inconsistent observers, they are a writer and a checker that share a blind spot. A fence-blind writer plus a fence-blind auditor means the defect passes both.

---

## 13. Rust checking is explicitly heuristic, but the API doesn't say so

**Classification: PROVED — specification/implementation terminology mismatch** (Part I §11)

Current:

```python
body.count("{") == body.count("}")
body.count("(") == body.count(")")
body.count("[") == body.count("]")
```

This proves only **cardinality**. It does not prove **balanced nesting**.

For example:

```rust
fn x() {
    let y = [1, 2 };
}
```

can have equal counts for some delimiters while being syntactically invalid. Likewise:

```rust
let s = "{";
```

contains a brace that isn't structural.

So the current check establishes:

```text
delimiter-count equality
```

not:

```text
Rust syntactic balance
```

The checker is useful, but its contract should say exactly what it proves.

A better name:

```python
check_rust_delimiter_counts()
```

Then eventually introduce:

```python
check_rust_lexical_balance()
```

using a small lexer that handles, before interpreting delimiters:

- normal code
- `"string"`
- `'char'`
- `r"raw string"`
- `r#"raw string"#`
- `//` comments
- `/* */` comments

A full Rust parser is not necessary for the first upgrade.

---

## 14. `marks()` and `bar()` still have an unclosed geometry invariant

**Classification: PROVED** (Part I §3, finding G-1 — now concrete)

`row()` explicitly detects overlap. `hjoin()` explicitly detects overlap.

But in `geo.py`:

```python
def marks(glyphs: Sequence[Tuple[int, str]]) -> str:
    out = ""
    prev = None
    for col, ch in sorted(glyphs):
        out = (" " * col + ch) if prev is None else out + " " * (col - prev - 1) + ch
        prev = col
    return out
```

```python
def bar(glyphs: Sequence[Tuple[int, str]], ch: str = HORIZ) -> str:
    out = ""
    prev = None
    for col, gl in sorted(glyphs):
        out = (" " * col + gl) if prev is None else out + ch * (col - prev - 1) + gl
        prev = col
    return out
```

So:

```python
marks([(10, "│"), (10, "▼")])
```

does **not** reject duplicate coordinates. The second glyph is emitted immediately after the first rather than occupying the same absolute coordinate, because `col - prev - 1 == -1` and `" " * -1 == ""`.

Likewise:

```python
bar([(10, "┌"), (10, "┬")])
```

does not represent a valid coordinate assignment.

So the geometry library currently has **asymmetric guarantees**:

| Primitive | Duplicate/overlap protected |
|---|---|
| `row` | yes |
| `hjoin` | yes |
| `marks` | **no** |
| `bar` | **no** |

That weakens the foundational invariant:

> All positions are absolute columns.

**Additional detail not previously noted.** `sorted(glyphs)` sorts tuples by `(col, ch)`. With duplicate columns, the emitted order is therefore determined by the **Unicode codepoint of the glyph**, not by input order:

```text
│ = U+2502    ▼ = U+25BC
sorted([(10, "│"), (10, "▼")])  →  [(10, "│"), (10, "▼")]     # │ first
sorted([(10, "┌"), (10, "┬")])  →  [(10, "┌"), (10, "┬")]     # ┌ first
```

So the failure is silently deterministic, which is worse than nondeterministic: the same wrong input renders identically every time, and reads as intentional.

**Recommended invariant** — for every coordinate-placement primitive:

```text
coordinate uniqueness
        +
coordinate >= 0
        +
valid glyph width
```

should be enforced uniformly.

Note also the negative-coordinate case: for the first glyph, `marks` computes `" " * col + ch`. With `col < 0`, `" " * -5 == ""`, so a negative column renders at **column 0** rather than raising.

---

## 15. `dchain([])` is undefined

**Classification: MINOR**

This function:

```python
def dchain(items: Sequence[str], c: int = 4, g: str = "↓") -> List[str]:
    out = [items[0]]
    for it in items[1:]:
        out += [" " * c + g, it]
    return out
```

assumes a non-empty sequence. There is no explicit contract saying `items must be non-empty` and no controlled exception.

Likewise `bw([])` calls:

```python
def bw(lines: Sequence[str]) -> int:
    return max(len(l) for l in lines) + 4
```

and fails indirectly with `ValueError: max() arg is an empty sequence`.

These are minor, but for a geometry library used as an invariant foundation, explicit failure is preferable:

```python
ValueError("dchain requires at least one item")
ValueError("box requires at least one content line")
```

rather than accidental `IndexError` / `ValueError` from an implementation detail. This matters because the whole point of the module is that the *error mode* is legible: Part I §2 noted the right failure shape is `incorrect geometry → exception → no artifact`, and an `IndexError` from `items[0]` is technically an exception but does not name the violated contract.

---

## 16. GitHub slugging remains an evidence boundary

**Classification: PROVED inconsistency.** With a correction to the "four interpretations" count in §10, and a downgrade of the collision risk to **LATENT**.

There are currently **three** slug implementations in the architecture:

```text
renumber.py
    github_slug()

auditlib.py
    slug()

linkaudit.py
    slug()
```

Concretely:

```python
# renumber.py:59
def github_slug(h: str) -> str:
    s = h.lower().replace("'", "")
    return re.sub(r"[^\w\- ]", "", s).replace(" ", "-")
```

```python
# auditlib.py:262 — strips punctuation via re.sub(r"[^\w\- ]", "", s)
def slug(heading: str) -> str:
    ...
```

```python
# linkaudit.py:11
def slug(h):
    s = h.lower()
    s = re.sub(r"<[^>]+>", "", s)
    out = []
    for ch in s:
        if ch.isalnum() or ch in "-_":
            out.append(ch)
        elif ch == " ":
            out.append("-")
    ...
```

`renumber.py.github_slug` and `auditlib.slug` appear equivalent. `linkaudit.slug` is a **different algorithm**: it strip-tags first and uses per-character `isalnum()` rather than a `\w` regex.

Even where they currently look equivalent, they are separately implemented. That violates a stronger principle than code reuse:

> Anchor identity is part of the repository's externally observable semantics.

If the slug algorithm changes, these can diverge silently.

### Duplicate headings — correction

The original claim was that duplicate headings collapse:

```python
anchors = {slug(h) for h in headings}
```

This **is** what `auditlib.py:281` does — confirmed. A `set` collapses duplicates, whereas GitHub appends `-1`, `-2` to repeated anchors. So:

```text
number of headings
        ≠
number of effective GitHub anchors
```

**can** occur in principle.

However, checking the corpus: duplicate heading titles were searched for across `RFL-LEDGER-V01.md`, `RFL-TRANSITION-V01.md`, and `ORCHESTRATION.md` and **none were found**. This is not a coincidence — every heading carries a unique corpus number (`## 994. …`), so the slug always begins with a distinct integer. Collision is structurally precluded by the numbering convention.

So this is a real fragility, not a live defect.

**Classification: LATENT.** It would become live the moment any unnumbered or advisory heading is added — which is precisely what the corpus does at document boundaries (root `README.md:380` and `:413` describe "an unnumbered closing section").

**Correct architecture**

```text
Heading sequence
      ↓
Canonical GFM anchor generator
      ↓
collision handling
      ↓
AnchorTable

then → link verification consumes the AnchorTable
```

This should be one shared implementation, not three.

---

## 17. `check_links()` and `linkaudit.py` verify different things

**Classification: OPEN — framing, not a defect. The current split is good; the description overclaims.**

This is not inherently bad, but it needs to be explicit.

`check_links()` is largely:

```text
source regex
+
locally computed anchors
```

`linkaudit.py` is:

```text
Markdown render
→ HTML hrefs
→ rendered headings
```

So the two checks have different evidence domains:

| Checker | Evidence domain |
|---|---|
| `check_links()` | source syntax |
| `linkaudit.py` | rendered HTML |
| GitHub | actual GFM rendering |

That is actually a good architecture if the distinction is intentional. Formalize it:

```text
SOURCE-LINK-CHECK
    proves source-level reference structure

RENDER-LINK-CHECK
    proves Markdown-rendered href structure

GITHUB-LINK-CHECK
    proves GitHub-specific anchor semantics
```

The current repository tends to describe these collectively as "link checks", which risks overclaiming. Note that "GITHUB-LINK-CHECK" is *not* a thing that exists — it is an aspiration, and the `slugger.py` golden-vector proposal (§16, Part I §21) is how it would become one.

---

## 18. The strongest next architectural move is now obvious

**Classification: recommendation.**

I would not add another isolated skill.

The next component should be:

```text
skills/
└── markdown-corpus-core/
    ├── SKILL.md
    └── scripts/
        ├── lexer.py
        ├── corpus_ir.py
        ├── headings.py
        ├── fences.py
        ├── provenance.py
        ├── anchors.py
        └── tests/
            ├── positive/
            └── negative/
```

Conceptually:

```text
Markdown
                       │
                       ▼
              ┌─────────────────┐
              │ Corpus Markdown │
              │ Lexer / IR      │
              └────────┬────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
    headings        fences         links
        │              │              │
        ▼              ▼              ▼
    numbering       splicing       anchors
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                  corpus audit
                       │
                       ▼
                 release gate
```

This becomes the semantic substrate of the skills directory.

The `tests/negative/` directory is the load-bearing part. Every finding in this review is a candidate fixture:

```text
tests/negative/
├── gap-in-source.md          → renumber must preserve §1,§3,§7 as 101,103,107
├── heading-in-fence.md       → renumber must not rewrite; audit must not count
├── duplicate-coords.py       → marks/bar must raise
├── duplicate-title.md        → anchors must disambiguate
├── ungrounded-import.py      → linkaudit must SKIP, not crash
└── closeout-no-push.md       → closeout must not certify publication
```

---

## 19. Revised maturity assessment

**Classification: assessment.**

| Layer | Status | Main issue |
|---|---|---|
| Geometry | VERIFIED / PROVISIONAL | duplicate coordinates not rejected |
| Provenance numbering | PARTIALLY VERIFIED | ordinal numbering instead of captured source numbering |
| Placeholder splice | STRONG / PROVISIONAL | limited fence grammar |
| Corpus audit | STRONG / PARTIAL | regex + heuristic parser boundaries |
| Link audit | PARTIAL | independent slug implementation + dependency handling |
| Skill creator | GOOD / STRUCTURAL | behavioral validation still limited |
| Closeout | PARTIAL | local content checked, remote publication not |
| Corpus README synchronization | **— WITHDRAWN —** | `skills/README.md` is accurate (§8) |
| Overall | SUBSTANTIALLY ENGINEERED / NOT SELF-PROVING | checker semantics are not yet unified |

The "corpus README synchronization" row is **withdrawn**. Part I §25 (closeout certifies content but not publication) remains the genuine documentation-state defect; this row was not.

---

## The important transition

**Classification: recommendation.**

The project has reached a point where adding more checks has diminishing returns.

The current failure pattern is:

```text
┌──────────────────┐
│ Markdown corpus  │
└────────┬─────────┘
         │
multiple independent parsers
         │
┌────────────┬───────┼───────┬────────────┐
▼            ▼       ▼       ▼            ▼
numbering   audit  splice  links      closeout
│            │       │       │            │
└────────────┴───────┴───────┴────────────┘
         │
possible disagreement
```

The next maturity boundary is therefore:

```text
ONE NORMATIVE CORPUS IR
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
   transformation   verification    publication
```

That would turn `skills/` from a collection of good scripts into a coherent verification substrate.

And the first release gate for that substrate should be deliberately adversarial:

```text
same input
   │
   ├── renumber
   ├── audit
   ├── splice
   ├── link resolution
   └── closeout
          │
          ▼
     all consume
     identical IR
          │
          ▼
     no semantic disagreement
```

That is the next thing to build before expanding RFL-AE's agentic architecture further.

---

## Bottom line

Part II's structural argument — **one normative corpus IR before more checks** — is correct, and is better supported than Part I's version because §11 and §12 show the split-brain spans the *write* path (`renumber.py`) as well as the read path.

The single most consequential correction: **`skills/README.md` is accurate.** The section count of 997 is `§1–§998` minus the deliberate `§108` gap, and both root `README.md:459` and `run_all.sh --expect-gaps 108` confirm it. That finding is withdrawn; only the weaker "ambiguously phrased number" observation survives.

The confirmed new findings are:

1. **§9** — `linkaudit.py` has no dependency guard and no `--strict`, contradicting `auditlib`'s `SKIPPED` contract while sitting in the same skill
2. **§11/§12** — `renumber.py` transforms ordinals and is fence-blind, and is the *writer* whose blind spot the fence-blind auditor shares
3. **§14** — `marks()` / `bar()` accept duplicate coordinates silently and deterministically
4. **§16** — three slug implementations, with `linkaudit.slug` algorithmically different from the other two

Findings §13, §15, and §17 restate or refine Part I findings and are correctly classified as refinements rather than new defects.

---

## Appendix — Part II finding index

| Part II § | Finding | Class | Part I relation | Verified at tip |
|---|---|---|---|---|
| 8 | `skills/README.md` is stale | **DISPROVED** | new | claimed stale; actually accurate (997 = 998 − §108 gap) |
| 8r | README numbers are ambiguous / unchecked | MINOR | relates to Part I §25 | yes |
| 9 | `linkaudit.py` unguarded `import markdown`, no `--strict` | PROVED | new | yes |
| 10 | Four Markdown interpretations → split-brain | PROVED | extends Part I §29 | yes (three, per §16) |
| 11 | `renumber.py` uses ordinal, ignores `m.group(1)` | PROVED | reinforces Part I §5 | yes |
| 12 | `renumber.py` + `auditlib.py` share fence-blind root | PROVED | extends Part I §18 | yes |
| 13 | Rust check proves cardinality, not balance | PROVED | = Part I §11 | yes |
| 14 | `marks()` / `bar()` accept duplicate coordinates | PROVED | resolves Part I G-1 | yes |
| 15 | `dchain([])` / `bw([])` fail indirectly | MINOR | = Part I G-3 | yes |
| 16 | Three slug implementations | PROVED | extends Part I §21 | yes |
| 16b | Duplicate-heading anchor collapse | **LATENT** | new | yes — no collisions found in corpus |
| 17 | `check_links()` vs `linkaudit.py` evidence domains | OPEN | extends Part I §19–20 | yes |
| 18 | `markdown-corpus-core` IR proposal | recommendation | = Part I §29 | n/a |
| 19 | Revised maturity table | assessment | Part I §30 | n/a |
