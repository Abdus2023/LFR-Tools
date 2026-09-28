# RFL-AE `skills/` — Independent Review, Part VI

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Parts I–V:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md) · [`part5`](rfl-ae-skills-review-part5.md)
**Review date:** 2026-09-28

> **Method.** Verified by execution. §68 required constructing a nested skill layout, §69 required a six-case frontmatter probe, §70 required counting every invocation form across the corpus, and §65 required constructing a vacuously-verified skill.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection |
| **RESTATED** | Already raised in an earlier part, appearing again |
| LATENT | The vulnerability is real and demonstrated, but the current corpus does not exhibit it |
| recommendation | Forward-looking design proposal |

---

## 0. Verification summary

| Part VI § | Claim | Verified? |
|---|---|---|
| 64 | `validate_skill.py` does not verify scripts "run" | **RESTATED (2nd)** — Part III §26; but newly found in frontmatter |
| 65 | Structural self-referential validation | **PROVED as mechanism / LATENT in effect** — corpus Verification sections are substantive |
| 66 | `SkillManifest` should separate claims from checks | recommendation — endorsed |
| 67 | Verification sections should be executable | recommendation — endorsed |
| 68 | Discovery assumes flat one-level topology | **PROVED by execution** |
| 69 | Frontmatter parser is not YAML | **PROVED, with a correction** — the cited example works; other cases break |
| 70 | Command-reference checker is pattern-limited | **PROVED but LATENT** — all 27 corpus invocations match |
| 71 | Four kinds of checker | recommendation — endorsed |
| 72 | Verification coverage matrix | recommendation — endorsed, measured matrix supplied below |
| 73 | Mutation testing the validator | recommendation — endorsed |
| 74–78 | Recursive kernel, bootstrap trust, object model, kernel axiom | recommendation — endorsed |
| 79 | Epistemic composition is the deepest risk | endorsed |

---

## 0.1 New finding: Rule 3 is declared, unenforced, and unmet by 2 of 6 skills

This is the strongest result in this pass, and it was not in the reviewed text. It bears directly on §65's self-attestation argument, and it is a sharper instance of it.

`skill-creator/SKILL.md` states an explicit rule:

> **3. Ship a negative test.** A checker that has never been shown a defect is unverified. Every audit skill here is demonstrated against a deliberately broken fixture, and the expected findings are listed. When you add a check, add the input that trips it.

And the rule is not merely prose — it is embedded in the skill's **frontmatter `description`**, which is the discovery surface an agent matches against when deciding whether to load the skill:

```yaml
description: Author, validate, and maintain skills in this repository. Use when
  creating a new skill from a repeated workflow, restructuring an existing skill,
  or checking that a skill's scripts actually run and fail loudly. Establishes the
  SKILL.md format, the scripts/ layout, and the rule that every skill must ship a
  negative test.
```

So the toolchain advertises the rule as one of the three things it establishes. Three measurements:

```text
1. Does validate_skill.py enforce it?
   → 7 assertions total, listed below. NONE checks for a negative test.

2. Does any skill ship a negative fixture in its own directory?
   → find skills/*/ -iname "*neg*" -o -iname "*fixture*" -o -iname "*test*"
     ascii-diagram-forge:          NONE
     corpus-provenance-numbering:  NONE
     markdown-corpus-audit:        NONE
     placeholder-splice:           NONE
     skill-creator:                NONE
     spec-turn-closeout:           NONE

3. Is the rule met in substance?
   → partially, and not where the rule is needed most.
```

The complete assertion set in `validate_skill.py`:

```text
:47  SKILL.md has no YAML frontmatter
:50  frontmatter name != directory
:53  description too short
:55  description never says WHEN to use it
:57  no '## Verification' section          ← existence only, see §65
:70  scripts/{f} does not compile
:76  SKILL.md references missing {rel}
```

**None of the seven concerns a negative test.**

### Substance versus mechanism

To be fair: the negative tests **do exist**, in `run_all.sh` stage 7, and they are good. `BROKEN.md` plants eight defects, and four skills' scripts are demonstrated against deliberately broken fixtures — `audit_file.py`, `splice.py`, `linkaudit.py`, and `verify_closeout.py`.

But that coverage is **centralized in the orchestrator**, not co-located with the skills, and it leaves two skills uncovered:

| Skill | Negative test | Where |
|---|---|---|
| `markdown-corpus-audit` | ✅ | `run_all.sh` stage 7 (`BROKEN.md`) |
| `placeholder-splice` | ✅ | `run_all.sh` stage 7 |
| `spec-turn-closeout` | ✅ | `run_all.sh` stage 7 |
| `ascii-diagram-forge` | ✅ in substance | 12 self-tests incl. `row() raises on overlap` |
| **`corpus-provenance-numbering`** | ❌ **none anywhere** | — |
| **`skill-creator`** | ❌ **none anywhere** | — |

The two skills with no negative test are exactly the two that fail Part IV §41/N-2 and §73:

- **`corpus-provenance-numbering`** is the only script that *writes* corpus documents, and Part IV §0.1 showed it fabricates provenance that the audit then certifies.
- **`skill-creator`** is the meta-skill — the one whose job is enforcing trustworthiness — and §73 is precisely the recommendation that it be mutation-tested.

So the pattern is not random. **Both untested components are the ones whose correctness the rest of the system depends on.** That is the structural form of the recursion problem §74–§75 raise: the bootstrap layer is the least verified layer.

### Why this matters more than §65 as stated

§65 argues that a skill could satisfy the `## Verification` requirement vacuously. True, and demonstrated below — but no current skill does so, and the actual Verification sections are substantive.

This finding is different in kind: **the rule is asserted in the machine-read discovery metadata, stated as universally quantified ("every skill must ship a negative test"), violated by two skills, and mechanically unenforceable by the validator that the rule instructs you to run.**

```text
skill-creator/SKILL.md      "every skill must ship a negative test"
        │
        ▼
skill-creator/scripts/validate_skill.py
        │
        └── 7 assertions, none about negative tests
                    │
                    ▼
        validate_skill.py skills/  →  "6 skill(s) valid"
                    │
                    ▼
        The rule is unenforced and the gap is invisible.
```

This is the same defect class as Part V §0.1 (`has_prov`) and Part III §21 (coverage asymmetry): **a declared guarantee that the mechanism cannot detect the absence of.** It is the recurring finding of this review, now located in the component responsible for preventing it.

**Classification: PROVED — declared rule, unenforced by the validator, unmet by 2 of 6 skills, and the 2 are the load-bearing ones.**

---

## 64. `validate_skill.py` is not actually validating that scripts "run"

**Classification: RESTATED — second appearance. Part III §26 reported this verbatim; the new element is where the overclaim lives.**

The docstring's first line:

```python
"""validate_skill.py — check a skill is well-formed and its scripts run.
```

Six lines later, the same docstring's bullet list:

```text
  * every scripts/*.py byte-compiles
```

And the implementation:

```python
py_compile.compile(fp, cfile=os.path.join(td, "x.pyc"), ...)
```

Part III §26 stated exactly this and recommended the one-line fix (`its scripts compile`). The finding's classification and remedy are identical and correct.

### The new element, which is worth recording

The overclaim is not confined to a module docstring. It also appears in `skill-creator/SKILL.md`'s **frontmatter description**:

```yaml
description: ... or checking that a skill's scripts actually run and fail loudly ...
```

That field is not documentation. Per the skill's own layout section:

> The description is the discovery surface. A description that only says what the skill *is* will not get loaded at the right moment; include the trigger.

So the false claim — *scripts actually run* — is in the text an agent uses to decide whether to invoke this skill, and in the text a human reads to decide whether to trust it. A docstring overclaim misleads a reader who opens the file; a frontmatter overclaim misleads every agent and reader who never opens it.

The fix is therefore two lines, not one: the docstring (`scripts compile`) and the frontmatter (`checking that a skill's scripts compile, and that every skill ships a negative test` — which, per §0.1, would then be false and would need implementing).

---

## 65. The validator is structurally self-referential

**Classification: PROVED as mechanism — LATENT in effect, with one substantive consequence.**

The mechanism is confirmed by execution. A skill containing:

```markdown
---
name: trivial
description: Use this when you want to demonstrate self-attestation in the validator.
---

# trivial

## Verification

This skill is verified. Trust me.
```

validates as:

```text
ok    trivial

1 skill(s) valid
EXIT: 0
```

The check is:

```python
if not re.search(r"^## Verification", text, re.M):
    errs.append(f"{name}: no '## Verification' section (skill-creator rule 3)")
```

A heading match. The content is never inspected. So the self-attestation boundary the finding describes is real:

```text
SKILL.md
   │
   ├── claims capability
   └── describes verification
            │
            ▼
     validate_skill.py
            │
            ▼
        "valid"
```

### Fairness correction

The finding's hypothetical — *"a skill can satisfy the requirement by merely writing `## Verification\n\nThis skill is verified.`"* — is **true of the mechanism** and **false of the corpus**. All six skills' Verification sections are substantive, containing real commands with expected outputs:

```text
ascii-diagram-forge      python3 .../examples.py  — "Expected tail: all self-tests passed, exit 0"
corpus-provenance-numbering  plan reproduces the shipped blockquote exactly
markdown-corpus-audit    --expect-gaps 108 — "Expected: 33 documents, 997 sections, range 1..998"
placeholder-splice       round-trip on a fixture with a planted defect
skill-creator            validate_skill.py over skills/
spec-turn-closeout       the real close-out passes
```

So this is a LATENT vulnerability, not a live one. It should be classified as *mechanism permits vacuity; current corpus does not exercise it* — the same shape as §70.

### The substantive consequence, which the finding does not mention

Prose verification is not inert. `markdown-corpus-audit`'s Verification section states:

> Expected: 33 documents, 997 sections, range `1 .. 998`, no duplicates

Those are **load-bearing numbers in prose that no check verifies** — and they are the same numbers whose misreading generated three false findings across Parts II–IV (the `997` vs `998` cardinality/maximum confusion). Part III §32 and Part V §63 both recommended disambiguating them; §65 explains why they were ambiguous in the first place: they live in a document the toolchain reads only for a heading.

**Classification: PROVED as mechanism, LATENT in effect.** The remedy (§66–§67) is endorsed.

---

## 66. A `SkillManifest` should separate claims from checks

**Classification: recommendation — endorsed.**

The proposal is correct, and §0.1 supplies the motivating case. The current arrangement derives the verification contract from prose that is only checked for existence. A manifest inverts this:

```yaml
skill:
  id: markdown-corpus-audit
  version: "1"

checks:
  - id: sections.contiguous
    implementation: auditlib.check_contiguity
    scope: document
    required: true

  - id: fences.balanced
    implementation: auditlib.check_fences
    scope: document
    required: true

  - id: rust.delimiters
    implementation: auditlib.check_rust_balance
    scope: document
    required: true
```

enabling the question the current system cannot ask:

```text
Does every declared check have:
    implementation?
    positive fixture?
    negative fixture?
    execution?
    result?
```

Applied to the current corpus, that question would immediately surface §0.1's gap (`corpus-provenance-numbering` declares no negative fixture) and Part III §21's (checks declared with `scope: document` that never run corpus-wide).

> Now prose is documentation. The manifest is the machine contract.

This is exactly right, and it is the same remedy as Part V §54's `CorpusManifest` applied one level up. Both replace a hand-maintained prose claim with a declared, checkable structure.

---

## 67. Verification sections should become executable declarations

**Classification: recommendation — endorsed.**

```text
SKILL.md
   │
   └── human explanation

SkillManifest
   │
   ├── claims
   ├── checks
   ├── fixtures
   ├── dependencies
   └── expected results
```

> `SKILL.md` ≠ verification authority. The authority becomes the manifest + executable verifier.

Endorsed. Worth noting the two artifacts serve genuinely different audiences and neither should be removed: `SKILL.md` is the **discovery surface** for an agent deciding whether to load the skill, and the manifest is the **machine contract** for the verifier. The defect today is not that prose exists — it is that prose is the *only* authority (§65).

---

## 68. `validate_skill.py` has another subtle scope issue

**Classification: PROVED by execution.**

The finding is correct about the code:

```python
if os.path.basename(os.path.abspath(target)) == "skills":
    targets = [os.path.join(target, d) for d in sorted(os.listdir(target))
               if os.path.isdir(os.path.join(target, d))]
```

One level, no recursion, no manifest. And the finding's predicted consequence is confirmed exactly. Constructing:

```text
skills/experimental/skill-X/
    SKILL.md          (valid frontmatter, valid name, ## Verification)
    scripts/x.py
```

and running the validator over `skills/`:

```text
ok    ascii-diagram-forge
ok    corpus-provenance-numbering
FAIL  experimental
        - experimental: no SKILL.md
ok    markdown-corpus-audit
ok    placeholder-splice
ok    skill-creator
ok    spec-turn-closeout

1 problem(s)
EXIT: 1
```

Three distinct wrong behaviours, all as predicted:

1. `experimental` is treated as a skill and reported **FAIL** for lacking `SKILL.md`.
2. `skill-X` — which is a fully valid skill — is **never discovered**.
3. The run **exits 1**, so a legitimate reorganization of the skills directory breaks stage 2 of the release gate.

### Note that this is worse than "unstated topology contract"

The finding frames it as an unstated contract, which is right. The additional point is the **failure direction**: a nesting change produces a loud, misattributed failure (`experimental: no SKILL.md`) rather than a silent omission. That is the good direction — and it is why this is MINOR rather than severe, in contrast to Part V §0.1's silent bypass.

Recommended, per the finding:

```text
SkillLayout = flat
```

or manifest-driven discovery. For a verification framework, manifest-driven is better: it makes the set of skills a **declared** value rather than a directory-listing consequence, which is the same principle as §66 and Part V §54.

**Classification: PROVED — MINOR (fails loudly and safely), mechanism confirmed by execution.**

---

## 69. The frontmatter parser isn't a YAML parser

**Classification: PROVED, with a correction — one of the two cited examples works correctly; the cases that actually break are different ones.**

The parser:

```python
def parse_frontmatter(text: str):
    m = re.match(r"^---\n(.*?)\n---\n", text, re.S)
    if not m:
        return None
    fm = {}
    for line in m.group(1).split("\n"):
        if ":" in line and not line.startswith((" ", "\t")):
            k, _, v = line.partition(":")
            fm[k.strip()] = v.strip()
    return fm
```

Six cases were probed directly through the real function:

| Case | Input | Parsed result | Verdict |
|---|---|---|---|
| simple | `name: a` / `description: short` | `{'name': 'a', 'description': 'short'}` | ✅ correct |
| **colon in value** | `description: "something: with colon"` | `{'description': '"something: with colon"'}` | ✅ **correct** |
| **block scalar** | `description: >` + 2 indented lines | `{'description': '>'}` | ❌ **broken** |
| **nested structure** | `meta:` + indented `key: value` | `{'meta': '', 'description': 'ok'}` | ❌ **silently dropped** |
| list value | `tags:` + `- one` + `- two` | `{'tags': ''}` | ❌ **silently dropped** |
| comment line | `# a comment` (no colon) | not captured | ✅ correct |

**The correction.** The finding cites `description: "something: with colon"` as a case the parser cannot handle. It handles it correctly: `str.partition(":")` splits at the **first** colon only, so everything after it — including embedded colons — is preserved in the value. Quoting is retained, which is a cosmetic difference, not a loss.

**The actual defects** are the two the finding does not cite:

- **Block scalars** produce the literal string `'>'` as the description — which then fails the `len(desc) < 40` and WHEN-clause checks, giving a confusing error about description content when the real problem is the scalar style.
- **Nested structures and lists are silently discarded.** `meta:` becomes the empty string and `key: value` disappears entirely, because the parser skips indented lines by design (`not line.startswith((" ", "\t"))`). A frontmatter key containing a structurally meaningful nested value is lost with no diagnostic.

So the direction is right and the diagnosis is slightly off-target. The distinction matters because the remedy differs: supporting colon-in-value requires nothing (already works), whereas supporting block scalars and nesting requires either a real YAML parser or a **declared subset** that rejects those forms loudly rather than dropping them.

Recommended, per the finding's own framing:

```text
YAML subset V1   — or a real YAML parser
```

with the added requirement that unsupported constructs **fail** rather than being silently discarded. Silent key-dropping in a validator's parser is the same defect class as `has_prov` and `load_probes`' pre-fix `#` handling (Part I §28's "52 silently skipped probes").

**Classification: PROVED — direction correct, cited example inaccurate, silent-drop cases are the real defect.**

---

## 70. The command-reference checker has the same problem

**Classification: PROVED but LATENT — the regex's coverage happens to be exhaustive for the current corpus.**

The regex:

```python
for m in re.finditer(r"python3 (skills/[^\s`\"]+\.py)", text):
```

The finding is right that it matches only one textual form. But the docstring's own bullet is already correctly scoped:

```text
  * every `python3 skills/.../x.py` command quoted in SKILL.md points at a file
    that exists
```

It says *every `python3 skills/.../x.py` command*, not *all referenced scripts*. So the bullet does not overclaim in the way the finding suggests — it names the pattern it checks.

**And measurement shows the narrow pattern is currently complete.** Counting every invocation form across all six `SKILL.md` files:

```text
$ grep -rhoE '(python3|\$PY|python|\./[^ ]+\.py) [^ ]*' skills/*/SKILL.md | ...
     27 python3

$ grep -rn '\$PY' skills/*/SKILL.md
     (no matches)
```

**All 27 invocations are `python3 skills/...`.** No `$PY`, no bare `python`, no `./` executable, no `python3 -m`. So there is currently no unchecked reference.

This matters for classification: §70 is a **latent** weakness, not a live gap. It becomes live the moment someone writes `$PY` in a SKILL.md — and note that `run_all.sh` already uses `$PY` throughout its 15 invocations, so the idiom exists in the repository and is one copy-paste away from appearing in a SKILL.md.

That is also precisely Part III §28's interpreter-abstraction problem: `$PY` is the recommended form for anything that must honour the interpreter override, and it is exactly the form this validator cannot see.

**Classification: PROVED but LATENT.** Remedy: prefer manifest-declared invocations (§66), or broaden the pattern to accept `$PY` and `python`.

---

## 71. We should formally distinguish four kinds of checker

**Classification: recommendation — endorsed.**

```text
STRUCTURAL   Is this syntactically/structurally valid?
SEMANTIC     Does this artifact satisfy the intended invariant?
BEHAVIORAL   Does the implementation actually behave as specified?
INTEGRATION  Do the components work together under the declared protocol?
```

The taxonomy is sound, and it maps cleanly onto findings already established:

| Kind | Existing receipt |
|---|---|
| STRUCTURAL | `validate_skill.py`'s 7 assertions (§0.1) — all structural |
| SEMANTIC | `check_provenance` — and Part V §0.1 shows its semantics are conditional |
| BEHAVIORAL | **largely absent** — §64: compilation is not behaviour |
| INTEGRATION | `run_all.sh` — and Part III §21 shows its coverage is partial |

> A skill should not be considered fully verified merely because structural validation passes.

Correct, and it is the precise statement of §64's defect: the current system reports structural success using language that claims behavioural verification.

---

## 72. This produces a verification coverage matrix

**Classification: recommendation — endorsed. Supplied below as measured, rather than illustrative.**

The finding's matrix is a template. Filled in from the previous five parts' receipts:

| Property | Structural | Semantic | Behavioral | Integration |
|---|---|---|---|---|
| SKILL.md grammar | ✅ §0.1 | — | — | — |
| frontmatter values | ⚠️ §69 (silent drops) | — | — | — |
| documented inputs | ✅ §70 (27/27) | — | ❌ §64 | — |
| documented outputs | ✅ | ⚠️ | ❌ §64 | ✅ stage 5 |
| failure behavior | — | ⚠️ §35 | ⚠️ §36 (no ERROR) | ✅ stage 7 |
| determinism | — | — | ❌ | ❌ |
| dependency handling | — | — | ⚠️ §9 / Part III §9 | ✅ §0 |
| negative fixture | ❌ **unenforced** (§0.1) | ❌ | ⚠️ | ✅ stage 7 |
| composition | — | — | — | ✅ §0 |

Per-skill, as measured:

```text
markdown-corpus-audit
    structural    VERIFIED          (compiles; frontmatter; references resolve)
    semantic      PARTIAL           (Part V §0.1 — provenance bypassed for 10/33 docs)
    behavioral    PARTIAL           (Part IV §0.1 — transform not exercised)
    integration   VERIFIED          (stages 3, 5, 7)

corpus-provenance-numbering
    structural    VERIFIED
    semantic      UNVERIFIED        (Part IV §0.1 — fabricates provenance)
    behavioral    UNVERIFIED        (Part IV §41/N-2 — zero exercise)
    integration   UNVERIFIED        (never invoked by run_all.sh)

skill-creator
    structural    VERIFIED
    semantic      PARTIAL           (§0.1 — rule declared, unenforced)
    behavioral    UNVERIFIED        (§73 — validator untested)
    integration   VERIFIED          (stage 2)
```

This produces a materially different statement from `6 skill(s) valid`, which is the finding's point:

> That is substantially more informative.

Agreed, and the two rows reading `UNVERIFIED` across the board — `corpus-provenance-numbering` and `skill-creator` — are the same two skills §0.1 identifies as lacking negative tests. The matrix and the negative-test gap agree, which is a useful cross-check that both are measuring the same underlying deficit.

---

## 73. Mutation testing should apply to the skills validator too

**Classification: recommendation — endorsed; strengthened by a measured fact.**

The proposal is exactly right, and its five mutations target the validator's real assertions:

```text
mutation A:  remove frontmatter check          → assertion :47
mutation B:  accept wrong skill name           → assertion :50
mutation C:  remove compile check              → assertion :70
mutation D:  invert missing Verification test  → assertion :57
mutation E:  make missing referenced path acceptable → assertion :76
```

Each maps to a specific line in the measured assertion set, so the mutation suite is directly constructible — no guesswork about what to mutate.

**The strengthening.** `validate_skill.py` has **no test of any kind**. A recursive search for its name across all `.sh` and `.py` files returns only:

```text
skills/run_all.sh:43                                  ← it is invoked as stage 2
skills/skill-creator/scripts/new_skill.py:78          ← a hint printed to the user
```

So the validator is invoked by the release gate, never verified by anything. It is in the same position as `renumber.py` (Part IV §41/N-2): **executed, never tested.**

Combined with §0.1 — the validator does not enforce the negative-test rule that `skill-creator`'s own frontmatter declares — the recursion is:

```text
skill-creator declares:  every skill must ship a negative test
        │
        ▼
skill-creator itself:    ships no negative test
        │
        ▼
who would catch that?    validate_skill.py
        │
        ▼
validate_skill.py:       does not check for negative tests
        │
        ▼
who would catch that?    mutation testing (§73)
        │
        ▼
validate_skill.py:       has no tests at all
```

Every level of the recursion fails at the same point. This is the concrete form of §74–§75's bootstrap-trust problem, and it is why those sections' proposals are not merely architectural preference.

---

## 74. The verification kernel now has a natural recursive structure

**Classification: recommendation — endorsed.**

```text
RFL-AE SPEC → SKILLS → SKILL VERIFIER → VERIFICATION KERNEL
            → EXECUTION EVIDENCE → RELEASE AUTHORITY
```

> The verification kernel cannot simply trust the skills it is responsible for verifying.

Correct. And §0.1/§73 show the chain currently terminates early: the `SKILL VERIFIER` box is `validate_skill.py`, which (a) does not check the rule its parent declares, (b) is not tested, and (c) is invoked by the gate it is supposed to make trustworthy.

---

## 75. Bootstrap trust therefore becomes a first-class problem

**Classification: recommendation — endorsed.**

> Who verifies the verifier?

The proposed structure is right:

```text
Trusted bootstrap → primitive verifier → higher-level verifier
                  → RFL-AE skills → RFL-AE corpus
```

with a deliberately small bootstrap layer:

```text
bootstrap/
├── file_digest
├── canonical_json
├── process_runner
├── exit_status
├── filesystem_scope
└── evidence_serializer
```

The choice of a minimal bootstrap is the correct engineering response to circularity — these six primitives are small enough to be reviewed by inspection, which is the only honest grounding for the whole chain.

Two observations from this review that bear on it:

1. **`process_runner` and `exit_status` are where the current toolchain is weakest.** Part IV §36, §37 and Part V N-6 all show the same defect: ERROR is reported as FAIL, exit codes are collapsed to one bit, and a missing input produces a traceback rather than a diagnosis. Those two bootstrap primitives are precisely the ones the current scripts get wrong, which supports keeping them tiny and separately verified.

2. **`canonical_json` is a prerequisite for Part V §54's `CorpusManifest` and §66's `SkillManifest`.** Manifest-driven discovery was recommended in §68; a canonical serialization is what makes a manifest digest stable. The two proposals are coupled.

---

## 76. This maps directly onto your "proof-carrying artifact" principle

**Classification: recommendation — endorsed.**

```text
SkillArtifact
├── source      → digest
├── manifest    → digest
├── fixtures    → manifest + digests
├── verifier    → identity
├── execution   → receipt
├── observations → results
├── coverage    → declared scope
└── gate        → decision derivation
```

> Then the artifact is not merely "skill passed". It is "here is the evidence chain from source to gate."

Endorsed. Part III §0 is a hand-assembled instance of this structure, built by correlating a commit message against eight printed lines — which is exactly the work `SkillArtifact` automates.

One concrete note: the `verifier → identity` component does not exist in any form today. Part V §56's repository-wide search found **no commit SHA, version, or hash anywhere in the toolchain**. So this field is not partially implemented; it is absent, and it is a prerequisite for the `EVIDENCE(P)` axiom in §78.

---

## 77. Proposed normative object model

**Classification: recommendation — endorsed.**

The type set:

```text
SkillId  SkillVersion  SkillManifest
CheckId  CheckVersion  CheckManifest
SubjectId  SubjectDigest  Scope
FixtureId  FixtureDigest
ToolIdentity  EnvironmentIdentity  DependencyIdentity
ExecutionId  ExecutionRecord
Observation  CheckResult
CoverageRecord  EvidenceRecord
GateId  GateResult  ReleaseRecord
```

and the relationships:

```text
SkillManifest → CheckManifest → { Scope, Fixtures, ToolIdentity }
                              → declared requirements
                                      ↓
                              ExecutionRecord → Observation → CheckResult
                                                          → EvidenceRecord
                                                          → CoverageRecord
                                                          → GateResult
```

Two fields warrant emphasis from the accumulated receipts:

- **`Scope`** is the field whose absence Part V §51–§52 documented (`audit_corpus` over 33 files, `linkaudit` over 50, with no declared scope for either) and whose misdeclaration Part V §0.1 exposed (`has_prov` silently narrowing scope to zero for 10 documents). Part V §61's gate conditions 3 and 8 depend on it.
- **`ToolIdentity`** is entirely absent today (Part V §56), making the chain unrunnable as specified.

`EnvironmentIdentity` and `DependencyIdentity` (Part V §57) are the fields that would have made Part III's two runs — `markdown` absent (exit 1) versus present (exit 0) — distinguishable in evidence rather than only in a startup banner.

---

## 78. One invariant should govern the entire kernel

**Classification: recommendation — endorsed.**

```text
EVIDENCE(P) ⇒
    DECLARED(P) ∧ EXECUTED(P) ∧ SUBJECT_BOUND(P)
    ∧ VERIFIER_BOUND(P) ∧ SCOPE_COVERED(P) ∧ RESULT_OBSERVED(P)

VERIFIED(P) ⇔ EVIDENCE(P) ∧ RESULT(P) = PASS

RELEASED ⇔ ∀ P ∈ RequiredProperties: VERIFIED(P)
```

This is the cleanest formulation in the whole six-part review, and the separation it enforces —

```text
Observation → Evidence → Verification → Release
```

— is exactly what the current toolchain lacks, having collapsed the entire chain into an exit code.

**Cross-check against the accumulated receipts.** Applying the axiom's six conjuncts to the four substantive defects found in Parts III–V:

| Defect | Conjunct that fails |
|---|---|
| Part III §21 corpus coverage asymmetry | `SCOPE_COVERED` |
| Part IV §0.1 fabricated provenance certified | `SCOPE_COVERED` |
| Part V §0.1 non-monotonic invariant | `SCOPE_COVERED` |
| Part V §55 document deletion undetected | `DECLARED` |
| Part V §56 no verifier identity | `VERIFIER_BOUND` |
| Part IV §36 ERROR as FAIL | `RESULT_OBSERVED` |
| §0.1 negative-test rule unenforced | `DECLARED` |

Seven independent defects reduce to three conjuncts — `SCOPE_COVERED` four times, `DECLARED` twice, `VERIFIER_BOUND` once — with `EXECUTED` and `SUBJECT_BOUND` never failing, because the checks that run, do run.

That concentration is the strongest evidence available that the axiom is correctly formulated rather than over-fitted. **`SCOPE_COVERED` is the conjunct that does the work**, and no component of the current toolchain models scope at all. It is the single highest-value addition to make.

---

## 79. Current deepest finding

**Classification: endorsed.**

> The individual skills are no longer the main architectural risk. The risk is now epistemic composition.

This is correct, and the six parts' receipts support each clause of the diagnosis:

| Clause | Receipt |
|---|---|
| A checker can be correct but incompletely executed | Part III §21 — 5 of 11 invariants corpus-wide |
| A complete execution can be real but stale | Part V §56 — no verifier identity, so staleness is undetectable |
| Fresh evidence can exist but cover only part of the corpus | Part V §0.1 — 10 of 33 documents outside the invariant |
| A passing checker can be real but itself insufficiently verified | §0.1, §73 — validator unenforced and untested |
| A fully verified artifact can exist but not be the artifact that was published | Part I §25 — closeout certifies content, not publication |

The final chain:

```text
SOURCE → TRANSFORMATION → ARTIFACT → CHECK → EXECUTION
       → OBSERVATION → EVIDENCE → COVERAGE → GATE → PUBLICATION
```

> with identity binding at every arrow.

That clause is the operative one, and §77's object model supplies the types for it. The gaps are concentrated at three arrows:

```text
SOURCE → TRANSFORMATION    no source identity    (Part V §50 — filename + integer)
CHECK  → EXECUTION         no verifier identity  (Part V §56 — absent entirely)
GATE   → PUBLICATION       no remote binding     (Part I §25 — content only)
```

---

## New findings from this pass

### N-9. Rule 3 is unenforced, and the validator's parser silently drops keys

Two additional instances of the review's recurring defect class, recorded separately from §0.1 and §69 because both are silent failures inside the components that exist to prevent silent failures:

```text
skill-creator/scripts/validate_skill.py
    :30-33   frontmatter keys with nested values are silently discarded
             ("meta:" → "", nested "key: value" → lost, list items → lost)
             with no diagnostic — a validator's parser failing silently

skills/skill-creator/SKILL.md
    :3       frontmatter description asserts a rule (:53 "Ship a negative test")
             that the validator does not enforce and 2 of 6 skills do not meet
```

**Classification: PROVED — MINOR individually, thematically central.** Both are the same shape as `has_prov` (Part V §0.1), `load_probes`' pre-fix `#` handling (Part I §28), and `check_links()`'s pattern limits (Part I §19): a mechanism that cannot report its own blind spot, inside the toolchain whose purpose is making blind spots visible.

### N-10. The two untested components are the two load-bearing ones

A structural observation that emerges only from aggregating the six parts:

| Component | Tests | Role |
|---|---|---|
| `renumber.py` | ❌ none (Part IV §41/N-2) | **writes** corpus documents |
| `validate_skill.py` | ❌ none (§73) | **certifies** all skills |

Every other script in the suite is exercised by at least one of `run_all.sh`'s seven stages or `examples.py`'s 12 self-tests. These two are executed but never verified, and they are precisely the write path and the trust path.

This is not coincidence — it is the structural shape of the bootstrap problem (§74–§75). The components closest to the root of the trust chain have the least verification, because verifying them requires the very machinery under construction.

**Classification: PROVED — structural.**

---

## Bottom line

Part VI's architectural direction is correct, and §78's kernel axiom is the strongest single artifact the review has produced: it reduces seven independent defects found across Parts III–V to three conjuncts, with `SCOPE_COVERED` accounting for four of them and no component of the current toolchain modelling scope at all.

**The most consequential new result is §0.1: Rule 3 is declared, unenforced, and unmet.** `skill-creator`'s frontmatter — the machine-read discovery surface — asserts that "every skill must ship a negative test." `validate_skill.py` has seven assertions and none concerns negative tests. No skill ships a fixture in its own directory. And the two skills with no negative test anywhere are `corpus-provenance-numbering` (which fabricates provenance, Part IV §0.1) and `skill-creator` (which certifies everything else). It is the same defect class as Part V §0.1 (`has_prov`) — a declared guarantee whose absence the mechanism cannot detect — now located in the component responsible for preventing it.

Confirmed as stated: §66, §67, §71, §72, §73, §74–§79. §68 confirmed by execution (nested layout yields `FAIL experimental: no SKILL.md`, `skill-X` undiscovered, exit 1 — loud and safe, hence MINOR).

Three classifications corrected:

1. **§64 is RESTATED** — Part III §26 reported it verbatim. The new element is that the overclaim also appears in the frontmatter `description`, elevating it from a reader-facing docstring to the agent-facing discovery surface.
2. **§65 is LATENT** — the mechanism permits vacuity (demonstrated: a skill with "This skill is verified. Trust me." validates, exit 0), but all six corpus Verification sections are substantive. The real cost is that they carry load-bearing prose numbers nothing checks.
3. **§70 is LATENT** — measured: all 27 invocations across the six SKILL.md files are `python3 skills/...`, so the regex is currently exhaustive. It becomes live one copy-paste away, since `run_all.sh` uses `$PY` in 15 places.

And one correction inside §69: the cited case `description: "something: with colon"` **works correctly**. `partition(":")` splits at the first colon only. The real defects are block scalars (yielding the literal string `'>'`) and nested structures/lists, which are **silently discarded** by design.

**Recommended immediate action**, in the same spirit as Part V but one level up: add negative-test enforcement to `validate_skill.py`, then give `validate_skill.py` itself a test. The first closes §0.1; the second closes `N-10` for the trust path and is the smallest possible instance of §73's mutation framework. Together they cost little and they are the bootstrap layer §75 identifies as the correct place to start.

---

## Appendix — Part VI finding index

| Part VI § | Finding | Class | Verified by | Note |
|---|---|---|---|---|
| 0.1 | Rule 3 declared, unenforced, unmet by 2/6 | **PROVED (new)** | 3 measurements | strongest result of the pass |
| 64 | Validator does not verify scripts "run" | **RESTATED (2nd)** | source | Part III §26; now also in frontmatter |
| 65 | Self-referential validation | PROVED / LATENT | execution | trivial skill validates, exit 0; corpus is substantive |
| 66 | `SkillManifest` | recommendation | — | endorsed; §0.1 motivates it |
| 67 | Executable verification declarations | recommendation | — | endorsed |
| 68 | Flat one-level discovery assumption | PROVED | execution | `FAIL experimental`, `skill-X` undiscovered, exit 1 |
| 69 | Frontmatter is not YAML | PROVED + correction | 6-case probe | cited example works; block scalars/nesting silently drop |
| 70 | Command-reference pattern limits | PROVED / LATENT | measurement | 27/27 invocations match `python3` |
| 71 | Four checker kinds | recommendation | — | endorsed; maps to existing receipts |
| 72 | Coverage matrix | recommendation | Parts III–V | measured matrix supplied |
| 73 | Mutation-test the validator | recommendation | search | no test of any kind exists |
| 74 | Recursive kernel structure | recommendation | §0.1, §73 | chain terminates early today |
| 75 | Bootstrap trust | recommendation | Part IV §36–37, V N-6 | `process_runner`/`exit_status` are the weak primitives |
| 76 | Proof-carrying artifact | recommendation | Part III §0 | `verifier → identity` absent entirely |
| 77 | Object model | recommendation | Parts V §51–57 | `Scope` and `ToolIdentity` are the missing fields |
| 78 | Kernel axiom | recommendation | 7 defects → 3 conjuncts | `SCOPE_COVERED` does the work |
| 79 | Epistemic composition | endorsed | all parts | gaps at 3 arrows |
| N-9 | Silent key-drop in validator's parser; unenforced rule | PROVED | execution | recurring defect class |
| N-10 | The two untested components are the load-bearing ones | PROVED | aggregation | structural, not coincidence |
