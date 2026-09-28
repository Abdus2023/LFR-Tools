# RFL-AE Prompt Packs — Part XV: Prompt Pack Specification v0.4 — Implementation-Blocking Analysis

**Subject:** [`rfl-ae-prompt-pack-specification-v0.4.md`](rfl-ae-prompt-pack-specification-v0.4.md) (§71–§105), continuing [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md) (§41–§70) and [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md) (§0–§40)
**Series:** [`part11`](rfl-ae-prompt-instruction-packs.md) · [`part12`](rfl-ae-prompt-packs-part12.md) · [`part13`](rfl-ae-prompt-packs-part13.md) · [`part14`](rfl-ae-prompt-packs-part14.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — implementation-blocking
>
> **v0.4 is the first document in this lineage that can be built.** §72–§94 specify concrete schemas; §101 defines a machine-checkable gate. And its §102 correctly sequences the work: *"The model-facing layer should therefore be late, not foundational."*
>
> **But §102 step 1 cannot be completed as specified.** §86 defines the digest over `CanonicalCompiledPack`, and §88 places `compiled_digest` **inside** the artifact it digests. That is circular. §84 — the section listing what canonicalization must define — has no exclusion rule, and §85 pushes the other way. **This is the first blocking specification defect in the lineage: one sentence resolves it, and every downstream gate depends on the resolution.**
>
> New findings: **§2** (blocking), **§3–§4** (v0.4's gate and Part XI's disagree), **§5** (§90 binds the runtime to the component that fails §94), **§8** (the schemas have been proposed before and never built).
>
> The pattern established in Parts XIII–XIV — *a requirement stated in one form and represented in another with no declared relation* — appears four more times. But it is no longer the leading finding, because v0.4's problem is a **hard** blocker rather than a soft one.

---

## 1. Standing: §0–§105 across three documents

v0.4 continues the numbering of v0.3 (§41–§70), which continues v0.2 (§0–§40). The effective specification is now **105 sections across three files**.

The dependency is again structural, not stylistic. v0.4 uses without defining:

| v0.4 uses | Defined only in |
|---|---|
| §78 `strength: immutable` and §79's five strengths | **v0.2 §21** (five strengths), **v0.3 §21** |
| §80's conflict semantics | **v0.3 §58** |
| §82 `BLOCKED` / `ERROR` | **v0.2 §7** — the six states |
| §75 `mode: read_only` | **v0.2 §2** — the default authority table |
| §94's `no_evidence: no_verified_claim`, `unknown: not_pass`, `error: not_fail` | **v0.2 §1** — core invariants |
| §93's `unsupported_completeness` | **v0.3 §69** — the completeness predicate |
| §97–§99's model boundary | **v0.2 §3**, **v0.3 §59** |

**Part XIV §1.1's identity finding is now worse, not better.** It raised the question of whether `PackDigest` covers §0–§40 or §0–§70. The answer today is *neither* — the protocol spans three documents and §73 defines identity only for the **pack**, not for the **specification that governs packs**.

§72's `api_version: rfl-ae.prompt-pack/v0.4` partially addresses this: the pack schema is now versioned by an explicit string rather than by filename. But §72's own §31 (v0.1) and §152 (Part XI) forbid filename-based identity, and v0.1–v0.3 carry no `api_version` at all. **The pack has an identity scheme; the protocol does not.**

---

## 2. BLOCKING: the digest is defined over an artifact that contains it

This is the finding that gates everything else.

### 2.1 The contradiction

**§86:**

```text
CompiledPackDigest = SHA256(CanonicalCompiledPack)
```

**§88:**

```yaml
compiled_pack:
  api_version: rfl-ae.compiled-pack/v0.4
  identity:
    id: rfl-audit
    version: 0.4.0
    source_digest: ...
    compiled_digest: ...      # ← the digest, inside the digested artifact
  ...
```

If `CanonicalCompiledPack` is the canonical form of the `compiled_pack` object, and that object contains `identity.compiled_digest`, then computing the digest requires the digest. **§86 and §88 cannot both be satisfied as written.**

### 2.2 §84 does not resolve it, and §85 makes it worse

§84 is the section that exists to remove representational ambiguity. Its required definitions:

```text
key ordering · array ordering · string normalization · number representation
· null semantics · omitted/default fields · encoding
```

**None of these is an exclusion rule.** There is no entry saying *which fields are outside the digest input*. §84 lists seven things canonicalization must define and the one thing this artifact needs is an eighth.

**§85 points the other way:**

> *"Every semantic default must appear in the canonical representation."*

That rule argues *for* including fields in the canonical form, and `compiled_digest` is a field. So §85, read literally, requires the field to be present in the canonical representation that §86 then hashes. The two sections are individually reasonable and jointly contradictory for this one artifact.

### 2.3 Two resolutions, and the repository already contains both

**Option A — declare an exclusion.** Define `CanonicalCompiledPack` as the canonical form *excluding* `identity.compiled_digest` (or excluding `identity` entirely), and add that exclusion to §84's list as an eighth required definition.

**Option B — move the digest outside the projection.** `compiled_digest` names the artifact rather than being a field within it:

```yaml
compiled_pack:
  api_version: ...
  identity:
    id: rfl-audit
    version: 0.4.0
    source_digest: ...
  ...                                    # everything here is digested

compiled_digest: sha256:...              # a name for the object above
```

**Option B is the pattern used in this repository already, twice:**

| Precedent | How it avoids the circularity |
|---|---|
| `vendor/PROVENANCE-rfl-ae.md` | The manifest digest is `sha256` over the newline-joined `git ls-tree -r HEAD` output — a **defined projection**, not the manifest file itself |
| Git object model | A commit's hash is computed over the object's content; the hash is the object's **name**, not a field inside it |

§88 places the digest in a field, which is the one arrangement that cannot work. §86 says the digest is *"calculated over semantics"* — Option B makes that literally true; Option A makes it true modulo a declared exception. Either is fine; leaving it undecided is not.

### 2.4 Why this blocks more than it appears to

The circularity is not an edge case — it is on the critical path of the first three implementation steps:

| Step | Depends on |
|---|---|
| §102 step 1 — `compiled-pack.json` | §88's schema, including where `compiled_digest` lives |
| §101 `G08` — deterministic canonicalization | §84's rules; needs the exclusion defined to be deterministic |
| §101 `G09` — deterministic digest | §86; needs the input boundary defined |
| §101 `G15` — compiled-pack reproducibility | Both of the above, and requires two runs to agree byte-for-byte |

§101's own rule is *"NO GATE EXECUTION → NO VERIFIED GATE RESULT"*. G08, G09 and G15 cannot be executed against a specification whose digest input is undefined — so **three of v0.4's fifteen gates are unrunnable until §2 is resolved**, and v0.4's release cannot be claimed.

**This is the smallest defect in this part and the only blocking one.** One sentence in §84 and one field moved in §88.

---

## 3. v0.4's gate omits the invariant both v0.4 and Part XI state

§94's verification contract:

```yaml
claim_rules:
  no_evidence:      no_verified_claim
  partial_coverage: no_complete_claim      # ← the partial-coverage invariant
  unknown:          not_pass
  error:            not_fail
```

§101's release gate, item by item: schema, identity, dependencies, cycles, conflicts, authority, scope, canonicalization, digest, positive fixtures, negative fixtures, injection fixtures, evidence-schema, status-transition, reproducibility.

**There is no coverage item.** `partial_coverage: no_complete_claim` is stated as invariant in §94, is a core invariant in v0.2 §1 (*"PARTIAL COVERAGE → NOT COMPLETE"*), and is Part XI §170's `PACK-010` verbatim — *"partial coverage cannot produce VERIFIED"*. **It is the only Part XI gate item with no counterpart anywhere in §101.**

And it is not the least important one. From the audit:

- It is the `NO COVERAGE ≠ PASS` principle, the most-cited rule in this series.
- Part VI §78 found **four of seven substantive defects reduce to it**.
- Part III §21 measured it failing: the corpus sweep runs 5 of 11 invariants and prints `ALL FILES OK`.

**Consequence under §101's own rule.** A pack could pass all fifteen gates with the partial-coverage rule unenforced, because *nothing in the gate tests it*. §101 says a gate item must be *established*, and establishing G14 ("status-transition validation") does not establish §94's `partial_coverage` rule — §94's claim rules are a **contract**, and the gate is what **verifies** the contract. Here the contract contains a rule the gate does not check.

That is the Part XIII–XIV pattern once more, and this instance carries the highest measured cost.

**Correction.** Add to §101:

```text
G16  partial-coverage claim suppression
```

or fold it into G13/G14 explicitly. §170's `PACK-010` should be the text.

---

## 4. v0.4's gate and Part XI §170's gate: both 15 items, not the same 15

Two fifteen-item gates now exist for the same release. The correspondence:

| v0.4 §101 | Part XI §170 | Relation |
|---|---|---|
| G01 schema validation | PACK-001 schema valid | 1:1 |
| G03 dependency resolution | PACK-002 dependencies resolve | 1:1 |
| G05 rule conflict detection | PACK-003 no conflicts | 1:1 |
| G08 deterministic canonicalization | PACK-004 canonicalization deterministic | 1:1 |
| G09 deterministic digest | PACK-005 digest deterministic | 1:1 |
| G06 authority non-escalation | PACK-006 authority cannot self-escalate | 1:1 |
| G07 scope non-expansion | PACK-007 scope cannot be silently broadened | 1:1 |
| G12 injection fixtures | PACK-011 hostile input cannot alter authority | 1:1 |
| G11 negative fixtures | PACK-015 negative fixtures are detected | 1:1 |
| G15 compiled-pack reproducibility | PACK-014 repeated compilation identical | 1:1 |
| **G14 status-transition validation** | **PACK-008 + PACK-009** | **one item absorbs two** |
| G13 evidence-schema validation | PACK-013 evidence binds subject digest | **partial** |
| **G02 identity validation** | — | v0.4 only |
| **G04 cycle detection** | — | v0.4 only |
| **G10 positive fixtures** | — | v0.4 only |
| — | **PACK-010 partial coverage** | **Part XI only** |
| — | **PACK-012 execution binds pack digest** | **Part XI only** |

**Ten map one-to-one, one absorbs two, one is partial, three are new, and two have no counterpart.**

Two observations:

- **The two gates are structurally compatible.** v0.4's is the better-organised: G04 promotes cycle detection from a prose requirement (§162 of Part XI) to a gate, G02 promotes identity validation from §165, and G10 splits positive from negative fixtures. These are genuine improvements.
- **PACK-012's absence is arguably acceptable but should be stated.** *"Execution binds pack digest"* is covered by §88's inclusion of a `compiler` block (*"the compiler itself becomes part of provenance"*) plus G02 — but §88 binds the **compiler**, not the **execution**. Under v0.2 §24 and v0.3 §51, execution identity is bound by the ExecutionRecord, which is a §102 step-3 artefact and therefore post-gate. **Deferring it is defensible; leaving it implicit is the pattern.**

**Correction.** Reconcile the two gates into one numbered list, note where v0.4 improves on Part XI, and either add `PACK-010` or record why it was dropped.

---

## 5. §90 binds the runtime to the component that fails §94

§90's check contract is illustrated with a concrete check:

```yaml
checks:
  - id: corpus.numbering
    predicate:
      type: unique_section_numbers
      subject:
        type: markdown_corpus
    procedure: audit_corpus
    expected:
      status: pass
    evidence:
      required:
        - subject_identity
        - execution_record
        - observation
        - coverage_record
```

**This is the RFL-AE corpus audit** — the component audited across Parts I–VIII.

Two consequences, both measured:

**5.1 The procedure produces none of the four required evidence artifacts.** Re-verified: `vendor/rfl-ae/skills/` contains **no `import json` and no `json.dumps` anywhere**. Exactly one `.py` file mentions the string at all (`splice.py`, in a comment about fence languages). The toolchain emits human-readable text on stdout. So §90's `evidence.required` list names four artifacts that `audit_corpus` cannot produce. Under §101's G13 (*evidence-schema validation*), this check fails its own contract on the first run.

**5.2 The procedure has no status model to validate.** §101's G14 is *status-transition validation*; v0.2 §7 defines six states. Part IV §36–§37 established that `run_all.sh` collapses every stage outcome into a single `fail=1` bit, and that an uncaught exception exits 1 — the same code as a content finding. **A check whose procedure has one status bit has no transitions to validate.**

So the two gates that §94's contract depends on most directly — G13 and G14 — are unrunnable against the check §90 chooses as its example.

### 5.3 This also settles a question Part XIII left open

Part XIII §6.3 recorded that no checker emits structured output. That check used the pattern `json\.` and returned nothing; a plain `json` search returns **39 hits**, so I re-ran it. The result is stronger than first stated:

| Category | Count | Example |
|---|---|---|
| Prose about ```` ```json ```` fences | ~6 | `splice.py:17`, `SKILL.md:52` |
| **Schema filenames in design probes** | **~28** | `probes/protocol-v01.txt:420-427`, `probes/ksir-impl.txt:401` |
| A Rust type name | 1 | `protocol-p58.txt:344` — `serde_json::Value` |
| **Executable JSON emission** | **0** | no `import json`, no `json.dumps` |

**"Zero JSON emissions" is correct**, and the reason is more interesting than the claim: the hits are overwhelmingly **schema filenames inside probe documents**. See §8.

---

## 6. §102 sequences the pack build and still omits `PHASE 0`

§102's order is sound and correctly reasoned — *"the model-facing layer should be late, not foundational"* is right, and it is the first place in the lineage where the model is explicitly deprioritised:

```text
step 1  PACK.yaml → schema validator → canonicalizer → SHA-256 → compiled-pack.json
step 2  dependency → rule → authority → scope resolvers
step 3  execution records → evidence records → coverage records → verification gates
step 4  LLM prompt projection
```

**Part XI §171's `PHASE 0` — the seven live defects — appears nowhere.**

This is the third occurrence of the same sequencing question:

| Document | Position |
|---|---|
| Part X §149 | Protocol first; defects deferred |
| Part XI §171 | **`PHASE 0` first** — corrected §149.2's inverted justification |
| v0.4 §102 | Starts at Part XI's `PHASE 2`/`PHASE 3`; **no `PHASE 0`** |

And §101 makes it concrete. Part XI §170.1 established that five of the seven `PHASE 0` items are precisely what would move `PACK-008`, `PACK-009`, `PACK-010`, `PACK-014` and `PACK-015` from failing to passing — *"the gate is calibrated against observed failures."*

§101 replaces four of those five with G14 (*status-transition*), G15, G11 and — per §3 — drops the fifth. **So v0.4 does not merely omit `PHASE 0`; it restates the affected gate items in a form that no longer references the defects they were calibrated against.**

That is not an argument that §101 is worse. It is an argument that **the connection has been lost**, and with it the information that these gate items were derived from measured failures rather than from first principles.

**Correction.** Add to §102, before step 1:

```text
PHASE 0 — live defects in the audited toolchain
    C-1 renumber source number     C-2 fence awareness
    C-3 remove has_prov gate       C-7 structured negative assertions
    temporary-directory isolation  $PY consistency
    ERROR/FAIL separation
```

or state explicitly why the pack system is being built independently of the corpus layer it will first be pointed at — which §90's example makes it.

---

## 7. Disposition of Part XIV's findings

| Part XIV finding | Disposition in v0.4 |
|---|---|
| **XIV §3** — §46's scope object has no `claimed` field | **RESTATED, and a dangling reference added** |
| **XIV §4** — three coverage representations | **RESTATED** — §91 adds a fourth, §101 gates none |
| **XIV §5** — four check-specification variants | **RESTATED — now five** |
| **XIV §6.2** — `SKIPPED` unreachable | **DEFERRED** to G14, unsolved |
| **XIV §1.1** — pack identity undefined | **PARTIALLY CLOSED** — §73 defines pack identity; protocol identity still undefined (§1) |

### 7.1 XIV §3 — the field is still absent, and now something references it

v0.3 §46 mandated `CLAIMED ⊆ EXECUTED ⊆ DECLARED` on a scope object with five fields and no `claimed`.

v0.4 §77 defines a scope schema with `subjects`, `revisions`, `paths`, `operations.{allow,deny}` — **the declaration side only**, which is arguably cleaner: §77 says *"scope resolution must produce an explicit effective scope"*, so the resolved scope is an output rather than a field.

**But `executed_scope` now appears by name in §91:**

```yaml
- id: coverage
  requires:
    - declared_scope
    - executed_scope
```

**Nothing in v0.4 defines `executed_scope`**, and `claimed_scope` appears nowhere in the document. So the containment still cannot be evaluated, and §91 adds a reference to a term with no schema.

This is the same defect as Part XII §1's `has_prov` and Part XIII §3's digest: **a name required by a contract, with no definition anywhere in the specification.** It is now the third occurrence in this series, and it is the one that carries Part XI §155's highest-value property.

### 7.2 XIV §5 — the check specification has a fifth variant

| Field | v0.2 §11 struct | v0.2 §11 instance | v0.3 §47 list | v0.3 §47 example | **v0.4 §90** |
|---|---|---|---|---|---|
| id | ✓ | ✓ | ✓ | ✓ | ✓ |
| predicate | ✓ | ✓ | — | ✓ | ✓ |
| subject | ✓ | ✓ | ✓ | ✓ | ✓ (nested in predicate) |
| scope | ✓ | ✓ | ✓ | ✓ | **—** |
| procedure | ✓ | ✓ | ✓ | ✓ | ✓ |
| expected | ✓ | — | ✓ | ✓ | ✓ |
| observation | — | ✓ | ✓ | — | **—** |
| evidence | ✓ | ✓ | ✓ | ✓ | ✓ |
| **verifier** | ✓ | — | — | — | **—** |
| status | — | ✓ | — | — | — (in `expected`) |

**Five variants across four documents. `verifier` appears in one of five** — while §94's verification contract lists `verifier_identity` as **required**:

```yaml
verification_contract:
  required:
    - subject_identity
    - check_identity
    - verifier_identity      # ← required here
    ...
```

So §94 requires a verifier identity that §90's check contract — the object every claim must map to — has no field for. **The check specification and the verification contract are inconsistent within the same document**, which is the sharpest instance of this recurring defect so far.

### 7.3 XIV §6.2 — deferred, not solved

v0.3 §41's failure transitions have no path to `SKIPPED`, while §1 makes `SKIPPED → NOT PASS` mandatory. v0.4 §101's G14 (*status-transition validation*) would detect this — **if the transition set were specified**. §101 says the gate must establish it; nothing states what the transitions are. The gap is now assigned to a gate rather than filled.

---

## 8. The schemas have been proposed before, and never built

§5.3's re-run showed where the `json` hits in the toolchain actually come from. The largest group is **schema filenames in probe documents.**

`vendor/rfl-ae/skills/markdown-corpus-audit/probes/protocol-v01.txt`, lines 420–427:

```text
epoch.schema.json
task.schema.json
authorization.schema.json
artifact.schema.json
evidence.schema.json
verification.schema.json
gate.schema.json
certificate.schema.json
```

**Eight named schemas. None of the eight exists.**

And `evidence.schema.json` has now been proposed **three times**:

| Proposal | Where |
|---|---|
| `protocol-v01.txt:424` | an upstream design probe, never implemented |
| Part XI §169 | this audit's recommended skeleton |
| v0.4 §101 `G13` | *"evidence-schema validation"* — implicitly required |

**Three independent proposals for the same artifact, across an upstream design probe and two specification revisions, and the file does not exist in the repository.**

This is worth recording precisely because it is not a criticism of any one document. It is a measured property of the trajectory: **the specification lineage has been pointing at the same small set of artifacts for some time, and the substrate is absent across the board.** Part XIII §6.3 found no structured evidence layer; Part XIV §8.1 found one of five enforced invariants unnamed; Part XV §5 finds the named procedure produces none of the required artifacts. **Every one of those findings is downstream of the same absence.**

It also gives §102 step 1 a sharper justification than "start small": **it is the first step in this lineage that would produce a file rather than describe one.**

---

## 9. Minor items

### 9.1 §82's `BLOCKED` for pack-dependency failure is strained, and §82 knows it

§82:

```text
A dependency failure is: BLOCKED
unless the failure is itself a malformed pack definition, in which case: ERROR
```

That exception clause exists because the mapping is a poor fit. **Pack dependencies are resolved at compile time**, before any execution exists — so a missing pack dependency is a *compiler* failure to produce a valid artifact, which is `ERROR` by v0.2 §7's definition (*"verification machinery failed to produce a valid evaluation"*), not `BLOCKED` (*"legitimate execution could not proceed"* — there is no execution yet).

v0.3 §41 maps *"missing dependency → BLOCKED"* for **runtime** dependencies. v0.4 §82 maps pack dependencies the same way. **Two different layers, one word, one status**, and §82's exception clause is the seam showing.

Both readings are defensible — a resolver that cannot satisfy a declared dependency has not *failed*, it has been *blocked* by an unavailable input. But the specification should say which layer it means, since a pack-dependency failure and a runtime-dependency failure produce different evidence and different retry semantics. **MINOR / OPEN.**

### 9.2 §73 and §105 repeat the ambiguous chain notation

Part XIII §7 flagged `A ≠ B ≠ C` in v0.2 §1, where the convention reads as consecutive inequalities rather than pairwise distinctness. v0.4 uses the same form twice:

```text
§73   same ID ≠ same version ≠ same source ≠ same semantics        (4 terms)
§105  REQUEST ≠ AUTHORIZATION ≠ … ≠ RELEASE                         (7 terms)
```

For §105 the intent — seven mutually distinct layers — is exactly what a compiler must not mis-read, and §104 makes the compiler's job explicit. **MINOR, recurring for the third time.** The fix remains one line: state that all terms are pairwise distinct, or emit the pairs.

### 9.3 §93's prohibited claims reference an undefined predicate

```yaml
prohibited_claims:
  - unsupported_completeness
  - unsupported_verification
  - unsupported_release
```

*"Unsupported"* is defined in **v0.3 §69** (completeness), **v0.2 §1** (verification), and **v0.3 §30** (release) — in a different document, with no cross-reference. §93 supplies the mechanism for v0.3's prose rules, which is a genuine improvement, but the predicate that decides whether a claim is prohibited is not in v0.4. **MINOR** — an inline reference resolves it.

---

## 10. What v0.4 improves

Stated plainly, because much of the above is critical and most of v0.4 is correct:

| Change | Significance |
|---|---|
| **§72 concrete minimum schema** | First machine-readable pack definition in the lineage; the normative field list is explicit and short (8 fields) |
| **§73 pack identity** | `PackID / PackVersion / SourceDigest / CompiledDigest` — separates logical, declared, source, and semantic identity, and states `same ID ≠ same version ≠ same source ≠ same semantics` |
| **§77 structured scope** | `subjects/revisions/paths/operations` with `include`/`exclude` and `allow`/`deny` — the first scope **schema** in the lineage; Part XI §155 called scope the highest-value object and this is its first concrete form |
| **§78–§81 rules as data** | Rule schema with `kind`, `strength`, typed `predicate`, and an explicit 13-step deterministic resolution algorithm. Part XI §153/§156 asked for this; v0.4 specifies it |
| **§80 forbids five resolutions** | explicitly bans source/filename/parser order, recency, and model preference — each a way the audited toolchain *does* resolve ambiguity |
| **§85 explicit defaults** | *"missing authority → explicit read_only"* — closes the `has_prov`-style class where absence is implicitly resolved |
| **§89 procedures cannot introduce authority** | one sentence, and it is the rule the whole lineage needed |
| **§90 check contract** | gives a check a machine-readable shape for the first time |
| **§93–§94 output and verification contracts** | `prohibited_claims` and `claim_rules` turn v0.3's prose rules into enumerated, checkable entries |
| **§95–§99 the model boundary** | *"MODEL OUTPUT IS NOT AUTHORITY / NOT EVIDENCE / A PROPOSAL UNTIL COMMITTED BY THE RUNTIME"* — the clearest statement in the lineage, and §96's `PROJECTION_MISMATCH` makes projection drift detectable |
| **§102 correct sequencing** | the model-facing layer is explicitly **late**. This is the right call and it is stated first, not buried |
| **§103 non-goals** | nine explicit exclusions, including *"probabilistic rule resolution"* and *"LLM-generated security policy"* |
| **§104 the asymmetry** | *"make this progression possible… and make this progression impossible"* — the clearest statement of the project's purpose in the series |

---

## 11. Standing

| Part XV section | Standing |
|---|---|
| §1 Three documents, protocol identity undefined | **PROVED** — by dependency on v0.2 §7, v0.3 §21/§58/§69 |
| **§2 Self-referential digest** | **PROVED and BLOCKING** — §86 vs §88; §84 has no exclusion rule; §85 contradicts §84 |
| §2.3 Two precedents exist in-repo | **PROVED** — `PROVENANCE-rfl-ae.md`, Git object model |
| **§3 `PACK-010` absent from §101** | **PROVED** — §94 states the rule, §101 does not test it |
| §4 Both gates 15 items, not the same 15 | **PROVED by correspondence table** |
| §5 §90 binds to `audit_corpus` | **PROVED** — zero `import json`; one status bit |
| §5.3 "zero JSON emissions" re-verified | **PROVED, strengthened** — 39 `json` hits are prose and probe filenames |
| §6 `PHASE 0` absent from §102 | **PROVED** — third occurrence of the sequencing question |
| §7.1 `executed_scope` dangling reference | **PROVED** — required by §91, defined nowhere |
| §7.2 Fifth check variant; §90 vs §94 | **PROVED** — §94 requires `verifier_identity`; §90 has no field |
| §7.3 `SKIPPED` deferred to G14 | **OPEN** — gate assigned, transitions unspecified |
| §8 Eight schemas proposed, none built | **PROVED** — `protocol-v01.txt:420-427` |
| §9.1 §82's `BLOCKED` | **MINOR / OPEN** |
| §9.2 Chain notation (3rd occurrence) | **MINOR** |
| §9.3 §93's undefined predicate | **MINOR** |

### Corrections, in dependency order

**1. §2 — resolve the digest boundary. One sentence, and it blocks three gates.**
Add to §84: *"the digest input is the canonical form excluding `identity.compiled_digest`"* — or move the digest out of the object per §2.3's Option B and adopt the Git model. **Nothing else in §102 step 1 can be completed first.**

**2. §3 — add `PACK-010` to §101.** One line. The rule is stated in §94 and tested by nothing.

**3. §7.2 — reconcile §90 with §94.** Add `verifier` to the check contract. §94 already requires it; five variants of the check object have accumulated.

**4. §6 — add `PHASE 0` to §102**, or state why the pack system is built independently of the corpus layer that §90 points it at.

**5. §7.1 — define `executed_scope` and `claimed_scope`,** and place the containment from v0.3 §46 where it can be evaluated.

**6. §4, §9 — reconcile the two gates; label §82's layer; inline §93's predicate.**

### The closing judgement

**v0.4 is the best document in the lineage.** It is concrete where its predecessors were descriptive, it sequences the work correctly, it puts the model last, and §104 states the project's purpose better than anything before it.

Its problem is different in kind from Parts XII–XIV's. Those found a specification whose rules were right and whose objects were inconsistent — a soft, recurring, survivable defect. **§2 is hard: §102's first step cannot be built until one sentence is written, and §101's release gate cannot be executed until it is.**

That is progress, and it should be said plainly. A blocking defect on the critical path is a better problem than an unenforceable invariant, because it stops work rather than permitting bad work.

**And §8 is the reason all of this matters.** Eight schemas were named in a design probe. `evidence.schema.json` has now been proposed three times across two specification revisions and an upstream document. The audited toolchain emits no structured output at all. Part XI §150 measured the layers — *"Instruction absent, Evidence absent-as-structure."*

**Five parts of analysis have now been written against this lineage. Every one of them converges on the same missing thing: the artifacts are specified and none of them exist.**

The single most useful next action is therefore not a v0.5 and not another analysis. It is §102 step 1 — `PACK.yaml` → schema validator → canonicalizer → digest → `compiled-pack.json` — after §2's one-sentence fix. **It is the first step in this lineage that produces a file rather than describing one**, it is small enough to complete, and it is the only way any of the fifteen gates can ever be executed.
