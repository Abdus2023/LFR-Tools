# RFL-AE Prompt Packs — Part XVIII: Executable Protocol Types v0.7 — Closures, and a Second Dropped Closure

**Subject:** [`rfl-ae-executable-protocol-types-v0.7.md`](rfl-ae-executable-protocol-types-v0.7.md) (§201–§271), continuing [`v0.6`](rfl-ae-protocol-schemas-v0.6.md), [`v0.5`](rfl-ae-prompt-instructions-v0.5.md), [`v0.4`](rfl-ae-prompt-pack-specification-v0.4.md), [`v0.3`](rfl-ae-operational-agent-protocol-v0.3.md), [`v0.2`](rfl-ae-master-prompt-instructions-v0.2.md)
**Series:** [`part14`](rfl-ae-prompt-packs-part14.md) · [`part15`](rfl-ae-prompt-packs-part15.md) · [`part16`](rfl-ae-prompt-packs-part16.md) · [`part17`](rfl-ae-prompt-packs-part17.md) · [`consolidated`](rfl-ae-skills-audit-consolidated.md)
**Date:** 2026-09-28

> ## Category: ANALYSIS — closures, and a repeated regression class
>
> **v0.7 contains real closures, including two that answer longstanding audit findings directly.** §258 states the audit's consolidated **P0 item 4** almost verbatim. §263 states the self-certification constraint that Part IV §0.1's `mapping errors=0` violated. §239 supplies the exact digest-construction pattern Part XV §2.3 identified as the fix for the lineage's blocking defect.
>
> **And it drops `claimed`.**
>
> v0.6 §154 added `claimed` to the Scope object, closing Part XIV §3 — a finding Part XI §155 called *"the highest-value part of the protocol"*, and which Part VI §78 reduced four of seven substantive defects to. Part XVII §2.1 recorded it **CLOSED**, verified 0 → 1 occurrence.
>
> **Measured in v0.7: `claimed` occurs zero times.** §216 is the *executable* Scope type — §201 says v0.7 *"SHALL define executable representations for the protocol objects introduced in v0.6"* — and it carries the declaration side only.
>
> **This is the second consecutive part in which a closure from the preceding document is dropped.** Part XVII §3 found v0.6 §160 reintroducing `BLOCKED` into an execution state set v0.5 §116 had deliberately made disjoint. v0.7 repeats the shape, on a higher-value property.
>
> **Disposition: Part XIV §3 is restated as `RESTATED (2nd)`.**

---

## 1. Standing: §0–§271 across six documents, and §200 is unassigned

v0.7 continues the numbering from v0.6. **v0.6 ends at §199 and v0.7 begins at §201 — §200 is absent from both.** Measured: zero occurrences of `## §200` in v0.7.

Given v0.7's §202 (*"The project SHALL designate exactly one normative semantic specification"*), a gap in the specification's own numbering is minor — but it is a second, smaller instance of the lineage's identity problem, which §202 now names without resolving (§9.3).

### 1.1 What v0.7 genuinely is

It is the first document with **executable artifacts**: fifteen JSON object definitions (§214–§225), Rust enums and newtypes (§226), TypeScript unions (§227), a 19-step build order (§262), a 14-file schema tree (§261), and twelve required artifacts (§269). §201's three-representation model — source → canonical object → canonical bytes → digest, validated independently by JSON Schema, Rust, and TypeScript — is a concrete architecture rather than a contract about one.

---

## 2. Closures, and the two that answer audit findings directly

### 2.1 §258 states the audit's P0 item 4

The consolidated audit's item 4 in its ranked priority list is:

> **`neg()`: require structured findings, not a non-zero exit**

**§258:**

> *"Negative tests SHALL assert the expected defect. **Forbidden:** `assert subprocess.run(...).returncode != 0` without establishing why the command failed."*

followed by the required pattern:

```text
execute → observe result → classify failure
        → assert expected failure class → assert expected finding
```

That is the same rule, and it names the same anti-pattern. `run_all.sh:120`'s `neg()` gates on `[ $rc -ne 0 ]` alone while **measuring** the finding count and discarding it (Part XII §2, Part XV §6.2). **§258 is that defect written as a normative prohibition** — and it is the direct specification-level counterpart of the audit's most-recommended fix.

### 2.2 §263 states the self-certification constraint

> *"The implementation SHALL avoid circular trust. The component under verification SHALL NOT be the sole source of evidence proving its own correctness. For high-assurance components: `implementation verifier ≠ implementation under test` SHOULD hold."*

Part IV §0.1's central finding was that `renumber.py` fabricated a source number and `auditlib` certified it with `mapping errors=0` — the verifier certifying its own defect. **§263 is that finding stated as a constraint.** It also closes Part V §56's absence of verifier identity from a second angle: a verifier that must differ from its subject must have an identity.

### 2.3 §239 supplies the missing half of §237 — for evidence

Part XV §2 established that v0.4 §86 defines `CompiledPackDigest = SHA256(CanonicalCompiledPack)` while §88 places `compiled_digest` **inside** `compiled_pack.identity` — circular — and that §84's canonicalization requirements contain no exclusion rule. Part XV's Option A was: declare the exclusion.

**§239:**

```text
EvidenceDigest = Digest(Canonicalize(EvidenceWithoutSelfDigest))
The digest field SHALL NOT recursively contain itself.
```

**That is Option A, and it is the general principle the lineage lacked.** Part XV §2.3 observed that the repository already contained both precedents — `PROVENANCE-rfl-ae.md`'s projected manifest digest, and Git's name-not-field model. §239 states the rule explicitly.

**But it is scoped to evidence, and the instance it would fix is not addressed.** `CompiledPackDigest` and `compiled_digest` occur **zero times** in v0.7 — the protocol-typification layer does not mention packs at all. So:

- the **pattern** is now specified (§239)
- the **instance** (v0.4 §86) is untouched, and remains the blocking defect on the critical path

**PARTIALLY CLOSED — pattern yes, instance no.** The remedy is now one sentence away from being applied.

### 2.4 §208 addresses the layer-separation finding — and §3 shows how far

The layer-separation finding was carried by Parts XII §5, XIII §5, XIV §6.2, XVI §2.2 and XVII §3: execution outcomes and verification outcomes must not share vocabulary.

**§208 states the intent at the type level:**

> *"Execution state SHALL be a separate enum… Execution states SHALL NOT share the same type as check statuses. This prevents `ExecutionState::PASS` from existing accidentally."*

That is the right rule, stated as a *type-system* constraint rather than a convention. §226's Rust enums and §227's TypeScript unions make them nominally distinct. **The intent is correct, and Part XVI §2.2's requirement is now normative.** §3 measures where the implementation does not reach it.

### 2.5 §259 partially closes the `SKIPPED` reachability gap

Parts XIV §6.2, XVI §7.3 and XVII §6 found `SKIPPED` unreachable: a legal status, bound by a mandatory claim rule, present in the lattice, and reachable by no transition in three separate state machines.

**§259:**

> *"A test that cannot execute SHALL produce `SKIPPED` or `BLOCKED` with an explicit reason. A skipped test SHALL NOT count as passing coverage."*

**The second sentence is the production rule `SKIPPED` lacked**, and it is aimed at the exact measured behaviour: `run_all.sh:33` announces *"render checks will be SKIPPED"* and stage 7 reports `PASS` with 6 findings instead of 8 (Part VIII §0.1). *"A skipped test SHALL NOT count as passing coverage"* is that defect prohibited.

**PARTIALLY CLOSED** — `SKIPPED` now has a cause at the test layer. The transition engine (§231) describes execution-state transitions and §208's execution enum contains no `SKIPPED`, so the protocol state machine still cannot transition into it. Fourth occurrence, with the test-layer half answered.

### 2.6 Three more rules that answer measured findings

| § | Rule | Finding it answers |
|---|---|---|
| **§247** | *"discovering previously unrecognized scope MAY decrease the effective coverage state… coverage is derived from current scope + current observations, not from historical optimism"* | Part V §0.1's non-monotonicity, and Part XVI §3's finding that §131 was its inverse |
| **§265** | Build SHALL detect *"schema changed but generated types unchanged"* and the reverse | The drift class Part XIII §6.3 found unrepresented — no component could tell what produced what |
| **§213** | `"additionalProperties": false`, to prevent *"`verifer_digest` instead of `verifier_digest`"* | Part VI §69's family: silently-accepted near-miss keys |

**§247 is the strongest.** Part XVI §3 found that measured non-monotonicity ran *backwards*: 30/29/15/1/0 provenance comments → PASS/FAIL/FAIL/FAIL/PASS, with the strongest verdict at zero evidence. §247 states that coverage is derived from *current* scope and *current* observations — which is the rule that forbids it.

---

## 3. HEADLINE: v0.7 §216 drops `claimed`, and Part XIV §3 is restated

### 3.1 The measurement

**v0.6 §154** (Part XVII §2.1 recorded this as a closure):

```yaml
Scope:
  scope_id:
  declared:
  permitted:
  excluded:
  executed:
  claimed:
```

with:

```text
CLAIMED ⊆ EXECUTED ⊆ PERMITTED ⊆ DECLARED
```

**v0.7 §216** — the executable type:

```json
{
  "scope_id": "scope:...",
  "schema": "rfl-ae/scope/v1",
  "subject_refs": [],
  "include": [],
  "exclude": [],
  "dimensions": {},
  "declared_at": "..."
}
```

**Measured in v0.7:**

| Term | Occurrences |
|---|---|
| `claimed` | **0** |
| `permitted` | **0** |
| `CLAIMED` | **0** |

### 3.2 Why this is the most consequential finding in Part XVIII

**§201's mandate makes §216 authoritative for exactly this.** v0.7 *"SHALL define executable representations for the protocol objects introduced in v0.6."* The executable `Scope` is §216. It carries the **declaration side** — `include`, `exclude`, `dimensions` — and the executed side has moved to §224's `CoverageRecord` (`executed_scope`, `observed_scope`, `checked_scope`, `excluded_scope`). But **`claimed` and `permitted` are absent from the entire document**, so the four-term containment cannot be evaluated from the executable representation.

And the property is the one this series repeatedly identified as most load-bearing:

- **Part XI §155:** *"This is the highest-value part of the protocol."*
- **Part VI §78:** four of seven substantive defects reduce to the `SCOPE_COVERED` conjunct.
- **Part XIV §3:** the containment was mandated over a structure with no `claimed` field.
- **Part XVII §2.1:** recorded CLOSED at v0.6 §154.

**Disposition, following the precedent set for the README/997 defect: RESTATED (2nd).** Not a new finding — the same finding, closed once and reopened.

### 3.3 The pattern is now confirmed as recurring

| Part | Preceding document added | Following document dropped |
|---|---|---|
| **XVII §3** | v0.5 §116: eight execution states, **verified 0 overlap** with §7 | v0.6 §160 reintroduces `BLOCKED` → overlap 0 → 1 |
| **XVIII §3** | v0.6 §154: `claimed` in Scope, four-term containment | v0.7 §216: **`claimed` absent from the document** |

**Two consecutive documents, two dropped closures.** In both cases the dropping document did not reference the section that established the property, and in both cases the property had been *computed* — verified by set difference in one, by occurrence count in the other — rather than merely asserted.

That is the mechanism, and it is now demonstrated twice within three documents.

---

## 4. §208's protection is nominal in Rust and absent in TypeScript

§208 states the intent correctly (§2.4). The implementation does not reach it.

### 4.1 The shared literals

**§227:**

```typescript
type CheckStatus = "PASS" | "FAIL" | "ERROR" | "UNKNOWN" | "SKIPPED" | "BLOCKED";
type ExecutionState = "CREATED" | "AUTHORIZED" | "RUNNING" | "SUCCEEDED"
                    | "FAILED" | "ERROR" | "CANCELLED" | "TIMED_OUT" | "BLOCKED";
```

**Measured — shared literals:**

```text
"BLOCKED"
"ERROR"
```

**§226's Rust enums cannot collide** — `CheckStatus::Error` and `ExecutionState::Error` are distinct nominal types. **§227's TypeScript union literals can** — under structural typing, the value `"ERROR"` satisfies both types, and over the wire (JSON) there is no type at all.

### 4.2 The three-way tension

| § | Requirement |
|---|---|
| **§201** | *"The three representations MUST preserve the same protocol semantics."* |
| **§208** | *"Execution states SHALL NOT share the same type as check statuses… prevents `ExecutionState::PASS`"* |
| **§227** | Gives `"ERROR"` and `"BLOCKED"` to both types |

**§208's specific example — `ExecutionState::PASS` — is prevented.** The two tokens that actually collide are not. So the rule is satisfied against its own example and violated against its stated purpose.

**And §201 is what makes this a defect rather than a stylistic note.** If the three representations must preserve the same semantics, then a guarantee that holds in Rust and fails in TypeScript means they do not. The nominal-vs-structural difference is exactly a semantics difference.

### 4.3 Full token census

| Token | Enums containing it | Count |
|---|---|---|
| `UNKNOWN` | §207 status · §209 authorization · §210 coverage · §250 gate | **4** |
| `BLOCKED` | §207 status · **§208 execution** · §250 gate | **3** |
| `ERROR` | §207 status · **§208 execution** | **2** |
| `PASS` | §207 status · §250 gate | 2 |
| `FAIL` | §207 status · §250 gate | 2 |

**`BLOCKED` and `ERROR` appear in §208's execution enum** — the enum §208 says must not share a type with check statuses. In Rust the types differ; in JSON they do not.

**Correction.** Either rename the execution-layer collisions (`EXEC_ERROR`, `EXEC_BLOCKED`, or fold them into `FAILED`/`CANCELLED`), or state that §208's rule is a Rust-only guarantee and that wire values are type-ambiguous. The first option restores §208's purpose; the second documents that it does not hold.

---

## 5. Digest representation: §215 contradicts §205

**§205:**

> *"Digest values SHALL identify both algorithm and digest. Canonical form: `<algorithm>:<hexadecimal digest>`… The implementation SHALL reject: bare hexadecimal digest…"*

**§215:**

```json
"identity": {
  "digest_algorithm": "sha256",
  "digest": "..."
}
```

**One string per §205; two fields in §215.** Either §215 is non-canonical — in which case it violates §205's *"Canonical form"* — or §205's canonical form does not govern §215, which §205 does not say.

This is not academic. §205 requires rejecting a **bare hexadecimal digest**; §215's `"digest": "..."` is a bare hexadecimal digest, with the algorithm carried in a sibling field. Under §205 read strictly, every §215 Subject is invalid.

And §223's `evidence_id: "evidence:sha256:..."` uses the §205 form, while §215 uses the split form — **so the two forms coexist within v0.7** and the canonicalization contract (§237) has to normalize between them.

---

## 6. Evidence status has three vocabularies, and §223 uses the one §244 forbids

| § | Vocabulary |
|---|---|
| **§223** | `"status": "PASS"` — the check-status vocabulary |
| **§243** | `VERIFIED` — a term in none of §207–§210's enums |
| **§268** | `SCHEMA_VALID · TYPE_VALID · SEMANTICALLY_VALID · CONFORMANCE_VERIFIED · RELEASE_GATED` |

**Three vocabularies for evidence status, none declared as the canonical one.**

And §244 states:

> *"Evidence status SHALL be independently evaluated. It SHALL NOT be copied blindly from `CheckResult.status`."*

**§223 types evidence status with `"PASS"` — the same token §222's `CheckResult` uses.** So the prohibition is stated in §244 and the schema in §223 performs the thing it prohibits. A reader, or a serializer, cannot distinguish an evidence `"PASS"` from a check-result `"PASS"` by value.

Worse, the three vocabularies are not merely different names for one scale — they measure different things. §268's hierarchy is a *validation-progress* ladder (schema → type → semantic → conformance → release). §243's `VERIFIED` is a *freshness-and-binding* judgement. §223's `PASS` is a *predicate outcome*. **One field is being asked to carry all three**, which is the defect Part XIV §6.2 and XVI §2.2 identified for statuses generally, now appearing in the fields that §244 exists to protect.

**Correction.** Give evidence its own closed enum, state it once, and let it reference rather than reuse `CheckResult.status`. §268's five-step ladder is the natural candidate for the validity axis, with §243's staleness as an independent field.

---

## 7. §262 and §196 specify different build orders

Two normative implementation orders for the same protocol, in consecutive documents:

| Step | v0.6 §196 | v0.7 §262 |
|---|---|---|
| 1 | Typed identifiers | Primitive types |
| 2 | **Schema definitions** | Identifier types |
| 3 | **Canonicalization** | Digest type |
| 4 | Referential validation | Timestamp type |
| 5 | Authority model | **Canonicalization** |
| 6 | Scope model | **JSON Schemas** |
| … | … | … |
| 19 | Determinism tests | Release gate |
| 20 | Evidence/replay tests | — |

**The dependency inverts.** §196 orders *Schema definitions* (2) before *Canonicalization* (3); §262 orders *Canonicalization* (5) before *JSON Schemas* (6).

Neither is obviously wrong independently, and §262's is arguably better — canonicalization is schema-independent and schemas reference digest forms, so §205's ordering has a case. **But two normative orders is one more than an implementation can follow**, and §196 says *"Implementation MUST proceed in this order"* while §262 says *"Implementation SHALL proceed in this order."* Both are mandatory.

This is the first time the lineage's recurring defect lands on **build sequence** rather than on object semantics — and it is the one form of the defect that cannot be worked around by reading both documents, because the orders are mutually exclusive.

---

## 8. §240's determinism test cannot detect the measured nondeterminism

> *"At minimum: `100 repeated compilations` SHOULD produce identical canonical output and digest."*

**First quantified determinism threshold in the lineage** — a genuine improvement over *"must be deterministic"* with no sample size. And still insufficient for the one case the audit actually measured.

**Part VIII §0.1's differential is environment-dependent, not repetition-dependent.** Same commit, same fixture, same command: `bare: 6 findings` versus `full: 8 findings`, differing only in whether `markdown` was installed. **Within either environment the result is deterministic** — running the corpus audit 100 times without `markdown` produces the same 6 findings 100 times.

So §240's protocol would pass 100/100 while the nondeterminism is present and causing the two environments to report `PASS` on materially different work.

**And v0.5 §143 already lists the cause:**

```text
time · randomness · network · filesystem ordering
· environment · dependency versions · external services
```

*Environment* and *dependency versions* — the two variables in the measured differential. **§143 names them; §240's method cannot vary them.** §240 tests one axis (repeatability) and the defect lives on another (environment).

**Correction.** §240 should require determinism across **environment variation**, not only repetition — the same fixture under at least two declared environments, with the difference recorded. Otherwise the contract certifies exactly the property that was violated.

---

## 9. Smaller findings

### 9.1 §203/§206/§226 — the newtype does not establish the invariant it implies

| § | Statement | Strength |
|---|---|---|
| §203 | *"A raw `String` SHOULD NOT be accepted where a typed identifier is required"* | SHOULD |
| §226 | *"SHOULD reject invalid construction rather than relying entirely on downstream validation"* | SHOULD |
| §206 | *"Identifier parsing SHALL validate the declared type"* | SHALL |

§206's SHALL applies at **parse** time. But `struct TaskId(String)` (§226) is a newtype over an unvalidated `String`, so `TaskId(String::from("junk"))` is constructible, and construction-time rejection is only a SHOULD. **The `<type>:` prefix contract of §206 is therefore unenforced at the type layer** — and §203's own example (`struct TaskId(String)`) cannot enforce it, because a newtype carries no such constraint.

**MINOR**, and the fix is small: a validating constructor (`TryFrom<String>`) rather than `From<String>`, with §226's SHOULD promoted to SHALL for protocol-critical identifiers. Otherwise §206's guarantee holds only on paths that go through a parser.

### 9.2 §202 requires a designation v0.7 does not make

> *"The project SHALL designate exactly one normative semantic specification."*

Measured: the protocol now spans **six documents** (v0.2 §0–§40, v0.3 §41–§70, v0.4 §71–§105, v0.5 §106–§149, v0.6 §150–§199, v0.7 §201–§271). **§202 does not say which one is normative**, and §202's own hierarchy places the SPECIFICATION above the SCHEMA — so the normative artifact is a document, and the document is not named.

**PARTIALLY CLOSED.** Parts XIV §1.1, XV §1, XVI §1 and XVII §1 each recorded *"the pack has an identity scheme; the protocol does not."* §202 is the first section to require that the issue be resolved. **The requirement now exists; the designation does not.** One sentence closes it.

### 9.3 §200 is unassigned

v0.6 ends at §199; v0.7 begins at §201. Measured: no `## §200` in either. Given §202's unresolvable designation and §206's type-prefix contract, a missing section number is trivial — but it is the **second** numbering gap in the lineage, and under §206's own logic a protocol whose sections are its identifiers should not have a hole in its identifier sequence. **MINOR.**

### 9.4 §261's `evidence.schema.json` is now the most-proposed artifact in the corpus

§261 lists **14** schema files. Measured: `evidence.schema.json` now appears by name in **8 files** — six documents of this lineage, one earlier pass, and the vendored upstream design probe.

**And `find . -name "*.schema.json"` returns 0 in this repository and 0 in the vendored upstream tree.**

Six proposals, eight documents, zero files. This remains the single most reliable indicator of the gap between the lineage's specification activity and its substrate.

---

## 10. Standing

| Part XVIII section | Standing |
|---|---|
| §1 §200 unassigned | **PROVED** — 0 occurrences in v0.6 and v0.7 |
| §2.1 §258 = audit P0 item 4 | **CLOSED** — same rule, same anti-pattern |
| §2.2 §263 = Part IV §0.1's constraint | **CLOSED** |
| §2.3 §239 pattern for evidence only | **PARTIALLY CLOSED** — `compiled_digest` 0 occurrences in v0.7 |
| §2.4 §208 states the separation intent | **CLOSED as intent**; implementation §4 |
| §2.5 §259 `SKIPPED` production rule | **PARTIALLY CLOSED** — 4th occurrence, test layer answered |
| §2.6 §247 · §265 · §213 | **CLOSED** |
| **§3 §216 drops `claimed`** | **PROVED — RESTATED (2nd)**; `claimed`/`permitted`/`CLAIMED` all 0 |
| §3.3 Two dropped closures in three docs | **PROVED** — XVII §3 and XVIII §3 |
| §4 §227 shares `"ERROR"` and `"BLOCKED"` | **PROVED by set intersection** |
| §4.2 §201 vs §208 vs §227 | **PROVED** — three sections in tension |
| §4.3 Token census | **PROVED** — `UNKNOWN`×4, `BLOCKED`×3, `ERROR`×2 |
| §5 §215 vs §205 | **PROVED** — two fields vs canonical one string |
| §6 Three evidence vocabularies; §223 uses the forbidden one | **PROVED** |
| §7 §262 vs §196 invert | **PROVED** — steps 2/3 vs 5/6 |
| §8 §240 cannot detect Part VIII §0.1 | **PROVED** — repetition vs environment axis |
| §9.1 Newtype vs §206 prefix | **MINOR** |
| §9.2 §202 designation | **PARTIALLY CLOSED** |
| §9.4 Eight documents, zero schema files | **PROVED** |

### Corrections, in dependency order

**1. §3 — restore `claimed` and `permitted` to §216,** or state where the four-term containment is evaluated. This is the second dropped closure in three documents, on Part XI §155's highest-value property.

**2. §4 — resolve §208/§227.** Rename the execution-layer collisions or declare §208 a Rust-only guarantee. §201 makes the current state a semantics divergence, not a style choice.

**3. §5 — pick one digest form.** §205's canonical string or §215's split fields, and make §205's rejection rule apply consistently.

**4. §6 — give evidence its own status enum.** §223 currently uses the vocabulary §244 prohibits.

**5. §7 — reconcile §196 and §262** into one build order. Two mandatory orders is the one defect class that reading both documents cannot resolve.

**6. §8 — extend §240 across environments,** not only repetitions. The measured nondeterminism does not vary with repetition.

**7. §9.2 — name the normative specification.** §202 requires the designation; one sentence supplies it.

**8. — still first on the critical path: v0.4 §86.** §239 now supplies the exact fix; it has not been applied to the artifact that needs it.

### Closing judgement

**v0.7 closes more than v0.6 did, and two of its closures are the strongest in the series because they answer audit findings at the specification level.** §258 prohibits the exact three-line pattern that `neg()` embodies. §263 forbids the verifier that certifies its own defect. §239 supplies the digest-construction principle Part XV identified as the remedy for the lineage's blocker. §247 states the rule that forbids Part V §0.1's non-monotonicity. Those are not incidental convergences — they are the audit's findings, restated as normative constraints on a system that does not yet exist.

**But it drops `claimed`**, one document after v0.6 added it, on the property Part XI §155 called the highest-value in the protocol — and this is the second consecutive dropped closure.

That pairing is the thing to name. **Six documents, 271 sections, ~40 schemas and enums, and the recurring failure is not that any document specifies its objects badly.** Each does so well, and v0.7 does so better than its predecessors. **The failure is that properties established by *computation* — a set difference, an occurrence count — do not survive into the next document, because the next document is written from the concept rather than from the invariant.** §116 had to compute disjointness to establish it; §154 had to add a field to close a finding. Neither computation is repeated, and in both cases the property lapsed one document later.

That is precisely the class of failure a schema plus a conformance suite is designed to catch — §265's drift detection is the closest thing in the lineage. **It is also, unavoidably, a failure that only an executable artifact can prevent.**

**And the substrate is still absent.** Eight documents name `evidence.schema.json`; zero exist. v0.4 §86's one-sentence defect is now spanned by three documents, and §239 shows that when the lineage does state the rule it states it well — for evidence, not for the pack.

§271 ends: *"NO SCHEMA → NO MACHINE CONTRACT."* §261 specifies fourteen.

**The next artifact is one file, and it is still the same recommendation.**
