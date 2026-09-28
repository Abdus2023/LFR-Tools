# RFL-AE Prompt Packs — Part XIX: Reference Implementation Blueprint v0.8 — Closures and an Internal Contradiction

**Subject:** [`rfl-ae-reference-implementation-blueprint-v0.8.md`](rfl-ae-reference-implementation-blueprint-v0.8.md) (§272–§361), continuing [`v0.7`](rfl-ae-executable-protocol-types-v0.7.md), [`v0.6`](rfl-ae-protocol-schemas-v0.6.md), [`v0.5`](rfl-ae-prompt-instructions-v0.5.md), [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md)
**Series:** [`part15`](rfl-ae-prompt-packs-part15.md) · [`part16`](rfl-ae-prompt-packs-part16.md) · [`part17`](rfl-ae-prompt-packs-part17.md) · [`part18`](rfl-ae-prompt-packs-part18.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — the strongest closure set in the series, and a new defect class
>
> **v0.8 closes more carried findings than any previous document** — atomicity, crash-vs-FAIL, CI authority, self-certification, checker independence, conformance-percentage, and the false-PASS chain, each with a section that states the rule in the form the audit needed.
>
> Two of those closures are direct answers to the audit's own critical findings:
>
> - **§358** — atomic persistence with read-back verification, and *"`"Atomic"` SHALL not be used as a synonym for `"durable."`"* (line 2021). That is the exact distinction Part XV needed to characterise `renumber.py`'s `open(out, "w")`.
> - **§342** — *"This reduces the risk of: implementation bug = verifier bug = false PASS."* That is Part IV §0.1's `mapping errors=0`, named as a failure chain and given a structural remedy.
>
> **And it introduces a new defect class: an internal contradiction with line numbers.**
>
> §305's coverage algorithm contains the branch **"PARTIAL or UNKNOWN"** (line 823) — one input, two legal outputs. §308, fifty-three lines later, requires *"Equivalent inputs SHALL produce equivalent results"* (line 876). **The coverage engine is specified to violate the gate determinism contract, in the same document.** Every prior finding in Parts XIII–XVIII concerned a property failing to carry *between* documents; this one is within one.

---

## 1. Standing: §0–§361 across seven documents

v0.8 continues from v0.7, and this time the numbering is clean: **§272–§361, ninety sections, verified contiguous.** No gap corresponding to §200.

The effective specification is now **361 sections across seven documents.**

### 1.1 The lineage's own instruction to stop specifying

§361 ends the document, and the document ends the lineage:

> *"v0.8 is the implementation boundary. The next stage is no longer primarily a prompt specification. It is the RFL-AE Protocol Kernel implementation itself: first the actual schema files and Rust types, then executable validators and transition tests…"*

**That is the correct assessment, and it matches the conclusion Parts XV–XVIII reached independently** — each ending with the observation that every artifact has been specified and none exists. §361 is the first place the specification agrees with its own audit.

---

## 2. Closures

### 2.1 §358 — the persistence finding, closed with the distinction it needed

Parts II, III and V found that the audited toolchain writes non-atomically and cannot tell durability from a successful write. `renumber.py:111` is a plain `open(out, "w")` — no `tempfile`, no `os.replace`, no `flush`, no `fsync` (verified in Part XV).

**§358:**

```text
temporary artifact → write → flush/fsync as required
→ atomic rename/commit → read-back → digest verification

The exact durability guarantee SHALL be documented.
"Atomic" SHALL not be used as a synonym for "durable."
```

**And §359 requires read-back revalidation** — persist, read, parse, validate, canonicalize, digest, compare, with *"Mismatch SHALL invalidate the persistence operation."*

**§357 supplies the boundary rule:** *"A successful write operation alone SHALL NOT establish durability."*

This is the full chain the audit asked for, and the final sentence is the one that matters most: the audited system conflated the two, and the protocol now forbids the conflation by name.

### 2.2 §342 and §298 — the self-certification finding, closed twice

Part IV §0.1's central receipt: `renumber.py` fabricated a source number, and `auditlib` certified it with `mapping errors=0`. The verifier certified its own defect.

**§342 names the failure mode as a chain:**

```text
This reduces the risk of:
implementation bug = verifier bug = false PASS
```

**§298 states the structural rule:**

```text
Bad:      implementation → writes "PASS" → checker reads "PASS"
Required: implementation → observation → independent predicate → checker → result
```

**And §348 closes the statement-level route:**

> *"The implementation SHALL NOT generate a final statement such as `"RFL-AE is verified."` and use that statement as evidence of verification. The actual gate artifact is authoritative."*

Three sections, three different mechanisms — the same finding. Part XVIII §2.2 recorded §263 of v0.7; v0.8 adds the concrete failure chain and the two implementations of the rule.

### 2.3 §3 — §318 states the audit's P0 item 4 for the third time

The consolidated audit's ranked priority list, item 4: **`neg()`: require structured findings, not a non-zero exit.**

| Document | Statement |
|---|---|
| v0.7 §258 | *"Forbidden: `assert subprocess.run(...).returncode != 0` without establishing why the command failed"* |
| **v0.8 §318** | *"A shell script MUST NOT infer semantic status solely from: `returncode != 0`"* |
| v0.8 §319 | 15 typed error classes, so *"negative tests can assert the actual failure class"* |

§318 also freezes an exit-code mapping (0–5) with the note *"The exact mapping SHALL be frozen before release."*

The three statements are complementary rather than redundant: §258 prohibits the assertion, §318 prohibits the inference, §319 supplies the vocabulary that makes the prohibition enforceable. **This is the most-recommended fix in the entire audit**, and it now has a normative home with a taxonomy behind it.

### 2.4 §330, §335 — two more closures against measured defects

| § | Rule | Carried finding |
|---|---|---|
| **§330** | Checker error → `CheckResult = ERROR`; *"SHALL NOT convert checker error into PASS or automatically into FAIL unless the gate policy explicitly defines that mapping"* | Part VII §0.2 — `ModuleNotFoundError` → `PASS (exit 1, 0 findings)` |
| **§335** | *"`LOCAL RESULT ≠ CI RESULT`. A local developer report cannot substitute for a CI execution record."* | Part VII §92 — the repository has no CI at all; Part VII §80 — publication is prescribed but unverifiable |

§330's final clause is the sharper half: it forbids *both* collapses, and permits the `ERROR → FAIL` mapping only if a policy declares it. That is the `ERROR ≠ FAIL` invariant with an escape hatch that must be explicit — the right shape.

### 2.5 Five more rules that answer carried findings

| § | Rule | Finding it answers |
|---|---|---|
| **§340** | *"A conformance summary SHALL NOT calculate `passed / total` and call that percentage 'verified'"* | `NO COVERAGE ≠ PASS`; Part V §0.1's `PROV = n/a` inside `ALL FILES OK` |
| **§326** | Scope-escalation fixture: *"The system SHALL NOT silently transform `A` into `A + B`"* | Part VI §78's `SCOPE_COVERED` conjunct |
| **§306** | Duplicate logical members must not count twice; *"Population identity SHALL be canonical"* | Part V §55 — deleting a corpus file made the corpus self-consistently report 32 docs |
| **§338** | *"same version / different artifact"* must be distinguishable; digest, not version string | Part V §56 — no commit/version/hash binding anywhere in the toolchain |
| **§351** | Seven explicit non-claims — *"the agent is safe… the verifier has no defects… the external world state is truthful"* | The entire audit's thesis, stated as a section |

**§351 is the most valuable single section in v0.8.** Seven sentences, each naming something conformance does not establish. It is `OBSERVED ≠ VERIFIED` and `VERIFIED ≠ RELEASED` generalised into a list of what a passing harness actually warrants — and it is the section most likely to be skipped by a reader who wants the gate to mean more than it does.

### 2.6 §333–§334 — two new surfaces

`unsafe` must be justified, isolated, tested, and evidenced (§333); Rust protocol code SHALL be tested with Miri before release, with the run recorded as `tool identity · Rust version · Miri version · commit · test selection · result` (§334).

§334's qualifier is the one that matters and it is stated correctly: *"A local Miri run is execution evidence for that run. It is not automatically CI evidence."* That is §335's rule applied one section earlier, to the tool the section introduces.

---

## 3. HEADLINE: §305's algorithm is non-deterministic, and §308 forbids it

### 3.1 The contradiction

**§305 — Coverage Algorithm** (line 823):

```text
if declared scope unresolved:
    UNKNOWN

else if unsupported exists:
    PARTIAL or UNKNOWN        ← one input, two legal outputs

else if missing exists:
    PARTIAL

else if skipped exists:
    PARTIAL

else:
    COMPLETE
```

**§308 — Gate Determinism** (line 876):

> *"Equivalent inputs SHALL produce equivalent results. `Evaluate(P, C, E, V) = Evaluate(P, C, E, V)`"*

**Fifty-three lines apart, in one document.** §307 additionally requires the gate evaluator to be *"a pure function where practical"*, and §249 of v0.7 requires the gate function to be *"deterministic for equivalent inputs."*

### 3.2 Why this is a new defect class

Every finding in Parts XIII–XVIII concerned a property that failed to carry **between** documents:

| Part | Defect | Span |
|---|---|---|
| XVII §3 | `BLOCKED` reintroduced into the execution states | v0.5 → v0.6 |
| XVIII §3 | `claimed` dropped | v0.6 → v0.7 |
| XVIII §4 | `verifier` missing from five check objects | v0.2 → v0.7 |
| XVIII §7 | Two inverted build orders | v0.6 → v0.7 |

**This one is internal.** §305 and §308 are sections of the *same* document, five sections apart, and they cannot both be satisfied. That is a different failure mode: not a lost invariant, but a **branch an implementer must resolve by choosing**, in a design whose entire purpose is to remove the choice.

And the branch is not incidental. §305 governs how coverage is calculated; §308 governs whether the gate is trustworthy. A coverage engine that may return `PARTIAL` or `UNKNOWN` for identical inputs makes §341's *"required fixtures missing from execution SHALL prevent a complete conformance claim"* undecidable, and §308's determinism claim false.

### 3.3 The escape clause does not cover it

§305's final line: *"The precise policy SHALL be versioned."* That defers the resolution to a versioned policy artifact, which is a reasonable structural answer — but as written, the algorithm **is** the specification, and the algorithm admits two answers. §308's own escape clause (*"unless explicitly part of the gate policy"*) does not reach a coverage algorithm.

**Correction.** §305 must select one branch, or state the condition that distinguishes them. The natural form, given §305's own structure:

```text
else if unsupported exists:
    if unsupported ⊇ any required population:  UNKNOWN
    else:                                       PARTIAL
```

Whatever the rule is, one input must produce one output. **This is the single highest-value correction in Part XIX**, because it is the only finding that would be reproduced as a defect by a faithful implementation of the specification.

---

## 4. `claimed` is still absent — third document — but §304 partially compensates

Part XVII §2.1 recorded `claimed` as **CLOSED** at v0.6 §154. Part XVIII §3 recorded it **dropped** in v0.7 and restated the finding. Measured in v0.8:

| Term | v0.6 | v0.7 | v0.8 |
|---|---|---|---|
| `claimed` | 1 | 0 | **0** |
| `permitted` | 3 | 0 | **0** |
| `CLAIMED` | 1 | 0 | **0** |

### 4.1 What v0.8 does supply

**§304 — Coverage Evaluator:**

```text
missing = declared - checked
extra   = checked - declared
```

*"The evaluator SHALL classify both. Unexpected extra observations SHALL NOT automatically expand the declared scope."*

**That is the set algebra Part XIV §3 found missing**, and `extra` is a term the earlier documents did not have — it is precisely the condition §326 forbids (`A` silently becoming `A + B`). §304 and §326 together give the containment an operational form at the coverage layer.

### 4.2 What remains

Two things, and the second is new:

1. **`claimed` is not a field in any schema.** §304 computes `declared - checked`; the claim side of `CLAIMED ⊆ EXECUTED` has no representation, so `CLAIMED > EXECUTED` — the condition Part XI §155 said *"must be impossible to classify as VERIFIED"* — remains inexpressible.
2. **§326 introduces a term with no definition.** The fixture's expected outcome is *"`B ∉ declared claim scope`"* — and `claim scope` is not defined in v0.8, v0.7, or v0.6. It is a third name for the missing concept, following v0.3 §46's `claimed` and v0.6 §154's `claimed`.

So the disposition is **PARTIALLY ADDRESSED**: the executed-vs-declared algebra now exists, the claimed-vs-executed comparison does not, and §326 references a term that no document defines. Part XIV §3 is **RESTATED (3rd)**.

---

## 5. `Claim` has a schema and a strength model — and no identifier

v0.8 makes `Claim` a first-class protocol object:

- **§310** — `GateResult → ClaimGenerator → Claim`, with *"The claim generator SHALL NOT invent evidence."*
- **§311** — a four-level strength model: `OBSERVED → SUPPORTED → VERIFIED → RELEASE_GATED`, each with a worked example.
- **§281** — `claim.v1.schema.json` in the schema tree.

**Measured:**

| | Count |
|---|---|
| §281 schemas | **14** |
| §275 identifier newtypes | **13** |
| Difference | exactly `claim` |
| `ClaimId` in §275 | **absent** |
| `claim_id` in v0.8 | **0** |
| `claim_id` in v0.7 | **0** |
| `claim_id` in v0.6 | **0** |

**A protocol object with a schema, a generator, a strength model, and no identifier — in three consecutive documents.**

This matters more than it looks. §310 requires a generated Claim, §311 requires it to carry a strength level, §349's release manifest must reference `evidence_ref` and `gate_ref`, and v0.6 §152 requires *"an identifier MUST NOT be treated as cryptographic integrity evidence unless it contains or is explicitly bound to a cryptographic digest."* v0.7 §223 binds `evidence_id` to content (`evidence:sha256:...`).

**No such provision exists for a Claim.** So the one object whose purpose is to state what may be claimed is the one object that cannot be referenced, versioned, or bound to the state it summarises — and §146 of Part XI's original argument was that *claims must be derivable and inspectable*.

**Correction.** Add `ClaimId` to §275 (13 → 14, matching §281), and bind it per v0.7 §223's pattern, since a Claim is the artifact whose integrity matters most.

---

## 6. The execution/status collision persists — third document

Part XVII §3 established that v0.5 §116's eight execution states had **zero overlap** with v0.2 §7's six verification states, verified by set difference. Part XVII §3 also found v0.6 §160 reintroduced `BLOCKED`. Part XVIII §4 found v0.7 §227 gave `"ERROR"` and `"BLOCKED"` to both TypeScript unions.

**v0.8 §278:**

```rust
pub enum ExecutionState {
    Created, Authorized, Running, Succeeded,
    Failed, Error, Cancelled, TimedOut, Blocked,
}
```

**v0.8 §277:**

```rust
pub enum CheckStatus { Pass, Fail, Error, Unknown, Skipped, Blocked }
```

**Measured shared variants: `Blocked`, `Error`.**

And §277 states: *"Stringly typed status handling SHALL NOT be used in the core protocol."*

### 6.1 Where the guarantee holds and where it does not

In **Rust**, the two enums are nominal types, so `CheckStatus::Error` and `ExecutionState::Error` cannot be confused — §208 of v0.7's stated intent is satisfied *within Rust*.

But:

- **§343 requires cross-language agreement**: *"Rust and TypeScript SHALL consume identical canonical fixtures."* v0.8 does not restate the TypeScript unions, so v0.7 §227 governs — and there, `"ERROR"` and `"BLOCKED"` satisfy both types structurally.
- **§317 requires machine-readable output** and §336 records evidence sources; on the wire, the values are strings and the types are gone.
- **v0.7 §201 required all three representations to preserve the same semantics** — so a guarantee holding in one representation and not another is the divergence §201 names.

**§277's own rule is the one that applies:** *"Stringly typed status handling SHALL NOT be used in the core protocol"* — and JSON, the canonicalization target of §284 and the digest input of §283, is stringly typed by construction.

**Third consecutive document carrying this collision.** §3's headline is a within-document contradiction; this is the cross-document one, now in its third instance.

---

## 7. §274's workspace chain omits two of §273's eight crates

§273 lists eight crates: `rfl-protocol`, **`rfl-canonical`**, `rfl-validation`, `rfl-transition`, `rfl-evidence`, `rfl-coverage`, `rfl-gate`, **`rfl-conformance`**.

§274's workspace dependency chain:

```text
rfl-protocol → rfl-validation → rfl-transition
→ rfl-evidence → rfl-coverage → rfl-gate
```

**Measured: six crates. `rfl-canonical` and `rfl-conformance` are absent**, and the chain also drops §274's own header claim of *"identifiers, timestamps, digests, enums, protocol objects."*

`rfl-canonical` is not a peripheral omission. §353's implementation dependency graph places **Canonicalizer between Digests and Schemas** — on the critical path — and §283's canonicalization pipeline and §284's contract are both normative. A workspace diagram that omits the crate implementing them is inconsistent with the graph one section later.

This is **Part XVII §7's finding** (trees versus chains disagreeing) recurring, and it is minor in isolation. It is worth recording because §274 is titled *"Rust Workspace Boundary"* and ends *"No dependency cycle SHALL exist"* — a boundary rule stated over an incomplete graph cannot be checked for cycles in the omitted nodes.

---

## 8. Smaller findings

### 8.1 §312's event list and §313's sequence disagree by one

| | Count | Members |
|---|---|---|
| **§312** minimum events | **10** | … `GateEvaluated`, `ClaimDerived` |
| **§313** numbered sequence | **9** | … `GateEvaluated` |

**`ClaimDerived` is in the required list and absent from the canonical sequence.**

§313 says *"Events SHOULD contain monotonic sequence numbers"* and *"Missing sequence entries SHALL be detectable."* So the document that requires gap detection contains a gap in its own example — and the missing event is the last step of §311's claim derivation, which §310 makes mandatory.

**MINOR**, and it is the same shape as §200's numbering gap and §274's omitted crates: three instances in two documents of an enumeration and its expansion not matching.

### 8.2 `NONE` was dropped between v0.6 and v0.7, and stayed dropped

| Document | Coverage states |
|---|---|
| v0.6 §164 | `COMPLETE`, `PARTIAL`, **`NONE`**, `UNKNOWN` |
| v0.7 §210 | `COMPLETE`, `PARTIAL`, `UNKNOWN` |
| v0.8 §277 | `Complete`, `Partial`, `Unknown` |

`NONE` is gone, and `NONE` appears **zero times** in v0.8.

**This is a dropped token rather than a hole, and I want to state that precisely rather than overstate it.** §305's algorithm still yields a defined answer for both zero cases: with a non-empty declared scope and nothing checked, `missing = declared - checked = declared`, so the result is `PARTIAL`; with no declared scope at all, the first branch gives `UNKNOWN`. Both cases are covered.

But it is the *third* dropped token in this series — after `claimed` and the v0.5 execution states — and it removes the state that named the condition the audit kept measuring: `NO COVERAGE ≠ PASS` had a symbol in v0.6 and now reports as `PARTIAL`.

### 8.3 Method note: two of my own checks were lexical, not semantic

Recorded because the audit's thesis is that this class of error is the dangerous one, and this is the second part in which I made it.

| Check | What I did | What went wrong |
|---|---|---|
| §336 evidence sources | regex `^[A-Z]{3,}$` | **`CI` is two characters.** Corrected count: **6**, not 5 |
| §281 schema stems | `'claim' in schemas` | Stems are `claim.v1`, not `claim`. §281 **does** include the claim schema. Corrected: present |
| §358 durability | f-string regex | Pattern error; the sentence **is** present at line 2021 |

All three were caught in the same run by reading the output rather than the boolean, and all three are corrected above. **The lesson is the one `has_prov` teaches:** a lexical test standing in for a semantic one returns a confident wrong answer, and the only defence is checking the result against the input rather than trusting the predicate.

### 8.4 §353's dependency graph is drawn ambiguously

The graph shows `Digests ───────┐` with a branch that appears to bypass `Canonicalizer` and `Schemas` to reach `Semantic Validator`. Whether `Digests` feeds both `Canonicalizer` and `Semantic Validator`, or only the former with the branch belonging to `Canonicalizer`, is not determinable from the rendering. §353 requires the graph to *"remain acyclic"* — for which the edges must be unambiguous. **MINOR**, and noted as a rendering question rather than a specification defect.

---

## 9. The pack instance: untouched for the third document

Part XV §2's blocking defect — v0.4 §86's `CompiledPackDigest = SHA256(CanonicalCompiledPack)` over an artifact containing `compiled_digest` — remains unresolved.

**Measured in v0.8:**

| Token | Occurrences |
|---|---|
| `CompiledPackDigest` | **0** |
| `compiled_digest` | **0** |
| `CompiledPack` | **0** |
| `pack` | 2 |

v0.8 does not mention the pack protocol at all. That is coherent — v0.7 and v0.8 specify the *runtime* protocol objects, and the pack is a separate artifact — but it means the defect blocking v0.4 §102 step 1 has now survived **three consecutive documents that were written to enable implementation.**

**And v0.8 supplies the general remedy without applying it.** §302:

```text
EvidenceId = digest(canonicalize(evidence_without_evidence_id))
```

This is the same `without_self` construction Part XV §2.3 proposed as Option A, stated a second time and again scoped to a single artifact. §284 defines canonicalization and lists nine requirements, exactly as v0.4 §84 and v0.6 §185 did, and again contains **no exclusion rule** — so the canonical form of an object that contains its own digest is still undefined.

**The rule exists in the lineage twice (§239 of v0.7, §302 of v0.8). The instance has not been touched.** Part XV §11 said the next artifact is a file; that remains true, and §302 shows how little would be needed to unblock it.

---

## 10. Standing

| Part XIX section | Standing |
|---|---|
| §1 §272–§361 contiguous | **PROVED** — 90 sections, no gap |
| §2.1 §358/§359/§357 persistence | **CLOSED** — atomic ≠ durable stated; read-back required |
| §2.2 §342/§298/§348 self-certification | **CLOSED** — three mechanisms |
| §2.3 §318/§319 returncode rule | **CLOSED** — third statement, now with a taxonomy |
| §2.4 §330/§335 | **CLOSED** |
| §2.5 §340/§326/§306/§338/§351 | **CLOSED** |
| §2.6 §333/§334 | **NEW** — first `unsafe`/Miri contract |
| **§3 §305 vs §308** | **PROVED — within-document contradiction**; lines 823 vs 876 |
| §4 `claimed` absent; §304 algebra added | **PARTIALLY ADDRESSED**; `claim scope` in §326 undefined; **RESTATED (3rd)** |
| §5 `Claim` with schema, no id | **PROVED** — 14 schemas vs 13 ids; `claim_id` = 0 in three documents |
| §6 `Blocked`/`Error` shared | **PROVED** — 3rd document |
| §7 §274 omits 2 of 8 crates | **PROVED** |
| §8.1 §312/§313 off by one | **PROVED** — `ClaimDerived` |
| §8.2 `NONE` dropped | **PROVED** — dropped token, not a hole |
| §8.3 My own lexical errors | **SELF-CORRECTED** — 3 instances, all caught |
| §9 Pack instance untouched | **PROVED** — 0 occurrences, 3rd document |

### Corrections, in dependency order

**1. §3 — resolve §305's double output.** One input must produce one output; §308's determinism claim and §341's completeness rule both depend on it. This is the only finding a faithful implementation would reproduce as a defect.

**2. §5 — add `ClaimId`.** 14 schemas, 13 identifiers; bind it per §302's pattern.

**3. §6 — resolve §277/§278.** §277 forbids stringly-typed status; §278 shares `Blocked` and `Error` with §277's own enum, and the JSON wire format is stringly typed.

**4. §4 — define `claim scope`** and give the claim term a schema field, or remove §326's reference to it.

**5. §7 — complete §274's chain** to match §273's eight crates before asserting acyclicity over it.

**6. §8.1 — add `ClaimDerived` to §313's sequence.**

**7. — still first on the critical path: v0.4 §86.** §302 states the remedy a third time. Applying it to the pack is one sentence, and it is the only thing standing between §102 step 1 and a first artifact.

### Closing judgement

**v0.8 closes more carried findings than any document in this series, and several are closings the audit had been recommending for seventeen parts.** §358 supplies atomicity *and* the atomic/durable distinction. §342 names the `implementation bug = verifier bug = false PASS` chain. §318 states the `returncode` prohibition a third time with a fifteen-class taxonomy behind it. §340 forbids the `passed / total` percentage. §351 lists seven things conformance does not prove. §333 and §334 open the `unsafe` and Miri surfaces. That is a substantial, well-aimed document, and §361's own conclusion — that the next stage is implementation — is the right call.

**Its principal defect is the first of its kind:** not a property lost between documents, but a branch in §305 that §308 forbids, in the same document. It is more tractable than the defects of Parts XVII and XVIII, because both halves are on one page — and it is more serious, because a specifier reading only §305 would implement a non-deterministic coverage engine and consider it conformant.

**And the substrate is still absent.** Nine documents now specify `evidence.schema.json` and none exists. v0.4 §86 has survived three documents written to enable implementation, while §302 demonstrates that the lineage states the remedy correctly every time it introduces a digest over a self-referential object.

The lineage has reached the boundary it declared. §361 says so; Parts XV through XIX say so; and the evidence — 361 sections, 14 schemas, 13 identifier types, eight crates, seven gate enumerations, zero files — says so most clearly.

---

## 11. Appendix — Provenance re-verification of v0.8

**Added after Part XIX was first published.** v0.8 was supplied a second time. This appendix records what the comparison found, and one defect it exposed in this audit's own recording practice.

### 11.1 Method

Both supplies were reduced to the section range §272–§361, then normalized by removing all whitespace, all backticks, and all code-fence language markers. In the recorded file, the `---` separators described in §11.3 were also removed. The two normalized character streams were compared with a sequence matcher.

### 11.2 Result

| Measure | Value |
|---|---|
| Recorded (normalized) | 23,490 characters |
| Re-send (normalized) | 23,495 characters |
| **Similarity** | **0.99985102** |
| Differing blocks | **7** — one em-dash, six slashes |
| Semantic differences | **0** |

The seven deltas:

| # | Delta | Disposition |
|---|---|---|
| 1 | §272 heading: recorded has `—`, re-send has two spaces | **Cosmetic.** Heading punctuation; no content change |
| 2–7 | §273 `tools/` subtree: re-send has trailing slashes (`schema-check/` … `conformance/`), recorded did not | **Corrected** — every other directory in the same tree carries a trailing slash, so the re-send is the faithful form |

**And the checks that matter for Part XIX's findings, run against the re-send:**

| Check | Re-send | Recorded |
|---|---|---|
| §305 contains `PARTIAL or UNKNOWN` | **yes** | yes |
| §308 contains *"Equivalent inputs SHALL produce equivalent results"* | **yes** | yes |
| Section range | §272–§361, 90 sections, contiguous | same |

**So the headline finding of §3 above survives re-verification: both supplies specify a coverage algorithm with two legal outputs and a gate contract that forbids exactly that.**

### 11.3 A defect in this audit's own recording practice — declared

The comparison exposed an undeclared addition of mine.

**The recorded documents contain a horizontal rule `---` before each section heading.** Measured across the corpus:

| Document | Sections | `---` lines | Match |
|---|---|---|---|
| v0.1 | 28 | 29 | no (+1) |
| v0.2 | 41 | 41 | yes |
| v0.3 | 30 | 30 | yes |
| v0.4 | 35 | 35 | yes |
| v0.5 | 44 | 44 | yes |
| v0.6 | 50 | 50 | yes |
| v0.7 | 71 | 71 | yes |
| v0.8 | 90 | 90 | yes |

One separator per section, consistently, across all eight recorded documents — and **the source supplies contain none.** My provenance headers declared the restoration of *"line breaks, indentation, and fenced structure"*, which does not cover inserted separators. The claim *"no wording was added"* was true of wording and false of markup.

This is worth recording as more than a housekeeping note, because it is **this audit's own instance of the failure the audit exists to find**:

- The lineage's rule is `representation ≠ semantics`, and §283 requires canonicalization to be explicit about representation.
- A recording that adds structure while declaring only a narrower set of transformations is exactly the class of undeclared transformation the lineage forbids.
- The addition was harmless — but it was **undetectable from the artifact itself**, which is the property that makes undeclared representation changes dangerous: a later reader comparing the recording to the source would find differences they could not attribute.

**Corrected by declaration, not by removal.** The separators are retained for navigability; they are now declared in v0.8's provenance header as a corpus-wide convention. Declaring is preferred to removing because removal would make the eight recorded documents mutually inconsistent in structure, and because the honest fix for an undeclared transformation is to declare it.

**Limitation, stated plainly:** only v0.8 was supplied twice, so only v0.8 could be re-verified against a source. The other seven documents' derivations **cannot** be checked from what is on disk, and this appendix makes no claim about them beyond the separator count.

### 11.4 What this does not change

Nothing in §1–§10. The re-send is the same document: same 90 sections, same §305 defect, same §308 rule, same inventories. **No Part XX analysis is warranted, and none was written** — the correct response to a duplicate supply is to verify it is a duplicate and say so, not to manufacture a new pass.
