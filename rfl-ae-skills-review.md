# RFL-AE `skills/` — Independent Review

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Inspected at commit:** `90660542bf72b5f794463e964fb3b485471f655b` (2026-09-28 06:44 UTC)
**Review date:** 2026-09-28
**Method:** reading the actual remote tree and sources on the branch — not the GitHub page, and not the commit message's self-reported results.

> The branch tip has since advanced to `1090511a4080987168d1b17d48de88137ec1c27a`. At save time the four headline findings (P0 renumber source loss, fence-blind parsing, divergent slug implementations, closeout publication gap) were re-checked against the new tip and **all four still reproduce**. Findings not re-checked are marked as reported at `90660542`.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established directly from source inspection, with the offending code path identified |
| PARTIALLY VERIFIED | Defect observed, but full impact depends on inputs not exercised here |
| OPEN | Contract ambiguity — behavior is not wrong, but the interface is underspecified |
| MINOR | Real but low-consequence |
| PROVISIONAL | Deliberately conservative behavior whose tradeoff is documented |

The commit itself claims the complete skill suite passes its local checks: 32 documents / 968 sections / gap §108, 817 local links, 405/405 probes for the newest document, and 4/4 negative tests. **Those are repository claims, not independently executed results here.**

---

## 1. What is actually in `skills/`

The tree is compact:

```text
skills/
├── README.md
├── run_all.sh
│
├── ascii-diagram-forge/
│   ├── SKILL.md
│   └── scripts/
│       ├── examples.py
│       └── geo.py
│
├── corpus-provenance-numbering/
│   ├── SKILL.md
│   └── scripts/
│       └── renumber.py
│
├── markdown-corpus-audit/
│   ├── SKILL.md
│   ├── probes/
│   │   ├── executable-kernel.txt
│   │   ├── first-migration.txt
│   │   ├── ksir-analyzer.txt
│   │   ├── ksir-impl.txt
│   │   ├── ksir-slice.txt
│   │   ├── orchestration.txt
│   │   ├── proof-carrying.txt
│   │   ├── protocol-impl.txt
│   │   ├── protocol-kernel.txt
│   │   ├── protocol-p58.txt
│   │   ├── protocol-v01.txt
│   │   ├── rfl-evidence.txt
│   │   ├── rfl-gates.txt
│   │   ├── rfl-ledger.txt
│   │   ├── rfl-transition-v01.txt
│   │   ├── rfl-transition.txt
│   │   ├── rfl-types-v01.txt
│   │   ├── rfl-types.txt
│   │   └── semantic-layer.txt
│   └── scripts/
│       ├── audit_corpus.py
│       ├── audit_file.py
│       ├── auditlib.py
│       └── linkaudit.py
│
├── placeholder-splice/
│   ├── SKILL.md
│   └── scripts/
│       └── splice.py
│
├── skill-creator/
│   ├── SKILL.md
│   └── scripts/
│       ├── new_skill.py
│       └── validate_skill.py
│
└── spec-turn-closeout/
    ├── SKILL.md
    └── scripts/
        └── verify_closeout.py
```

There are **6 actual skills**, plus the orchestration script and corpus-specific probe corpus.

The architecture is coherent:

```text
RFL-AE SPECIFICATION INPUT
                             │
                             ▼
             corpus-provenance-numbering
                  source → corpus mapping
                             │
                             ▼
                     document authoring
                             │
                             ▼
                ascii-diagram-forge
                  deterministic geometry
                             │
                             ▼
                  placeholder-splice
                   generated artifact
                             │
                             ▼
                markdown-corpus-audit
              structural + semantic checks
                             │
                             ▼
                    spec-turn-closeout
             continuity + corpus integration
                             │
                             ▼
                         COMMIT
```

This is more than a collection of helper scripts: the skills form a **pipeline with explicit handoff contracts**.

---

## 2. `ascii-diagram-forge`

This is the strongest part of the directory conceptually.

Its central invariant is:

> all diagram coordinates are absolute columns.

`geo.py` implements:

`cen` · `row` · `marks` · `bar` · `hjoin` · `bw` · `box` · `colbox` · `flow` · `boxfix` · `dchain` · `align` · `padc` · `tree`

The important design choice is that **geometry is calculated rather than manually typed**.

For example:

```text
C = 25

                 Migration Unit
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Implementation     Contract Review
```

The source code explicitly guards against the historical failure where:

```text
accept + quarantine
```

became:

```text
acceptquarantine
```

because a calculated column landed inside an existing string. `row()` now calculates all spans first and raises on overlap.

That is a genuine invariant rather than a cosmetic convention.

### Important positive finding

`row()` is appropriately defensive:

```python
if e0 > s1:
    raise ValueError(...)
```

Likewise `hjoin()` refuses overlap.

This is exactly the right failure mode for generated geometry:

```text
incorrect geometry
      ↓
exception
      ↓
no artifact
```

rather than:

```text
incorrect geometry
      ↓
best effort rendering
      ↓
apparently successful document
```

The skill's own negative-test philosophy is therefore sound.

---

## 3. But `geo.py` has some residual weaknesses

### Finding G-1 — `marks()` does not reject duplicate columns

`marks()` assumes monotonically increasing positions after sorting:

```python
for col, ch in sorted(glyphs):
```

but **duplicate columns are not rejected**. With two glyphs at the same coordinate, the spacing calculation can produce malformed composition instead of a hard error.

That is weaker than the contract established for `row()` and `hjoin()`.

**Classification: PARTIALLY VERIFIED defect.**

Recommended invariant:

```text
same absolute column + two different glyphs = hard error
```

Likewise `bar()` should probably reject duplicate coordinates.

### Finding G-2 — `hjoin()` does not sort or validate coordinate ordering

Unlike `marks()` and `bar()`, `hjoin()` consumes the supplied sequence in caller order.

So this:

```python
hjoin([
    (30, "right"),
    (10, "left"),
])
```

does not behave like the sorted coordinate primitives.

That isn't necessarily wrong — the API may deliberately require ordered segments — but the contract does not state:

> segments must already be supplied in increasing column order.

The current documentation says "absolute columns" and calls overlap errors, but doesn't explicitly establish ordering as a precondition.

**Classification: OPEN / contract ambiguity.**

For a geometry library whose whole purpose is preventing silent layout drift, ambiguity here is undesirable.

### Finding G-3 — `box()` can produce invalid geometry for bad inputs

There is no explicit validation for:

- empty lines
- negative center columns
- impossible widths

For example, `bw([])` fails through `max()` rather than a domain-specific error. Not serious, but the library's philosophy suggests explicit geometry errors would be preferable.

**Classification: MINOR.**

---

## 4. `corpus-provenance-numbering`

The underlying model is excellent:

```text
corpus_section = source_section + offset

offset = previous_document.last_corpus_section
```

And the representation:

```markdown
## 463. Orchestration architecture
<!-- source: ORCHESTRATION.md §1 -->
```

creates **two simultaneously usable namespaces**:

```text
CORPUS: §463
SOURCE: §1
```

That is particularly valuable for the broader evidence architecture because it separates:

```text
canonical corpus position
        ≠
source identity
```

The skill also correctly identifies a subtle Markdown problem: **putting the HTML comment directly inside the heading changes the rendered heading/slug behavior.** That is a real semantic distinction, not merely formatting.

---

## 5. Important defect in `renumber.py`

This is the largest finding in the entire `skills/` directory.

The apply mode does:

```python
for line in lines:
    m = re.match(r"^## (?:§)?(\d+)\. (.*)$", line)
    if m:
        n += 1
        corpus = n + offset
```

It therefore does **not** actually preserve source numbering. It preserves **heading order**.

Suppose the source is:

```markdown
## 1. First
## 3. Third
## 4. Fourth
```

The tool generates:

```markdown
## 463. First
<!-- source: X §1 -->

## 464. Third
<!-- source: X §2 -->

## 465. Fourth
<!-- source: X §3 -->
```

**The original §3 has become source §2.**

That directly conflicts with the skill's stated invariant:

> "source numbering losslessly recoverable"

and:

```text
corpus − offset == source
```

This is a real semantic defect.

The correct transformation must use the **actual source number captured by the regex**:

```python
corpus = source_number + offset
```

not:

```python
corpus = heading_sequence + offset
```

This matters especially because the skill explicitly says **source gaps can exist** — and RFL-AE itself has deliberate gap §108.

So the implementation's most important promise — lossless source numbering — is not guaranteed by apply.

**Classification: PROVED by source inspection.**

This is exactly the kind of defect that a checker should catch but currently does not.

---

## 6. Second `renumber.py` defect: fenced code

`apply()` scans every line. It does not know whether it is currently inside:

````text
```text
## 123. Example
```
````

Consequently a numeric `## ...` line **inside a fenced block** can be rewritten.

That is a classic source-transformation boundary violation:

```text
Markdown heading
      ≠
text that merely resembles a Markdown heading inside code
```

The tool should lex Markdown fences before transforming headings.

**Classification: PROVED.**

This is particularly important in RFL-AE because the corpus contains large amounts of embedded Rust and generated diagrams.

---

## 7. `placeholder-splice`

This skill has a very strong trust boundary.

Its pipeline is:

```text
TARGET.md
   │
   ├── @@KEY@@
   │
   ▼
diagram directory
   │
   ├── DKEY.txt
   │
   ▼
preflight
   ├── missing?
   ├── orphan?
   ├── duplicate?
   │
   ▼
substitution
   │
   ▼
exact fenced-body verification
   │
   ▼
write
```

The most important improvement is that it does not merely check:

```text
placeholder count == diagram count
```

It checks that **the resulting diagram body is the exact body of a classified fenced block**.

That directly addresses the historical failure mode:

```text
diagram generated
       ↓
placeholder substituted
       ↓
not actually fenced
       ↓
Markdown renders it differently
       ↓
naive structural checker says OK
```

This is a very good example of **verification against the actual semantic failure mode**.

---

## 8. One residual issue in `splice.py`

The fence regex is:

```python
r"^```(\w*)\n(.*?)\n^```"
```

This is intentionally narrow. It does not cover all Markdown fence forms, such as:

````markdown
```text {.class}
```
````

or longer fences:

``````text
````text
...
````
``````

or some valid indentation variants.

That isn't necessarily a bug **for the frozen RFL-AE document dialect**, but the skill description presents it as a general Markdown mechanism.

**Classification: OPEN — dialect limitation.**

The skill should either:

1. explicitly define its supported fence grammar, or
2. implement a proper fence scanner.

Given the "interface contracts are theorems" approach, option 1 is sufficient if the dialect is deliberately frozen.

---

## 9. `markdown-corpus-audit`

This is the largest and most interesting component.

It checks several independent layers:

```text
                   Markdown source
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
    numbering          fences           Rust
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                      render
                         │
                         ▼
                    structure
                         │
                         ▼
                    provenance
                         │
                         ▼
                       links
                         │
                         ▼
                      probes
```

That separation is good.

The particularly important distinction is:

```text
source-level checks
        ≠
render-level checks
        ≠
structural diagram checks
        ≠
provenance checks
        ≠
link checks
```

This is consistent with the evidence architecture used elsewhere in the project.

---

## 10. `audit_file.py` has good failure semantics

The strict mode behavior is exactly right:

```text
markdown unavailable
       ↓
SKIPPED
       ↓
--strict
       ↓
FAIL
```

rather than:

```text
markdown unavailable
       ↓
skip
       ↓
green
```

That is one of the strongest design decisions in the repository. The code explicitly returns:

```text
2 = checks could not run
```

and strict mode turns that into a failure. This implements the principle

> NO EVIDENCE → NO VERIFIED CLAIM

unusually well.

---

## 11. Major weakness: Rust balance is only counting delimiters

`check_rust_balance()` checks:

```python
body.count("{") == body.count("}")
body.count("(") == body.count(")")
body.count("[") == body.count("]")
```

This does **not** establish balanced nesting.

For example:

```text
{]
```

has:

```text
{ = 1     } = 0
[ = 0     ] = 1
```

so it fails. But:

```text
{ [ } ]
```

has equal counts for every delimiter and still has invalid nesting.

Likewise, delimiters inside Rust strings/comments are counted as code delimiters.

Therefore:

```text
delimiter counts
       ≠
Rust syntactic balance
```

The check is useful as a cheap heuristic, but its description currently makes it sound stronger than it is.

**Classification: PROVED limitation.**

Rename the metric:

```text
rust_delimiter_counts
```

or implement a small lexical scanner that ignores:

- strings
- raw strings
- character literals
- line comments
- block comments

and tracks nesting. A full Rust parser is unnecessary for this specific invariant.

---

## 12. Another audit limitation: fence counting

`check_fences()` uses:

```python
text.count("```")
```

This is a very blunt balance test. It can count literal backticks occurring in prose or code and can miss other valid fence forms.

Again, within the RFL-AE dialect it may be sufficient, but it is not a general Markdown fence parser.

**Classification: PROVED limitation.**

The better architecture is **one shared fence scanner** used by:

- `auditlib`
- `splice`
- probe handling
- render extraction

rather than several independent regex interpretations.

---

## 13. `check_box_widths()` is intentionally narrow

This checker is good at catching:

```text
┌────────────┐
│ content    │
└───────────┘
```

but it only recognizes the specific pattern:

```text
top border
following │ rows
```

It doesn't establish that:

- every `┌` has a matching `┐`
- every box has a matching bottom
- corners are semantically compatible
- internal junctions are valid
- multiple boxes don't collide

That is not necessarily a defect. It should simply be understood as a **partial structural invariant**.

This is where the README's warning is particularly important:

> read the checker's coverage before trusting its verdict.

That principle is correct.

---

## 14. `check_orphans()` is deliberately conservative

This is actually a good example of avoiding false positives.

It doesn't say:

```text
every ▼ needs a connector above and below
```

Instead it considers neighboring rows and only reports when an adjacent row contains connectors but nothing aligns. That correctly accommodates ordinary:

```text
label
  ↓
label
```

chains.

The tradeoff is documented:

> a connector can pass if either neighbor aligns.

So a dangling endpoint can remain undetected. That's acceptable **if** the invariant is explicitly only adjacency consistency.

**Classification: PROVISIONAL but well documented.**

---

## 15. `check_offcentre()` has the right semantic definition

The code checks:

```text
does ▼ land on a label character?
```

rather than:

```text
is every whitespace-delimited token mathematically centered?
```

That distinction is important. A label such as:

```text
Agent A
```

is not one token geometrically, and requiring every token to be centered would produce false positives. The implementation therefore aligns well with the intended semantic invariant.

---

## 16. `check_ascii_substitution()` is appropriately scoped

It does not prohibit ASCII art globally. Instead:

```text
ASCII-only source diagram
       → allowed

box-drawing diagram
       +
ASCII "|" accidentally inserted
       → defect
```

That is exactly the correct distinction. Otherwise the checker would destroy source fidelity in older corpus documents.

---

## 17. Provenance checking is strong

`check_provenance()` correctly allows:

```text
heading
comment
```

and requires the comment to be the **next nonblank line**. It also derives the offset when one isn't supplied.

The critical invariant:

```text
corpus − source = constant offset
```

is directly tested. This is substantially better than merely checking:

```text
number of comments == number of headings
```

because it verifies the **mapping itself**.

---

## 18. But provenance checking inherits the `##` parser boundary

`sections()` is:

```python
re.match(r"^## (\d+)\. (.*)$", line)
```

and therefore also operates on raw text without understanding fences.

So a Rust/text block containing:

```markdown
## 123. Something
```

can be interpreted as a document section. This affects:

- numbering
- provenance
- corpus counts
- link anchors

The same fundamental problem appears in several components:

```text
RAW REGEX
   │
   ├── numbering
   ├── provenance
   ├── links
   └── fences
```

A shared Markdown lexical layer would eliminate this class of errors.

**This is the biggest architectural improvement to make to the audit subsystem.**

---

## 19. `check_links()` has an important weakness

The raw-source checker does:

```python
md_links = re.findall(r"\]\(([^)#]+\.md)\)", text, re.M)
```

This only handles relatively simple Markdown links. It doesn't comprehensively parse:

- titles
- escaped parentheses
- reference links
- URLs containing `.md`
- query strings
- nested constructs

Again, it is adequate for the repository's controlled dialect, but should be **described as such**.

---

## 20. `linkaudit.py` is conceptually better

The dedicated linker renders Markdown first and examines HTML `href`s.

That's an important improvement:

```text
raw source
   ↓
regex
```

can accidentally inspect links inside code fences. Whereas:

```text
Markdown
   ↓
render
   ↓
HTML
   ↓
href
```

tracks what the document actually exposes.

It also correctly resolves:

```text
OTHER.md#fragment
```

relative to the containing document. That's a significant correctness detail.

---

## 21. But `linkaudit.py` has a slug mismatch risk

Its slug implementation differs from `auditlib.slug()`.

`auditlib` uses:

```python
re.sub(r"[^\w\- ]", "", s)
```

while `linkaudit.py` uses:

```python
ch.isalnum() or ch in "-_"
```

These are not guaranteed to produce identical behavior for all Unicode characters. More importantly, **neither is actually GitHub's full slugging algorithm in all cases.** The repository itself acknowledges this risk in places.

This means the system has:

```text
slug implementation A
slug implementation B
GitHub's actual slugger
```

That violates an otherwise strong single-source-of-truth principle.

**Classification: PROVED architectural inconsistency.**

There should be exactly **one** slug implementation. Preferably `slugger.py`, with golden test vectors captured from actual GitHub rendering.

---

## 22. `skill-creator`

This is a good meta-layer. It enforces:

```text
SKILL.md exists
       +
frontmatter valid
       +
name == directory
       +
description meaningful
       +
scripts compile
       +
referenced python paths exist
       +
Verification section exists
```

That makes the skill directory self-describing and mechanically checkable.

The `new_skill.py` scaffold also embeds the expected structure:

```text
Purpose
When to use
When not to use
Quick start
Invariants
Traps
Verification
```

This is exactly the right minimum structure for a repository-governed skill.

---

## 23. `validate_skill.py` limitation

It only byte-compiles scripts:

```python
py_compile.compile(...)
```

It does not execute them. Therefore:

```text
compiles
```

is not:

```text
works
```

The README correctly separates compilation from the individual skill verification, but the wording

> "every skill is well-formed"

is stronger than "all scripts compile".

A future `validate_skill.py` could require:

```text
SKILL.md
  ↓
declared positive test
  ↓
declared negative test
  ↓
execute both
```

That would turn skill validation from **structural** validation into **behavioral** validation.

---

## 24. `spec-turn-closeout`

This skill closes a subtle governance gap. It checks:

```text
previous document
        ↓
forward link

README
        ↓
new document listed

README status
        ↓
derived corpus count/max

new document
        ↓
previous document provenance
```

This is valuable because the normal Markdown audit cannot establish corpus integration. The distinction is:

```text
document is valid
       ≠
document is integrated into corpus
```

Correct.

---

## 25. But closeout's implementation does not verify commit/push

The SKILL says the ritual includes:

```text
commit
push
confirm remote SHA
```

Yet `verify_closeout.py` only checks the four content conditions. Its own docstring actually says:

> The close-out for every new document is:
> 1. forward link
> 2. README
> 3. README status
> 4. provenance

So there is a mismatch between the **SKILL.md contract** and the **verify_closeout.py implementation**. The skill describes repository-history verification, but the checker doesn't verify:

- commit message
- current commit
- remote branch
- remote SHA
- push state

This is particularly important given the project's own rule:

> "Assuming a push succeeded" is a trap.

**Classification: PROVED contract gap.**

It should either:

1. rename the skill's ritual to "content closeout", **or**
2. add a separate `verify_remote_closeout.py` capable of checking Git state.

Given the evidence model, prefer separating:

```text
ContentCloseout
RemotePublicationVerification
```

because local filesystem state and remote Git state are **different evidence domains**.

---

## 26. `run_all.sh`

The orchestration is sensible:

1. geometry
2. skill validation
3. corpus audit
4. cross-corpus links
5. newest document
6. closeout
7. negative tests

The negative tests are particularly important. The final gate:

```text
every negative test MUST fail
```

is much stronger than merely having positive tests. The intended logic is:

```text
known-good → must pass
known-bad  → must fail
```

That gives the checker some behavioral evidence.

---

## 27. But `run_all.sh` itself contains a subtle reproducibility issue

It accepts:

```bash
./skills/run_all.sh [python]
```

and uses the supplied interpreter for most Python stages, but geometry explicitly invokes:

```bash
python3
```

rather than:

```bash
$PY
```

So:

```bash
./skills/run_all.sh .venv/bin/python
```

doesn't actually make the whole pipeline use that interpreter. That means the advertised interpreter override is **only partial**. If the system depends on a particular Python environment, this can produce:

```text
stage 1     → system Python
stage 2+    → selected Python
```

**Classification: PROVED minor defect.** Use `$PY` consistently.

*(Re-checked at tip `1090511a`: line 37 still hardcodes `python3` while line 12 defines `PY="${1:-python3}"`.)*

---

## 28. The most important architectural observation

The `skills/` directory has evolved around **real failure incidents**, not hypothetical style rules. That is visible throughout:

- `acceptquarantine`
- 37 unfenced diagrams
- 17 false orphan reports
- 2 phantom off-centre reports
- 52 silently skipped probes
- hyphenated README number
- wrong relative link resolution
- `/tmp` toolchain loss

This is good engineering practice. The strongest pattern is:

```text
DEFECT
  ↓
reproduce
  ↓
identify failure mechanism
  ↓
write invariant
  ↓
write checker
  ↓
write negative test
  ↓
record trap
```

That is much more credible than simply adding more validators.

---

## 29. The main weakness: duplicated parsers

The common root of most residual weaknesses is that several scripts **independently understand fragments of Markdown**:

```text
renumber.py
   └── regex headings

auditlib.py
   ├── regex headings
   ├── regex fences
   ├── regex links
   └── regex provenance

splice.py
   └── regex fences

linkaudit.py
   ├── Markdown renderer
   └── separate slugger
```

This creates **multiple interpretations of the same document**.

The architecture should evolve toward:

```text
Markdown document
                           │
                           ▼
                  ┌─────────────────┐
                  │ Markdown Lexer  │
                  │ / Corpus IR     │
                  └────────┬────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
       headings          fences            links
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                     common semantics
                           │
       ┌───────────────┬───┴──────────────┬──────────┐
       ▼               ▼                  ▼          ▼
   numbering       provenance          splice      audit
```

This would eliminate an entire class of **"checker disagrees with checker"** failures.

---

## 30. Verification status

| Component | Assessment | Main reason |
|---|---|---|
| `ascii-diagram-forge` | STRONG / PROVISIONAL | Good invariant-driven geometry and negative tests |
| `corpus-provenance-numbering` | PARTIALLY VERIFIED | `apply()` does not actually preserve source gaps |
| `placeholder-splice` | STRONG / PROVISIONAL | Excellent exact-fenced-body assertion |
| `markdown-corpus-audit` | STRONG / PARTIAL | Broad coverage, but many regex/heuristic boundaries |
| `skill-creator` | GOOD / STRUCTURAL | Strong schema validation, limited behavioral validation |
| `spec-turn-closeout` | PARTIAL | SKILL promises remote verification that checker doesn't perform |
| `run_all.sh` | GOOD / MINOR DEFECT | Strong sequencing/negative tests; interpreter inconsistency |
| **Overall** | **SUBSTANTIALLY ENGINEERED, NOT YET SELF-PROVING** | The checker suite itself has unverified semantic boundaries |

The repository's own latest commit says all stages passed, but that is evidence that the author **claims** those runs occurred; it is not the same thing as independently executing the branch's scripts. The branch is also unprotected, and the latest commit is unsigned (`verification.verified=false`).

---

## 31. Priority findings

Fixes, in order:

### P0 — `renumber.py` apply

Change:

```python
n += 1
corpus = n + offset
```

to use the captured source number:

```python
source_n = int(m.group(1))
corpus = source_n + offset
```

and explicitly reject duplicate/non-monotonic source headings if that is the intended input contract. Also make it **fence-aware**.

### P1 — Introduce one Markdown lexical layer

At minimum `markdown_ir.py` providing:

- `sections()`
- `fences()`
- `headings()`
- `links()`
- code spans

Then all skills consume the same interpretation.

### P1 — One canonical GitHub slug implementation

Remove the two independent implementations. Add golden vectors:

```text
heading
expected GitHub anchor
```

### P1 — Upgrade Rust delimiter checking

Replace character counting with a lightweight lexical stack.

### P2 — Make closeout semantics honest

Split `verify_closeout.py` into:

```text
verify_content_closeout.py
verify_remote_publication.py
```

or extend it with explicit Git evidence.

### P2 — Harden geometry primitives

Reject, according to an explicit API contract:

- duplicate coordinate
- negative coordinate
- empty box
- unsorted `hjoin`

### P3 — Behavioral skill validation

Make `skill-creator` require executable:

```text
positive test
negative test
```

rather than merely Python compilation.

---

## 32. Overall architecture

The interesting conclusion is that RFL-AE's `skills/` directory is already becoming a small **verification framework** rather than merely a toolbox.

Its governing philosophy is essentially:

```text
AUTHORING
                    │
                    ▼
             GENERATED ARTIFACT
                    │
                    ▼
             STRUCTURAL CHECK
                    │
                    ▼
             RENDER CHECK
                    │
                    ▼
             PROVENANCE CHECK
                    │
                    ▼
               LINK CHECK
                    │
                    ▼
              NEGATIVE TEST
                    │
                    ▼
             CORPUS INTEGRATION
                    │
                    ▼
              PUBLICATION
```

The next maturity step should therefore **not** be adding more isolated checks. It should be making the existing checks share a **single normative document/Markdown IR and evidence model**.

That would turn the current situation:

```text
many good checkers
        +
some duplicated parsing
        +
some heuristic boundaries
```

into:

```text
one normative interpretation
          ↓
many independent projections/checkers
          ↓
independent evidence
          ↓
release gate
```

That is much closer to the RFL-AE architecture being built elsewhere.

---

## Bottom line

The `skills/` directory is substantially real engineering, with several unusually good failure-driven invariants. Four concrete implementation/contract weaknesses are worth fixing rather than merely polishing:

1. **the source-number loss in `renumber.py`** (§5)
2. **fence-blind transformations/parsing** (§6, §18)
3. **divergent slug implementations** (§21)
4. **the closeout checker not verifying the publication portion of its own stated ritual** (§25)

The rest are mostly deliberately bounded heuristics whose limitations are already documented — which is a much healthier situation than hidden checker assumptions.

---

## Appendix — finding index

| # | Finding | Class | Still present at tip `1090511a`? |
|---|---|---|---|
| G-1 | `marks()` does not reject duplicate columns | PARTIALLY VERIFIED | not re-checked |
| G-2 | `hjoin()` ordering precondition undocumented | OPEN | not re-checked |
| G-3 | `box()` unvalidated bad inputs | MINOR | not re-checked |
| 5 | `renumber.py` apply loses source numbers | PROVED | **yes** |
| 6 | `renumber.py` rewrites fake headings inside fences | PROVED | **yes** |
| 8 | `splice.py` fence regex is dialect-narrow | OPEN | not re-checked |
| 11 | Rust "balance" is delimiter counting | PROVED limitation | not re-checked |
| 12 | Fence counting via `text.count("```")` | PROVED limitation | not re-checked |
| 18 | Provenance/`sections()` is fence-blind | PROVED | **yes** |
| 19 | `check_links()` regex is dialect-narrow | OPEN limitation | not re-checked |
| 21 | Two divergent slug implementations | PROVED inconsistency | **yes** |
| 23 | `validate_skill.py` compiles but does not execute | PROVED limitation | not re-checked |
| 25 | Closeout checks content only, not publication | PROVED contract gap | **yes** |
| 27 | `run_all.sh` hardcodes `python3` for geometry | PROVED minor defect | **yes** |
