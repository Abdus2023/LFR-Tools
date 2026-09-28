# RFL-AE Prompt Packs — Part XXI: Conformance, Evidence & Release v1.0 — Two Carried Items Move, and §1–§28 Is Occupied Twice

**Subject:** [`rfl-ae-conformance-evidence-release-v1.0.md`](rfl-ae-conformance-evidence-release-v1.0.md) (§456–§555), continuing [`v0.9`](rfl-ae-executable-protocol-kernel-v0.9.md), [`v0.8`](rfl-ae-reference-implementation-blueprint-v0.8.md), [`v0.7`](rfl-ae-executable-protocol-types-v0.7.md), [`v0.6`](rfl-ae-protocol-schemas-v0.6.md), [`v0.5`](rfl-ae-prompt-instructions-v0.5.md), [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md), [`v0.1`](rfl-ae-master-agent-instructions-v0.1.md)
**Series:** [`part17`](rfl-ae-prompt-packs-part17.md) · [`part18`](rfl-ae-prompt-packs-part18.md) · [`part19`](rfl-ae-prompt-packs-part19.md) · [`part20`](rfl-ae-prompt-packs-part20.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — the two longest-carried open items both move
>
> **v1.0 restores `CLAIMED`.**
>
> v0.6 §154 added `claimed` and `CLAIMED ⊆ EXECUTED ⊆ PERMITTED ⊆ DECLARED`. v0.7 dropped it, v0.8 dropped it, v0.9 dropped it. Parts XVIII §3, XIX §4 and XX §5 each recorded the absence. **v1.0 §545 restores it** — `claimed_scope ⊆ executed_scope`, and `CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED` as **a release-critical invariant**. Measured: `claimed` 0→1, `CLAIMED` 0→1, `claimed_scope` 0→1.
>
> **And v1.0 re-enters the pack protocol after six documents.**
>
> §541–§544 bind prompt packs to evidence for the first time since v0.4. **§541 places both digests outside the artifact** — *"evidence SHALL bind: pack identity, pack version, pack digest, compiled representation digest"* — which is structurally the fix for the circularity Part XV §2 identified in v0.4 §86 and carried through six parts.
>
> **But the measurement also produced something I was not looking for, and it invalidates a claim I have been making in every provenance header.**
>
> **§1–§28 is occupied twice, by different content.** Measured: v0.1 and v0.2 share **28 section numbers and 0 identical headings.** v0.1 §4 is *AUTHORITY*; v0.2 §2 is *AUTHORITY*. v0.1 §3 is *TASK INTAKE*; v0.2 §4 is *TASK INTAKE*.
>
> So *"the effective specification is §0–§555"* is **not a valid global citation space**, and my headers have been undercounting documents in the same breath. Both are corrected in §7.

---

## 1. Standing

**The transition is clean.** v0.9 ends at §455; v1.0 opens at §456. Measured: **100 sections, §456–§555, contiguous.**

| Measure | Value |
|---|---|
| Documents supplied | **10** |
| Union of section numbers | **§0–§555** |
| Gaps in the union | **one: §200** |
| Documents forming one numbered progression | **9** (v0.2 §0–§40 → v1.0 §456–§555) |
| Documents whose numbers overlap another's | **2** (v0.1, v0.2 — see §7) |

---

## 2. What v1.0 closes

This is the largest closure set in the lineage, and it is aimed almost entirely at the audit's own findings.

### 2.1 §554 is the audit's findings written as a prohibition list

> *"The following SHALL NOT establish release eligibility by themselves: `README statement · CI green badge · developer assertion · model output · test count · percentage passed · local build · successful compilation`"*

**Eight items, and the first is `README statement`.**

Parts II–VIII carried one recurring defect — a README claiming 997 sections where the corpus had 998, restated **seven times** across the audit before Part VIII §112 ranked the live P0 items and closed it. **§554 names the category as prohibited**, and does so first.

The other seven map to findings as well:

| §554 item | Audit finding |
|---|---|
| `CI green badge` | Part VII §92 — no CI exists; Part VII §80 — publication prescribed but unverifiable |
| `developer assertion` | Part IV §0.1 — the author's own certification through `mapping errors=0` |
| `model output` | `MODEL OUTPUT IS NOT AUTHORITY` |
| `test count` | Part III §37 — counts reported with no coverage relationship |
| `percentage passed` | `NO COVERAGE ≠ PASS`; Part V §0.1's `PROV = n/a` inside `ALL FILES OK` |
| `local build` | §512's `LOCAL RESULT ≠ CI RESULT` |
| `successful compilation` | `OBSERVED ≠ VERIFIED` |

**§554 is the single highest-value section in the lineage**, because it converts the audit's central observation into an enumerated prohibition.

### 2.2 Four non-PASS states, four separate rules preventing collapse to PASS

| § | Rule |
|---|---|
| **§500** | *"A skipped required test SHALL prevent complete conformance."* |
| **§501** | *"Blocked SHALL not count as PASS."* |
| **§502** | *"Unknown SHALL not be converted to PASS for summary convenience."* |
| **§517** | A missing matrix member *"SHALL be: `MISSING` — not PASS."* |

**All four in one document.** The audit carried `SKIPPED ≠ PASS` (three occurrences across Parts XIV, XVI, XVII), `BLOCKED ≠ PASS`, `UNKNOWN ≠ PASS` and `NO COVERAGE ≠ PASS` separately; v1.0 states each against a named state.

§498 reinforces it: *"These statuses SHALL not be collapsed into Boolean success/failure."*

### 2.3 The returncode prohibition, fifth statement — and the best one

| Document | Statement |
|---|---|
| v0.7 §258 | *"Forbidden: `assert subprocess.run(...).returncode != 0` without establishing why the command failed"* |
| v0.8 §318 | *"A shell script MUST NOT infer semantic status solely from `returncode != 0`"* |
| v0.8 §319 | 15 typed error classes, so negative tests assert the actual failure class |
| v0.9 §443 | *"Any nonzero exit code SHALL NOT automatically count as a correct negative test."* |
| **v1.0 §474** | *"This is invalid: `expected: nonzero` — because unrelated failures may also return nonzero."* |

**§474 names the exact anti-pattern in the negative-fixture format**, which is where the audited toolchain's `neg()` actually lived — `run_all.sh:120`, gating on `[ $rc -ne 0 ]` while measuring and discarding the finding count.

The lineage has now specified this fix five times, each time closer to the code that violated it.

### 2.4 Identity binding — the whole stack, each against a named failure

| § | Rule | What it forbids |
|---|---|---|
| **§459** | *"A branch name SHALL NOT substitute for an immutable subject identity."* | Part V §56 — no commit/version/hash binding anywhere |
| **§460** | `main · master · latest · HEAD` *"SHALL NOT constitute sufficient implementation identity"* | Mutable reference as identity |
| **§461** | *"A commit SHA alone SHALL NOT prove that the executed artifact corresponded exactly to that commit"* — requires `working_tree.clean` + `status_digest` | The gap between committed and executed source |
| **§466** | *"A tool's display version alone SHALL NOT establish implementation identity."* | Part VII §82–83 |
| **§467** | *"`subject identity + verifier identity + predicate identity` form the minimum verification identity"* | Part XIV §4 — the check object with no verifier |
| **§468** | *"A test result generated under predicate `P1` SHALL NOT automatically validate predicate `P2`."* | Predicate drift |

**§461 is the sharpest.** It identifies a gap that the audited toolchain actually had and that no prior document named: a commit SHA identifies *source*, not *the artifact that ran*. The `working_tree.clean` field and `status_digest` close it.

### 2.5 CI authority, independently of local execution

| § | Rule |
|---|---|
| **§511** | *"CI execution SHALL be the authoritative execution source"* for CI-requiring claims |
| **§512** | Local execution *"SHALL NOT establish: `CI test passed` without corresponding CI evidence"* |
| **§513** | *"A screenshot or textual report alone SHOULD NOT be considered sufficient machine evidence."* |
| **§514** | CI artifacts bind `run_id · commit · artifact_digest · test_manifest_digest · result_digest` — *"prevents unrelated artifacts from being attached to a successful run"* |
| **§515** | *"`requested commit = executed commit`"* and *"`reported artifact = artifact under verification`"* |
| **§516** | `TEST_FAIL · INFRA_ERROR · CANCELLED · TIMEOUT · BLOCKED` — *"classified separately"* |
| **§518–§519** | *"Reported configuration SHALL be distinguished from observed configuration"*; toolchain identity obtained from `rustc --version` output, not config text |

**§515 is the strongest CI rule in the lineage**: it requires the verifier to establish that the thing reported *is* the thing verified. That is the CI analogue of §461, and it is the same insight — a record of an execution is not automatically a record of *this* execution.

### 2.6 Independence, defined so it can fail

**§532:**

> *"Two verifiers SHALL NOT be considered independent merely because they are separate functions if they share the same defect source."*

with six factors: `source code · algorithm · generated code · shared dependencies · fixture generation · semantic assumptions`.

Parts XII §5, XVI §6.2 and XVII §6.2 each noted that the lineage named verifier identity without saying what made two verifiers different. **§532 defines it, and defines it negatively** — the list is of *shared* things, i.e. the ways independence can be illusory.

**§533 and §534 complete it:**

- *"Purpose: `verify the verifier` — not: `prove the entire protocol correct`"*
- *"The bootstrap verifier SHALL itself be treated as an implementation… It SHALL not receive unconditional trust merely because it is small."*

**§534 is the exact inversion of the audited toolchain's failure.** Part IV §0.1's `auditlib` certified `renumber.py`'s fabrication with `mapping errors=0` precisely because the small auditor was trusted. §534 forbids that trust *by name*, and §263 of v0.7 and §342 of v0.8 had already forbidden the chain that produces it.

### 2.7 §456 — five states, five negations

> `IMPLEMENTED ≠ CONFORMANCE-TESTED ≠ EXECUTION-VERIFIED ≠ RELEASE-GATED ≠ RELEASED`

That is `OBSERVED ≠ VERIFIED ≠ RELEASED` expanded into a five-state ladder, and it closes the class Part XII §4.1, XIV §6.2, XVI §2.2 and XX §2 identified: statuses collapsing into each other. §506–§510 give the conformance verdicts five values **and define each**, including §510's *"It SHALL not be silently converted into `NON_CONFORMANT` or `CONFORMANT`"* — `ERROR ≠ FAIL` at the conformance layer.

### 2.8 Everything else, briefly

| § | Rule | Finding |
|---|---|---|
| **§546** | *"Forbidden transformation: `missing → empty → successful`. Required: `missing → explicit completeness state → downstream policy decision`"* | The audit's `mutation survives → verification gap`; `has_prov`'s conditional gate |
| **§527** | Prefer `persist facts → derive summaries → derive claims` over `persist final claim → treat claim as fact` | `A CLAIM IS A DERIVATION FROM EVIDENCE, NOT A REPHRASING OF INTENT` |
| **§537** | *"No aggregate percentage may conceal a required mutation failure."* | Stage-7 findings count, environment-dependent (6 vs 8) |
| **§548–§549** | Reports are projections; *"A report SHALL never be the sole source of a release decision when machine-readable evidence exists"* | The README/997 class |
| **§488** | The gate *"SHALL NOT trust: caller-provided gate status · stored textual claim · model-generated verdict · previous gate assertion"* | Fourth layer of the §425/§427 closure |
| **§492** | Atomic vs durable, per backend, documented | `renumber.py:111` |
| **§529** | Eleven-link evidence chain: Claim → Gate → Coverage → Evidence → Check → Observation → Execution → Authorization → ToolRequest → Task → Subject | The `CLAIM → EXECUTION → RECEIPT → EVIDENCE → VERIFIED` law, realised |
| **§463** | Generated artifacts *"SHALL NOT silently become independent semantic authorities"* | `NO GENERATED ARTIFACT MAY OVERRIDE THE NORMATIVE CONTRACT` |
| **§535–§536** | Seven critical mutations named: `remove scope check · remove subject binding · accept stale verifier · force PASS · ignore missing coverage · skip authorization · ignore digest mismatch` | C-7; each is a check that failed to detect its own remover |

**§536's seven mutations are the audit's findings as test targets.** *"ignore missing coverage"* is Part XVI §3's PASS-at-zero-evidence; *"force PASS"* is Part VII §0.2's crash-as-pass; *"accept stale verifier"* is Part V §0.1's non-monotonicity.

---

## 3. HEADLINE A: `CLAIMED` returns after three documents absent

### 3.1 The measurement

| Token | v0.6 §154 | v0.7 | v0.8 | v0.9 | **v1.0 §545** |
|---|---|---|---|---|---|
| `claimed` | 1 | **0** | **0** | **0** | **1** |
| `CLAIMED` | 1 | **0** | **0** | **0** | **1** |
| `claimed_scope` | 0 | 0 | 0 | 0 | **1** |

### 3.2 What v1.0 restores, and what it does not

**v0.6 §154** — four terms, with the six-field `Scope` object (`declared · permitted · excluded · executed · claimed`):

```text
CLAIMED ⊆ EXECUTED ⊆ PERMITTED ⊆ DECLARED
```

**v1.0 §545** — three terms, as a *release-critical invariant*:

```text
executed_scope ⊆ authorized_scope
claimed_scope ⊆ executed_scope
therefore:  CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED
```

| | v0.6 §154 | v1.0 §545 |
|---|---|---|
| Terms | 4 | 3 |
| `CLAIMED`, `EXECUTED` | present | present |
| `PERMITTED` | present | **absent** |
| `DECLARED` | present | **absent** |
| `AUTHORIZED` | absent | **present** — a new term in this chain |
| Status | *"MUST preserve"* | *"release-critical invariant"* |
| Representation | YAML fields on `Scope` | prose relation; no field list |

**So the containment returns strengthened in status and reduced in resolution.** *"Release-critical invariant"* is the strongest normative form any relation in the lineage has received — but two of the four terms are gone, and the replaced term (`AUTHORIZED`) is the one v0.9 §414 introduced as `effective_scope ⊆ granted_scope`.

**The two halves of the chain now live in two documents with different vocabularies:** v0.6 §154's `PERMITTED`/`DECLARED` and v0.9 §414's `effective_scope`/`granted_scope` and v1.0 §545's `AUTHORIZED`. No document reconciles them.

### 3.3 Disposition

**Parts XVIII §3, XIX §4 and XX §5 are CLOSED in substance** — the term and the containment are back, and at the highest normative strength the lineage uses. **A residual finding remains:** the chain's terms are not stable across the lineage, and the four-term source form is not recoverable from any later document.

---

## 4. HEADLINE B: the pack returns, and the digest moves outside the artifact

### 4.1 The defect, restated

Part XV §2 established the lineage's blocking defect:

- **v0.4 §86:** `CompiledPackDigest = SHA256(CanonicalCompiledPack)`
- **v0.4 §88:** `compiled_pack.identity.compiled_digest: ...`

The digest is defined over the artifact that contains the digest field, and **v0.4 §84's canonicalization requirements contain no exclusion rule** — so the canonical form of the pack includes its own digest. Verified verbatim this pass.

It survived v0.7, v0.8, v0.9 without any of them mentioning the pack.

### 4.2 What v1.0 does

**§541:**

```text
When a prompt/instruction pack materially affects execution,
evidence SHALL bind:
    pack identity
    pack version
    pack digest
    compiled representation digest
```

**§542:**

```text
source pack → parse → Pack IR → resolve inheritance → apply precedence
→ canonicalize → digest → compiled pack
```

**§544:**

```text
pack authority ∩ granted authority
```

### 4.3 What this resolves

**Both digests are bound by the evidence record — which is outside the compiled pack.**

v0.4's circularity was that `compiled_digest` sits *inside* `compiled_pack.identity` while being computed over `CanonicalCompiledPack`. v1.0 gives the compiled representation's identity an **external binding locus**: whatever the pipeline computes, the *identity* that matters is asserted by evidence, and evidence is not part of the pack. §542's ordering is consistent — `digest` precedes `compiled pack` in the pipeline, so the digest is computed over the canonicalized intermediate rather than over the named output.

**That is Option B from Part XV §2.3, reached structurally rather than by an exclusion rule.**

**Disposition: PARTIALLY CLOSED — by relocation.**

### 4.4 What it does not resolve

| Check | Measured |
|---|---|
| `CompiledPackDigest` in v1.0 | **0** |
| `compiled_digest` in v1.0 | **0** |
| `without_` / `excluding` (an exclusion rule) | **0** |
| A new name — `compiled representation digest` | introduced, not reconciled with v0.4's |

So the fix works by **moving the digest's binding out of the artifact**, not by declaring what the digest covers. Two things follow:

1. **If any future document puts `compiled_digest` back inside `compiled_pack.identity`, the circularity returns unchanged**, because no exclusion rule was ever stated. The remedy is positional, not semantic.
2. **The lineage now has two names for the same digest** — v0.4's `CompiledPackDigest` and v1.0's `compiled representation digest` — with no statement that they are the same object. That is the `same ID ≠ same version ≠ same source ≠ same semantics` problem in its most literal form.

**This is the best outcome available for the oldest defect in the series**, and it should be recorded as such: after six parts of carrying it, the artifact no longer contains its own identity. The one-sentence exclusion rule would make it permanent.

---

## 5. What remains open

### 5.1 Coverage status: required, derived, undefined

| Check | v1.0 |
|---|---|
| §504 requires `status` | yes — `status: COMPLETE` |
| §504 *"`COMPLETE` SHALL be derived, not supplied by the caller"* | yes |
| §504 supplies a derivation rule | **no** |
| `PARTIAL` as a protocol token | **0 occurrences** |
| `partial` (lowercase, in fixture classes) | 3 |

**Part XX §4 is RESTATED (2nd).** v0.8 §305 gave an ambiguous derivation; v0.9 removed it; **v1.0 re-requires the field and still gives no rule.**

**What v1.0 adds is behavioural: §484's nine fixture classes.**

```text
complete · partial · empty · duplicate declared · duplicate checked
· unexpected member · unresolved member · unobservable member · skipped member
```

**That is a better instrument than a status mapping.** Nine conditions that the coverage evaluator must handle, each of which a conformance run exercises. Two of them — `duplicate declared` and `duplicate checked` — correspond exactly to §306's and §485's duplicate-population rule; `unexpected member` is §326's `A + B` condition; `unobservable member` is §386's `NOT_OBSERVABLE`.

So the gap is narrower than it looks: the *inputs* are now enumerated and testable, and only the mapping to `COMPLETE`/`PARTIAL`/`UNKNOWN` is missing. **PARTIALLY ADDRESSED.**

### 5.2 `Claim` still has no identifier

| Token | v0.9 | v1.0 |
|---|---|---|
| `ClaimId` | 0 | **0** |
| `claim_id` | 0 | **0** |
| `pub struct Claim` | 0 | **0** |

**Fourth document.** And v1.0 gives `Claim` more structure than any predecessor — §550's eight-field conceptual claim, §551's scope-sum law, §526's `evidence/claims/` directory, §529's chain rooted at `Claim` — while the object still has no identifier.

**§522 compounds it.** The release manifest's `verification` block references `execution_id`, `conformance_id`, `coverage_id`, `gate_id` — **and not evidence.** Measured: the string `evidence` does not appear in §522 at all. v0.8 §349 had `evidence_ref` in that position.

So the release manifest binds four of the five verification artifacts, §530 requires *"evidence closure"* for every referenced object, and §529's chain begins `Claim → Gate → Coverage → Evidence` — **but the manifest cannot reach Evidence directly.** A release manifest is the one artifact whose entire purpose is to bind everything, and the link to the evidence store is the one it omits.

**Part XX §5 is RESTATED (3rd), with a new sub-finding.**

### 5.3 Authority: three terms, then five, then two

| Document | Effective authority |
|---|---|
| **v0.2** | `PackAuthority ∩ GrantedAuthority ∩ EnvironmentAuthority` — 3 terms |
| **v0.6 §155** | `DeclaredAuthority ∩ GrantedAuthority ∩ ScopeAuthority ∩ CapabilityAuthority ∩ RuntimePolicy` — 5 terms |
| **v1.0 §544** | `pack authority ∩ granted authority` — 2 terms |

**v0.6 §155's five-term set shares exactly one term with v0.2's three.** And v1.0 §544's two terms are **v0.2's three minus `EnvironmentAuthority`** — the pack term returns, which is coherent with §541–§544 reintroducing the pack.

**`EnvironmentAuthority` is absent from v1.0** (0 occurrences). And the term matters: Part VIII §0.1's two-environment differential — `bare: PASS, 6 findings` versus `full: PASS, 8 findings`, differing only in whether `markdown` was installed — is the case where the environment determines the result. Part XVII §2 found v0.6 §155 had already dropped it.

§544's second sentence supplies it in prose — *"A prompt pack SHALL not grant itself capabilities unavailable to the runtime"* — and §§495–§496 handle environment identity for executions (`required · observed · irrelevant · unknown`). **So the environment is addressed as execution metadata and not as an authority conjunct.** That is defensible; it is worth stating because the differential that motivated the term is a *result* difference, not a metadata difference.

### 5.4 `ResultStatus` / `CheckStatus` — the Part XX §3 defect is not repaired

v1.0 uses neither name. Measured: `ResultStatus` 0, `CheckStatus` 0, `CoverageStatus` 0, `GateStatus` 0 in v1.0 — it refers to statuses by value (§498's six, §553's four) rather than by type.

**So v0.9's split is neither fixed nor compounded by v1.0.** It remains exactly where Part XX §3 left it: `ResultStatus` declared at v0.9 L361 and used nowhere, `CheckStatus` used at v0.9 L590 and L740 and declared nowhere.

---

## 6. Smaller findings

### 6.1 §484 has nine classes and §487 has ten — both correct

I checked these against the source rather than a regex, having been caught twice: §484 = **9**, §487 = **10**, §489 = **8**, §536 = **7**, §552 = **13**, §555 = **16**, §472 = **12**, §473 = **17**, §554 = **8**.

### 6.2 §553's `READY` is prose, not a status

Measured in §553's body: `PASS · FAIL · BLOCKED · UNKNOWN · READY`. Four are the gate's return values; `READY` appears in the following sentence — *"A human-readable: `READY` flag SHALL not substitute for the structured gate result."*

It is the correct rule, and it is worth confirming that `READY` is not a fifth status: the gate returns four values, and §553 says the fifth thing does not substitute for them.

### 6.3 §481's eleven evidence vectors

`valid binding · wrong subject · wrong execution · wrong observation · wrong checker · wrong verifier · wrong predicate · wrong policy · wrong digest · missing dependency · stale dependency` — **eleven**, and **one valid case to ten invalid ones.**

That ratio is the document's philosophy in one number: the corpus's purpose is to demonstrate rejection. §473 has **17** negative classes against §472's **12** positive.

### 6.4 §495–§496 environment identity is well-constructed

`required · observed · irrelevant · unknown` — four classes, with *"An unspecified environment property SHALL not be silently assumed."* That is §386's five-state completeness model applied to the environment, and it is the mechanism §544 lacks as a conjunct.

### 6.5 §563 — not a section; noted

v1.0 ends at §555. The range closes without declaring what v1.1 would be, unlike v0.8 §361 and v0.9 §455 which both stated the next boundary. §555's chain ends at `SCOPED CLAIM`. **MINOR** — the lineage has stopped projecting forward, which is arguably correct at v1.0.

---

## 7. A defect in this audit's own provenance — §1–§28 is not a unique identifier

### 7.1 The measurement

| Document | Range | Documents |
|---|---|---|
| v0.1 | §1–§28 | |
| v0.2 | **§0–§40** | overlaps v0.1 entirely in the range §1–§28 |

**Section numbers appearing in both v0.1 and v0.2: 28. Identical headings: 0.**

| § | v0.1 | v0.2 |
|---|---|---|
| 1 | ROLE | CORE INVARIANTS |
| 2 | CORE PRINCIPLES | AUTHORITY |
| 3 | TASK INTAKE | TRUST BOUNDARY |
| 4 | AUTHORITY | TASK INTAKE |
| 5 | TRUST BOUNDARY | SUBJECT IDENTITY |
| 6 | INSPECTION VS EXECUTION | SCOPE |

**v0.2 renumbers and reorders v0.1's material.** v0.1's §4 *AUTHORITY* becomes v0.2's §2; v0.1's §3 *TASK INTAKE* becomes v0.2's §4; v0.1's §5 *TRUST BOUNDARY* becomes v0.2's §3.

### 7.2 Two consequences

**First, and this is the substantive one: `§N` is not a unique identifier across the lineage.**

The audit has been citing sections in the form `§305`, `§545`, `§155` throughout Parts XII–XXI, and for §1–§28 that citation is **ambiguous** — it resolves to two different sections in two different documents. The lineage's own doctrine names this precisely: `locator != identity`, and `same ID ≠ same version ≠ same source ≠ same semantics`. **§1–§28 is a locator shared by two distinct semantic objects.**

The practical rule: **any citation to §1–§28 must be qualified by document.** §29 onward is unambiguous, because v0.3 begins at §41 and each subsequent document continues the previous range.

**Second: my own headers have been undercounting.** v0.8's header says *"seven documents"*, v0.9's says *"eight"*, v1.0's says *"nine"*. Measured: the union §0–§361 spans **eight** documents, §0–§455 spans **nine**, §0–§555 spans **ten**.

The likely cause is the overlap itself — I treated v0.1 as subsumed by v0.2 rather than counted separately, which the §7.1 measurement shows is wrong: they are materially different documents.

### 7.3 Correction

- **v0.8 header:** seven → **eight** documents
- **v0.9 header:** eight → **nine**
- **v1.0 header:** nine → **ten**, with the §1–§28 overlap declared
- **Part XX §1:** eight → **nine**
- **README:** the span line, and the count

And a standing rule recorded in the README: **citations to §1–§28 must name the document.**

This is the second self-correction in as many parts, after Part XIX §11.3's `---` separators, and it is the same class: **an undeclared property of the recording that makes the artifact harder to use than it appears.** The separators were an undeclared addition; this is an undeclared ambiguity I had propagated into every header.

---

## 8. Standing

| Part XXI section | Standing |
|---|---|
| §1 §456–§555 contiguous, 100 sections | **PROVED** |
| §2.1 §554's eight exclusions | **CLOSED** — `README statement` named first |
| §2.2 Four non-PASS states | **CLOSED** — §500/§501/§502/§517 |
| §2.3 §474 returncode, 5th | **CLOSED** — names `expected: nonzero` |
| §2.4 §459–§468 identity stack | **CLOSED** |
| §2.5 §511–§519 CI authority | **CLOSED** |
| §2.6 §532–§534 independence | **CLOSED** — defined so it can fail |
| §2.7 §456 + §506–§510 | **CLOSED** |
| §2.8 §546/§527/§537/§548/§488/§492/§529/§463/§536 | **CLOSED** |
| **§3 `CLAIMED` returns at §545** | **CLOSED in substance**; term set 4 → 3; residual on term stability |
| **§4 Pack returns; digest externalised at §541** | **PARTIALLY CLOSED — by relocation**; no exclusion rule; names not reconciled |
| §5.1 Coverage status | **PARTIALLY ADDRESSED** — §484's 9 classes; `PARTIAL` = 0; RESTATED (2nd) |
| §5.2 `Claim` has no identifier; §522 omits evidence | **PROVED** — 4th document; RESTATED (3rd) |
| §5.3 Authority 3 → 5 → 2 terms | **PROVED** — `EnvironmentAuthority` absent |
| §5.4 `ResultStatus`/`CheckStatus` | **UNCHANGED** from Part XX §3 |
| **§7.1 §1–§28 occupied twice** | **PROVED** — 28 numbers, 0 identical headings |
| §7.2 My header undercount | **SELF-CORRECTED** — three headers, one part, one README |

### Corrections, in dependency order

**1. §7 — qualify citations to §1–§28 by document.** This is a correctness issue in how the specification is cited, not a formatting one.

**2. §4.4 — state the exclusion rule.** The relocation at §541 works, and one sentence makes it permanent: the digest is computed over the pack *excluding* its own digest field. v0.7 §239, v0.8 §302 and v0.9 §392 each state this pattern for other objects; v1.0 is where the pack finally needs it.

**3. §5.2 — add the claim identifier, and reference evidence from §522.** A manifest that binds four of five verification artifacts and omits the evidence store is the one link the whole chain depends on.

**4. §5.1 — map §484's nine classes to the coverage status.** The inputs are enumerated; only the mapping is missing.

**5. §5.3 — reconcile the three authority term sets**, or state that §544 supersedes v0.6 §155 and v0.2.

**6. — still unresolved: v0.9's `ResultStatus`/`CheckStatus` split.** Neither document fixes it; it requires one word in one of them.

### Closing judgement

**v1.0 closes more of the audit's own findings than any document in the lineage, and two of its closures are the largest open items the series carried.**

§554 names `README statement` first among things that cannot establish release eligibility — the defect Parts II–VIII restated seven times, prohibited by category. §474 names `expected: nonzero` — the `neg()` pattern, five statements in. §532 defines independence by what makes it illusory. §534 forbids trusting the bootstrap verifier for being small, which is exactly the trust that produced Part IV §0.1's `mapping errors=0`. §461 identifies a gap no prior document named: a commit SHA identifies source, not the artifact that ran.

**And the two long-carried items both move.** `CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED` returns as a release-critical invariant, three documents after the term vanished. The pack returns after six, and **the digest is finally outside the artifact that it identifies** — reached by relocation rather than by rule, which is why I record it as partially closed rather than closed.

**The measurement I did not expect is §7.** `§1–§28` denotes two different sets of sections in two different documents, and I had propagated that ambiguity into every header by undercounting documents by one. Neither the ambiguity nor the undercount was visible from the artifacts alone — which is the property that makes both worth recording.

**And the substrate is still absent.** Ten documents, 555 sections, and `evidence.schema.json` is named in ten files in this repository and exists in none of them; `find` returns zero, across the whole corpus and the vendored upstream tree alike.

v1.0's own §456 draws five distinctions between existing, conforming, being observed, passing a gate, and being released. **The lineage has now, in v1.0, specified how to tell those five apart — and the artifact that would occupy the first of them does not exist.**
