# RFL-AE `skills/` — Independent Review, Part VII

**Subject:** [`Abdus2023/RFL-AE` — `skills/` tree, branch `arena/01a0e252-rfl-ae`](https://github.com/Abdus2023/RFL-AE/tree/arena/01a0e252-rfl-ae/skills)
**Branch tip at review:** `1090511a4080987168d1b17d48de88137ec1c27a`
**Parts I–VI:** [`part1`](rfl-ae-skills-review.md) · [`part2`](rfl-ae-skills-review-part2.md) · [`part3`](rfl-ae-skills-review-part3.md) · [`part4`](rfl-ae-skills-review-part4.md) · [`part5`](rfl-ae-skills-review-part5.md) · [`part6`](rfl-ae-skills-review-part6.md)
**Review date:** 2026-09-28

> **Method.** Verified by execution. §90 and §92 required direct experiment; both were confirmed and both are stronger than stated.

> **Numbering note.** These sections are numbered 80–96 as given in this pass. §80.1 is a sub-finding.

**Classification legend**

| Class | Meaning |
|---|---|
| PROVED | Established by execution or direct source inspection |
| PROVED — stronger | The direction is right; the actual defect is materially worse than stated |
| **RESTATED (n)** | Already established in n earlier parts; appearing again |
| LATENT | Real and demonstrated, but the current corpus does not exhibit it |
| recommendation | Forward-looking design proposal |

---

## 0. Verification summary

| Part VII § | Claim | Verified? |
|---|---|---|
| 80 | Closeout contract weaker than SKILL claims | **PROVED — stronger: the SKILL prescribes `git ls-remote` and the verifier omits it** |
| 80.1 | Substring evidence where structure is required | **PROVED** |
| 81 | Two independent gates | recommendation — endorsed |
| 82 | `renumber.py` ignores `m.group(1)` | **RESTATED (3rd)** — Part I §5, Part IV §0.1 |
| 83 | `renumber.py` is not fence-aware | **RESTATED (3rd)** — Part I §6, Part IV §0.2 |
| 84 | Losslessness theorem needed | recommendation — endorsed |
| 85 | `splice.py` fence grammar narrow | **RESTATED (2nd)** — Part I §8 |
| 86 | `splice.py` write is not crash-durable | **PROVED (new)** |
| 87 | `linkaudit.py` is a third interpretation | **RESTATED (2nd)** — Part I §19–20, Part II §10 |
| 88 | Two divergent slug algorithms | **RESTATED (3rd) + internally inconsistent** — there are three |
| 89 | Duplicate anchors under-modelled | LATENT — Part II §16b |
| 90 | Negative tests pass on a crash | **PROVED — DECISIVE RECEIPT (new)** |
| 91 | Fixed fixtures are not mutation tests | **RESTATED (3rd)** — Part IV §39–40, Part VI §73 |
| 92 | No Actions run attached to this commit | **PROVED — stronger: no `.github` directory exists at all** |
| 93 | Three evidence planes | recommendation — endorsed |
| 94 | `ExecutionRecord` | recommendation — endorsed |
| 95 | `ALL FILES OK` needs scope | **RESTATED (2nd)** — Part III §23 |
| 96 | Revised status table | endorsed |

---

## 0.1 First: this pass self-corrects the 997/998 error

Credit where it is due, and it belongs at the top rather than the bottom.

§80 states:

> The SKILL's own Verification section explicitly expects `documents : 33, corpus max section: 998` … and the current corpus indeed has 33 documents / 997 actual sections / maximum section 998, because §108 is the intentional gap.
>
> **That corrects an earlier possible interpretation: 997 sections and range 1–998 are not contradictory.**

This is the first pass in four attempts to state the cardinality/maximum distinction correctly and to identify §108 as the reason the two numbers differ.

For the record, the history of this claim:

| Pass | Claim | Outcome |
|---|---|---|
| Part II §8 | "the skills README is stale (997 vs 998)" | DISPROVED |
| Part III §20 | "run_all.sh cannot produce the claimed clean result" | DISPROVED by execution |
| Part III §32 | conceded: "not a defect; nothing to fix" | — |
| Part IV §47 | reinstated as risk item 1 | flagged RESTATED |
| Part V §63 | P0 item 3 | flagged RESTATED (5th) |
| **Part VII §80** | **explicitly corrects it** | ✅ |

I recommended in Part V that the numbers be disambiguated in place — `§1–§998 (997 sections; §108 absent)` — precisely because the ambiguity had generated four false findings. That recommendation is still worth acting on, but the reasoning error is now closed independently, which is the outcome that matters more.

This is also, incidentally, a demonstration of §79's thesis in miniature: an observation (`997 sections, range 1..998, gaps [108]`) printed three lines apart in a single block was repeatedly reinterpreted as a contradiction. Under the `ExecutionRecord` model of §94, the observation and the verdict would travel together.

---

## 0.2 Decisive receipt: the negative-test harness accepts a crash as a pass

This is the strongest new result in this pass, and it compounds two findings from earlier parts into one.

The harness in `run_all.sh`:

```bash
neg() {
  desc="$1"; shift
  "$@" > /tmp/_neg.log 2>&1
  rc=$?
  if [ $rc -ne 0 ]; then
    echo "  PASS  $desc  (exit $rc, $(grep -c '^  - ' /tmp/_neg.log) findings)"
  else
    echo "  FAIL  $desc  -- expected a non-zero exit, got 0"
    fail=1
  fi
}
```

The gate is `rc -ne 0`. **Any** non-zero exit passes, including one produced by an interpreter crash before the checker's first line executes.

**Experiment.** Run the linkaudit negative test's command in an environment without the `markdown` package:

```bash
python3 skills/markdown-corpus-audit/scripts/linkaudit.py --root /tmp/negtest2
```

**Result:**

```text
exit code: 1

Traceback (most recent call last):
  File ".../linkaudit.py", line 9, in <module>
    import markdown
ModuleNotFoundError: No module named 'markdown'
```

**What `neg()` reports:**

```text
  PASS  link checker catches a broken anchor and a missing target  (exit 1, 0 findings)
```

**The negative test passes while the checker never ran.** It reports `PASS`, with the crash captured to `/tmp/_neg.log` where nothing reads it, and the gate's exit code is unaffected.

### Why this compounds two earlier findings

This is not an independent defect. It is Part II §9 and Part VII §90 combining:

```text
Part II §9   linkaudit.py has no dependency guard
             (bare `import markdown`; auditlib has HAVE_MARKDOWN, linkaudit has nothing)
                    +
Part VII §90 neg() accepts any non-zero exit as a pass
                    =
             the linkaudit negative test is vacuously satisfied
             whenever the dependency is missing
```

In such an environment, `run_all.sh` reports:

```text
stage 4  → FAIL (exit 1)    ← linkaudit crashes; stage correctly fails
stage 7  → PASS             ← SAME crash; negative test passes
```

**The same crash is a failure at stage 4 and a success at stage 7.** No stage compares the two.

### It also resolves Part IV's N-1

In Part IV I reported that the harness printed `(exit 1, 0 findings)` for the linkaudit negative test and classified it MINOR — a format-coupling defect, since `grep -c '^  - '` matches `audit_file.py`'s problem format but not `linkaudit.py`'s.

That was correct as far as it went, but incomplete. `0 findings` has **two** causes, and the harness cannot distinguish them:

```text
1. checker ran, found 2 problems,  but its output format doesn't match the counter   → 0
2. checker never ran at all (import crash, no findings produced)                     → 0
```

Case 1 is cosmetic. Case 2 is a vacuous pass. Both render as the same string in the same field of the same line, and the field that would distinguish them is the one that reports `0`.

The general form, which is the review's recurring finding:

> A measurement that reports zero is indistinguishable from a measurement that did not occur.

**Classification: PROVED — the strongest new finding of this pass.**

---

## 80. Close-out verifier: the contract is weaker than the SKILL claims

**Classification: PROVED — and stronger than stated.**

The section-level claim is correct. The SKILL specifies the ritual:

```text
5. **Commit** with a message stating both ranges: source and corpus.
6. **Push** to the working branch, then **confirm** the remote SHA equals the ...
```

and `verify_closeout.py` implements four content checks plus numbering continuity:

```text
verify_closeout.py
    ├── previous → new forward link        CHECKED  (substring — see §80.1)
    ├── README → new document              CHECKED  (substring)
    ├── README count/max                   CHECKED  (regex)
    ├── new → previous provenance          CHECKED  (substring)
    ├── previous.last + 1 == new.first     CHECKED
    │
    ├── commit message                     NOT CHECKED
    ├── local commit identity              NOT CHECKED
    ├── push                               NOT CHECKED
    └── remote branch SHA                  NOT CHECKED
```

**What makes it stronger.** The SKILL does not merely list `push` among its steps. Its **trap section prescribes the exact command** by which the push is to be verified:

> 3. **Assuming a push succeeded.** `git push` can fail on auth or a non-fast-forward … `git ls-remote origin refs/heads/<branch>`.

So the repository contains:

```text
spec-turn-closeout/SKILL.md
    ├── frontmatter:  "...commit, push, and verification that all of it
    │                   actually happened"
    ├── step 5:       Commit
    ├── step 6:       Push, then confirm the remote SHA
    └── trap 3:       "Assuming a push succeeded" — use `git ls-remote`
                                  │
                                  ▼
spec-turn-closeout/scripts/verify_closeout.py
    └── does not invoke git, does not consult a remote,
        does not verify a commit, and is not capable of any of the above
```

The skill names the command that would close its own gap, and its verifier omits it. This is Part I §25 confirmed at the level of implementation detail, and it is the cleanest example in the review of a **contract stated in prose that no executable artifact attempts to satisfy**.

Note also that the trap explicitly frames the failure mode as recurrence ("Assuming a push succeeded" is a recorded trap, implying it once happened) — so the gap is known, documented, and still open.

**Minimum fix:** either rename the skill to `spec-content-closeout`, or add the `git ls-remote` check. The finding prefers splitting into two gates (§81); either is defensible, but the naming must not claim what the code does not do.

---

## 80.1 The verifier also uses substring evidence where structural evidence is required

**Classification: PROVED.**

Three of the five checks are substring tests:

```python
if f"]({a.new})" not in prev_t:        # forward link
if f"]({a.new})" not in readme:        # README entry
if a.prev not in new_t:                # provenance blockquote
```

The finding's analysis is correct, particularly its sharpest point:

> The verifier's message says *provenance blockquote should state what it continues from* — but the implementation merely proves that the filename occurs somewhere in the document.

`a.prev not in new_t` is the weakest of the three: it is a bare filename containment test with no link syntax, no blockquote syntax, and no position requirement. The diagnostic names a structure the check cannot observe.

All three are also fence-blind, in the manner established in Part II §12, Part IV §0.2, and Part V §0.1 — a fenced example containing `](RFL-LEDGER-V01.md)` would satisfy check 2.

So:

```text
"string exists"  ≠  "required semantic structure exists"
```

This is the same representation/semantics boundary the architecture enforces elsewhere, and it is worth recording that it now appears in **four** distinct components: `has_prov` (Part V §0.1), `check_provenance`'s next-nonblank-line scan (Part I §17), `load_probes`' pre-fix `#` handling (Part I §28), and here. Part II §10's "semantic split-brain" is the aggregate form of this.

---

## 81. Close-out verification should become two independent gates

**Classification: recommendation — endorsed.**

```text
CLOSE-OUT
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
     CONTENT CLOSEOUT       PUBLICATION CLOSEOUT
             │                   │
             ├── forward edge    ├── HEAD identified
             ├── README entry    ├── commit exists
             ├── README status   ├── commit message valid
             ├── provenance      ├── branch identified
             └── numbering       ├── remote ref queried
                 continuity      └── remote SHA == expected SHA
```

> Neither implies the other. A locally perfect working tree does not prove publication. A remote commit does not prove that the local close-out ritual was correctly checked before publication.

Endorsed, and the second direction is worth emphasizing because it is the less obvious one: a remote SHA match does **not** establish that the content was audited before it was pushed. That requires the content gate's evidence to be bound to the same SHA — which is §94's `ExecutionRecord` and Part VI §78's `EVIDENCE(P)` axiom, not a shell script.

This is the same separation Part III §93's three planes describe, expressed at gate granularity.

---

## 82. `renumber.py`: the previously identified defect is definitely real

**Classification: RESTATED (3rd) — Part I §5, Part IV §0.1, now Part VII §82.**

The finding is correct in every particular, and its worked example (`§1, §3, §7` with `offset=100` → `101, 102, 103` instead of `101, 103, 107`) matches the receipt produced in Part IV §0.1.

It remains unfixed, which is why it keeps being rediscovered. Worth recording the accumulated evidence, since this is now the best-characterised defect in the repository:

| Part | Evidence added |
|---|---|
| I §5 | Source inspection: `n += 1; corpus = n + offset`; `m.group(1)` unused |
| IV §0.1 | **Execution:** produces `101/102/103`; writes **false** comments `§1/§2/§3` |
| IV §0.1 | **Execution:** `auditlib` then **certifies** the fabrication — `mapping errors=0` |
| IV §41/N-2 | `renumber.py` is referenced by **no** `.sh`/`.py` file — zero automated exercise |
| IV N-3 | Success message prints `source 1..3` for a `§1,§3,§7` input |
| VII §82 | Independent third confirmation by source inspection |

The single most important property of this defect is not the arithmetic. It is that **the fabricated provenance is certified by the auditor**, because writer and verifier share one ordinal model. That is Part V §61's gate failing on four independent conjuncts.

---

## 83. `renumber.py` has a second independent trust-boundary defect

**Classification: RESTATED (3rd) — Part I §6, Part IV §0.2, now Part VII §83.**

Correct, and verified twice. Part IV §0.2 produced the artifact:

````markdown
## 101. Real section
<!-- source: SRC.md §1 -->

```text
## 102. This is not a section
<!-- source: SRC.md §2 -->
```
````

from a source whose fenced body contained `## 999.` — two distinct failures: the fenced body was rewritten, and a provenance comment was **injected inside the code fence**. The tool reported `renumbered 2 sections` for a one-section document.

The finding's framing is precise and worth keeping:

> This is particularly serious because `renumber.py` is itself part of the transformation authority.

That is the correct characterization, and it connects to Part IV §41/N-2: the transformation authority is the one component with no test.

---

## 84. The numbering skill therefore needs an explicit losslessness theorem

**Classification: recommendation — endorsed, and it is the right artefact to build first.**

The proposed property:

```text
T :  S ↦ C = S + O
T⁻¹: C ↦ S = C - O
```

with the round-trip condition:

```text
decode(encode(document, offset)) == document
```

modulo the deliberately introduced corpus numbering and comment transformation, plus the per-heading invariant:

```text
∀ heading H:  source(H_after) == source(H_before)
              corpus(H_after) == source(H_before) + offset
```

and the gap case:

```text
source = [1, 3, 7]  →  corpus = [101, 103, 107]     not [101, 102, 103]
```

The finding's recommendation to make this **property-based or metamorphic rather than a single fixture** is correct and is the strongest form of the suggestion.

One refinement from the receipts. `T⁻¹` as stated is not currently implementable, because `C` is written to the document and `S` is written only into a comment — and Part IV §0.1 showed those two can disagree. So the round-trip test is not merely a check on `T`; it is a check that would **fail today in a specific, diagnosable way**:

```text
decode(encode(doc, O), O)  →  §1, §2, §3      (from the comments)
   vs.  the actual source  →  §1, §3, §7
```

Making `T⁻¹` a first-class function is therefore the right design move, because it forces the comment to be the sole carrier of source identity and makes any disagreement with a real source document detectable.

This is what §41 demanded in Part IV and what §0.1's fabrication exploited. It remains the single cheapest high-value addition to the suite.

---

## 85. `placeholder-splice.py`: strong local design, but its proof boundary is narrower than it appears

**Classification: RESTATED (2nd) — Part I §8.**

The implementation:

```python
blocks = re.findall(r"^```(\w*)\n(.*?)\n^```", out, re.S | re.M)
```

Confirmed verbatim, including the `re.S | re.M` flags. Part I §8 already established that this does not cover `` ```text {.class} ``, longer fences, or indentation variants, and classified it OPEN — dialect limitation.

The finding adds a valuable structural point that Part I did not make: **Option A vs Option B** as an explicit fork —

```text
Option A — deliberately freeze the dialect:
    RFL-AE Markdown dialect: fences MUST be exactly ```LANG ... ```
Option B — make the parser Markdown-aware (fence length, indentation,
    info strings, opening/closing matching)
```

> For a controlled corpus, Option A is perfectly defensible, but it must be normative rather than implicit.

Agreed, and this is the same recommendation as Part V §49: name the dialect, then future authors cannot mistake a parser limitation for a specification rule.

One observation in the skill's favour, worth recording. The `--fence-lang` argument is documented as deprecated:

```python
ap.add_argument("--fence-lang", default="text",
                help="retained for compatibility; the fence assertion is ...")
```

and the docstring explains why:

> The original defect: a placeholder left OUTSIDE a fence renders as prose ... So require each body to be the EXACT content of some fenced block [rather than] an old substring test against a single hardcoded `--fence-lang`.

So this skill has already replaced a weaker check with a stricter one, and left the deprecated flag documented. That is the right pattern, and it is the same trajectory §85 and §90 recommend elsewhere.

---

## 86. `placeholder-splice.py` also does not prove crash durability

**Classification: PROVED (new).**

```python
with open(a.target, "w", encoding="utf-8") as fh:
    fh.write(out)
```

Confirmed at `splice.py:112-113`. No temporary file, no `flush`, no `fsync`, no `os.replace`. A repository-wide grep finds no `rename`, `fsync`, `flush`, or `tempfile` anywhere in the script.

The finding's decomposition is correct and worth preserving:

```text
semantic correctness  ≠  atomic update  ≠  crash durability
```

The skill establishes the first. It does not establish the second or third.

The finding's own scoping is also right — *"this matters less for an interactive authoring utility than for a release-authoritative transformation system"* — and I would sharpen it slightly. `splice.py` is invoked by `run_all.sh`'s negative test but **not** as a corpus-transforming stage in the release gate; it is used by an author during authoring. So the risk is bounded today. It becomes material if splicing ever runs unattended in a pipeline, because the failure mode is a **truncated target document**: `open(..., "w")` truncates before writing, so an interruption leaves a partial file with no indication.

**Classification: PROVED — MINOR today, contract must not conflate the three properties.**

---

## 87. `linkaudit.py`: another independent Markdown interpretation

**Classification: RESTATED (2nd) — Part I §19–20, Part II §10.**

Confirmed: `linkaudit.py` renders via Python-Markdown with `["tables", "fenced_code"]` and extracts anchors from the HTML (`anchors_of(html)`, per-file).

The three-interpretation diagram in the finding is correct, and Part II §10 already counted them, with the correction that the fourth "interpretation" is GitHub itself, which is an oracle rather than an implementation.

The finding's key sentence:

> `checker interpretation ≠ GitHub interpretation` — unless this equivalence is independently established.

This is exactly Part V §58's untrusted-oracle observation, and it remains open. The finding is a correct restatement; nothing new was added, and nothing has changed.

---

## 88. There are actually two different slug algorithms

**Classification: RESTATED (3rd) — Part I §21, Part II §16. And the section is internally inconsistent.**

The heading says **two**. The closing diagram lists **three**:

```text
auditlib.slug()
linkaudit.slug()
renumber.github_slug()
```

> Three implementations mean three opportunities for semantic drift.

Part II §16 already established that there are three, and — more importantly — that they are **not merely duplicated but algorithmically different**: `linkaudit.slug` uses per-character `ch.isalnum()` and strips HTML tags first, whereas `auditlib.slug` uses `re.sub(r"[^\w\- ]", "", s)`. Those are different functions that agree on today's corpus by coincidence of its limited character set, not by construction.

The heading's undercount matters because "two implementations" invites the interpretation *merge them*, whereas the correct reading is *there are three, one of which behaves differently, and none is validated against the target*. The second framing is the one that produces the golden-vector requirement.

The proposed golden-vector list is excellent and I would adopt it as written:

```text
simple heading · apostrophe · Unicode · punctuation · multiple spaces
em dash · en dash · symbols · HTML inline content · duplicate headings
heading beginning with number · heading containing §
```

with the direction:

```text
GitHub anchor semantics → golden vectors → all internal implementations
```

**The open question this raises**, which no pass has addressed: golden vectors must be captured *from GitHub*, and there is currently no mechanism to do so. Part V §58 established that the local slugger is a reverse-engineered approximation and that `auditlib` asserts `Must match GitHub exactly`. Capturing the vectors requires either publishing a test document and reading GitHub's rendered HTML, or accepting a documented exception list. Until then, the golden vectors would only pin the **current** implementation's behaviour, which prevents drift but does not establish correctness against the target.

---

## 89. Duplicate anchors are currently under-modeled

**Classification: LATENT — Part II §16b.**

Correct as a design observation:

> A set answers "Does this slug occur?" It does not answer "What canonical anchor does GitHub assign when this heading occurs multiple times?"

And the principle is well stated:

> **Do not discard identity before verification.**

`linkaudit.py` does hold anchors per file (`anchors[f] = anchors_of(html)`), so the set-collapse concern applies as Part II §16b described.

**The latency qualification stands as established in Part II §16b:** duplicate heading titles were searched for across the corpus and **none were found**, because every heading carries a unique `§NNN` prefix, making the slug begin with a distinct integer. Collision is structurally precluded by the numbering convention rather than by the anchor model.

So this is a real fragility with no current exposure — which is the same classification as Part II §16b, and I see no reason to change it.

---

## 90. The negative tests are weaker than the SKILL documentation says

**Classification: PROVED — decisive receipt in §0.2.**

The strongest finding of this pass. The `neg()` harness gates on `rc -ne 0`, so:

```text
exit != 0  →  "negative test passed"
```

is satisfied by **any** non-zero exit, including an interpreter crash before the checker executes. Demonstrated: the linkaudit negative test reports `PASS` under `ModuleNotFoundError`.

The finding's structural point is exactly right:

> it does not establish *correct checker detects the intended defect, with the intended diagnostic, without an infrastructure error*

And the proposed contract is the correct remedy:

```text
NegativeFixture → ExpectedFinding { check_id, subject, category, severity }
                → ObservedFinding
                → exact/structural match
```

rather than `returncode != 0`.

Two additions from the receipts:

1. **The crash case is not hypothetical here.** Part II §9 established that `linkaudit.py` has no dependency guard. So the exact scenario — missing `markdown` → import crash → negative test passes — is reachable in the repository's own documented degraded environment, and `run_all.sh` explicitly contemplates that environment (`markdown: NOT INSTALLED -- render checks will be SKIPPED`).

2. **Part IV N-1 is subsumed.** I classified the `0 findings` count as a MINOR format-coupling defect. It is in fact the same field reporting two different things: a checker that ran and found problems in an unmatched format, and a checker that never ran. See §0.2.

---

## 91. Fixed negative fixtures are not mutation testing

**Classification: RESTATED (3rd) — Part IV §39–40, Part VI §73.**

Correct, and the mutation-operator list is well-formed:

```text
delete provenance · change offset · remove fence language · break anchor
alter border · alter Rust delimiter · corrupt source number
introduce duplicate section
```

Part IV §39–40 established this, and Part IV §40 added the row that matters most:

| Invariant | Mutation | Expected | Current |
|---|---|---|---|
| source-number fidelity | gapped source `§1, §3, §7` | FAIL | **PASSES** |

Part VI §73 extended the same method to `validate_skill.py`, which has no tests of any kind.

Worth noting the operators in this list already have receipts for three of the eight:

```text
change offset          → Part IV §0.1  (fabricated, then certified — mutation SURVIVES)
corrupt source number  → Part IV §0.1  (mutation SURVIVES)
delete provenance      → Part V §0.1   (mutation SURVIVES when total)
```

Three surviving mutants are already documented. That is a stronger statement than the finding makes, and it is available without building the framework: **the mutation results for three of the eight operators are known and the checkers are insensitive to all three.**

---

## 92. Current CI/publication situation: no workflow run is attached to this commit

**Classification: PROVED — and stronger than stated.**

The finding reports that querying workflow runs for `90660542` returned `workflow_runs: []`, and correctly hedges: this does not prove the scripts were never run locally.

The stronger fact: the repository has **no CI configuration at all**.

```text
$ gh api repos/Abdus2023/RFL-AE/actions/workflows --jq '.total_count'
0

$ gh api "repos/Abdus2023/RFL-AE/actions/runs" --jq '.total_count'
0

$ gh api repos/Abdus2023/RFL-AE/contents/.github
404 Not Found

$ ls -la /tmp/rflae/.github
No such file or directory
```

There are **zero workflows configured and zero runs**, ever — not "no run attached to this commit". The repository has never had a CI system.

This sharpens two things:

1. **The commit-message claims have no machine evidence.** `run_all.sh -> all stages pass, 4/4 negative tests, exit 0` is a claim about a local execution. Part III §0 produced the first **third-party** execution evidence for this repository, by cloning and running the pipeline independently. Before that, the "all stages pass" claim rested entirely on the author's assertion.

2. **The negative-test finding is more serious in this light.** If a CI system existed and ran `run_all.sh`, the vacuous-pass defect (§0.2) would be a live exposure in an automated gate. Because no CI exists, the defect is currently latent — the harness is only ever run by a human who can read `/tmp/_neg.log`. Adding CI before fixing §0.2 would convert a latent defect into an active one.

The finding's three-plane framing is correct, with the correction that Plane C is not merely unverified but unexercised: there is no pipeline to verify.

---

## 93. The repository therefore currently has three distinct evidence planes

**Classification: recommendation — endorsed.**

```text
PLANE A — SOURCE       what code/document exists
PLANE B — EXECUTION    what actually ran → exit status / stdout / stderr
PLANE C — PUBLICATION  commit SHA → branch ref → remote SHA
```

> Current implementation is strongest in Plane A. Plane B is represented mostly by human-readable stdout. Plane C is described by the ritual but not mechanically verified.

Correct, and the receipts give each plane a precise status:

| Plane | Status | Receipt |
|---|---|---|
| A — Source | **strongest** | Part III §0: repo cloned at a pinned SHA; every commit-message number reproduced |
| B — Execution | **partial** | Part III §0 is a real receipt, but hand-assembled by correlating logs; §94's `ExecutionRecord` does not exist |
| C — Publication | **absent** | §80: `verify_closeout.py` does not invoke `git`; §92: no CI |

The finding's ASCII diagram showing four `X` marks — no structured binding, no execution receipt, no commit binding, no remote binding — is an accurate depiction of the gap between `CODE EXISTS` and `"PASS"`.

---

## 94. The next architectural object should be `ExecutionRecord`

**Classification: recommendation — endorsed.**

The proposed record:

```text
ExecutionRecord
record_id · check_id · subject_id · subject_digest
verifier_id · verifier_digest · toolchain_id · dependency_set · environment_id
started_at · finished_at · exit_code · stdout_digest · stderr_digest
result · scope
```

with

```text
result ∈ { PASS, FAIL, ERROR, SKIPPED, UNKNOWN }
```

is the right object, and the five-valued result is the part that matters most. Parts IV and V supplied receipts for four of the five values being **conflated** today:

| Value | Current representation | Receipt |
|---|---|---|
| `PASS` | exit 0 | — |
| `FAIL` | exit 1 | — |
| `ERROR` | **exit 1** — indistinguishable from FAIL | Part IV §36, Part V N-6 (traceback, exit 1) |
| `SKIPPED` | exit 2 under `--strict`; **`n/a` in a PASS cell** | Part V §0.1 (`has_prov`) |
| `UNKNOWN` | **rendered as PASS** | Part V §0.1 |

So the object is not a refinement of current practice; it is the missing vocabulary. Two of the five values have no representation at all, and the two that do are collapsed into one bit (Part IV §37).

The full chain the finding proposes:

```text
SOURCE → DIGEST → CHECK DEFINITION → VERIFIER → EXECUTION
       → OBSERVATION → EVIDENCE → COVERAGE → GATE → COMMIT → REMOTE REF
```

is Part VI §78's axiom extended through `COMMIT` and `REMOTE REF` — i.e. §93's Plane C appended. That is the correct completion.

---

## 95. One particularly important semantic distinction

**Classification: RESTATED (2nd) — Part III §23.**

> `ALL FILES OK` is dangerous unless its scope is attached.

Correct, and Part III §23 quoted the string verbatim from a real run. The finding's proposed replacement is better:

```text
CORPUS AUDIT RESULT
scope:    root specification documents
checks:   numbering, fences, render, raw links, provenance
coverage: 33 / 33 documents
result:   PASS

LATEST DOCUMENT AUDIT
scope:    RFL-LEDGER-V01.md
checks:   numbering, fences, Rust, rendering, structure, provenance, links, probes
coverage: 1 / 1
result:   PASS
```

The measured asymmetry that motivates it, from Part III §21: the corpus sweep runs 5 checks over 33 documents; the single-document audit runs 11 over 1. Both currently report success using language that does not distinguish them.

---

## 96. Revised status of the skills subsystem

**Classification: endorsed, with two amendments.**

The table is accurate. Two notes:

**Amendment 1 — "Provenance numbering: OPEN / DEFECTIVE" is right and should be ranked first.** It is the only row whose status is *defective* rather than *partial*, and its consequences are established by execution rather than inference: fabricated provenance (Part IV §0.1), certified by the auditor, in a component with zero automated exercise (Part IV §41/N-2).

**Amendment 2 — "Whole-pipeline evidence: OPEN" should record that a receipt now exists.** Part III §0 produced a third-party execution receipt at a pinned SHA, and Part III §0 is in the repository alongside this review. The status is not *no evidence*; it is *no **structured** evidence* — the receipt was hand-assembled and is not machine-checkable. That is a meaningful difference for prioritization: the gap is `ExecutionRecord` (§94), not the absence of anyone having run the pipeline.

> The key conclusion is not that the system is unsound. It is that the skills are currently good engineering tools, but they have not yet become a self-verifying verification system.

Endorsed, and it is the correct summary of the seven passes.

---

## New findings from this pass

### N-11. A crash and a pass are indistinguishable at the gate

Established in §0.2 by execution: `neg()` gates on `rc -ne 0`, so `ModuleNotFoundError` satisfies the negative test. The same crash fails stage 4 and passes stage 7 in a single run.

This is the purest instance of the review's recurring form — *a check that reports success when it did not run* — because the harness's entire purpose is verifying that checkers detect defects, and it cannot tell detection from failure to start.

**Classification: PROVED.**

### N-12. Three mutation operators already have known surviving mutants

Not a new test result, but a new aggregation. From Parts IV and V, three of §91's eight proposed operators have already been executed against the real checkers:

| Operator | Result | Receipt |
|---|---|---|
| corrupt source number | **SURVIVES** | Part IV §0.1 — `§3` → `§2`, certified |
| change offset | **SURVIVES** | Part IV §0.1 — `mapping errors=0` |
| delete provenance (all) | **SURVIVES** | Part V §0.1 — `n/a`, passes |

So the mutation framework §91 recommends does not need to be built before it produces results. Three mutants survive today, and their receipts are already in this repository.

**Classification: PROVED.**

### N-13. Positive observation: `splice.py` has already deprecated a weaker check

`splice.py`'s `--fence-lang` is retained but documented as superseded:

> retained for compatibility; the fence assertion is [now the exact-fenced-body test]

with the docstring recording *why* — the original substring test against a single hardcoded language was the defect that let placeholders escape their fences.

This is the trajectory §85 and §90 recommend for the rest of the suite: replace a weak check with a strict one, keep the deprecated surface documented, and record the defect that motivated the change. Recording it because a review that lists only defects understates a component that has already made this improvement.

**Classification: positive.**

---

## Bottom line

Part VII's architecture is correct and its two new receipts are the strongest additions since Part V.

**The most consequential new result is §0.2/N-11: the negative-test harness accepts a crash as a pass.** `neg()` gates on `rc -ne 0`, so `linkaudit.py` failing to import `markdown` satisfies the negative test — and in a single `run_all.sh` run the same crash fails stage 4 and passes stage 7. This compounds Part II §9 (no dependency guard) with Part VII §90 (any nonzero passes) into a vacuous test, and it subsumes Part IV's N-1: `0 findings` has two causes — a checker that ran with unmatched output format, and a checker that never ran — and the harness cannot distinguish them.

**§92 is stronger than stated: there is no CI at all.** Zero workflows, zero runs, no `.github` directory. The commit-message claims are local-execution assertions; Part III §0 was the first third-party execution evidence. This also means N-11 is currently latent — adding CI before fixing it would make it an active defect.

**This pass self-corrects the 997/998 error**, stating explicitly that "997 sections and range 1–998 are not contradictory" and identifying §108 as the reason. That closes a claim which had appeared in five prior passes.

Confirmed as stated: §80 (and stronger — the SKILL's own trap section prescribes `git ls-remote`, which the verifier omits), §80.1, §86 (new), §89 (LATENT, unchanged from Part II §16b), §94–§96.

Six sections are restatements: **§82 (3rd)**, **§83 (3rd)**, **§85 (2nd)**, **§87 (2nd)**, **§88 (3rd)**, **§91 (3rd)**, **§95 (2nd)**. That is not a criticism of their correctness — all are accurate, and several are restated because they remain unfixed. It is worth recording for a different reason: **the review has now reached the point where re-examination produces diminishing new findings.**

The novelty in this pass is concentrated in four items (§80.1's substring evidence, §86's non-atomic write, §90's crash-pass, §92's absent CI), and three of the four are minor. The substantive architectural conclusions — one Markdown IR, one anchor implementation, a corpus manifest, scope-aware verdicts, and an `ExecutionRecord` — were all established by Part V, and every pass since has confirmed them without adding to them.

**§88 also needs an internal correction**: the heading says two slug algorithms; the section's own diagram lists three, and Part II §16 established that the third is algorithmically different rather than merely duplicated.

**Recommended immediate action**, unchanged and now confirmed by a third independent inspection: fix `renumber.py`. It is the only component rated *defective*, its fabrication is certified by the auditor, it has zero automated exercise, and §84's round-trip property would have caught it on the day it was written. Fixing `neg()` to require structured findings rather than a non-zero exit is the second priority, and it should precede any CI work.

---

## Appendix — Part VII finding index

| Part VII § | Finding | Class | Verified by | Note |
|---|---|---|---|---|
| 0.1 | Self-correction of 997/998 | ✅ positive | source | first correct statement in 4 attempts |
| 0.2 | Negative test passes on a crash | **PROVED (receipt)** | execution | strongest new result |
| 80 | Closeout contract weaker than SKILL claims | PROVED — stronger | source | SKILL prescribes `git ls-remote`; verifier omits it |
| 80.1 | Substring evidence misnamed as structure | PROVED | source | fourth component with this defect |
| 81 | Two independent gates | recommendation | — | endorsed |
| 82 | `renumber.py` ignores `m.group(1)` | **RESTATED (3rd)** | Parts I, IV | + 3rd independent confirmation |
| 83 | `renumber.py` not fence-aware | **RESTATED (3rd)** | Parts I, IV | artifact produced in Part IV §0.2 |
| 84 | Losslessness theorem | recommendation | — | endorsed; `T⁻¹` would fail today, diagnosably |
| 85 | `splice.py` fence grammar narrow | **RESTATED (2nd)** | Part I §8 | Option A/B fork is a useful addition |
| 86 | `splice.py` write not crash-durable | **PROVED (new)** | source | `splice.py:112-113`, no temp/fsync/rename |
| 87 | `linkaudit.py` third interpretation | **RESTATED (2nd)** | Parts I, II | unchanged |
| 88 | Two slug algorithms | **RESTATED (3rd) + inconsistent** | Parts I, II | heading says two; diagram lists three |
| 89 | Duplicate anchors under-modelled | LATENT | Part II §16b | no collisions exist in corpus |
| 90 | Negative tests pass on crash | **PROVED** | execution | see §0.2 |
| 91 | Fixtures ≠ mutation tests | **RESTATED (3rd)** | Parts IV, VI | 3 of 8 operators already have surviving mutants |
| 92 | No Actions run attached | **PROVED — stronger** | `gh api` | zero workflows, zero runs, no `.github` |
| 93 | Three evidence planes | recommendation | §0, §80, §92 | Plane C absent, not merely unverified |
| 94 | `ExecutionRecord` | recommendation | Parts IV, V | 2 of 5 result values have no representation |
| 95 | `ALL FILES OK` needs scope | **RESTATED (2nd)** | Part III §23 | measured asymmetry in Part III §21 |
| 96 | Revised status table | endorsed | all parts | 2 amendments |
| N-11 | Crash and pass indistinguishable at the gate | **PROVED** | execution | purest instance of the recurring defect |
| N-12 | Three mutation operators already have surviving mutants | PROVED | Parts IV, V | framework not needed to get results |
| N-13 | `splice.py` already deprecated a weaker check | positive | source | the right trajectory |
