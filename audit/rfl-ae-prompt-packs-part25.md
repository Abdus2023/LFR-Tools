# RFL-AE Prompt Packs — Audit Part XXV

**Subject:** `RFL-AE v1.4 — Release Gate, Claim Derivation & Immutable Release Manifest Specification`, §998–§1200 (203 sections)
**Recorded verbatim at:** `audit/rfl-ae-release-gate-manifest-v1.4.md`
**Predecessor:** Part XXIV (v1.3, §801–§997)
**Date of analysis:** 2026-09-28
**Method:** all counts below were produced by executed measurement scripts against the recorded
verbatim file. Where two enumerations differ in spelling but not in intent, the mapping is shown
rather than the raw set difference. Where a finding rests on a lexical test rather than a semantic
one, that is stated in the finding.

---

## §1 — Scope, method, and verification of the recording

### §1.1 What was measured

| Measurement | Method | Result |
|---|---|---|
| Section range | regex over `^#{1,2}\s*§N` | **§998–§1200** |
| Section count | count of matches | **203** |
| Contiguity | `nums == range(998, 1201)` | **True** |
| Separators | count of `^---$` | **203** (one per section) |
| Continuity vs v1.3 | v1.3 ends §997 → v1.4 opens §998 | **adjacent, no gap** |
| Heading format | detected before counting | `##` at section level, as in v1.2/v1.3 |
| Restored blocks | block-index census | **§998–§1200, 95+ blocks** |
| Restored table | §1131's claim-scope matrix | **6 rows × 3 columns** |
| Union across fourteen documents | set union of all ranges | **§0–§1200, one gap: §200** |

### §1.2 The restoration, declared

The supplied text arrived with every list, JSON object, pipeline, state machine, graph, matrix and
truth-table collapsed into inline single-backtick runs. The provenance header of the recorded file
enumerates every restored passage by section. As always, `---` separators and fenced code blocks with
language tags are my presentation additions, declared as such, applied to all fourteen recorded
documents.

**One block required a judgement the others did not.** §1091's release state machine arrived as a
collapsed box-drawing matrix with all alignment lost. Its token sequence is preserved exactly as
supplied; its **geometry** is reconstructed. This is the only block in this document that a reader
cannot check against the arrival form, and the header says so explicitly, because every other
restoration in this corpus is mechanically verifiable from the inline text.

### §1.3 The fourteen-document union

| Document | Range | Sections | Supplied |
|---|---|---|---|
| v0.1–v1.3 (thirteen documents) | §1–§997 | 998 numbers | 1st–13th |
| **v1.4** | **§998–§1200** | **203** | **14th** |
| **union** | **§0–§1200** | **1200 numbers, §200 unassigned** | |

The effective specification is now **§0–§1200 across fourteen documents**. §200 remains the only
unassigned number; the §1–§28 double-occupancy rule is untouched.

**§1200 is the highest number and the last section supplied, named *Final Release Invariant*.** That
is content, not a boundary marker: the numbering reached a round number because the preceding ranges
were contiguous, not because the author chose it. The same caution applies to §1000. Both are recorded
as ordinary positions in the header note so that neither is later cited as evidence of intentional
completion.

---

## §2 — Structural census of §998–§1200

| § | Function | Count |
|---|---|---|
| 998 | normative objects declared | **9** |
| 998 | pipeline stages | **4** |
| 999 | fundamental distinctions | **6** |
| 999 | stated inequalities | **5** |
| 1000 | candidate record fields | **7** |
| 1001 | candidate identity bindings | **8** |
| 1002 | candidate digest terms | **7** |
| 1004 | gate-evaluation record fields | **7** |
| 1005 | gate states | **9** |
| 1014 | reference release predicates | **13** |
| 1015 | blocking states | **6** |
| 1018 | gate input closure conjuncts | **7** |
| 1023 | release-specific requirement examples | **7** |
| 1024 | policy layers (BASE + branches) | **8** |
| 1026 | release classes | **5** |
| 1031 | artifact fields | **5** |
| 1033 | packaging chain stages | **5** |
| 1037 | build identity fields | **7** |
| 1040 | provenance chain stages | **6** |
| 1044 | CI execution-record bindings | **8** |
| 1052 | CI evidence scopes | **6** |
| 1056 | freeze bindings | **8** |
| 1061 | release manifest fields | **16** |
| 1067 | manifest completeness minimum | **8** |
| 1068 | traceability chain stages | **8** |
| 1069 | release claim fields | **6** |
| 1076 | signer identity fields | **4** |
| 1080 | publication metadata fields | **5** |
| 1088 | release statuses | **7** |
| 1090 | failure transitions | **5** |
| 1091 | state-machine states drawn | **9** |
| 1094 | override identity fields | **7** |
| 1105 | critical gate mutations | **9** |
| 1107 | gate negative fixtures | **11** |
| 1110 | manifest validation fixtures | **10** |
| 1111 | manifest validation checks | **7** |
| 1116 | release closure conjuncts | **9** |
| 1118 | evidence bundle items | **12** |
| 1124 | release audit questions | **15** |
| 1125 | release report fields | **10** |
| 1131 | claim-scope matrix rows | **6** |
| 1132 | prohibited promotions | **3** |
| 1151 | trust boundaries | **10** |
| 1153 | independent-verification checks | **7** |
| 1154 | minimal verifier requirements | **8** |
| 1164 | release mutation targets | **9** |
| 1166 | release negative corpus items | **11** |
| 1178 | gate report fields | **7** |
| 1194 | type-level separations | **8** |
| 1198 | release transition errors | **14** |
| 1200 | verification chain stages | **13** |
| 1200 | governing laws | **15** |
| 1200 | invariant lines | **13** |

v1.4 is the **boundary document**: it takes v1.3's derived conformance verdict and defines the
conditions under which that verdict may become a release. Its shape follows the boundary —
candidate (§1000–§1002), gate (§1003–§1022), release-specific requirements (§1023–§1030), artifact
and source (§1031–§1042), CI authority (§1043–§1055), freeze (§1056–§1060), manifest
(§1061–§1068), claim and signature (§1069–§1079), publication (§1080–§1087), transition and override
(§1088–§1099), gate evidence and fixtures (§1100–§1110), manifest validation and the identity graph
(§1111–§1119), portability (§1120–§1123), reporting (§1124–§1132), reproduction (§1133–§1149),
trust and independence (§1150–§1159), determinism (§1160–§1172), retention and audit
(§1173–§1177), fail-fast and gate coverage (§1178–§1186), bootstrap (§1187–§1192), implementation
guidance (§1193–§1199), and the terminus (§1200).

---

## §3 — Headline A: the inverted scope relation is superseded in substance

### F-25.1 — PARTIALLY PROVED — §1071 restores the containment direction in the lineage's own notation, and §1200 makes it a release law; v1.2 §749's symbol is still wrong text

The single highest-priority item carried out of Part XXIII was v1.2 §749's
`CLAIMED_SCOPE ⊇ EXECUTED_SCOPE`. Measured symbol counts across the lineage:

| Document | § | Statement | Symbol |
|---|---|---|---|
| v0.3 | 41 | `CLAIMED ⊆ EXECUTED ⊆ DECLARED` | `⊆` correct |
| v1.0 | 545 | `CLAIMED ⊆ EXECUTED ⊆ AUTHORIZED` | `⊆` correct |
| v1.2 | 749 | `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` | **`⊇` inverted** |
| v1.2 | 749 | *"A claim exceeding executed scope is invalid."* | prose correct |
| v1.3 | 820 | *"A broader claim cannot substitute for missing evidence…"* | prose correct |
| **v1.4** | **1071** | **`ClaimScope ⊆ EvidenceScope`** and **`ClaimedPopulation ⊆ CoveredPopulation`** | **`⊆` correct, twice** |

Measured in v1.4: `⊆` occurs **2 times**, both in §1071; `⊇` occurs **0 times**; `CLAIMED_SCOPE` = 0.
§1070 additionally states the non-implications the audit has insisted on since Part XI — a claim of
protocol conformance does **not** imply bug-free, secure, production-safe, or correct for all
environments — and §1200 closes with `NO TRACEABLE CLAIM SCOPE → NO RELEASE CLAIM`.

**Classification: PARTIALLY PROVED — CLOSED IN SUBSTANCE, OPEN IN TEXT.** The distinction matters and
should be stated rather than elided:

* **Substance:** a conforming implementation that reads v1.0, v1.3 and v1.4 has the correct relation
  stated three times in notation or prose, makes it a release law, and has no route to the inverted
  one — v1.2 §749's symbol is the only place the wrong direction appears, and it is contradicted by
  its own next sentence.
* **Text:** v1.2 §749 still reads as it reads. A reader with only v1.2 gets the relation backwards.

So C-23.1 and C-24.10 are **downgraded from release-critical to corrective**: the fix is still worth
one character, and nothing now depends on it being made.

---

## §4 — Headline B: the release candidate's identity drops coverage

### F-25.2 — PROVED — §1001 requires eight identity bindings, §1000's record carries seven fields, and §1002's digest covers seven terms — `coverage` is absent from the record and from the digest

| § | What it states | Contains `coverage`? |
|---|---|---|
| 1001 | *"A release candidate MUST bind:"* — subject, artifact, protocol, fixture population, evidence population, **coverage**, policy, release policy | **yes** |
| 1000 | the candidate JSON: `release_candidate_id`, `subject_id`, `artifact_id`, `protocol_digest`, `population_digest`, `evidence_population_digest`, `policy_digest` | **no** |
| 1002 | `candidate_digest = H(canonical(subject_identity + artifact_identity + protocol_identity + population_identity + evidence_population_identity + policy_identity + release_policy_identity))` | **no** |

The consequence is concrete and release-relevant: **two release candidates that differ only in their
coverage identity produce the same candidate digest.** §1001 says the candidate MUST bind coverage;
§1002 — which defines the candidate's canonical identity — has no term for it. This is the same defect
shape Part XXIV found three times in v1.3 (*a rule stated whose domain is not*), and it is the first
instance where the dropped domain is an **identity** rather than a vocabulary: the candidate digest is
the value §1004's gate evaluation points at and §1061's manifest carries.

It sits directly beside §1030 (`a release requires release gate PASS + immutable release manifest`) and
§1132's prohibition on promotion — the machinery that is supposed to prevent an under-evidenced release
from becoming a claim. A candidate whose coverage identity is invisible to its own digest is exactly
the candidate §1132 forbids promoting.

**Fix (one line):** add `coverage_identity` to §1002 and `coverage_digest` to §1000's record.

### F-25.3 — PROVED — the release manifest carries the evidence reference the audit has tracked since Part XXI, but §1067's completeness rule does not require it

Part XXI recorded: *"v1.0 §522's release manifest carries NO evidence reference."* Measured: v1.0 §522
contains no occurrence of the string `evidence` at all.

v1.4 §1061's manifest has **16 fields**, including three that §522 lacks entirely:

| §1061 field | v1.0 §522 equivalent | Status |
|---|---|---|
| `protocol` | `protocol{specification_digest, schema_digest}` | present |
| `subject` | `subject{commit, implementation_digest}` | present |
| `source` | — | **new** |
| `artifact` | — | **new** |
| `build` | — | **new** |
| `fixture_population` | `fixtures{manifest_digest}` | present |
| **`evidence_population`** | **—** | **new — the missing reference** |
| `coverage` | `verification{coverage_id}` | present |
| `conformance` | `verification{conformance_id}` | present |
| `gate` | `verification{gate_id}` | present |
| `policies` | — | **new** |

So the reference the audit asked for **exists**. But §1067 — *"The manifest MUST identify every
release-critical dependency. At minimum:"* — lists **eight** items and omits three of the eleven
sections §1061 defines:

| §1061 section | Required by §1067? |
|---|---|
| protocol | yes |
| subject | yes |
| artifact | yes |
| fixture population | yes |
| coverage | yes |
| conformance | yes |
| gate policy / gate evaluation | yes |
| **evidence_population** | **no** |
| **source** | **no** |
| **build** | **no** |

**Classification: PROVED — PARTIALLY CLOSED.** The field exists and §1116's `ReleaseClosure` includes
`EvidenceValid`, so evidence is inside the closure predicate; but the one rule that makes a manifest
*complete* does not name it. The Part XXI finding is therefore closed in structure and open in
requirement strength — the same pattern as F-25.1, and worth naming as such: **v1.4 supplies the shape
of the fix without making it mandatory.**

**Fix (three words):** add `evidence population`, `source` and `build` to §1067's list.

---

## §5 — Headline C: the release vocabulary disagrees with itself across five enumerations

### F-25.4 — PROVED — §1005 gives nine gate states, §1015 six blocking states, §1088 seven release statuses, §1090 five failure transitions, and §1091's diagram draws three of them plus one state that appears in no list

| Enumeration | § | Members | Count |
|---|---|---|---|
| gate states | 1005 | `PENDING EVALUATING PASS FAIL BLOCKED ERROR UNKNOWN STALE INVALID` | 9 |
| blocking states | 1015 | `FAIL ERROR UNKNOWN BLOCKED STALE INVALID` | 6 |
| release statuses | 1088 | `CANDIDATE GATED ELIGIBLE RELEASED PUBLISHED REVOKED SUPERSEDED` | 7 |
| failure transitions | 1090 | `GATED → FAIL / BLOCKED / ERROR / UNKNOWN / INVALID` | 5 |
| states drawn in the machine | 1091 | `CANDIDATE FROZEN GATED FAIL BLOCKED ERROR ELIGIBLE RELEASED PUBLISHED` | 9 |

Measured facts:

1. **§1005 and §1088 share zero members.** They are disjoint vocabularies — gate *states* versus
   release *statuses* — which is defensible in principle given §999's separation of the concepts.
2. **§1091's machine mixes both vocabularies and adds a tenth state.** It draws `FROZEN`, which appears
   in **neither** §1005's nor §1088's list, even though §1056 and §1090 both make freezing a mandatory
   stage. `FROZEN` is defined operationally (a freeze exists, with a `freeze_digest` per §1057) and is
   absent from the status vocabulary.
3. **The diagram omits three of the six blocking states.** Measured: §1091's block contains `FAIL`,
   `BLOCKED`, `ERROR`; it does **not** contain `UNKNOWN`, `INVALID` or `STALE`. Yet §1090 declares five
   failure transitions (adding `UNKNOWN` and `INVALID`) and §1015 declares six blocking states (adding
   `STALE` as well).

This matters more than a diagram usually would, because §1091 is the section titled *"The complete
release state machine"* — the artifact an implementer will port directly into an enum and a transition
table. An implementation built from §1091 has three terminal failure states; the document requires six.
§1144 then makes `historical gate ≠ current gate` a re-evaluation trigger (STALE), §1145 forbids
misrepresenting a protocol identity, and §1198 requires `E_GATE_STALE` **and** `E_GATE_INVALID` as
error identities — codes that the machine as drawn can never produce.

**Classification: PROVED.** All five enumerations were extracted verbatim and compared as sets; the
mapping from §1090's transitions to §1091's branches is textual. **Fix:** add `FROZEN` to §1088 and
draw all five failure branches (or reduce §1090/§1015 to match the machine and delete the
`E_GATE_STALE`/`E_GATE_INVALID` codes).

### F-25.5 — PROVED — four bare `"status"` keys carry four vocabularies in one document, and the fourth is undeclared

Measured: v1.4 uses the bare key `"status"` four times, with no `*_status` name appearing anywhere in
the document (`named status fields: ['status']`).

| § | Value | Vocabulary | Enumerated? |
|---|---|---|---|
| 1004 | `"PASS"` | gate state (§1005) | yes |
| 1069 | `"RELEASED"` | release status (§1088) | yes |
| 1100 | `"PASS"` | predicate status | **no** — no predicate-status set is defined |
| 1156 | `"VERIFIED"` | verifier outcome | **no** — `VERIFIED` appears only as a term in inequalities |

The `VERIFIED` case is the sharpest: measured, `VERIFIED` occurs **4 times** in v1.4 — in §999's
`SIGNED ≠ VERIFIED`, in §1117's `RELEASE ≠ VERIFIED`, and as the terminal value of the independent
verifier's output in §1156. No section lists the permitted values of a verifier's status, so §1156's
`status: VERIFIED` is a field whose domain is defined only by the example that uses it.

This is the **third consecutive document** with an undeclared field domain:

| Document | § | Undeclared domain |
|---|---|---|
| v1.2 | 673 | observation status (`OBSERVED`) |
| v1.3 | 843 | conformance-claim status (`CONFORMANT`) |
| **v1.4** | **1156** | verifier status (`VERIFIED`) |

and the **fourth document** using a bare `status` key for vocabularies that the document itself insists
are distinct (v1.2 had six such sites; v1.4 has four). C-23.3's namespacing rule remains the cheapest
outstanding correction in the lineage — one rule, four documents, five vocabularies.

---

## §6 — Headline D: the mutation-versus-fixture gap recurs — and the release layer shows it can be closed

### F-25.6 — PROVED — §1105's nine critical gate mutations include two that §1107's eleven fixtures cannot detect, two sections apart in the same document

| § | Statement | Count |
|---|---|---|
| 1105 | *"Critical mutations include:"* — `ignore required predicate`, **`convert BLOCKED → PASS`**, **`convert UNKNOWN → PASS`**, `ignore stale evidence`, `ignore artifact mismatch`, `ignore population mismatch`, `ignore failed mutation`, `ignore missing CI evidence`, `ignore manifest mismatch` | 9 |
| 1106 | *"Every critical gate mutation MUST be detected."* | — |
| 1107 | *"Required gate fixtures SHOULD include:"* — `all predicates pass`, `required predicate fails`, `required evidence missing`, `evidence stale`, `artifact mismatch`, `subject mismatch`, `population mismatch`, `policy mismatch`, `CI evidence missing`, `critical mutation survives`, `manifest mismatch` | 11 |
| 1109 | near-miss fixtures *"differing from the valid candidate by exactly one critical condition"* | — |

Measured presence in §1107's fixture list:

| Scenario needed | In §1107? |
|---|---|
| a `BLOCKED` candidate | **no** |
| an `UNKNOWN` candidate | **no** |
| an `INVALID` candidate | **no** |
| a `STALE` candidate | yes (`evidence stale`) |
| a failing predicate | yes |
| missing evidence / CI | yes |
| mismatch of artifact / population / manifest | yes |

So the two mutations §1105 names in the `convert X → PASS` form for `X ∈ {BLOCKED, UNKNOWN}` have **no
fixture** that would expose them: a gate that converts `BLOCKED` to `PASS`, exercised against §1107's
eleven scenarios, passes every one. §1109, two sections later, asks for exactly the missing artifact —
candidates differing by one critical condition — and §1015 makes all six blocking states critical.

This is **Part XXIV's F-24.8 recurring in a new document and a new layer**, and the recurrence is the
finding: in v1.3 the gap was between two *mutation* lists (§936 vs §994); in v1.4 it is between a
mutation list and a *fixture* list, in the same document, two sections apart. The pattern is stable
across both: **the states that are neither PASS nor FAIL are the ones omitted.**

### F-25.7 — PROVED (positive, by contrast) — the release layer's mutation-to-corpus mapping is complete

| §1164 mutation target | §1166 negative-corpus item | Match |
|---|---|---|
| artifact digest | wrong artifact | ✓ |
| source digest | wrong source | ✓ |
| population digest | wrong population | ✓ |
| coverage digest | wrong coverage | ✓ |
| conformance digest | wrong conformance | ✓ |
| gate result | failed gate | ✓ |
| policy digest | wrong policy | ✓ |
| manifest digest | invalid manifest | ✓ |
| signature | invalid signature | ✓ |
| — | stale evidence | extra |
| — | blocked gate | extra |

**9 of 9 targets mapped, with two extra scenarios.** §1165 then says every critical mutation MUST be
detected and a survivor blocks release, and §1166 says the corpus *MUST* contain those eleven — a MUST
against a MUST, unlike §1105/§1107's MUST against SHOULD.

This comparison is what makes F-25.6 a defect rather than a house style: **the same document, using the
same method, gets the mapping right at the release layer and wrong at the gate layer.** The remedy is
therefore not a new design — it is to write §1107 the way §1166 was written.

---

## §7 — The specification states one of the audit's own founding rules

### F-25.8 — PROVED (positive) — §1051 forbids the absence-of-observation inference, in the audit's own terms

§1051 verbatim:

> "Searching a CI system and finding no matching run is evidence about observability, not proof that no
> run exists. Therefore `NOT_FOUND` MUST NOT automatically become `NO_EXECUTION_OCCURRED` unless the
> source is authoritative for that conclusion."

This is, in a single normative section, the rule this audit has carried since Part V under two names —
*"Absence of an observation must not be confused with absence of the object being observed"* and
*"verification failure vs verifier failure"* — expressed in the one place where it does real work in
practice: a CI search that returns nothing. It is the first time a supplied document states one of the
audit's founding rules **verbatim in substance**, and it is stated as a MUST NOT with an explicit
exception condition rather than as an admonition.

Read with §904 of v1.3 (*"CI claimed PASS does not establish verified evidence"*) and §1046 of v1.4
(*"A green workflow badge does not establish artifact identity"*), the lineage now covers both
directions of the CI confusion: a claimed pass is not evidence, and a missing run is not proof of
non-execution.

### F-25.9 — PROVED (positive) — §1187–§1192 close the self-certification problem at the system level

| § | Statement |
|---|---|
| 1186 | *"The release gate therefore has the same fundamental structure as fixture conformance"* — declared predicates + executed predicates + evidence + coverage + policy |
| 1187 | the release gate MAY itself be verified by the same architecture — producing a gate protocol, gate fixtures, gate evidence, gate conformance |
| 1188 | the verifier MUST have a bootstrap path *"independent enough to avoid circular validation"* |
| 1189 | `RFL-AE verifies RFL-AE using RFL-AE` is **prohibited as sole assurance** without an independently trusted bootstrap layer |
| 1190 | bootstrap via an independent implementation, a small trusted core, or a manually auditable canonical verifier |
| 1192 | minimise the trusted computing base — *"the more code required to validate a release, the larger the validation trust boundary"* |

v1.2 §698 prohibited a *program* from certifying itself; §1189 prohibits a *system* from doing so, and
§1186 makes the gate subject to the coverage discipline it enforces on everything else. §1183–§1186
supply the mechanism: gate predicates form a declared population with an immutable digest (§1184), a
complete gate claim may not be made when required predicates were omitted (§1185), and coverage is
reported over declared/evaluated/passed/failed/blocked/unknown predicates (§1183).

This is the recursive closure of the whole lineage: **the verifier is now inside the same evidence
discipline it applies to its subjects**, with an explicit prohibition on closing the loop purely within
itself and an explicit instruction to keep the loop small.

---

## §8 — The exclusion rule reaches five artifacts; the one that needed it first still lacks it

### F-25.10 — PROVED — v1.4 applies the self-reference exclusion to two more artifacts, and the compiled pack remains the only one without it

| # | Artifact | § | Formula |
|---|---|---|---|
| 1 | evidence | v0.7 §239, v0.8, v0.9, v1.2 §682 | `evidence_id = sha256(canonical(evidence_without_evidence_id))` |
| 2 | compiled pack | v0.4 §86 vs §88 | **none — never stated** |
| 3 | **release manifest** | **v1.4 §1064, §1112** | `manifest_digest = H(canonical(manifest_without_manifest_digest))` |
| 4 | **gate evaluation** | **v1.4 §1102** | `gate_digest = H(canonical(gate_evaluation_without_digest))` |
| 5 | coverage record | v1.3 §847 (by construction) | digest over population + entries + policy identity |

Measured in v1.4: `manifest_without_manifest_digest` = 1, `gate_evaluation_without_digest` = 1,
`recursively including itself` = 1 (in §1112, which restates §1064 in prose:
*"The manifest digest MUST be calculated without recursively including itself. The digest input MUST be
explicitly defined."*).

The compiled pack remains at `CompiledPack` = 0, `compiled_digest` = 0 — **the fourteenth document**
without the rule, in a lineage that has now applied it four times to four other artifacts.

**Classification: PROVED, carried.** The v0.4 §86 defect is no longer merely unanswered; it is now
*outnumbered*. §1112's prose is the exact sentence v0.4 §86 needs, and it is six words: *"MUST be
calculated without recursively including itself."* C-23.14 remains open and its cost has not changed.

---

## §9 — Smaller findings

### F-25.11 — MINOR — the release claim fixes both of Part XXIV's claim-record defects, but only for the release claim

| Defect (Part XXIV) | v1.4 response |
|---|---|
| F-24.2: §844 requires a claim to state its scope; v1.3 §843's canonical claim record has no `scope` key | v1.4 §1069's claim JSON **contains** `"scope": "RFL-AE protocol conformance"` |
| F-24.3: the claim status vocabulary is never enumerated | v1.4 §1069's `"status": "RELEASED"` is a member of §1088's enumerated release statuses |

Both fixes are real, and both apply to the **release claim** — a different object from v1.3's
conformance claim, which §1072 consumes unchanged: *"A release claim MUST be derived from frozen
candidate + conformance claim + release gate + release manifest."*

So a conforming implementation now derives a scoped, status-typed release claim **from** an unscoped,
status-untyped conformance claim. The chain is only as typed as its weakest link, and §1021's gate
binding (`conformance_claim_id`, `conformance_digest`) propagates the weaker object's identity while
adding nothing to its scope.

This is worth naming as a **structural property of the whole lineage** rather than as a one-off: the
documents are additive, so a fix is never retro-applied. Every repair in this corpus has arrived as a
correction *within a new document's own objects*. The consequence for the audit is that "fixed" has to
be resolved per object, not per defect — which is why Part XXV keeps a supersession ledger (§11) rather
than a defect list.

### F-25.12 — PROVED — both error-code conventions are used again, for the third consecutive document

| § | Code | Convention | Origin |
|---|---|---|---|
| 1198 | fourteen identities: `E_CANDIDATE_NOT_FROZEN` … `E_RELEASE_IDENTITY_MISMATCH` | `E_`-prefixed | v1.2 §679 |
| 1179 | `"code": "REQUIRED_CI_EVIDENCE_MISSING"` | bare uppercase | v1.2 §719 |

Measured: 14 distinct `E_`-prefixed codes in v1.4 and exactly one bare code, six sections after the
prefixed set is declared. Sequence across documents: v1.2 introduced both (F-20), v1.3 consumed one of
each (F-24.13), v1.4 consumes fourteen of one and one of the other. The conventions are not converging;
they are both in active use, and §1179's single bare code is the one a blocking-reason consumer will
actually read.

**Fix:** C-23.9 / C-24.11, unchanged, plus naming which space §1179's `code` comes from.

### F-25.13 — MINOR — §1005's non-terminal gate states have no transition rules

§1005 declares `PENDING` and `EVALUATING` alongside the seven terminal-ish states, and §1015's
`blocking_states` omits both. Nothing states when a gate leaves `PENDING`, whether `EVALUATING` is
observable, or what a consumer should do with either. §1091's machine does not draw them. In a
document that elsewhere refuses unspecified state domains (F-25.5), two of nine states are left
without semantics.

### F-25.14 — MINOR — §1198's fourteen error identities are not in §1198's own API

§1197's `release(frozen_candidate, passing_gate, valid_manifest) -> Release` "MUST reject: non-passing
gate / stale candidate / invalid manifest / artifact mismatch" — four conditions. §1198 declares
fourteen error identities. Nine of them have no stated trigger in the API that would return them
(`E_CONFORMANCE_MISSING`, `E_COVERAGE_MISSING`, `E_REQUIRED_EVIDENCE_MISSING`, `E_CI_EVIDENCE_MISSING`,
`E_MUTATION_REQUIREMENT_FAILED`, `E_POLICY_MISMATCH`, `E_MANIFEST_DIGEST_MISMATCH`,
`E_RELEASE_IDENTITY_MISMATCH`, `E_GATE_STALE`). Each is plausibly reachable from other sections —
`E_REQUIRED_EVIDENCE_MISSING` from §1153, `E_CI_EVIDENCE_MISSING` from §1050 — but the mapping is
nowhere stated, so an implementer cannot know which errors their own function must produce.

**Fix:** one column in §1198 naming the section and the API that emits each code.

### F-25.15 — PROVED (positive) — the type-name residual from Part XXIV closes, and §1194 is the types-level answer

Part XXIV's F-24.1 recorded a residual: `claim_id` had arrived (§843) but the CamelCase type-system
spelling `ClaimId` was still 0, and `pub struct Claim` was 0. Measured in v1.4: **`ClaimId` = 1** and
**`ReleaseId` = 2**, both in §1194, which names eight identifier types —

```
SubjectId  ArtifactId  FixtureId  EvidenceId  CoverageId  ClaimId  ReleaseId  Digest
```

— and states they *"must not all be interchangeable strings"*, with §1193's Rust guidance (*"use strong
identifier types"*, *"avoid implicit string IDs"*, *"validate before construction"*). §1198 then gives
fourteen explicit error identities, satisfying §1193's *"use explicit error enums"*.

The identifier gap that Part XX opened four Parts ago is now closed at all three levels: the value
(`claim_id`, `release_id`), the type name (`ClaimId`, `ReleaseId`), and the type-safety rule (§1194).

### F-25.16 — PROVED (positive) — the boundary rules that make the release layer meaningful

Twelve new guards, each closing a specific route by which a release could be claimed without being
established:

| § | Guard |
|---|---|
| 1041 | tested artifact ≠ released artifact → gate MUST fail or become invalid |
| 1046 | a green workflow badge does not establish artifact identity |
| 1051 | `NOT_FOUND` ≠ `NO_EXECUTION_OCCURRED` |
| 1060 | the gate MUST prevent time-of-check/time-of-use divergence |
| 1081 | the published artifact SHOULD be re-digested against `manifest.artifact_digest` |
| 1082 | `GATE RESULT ≠ PUBLISHED ARTIFACT` — the claim MUST NOT be extended to what was published |
| 1092 | `CANDIDATE → RELEASED` is prohibited without the gate |
| 1095 | an override means *policy exception granted*, **not** *predicate passed* |
| 1097 | a release MUST NOT become eligible because a human ignored an unresolved gate |
| 1099 | an emergency release MUST NOT claim that omitted requirements were satisfied |
| 1101 | a gate PASS without predicate-level evidence is not release-grade |
| 1136 | reproducibility failure MUST be preserved; the successful build MUST NOT be the only one retained |

§1095 and §1099 are the two that matter most: together they close the last and most common route by
which a real release process acquires an unearned claim — a human decision recorded as a technical
result. §1095 forbids the *semantics* of the confusion and §1099 forbids the *sentence* it produces.

Cumulative guard count: **at least 45** across the lineage (21 after Part XXIII, +12 in v1.3,
+12 in v1.4), still with no index. That absence has now been a finding for three consecutive Parts.

---

## §10 — Carried items: the fourteenth-document census

| Item | First raised | Status in v1.4 |
|---|---|---|
| v1.2 §749's inverted scope symbol | Part XXIII | **superseded in substance** by §1071 + §1200; text unchanged (F-25.1) |
| compiled-pack digest (v0.4 §86 vs §88) | Part XV | `CompiledPack` = 0, `compiled_digest` = 0 — **open, and now outnumbered 4:1 by other artifacts that have the rule** |
| `EnvironmentAuthority` | Part XIII | **0** — undefined in the fourteenth document |
| `evidence.schema.json` | Part XV | **0** |
| `ResultStatus` / `CheckStatus` as types | Part XX | both **0** — the lineage's status-type naming never converged |
| `ClaimId` / `ReleaseId` type names | Part XX | **CLOSED** — §1194 names both (F-25.15) |
| three manifest-like records unlinked | Part XXI/XXIII/XXIV | **PARTIALLY CLOSED** — §1061's manifest is a superset of v1.0 §522's and carries `evidence_population`; §1067 does not require it (F-25.3) |
| bare `"status"` key | Part XXIII | **worsened** — four sites, four vocabularies, one undeclared (F-25.5) |
| two error-code conventions | Part XXIII | **worsened** — third document, both in active use (F-25.12) |
| mutation/fixture list gap | Part XXIV | **recurred** in a new document and layer (F-25.6) |
| README/997 repository claim | Part II | **CLOSED, cosmetic** |
| coverage status underived | Part XX | **CLOSED** (v1.3) |
| v1.2 §790 `Covered` | Part XXIII | **CLOSED** (v1.3) |

Two items closed, three partially closed, four open, four worsened-or-recurred. The lineage closes
forward faster than it repairs backward — which is precisely what an additive specification should do,
and precisely why the audit must maintain a supersession ledger rather than a defect list.

---

## §11 — The supersession ledger

This Part introduces a distinction the previous twenty-four did not need, because the lineage has now
run long enough for later documents to answer earlier ones. Every finding the audit has carried is
resolvable in exactly one of three ways, and they must not be conflated:

| Status | Meaning | Example |
|---|---|---|
| **CLOSED — corrective** | a later document defines the concept such that the earlier text can no longer mislead | v1.2 §790's `Covered` column, closed by v1.3 §810 + §842 |
| **SUPERSEDED — in substance** | the earlier text is still wrong, but the release boundary makes it unreachable by a conforming implementation | v1.2 §749's `⊇`, superseded by v1.4 §1071 + §1200 |
| **OPEN — live** | no later document addresses it | the compiled-pack digest (v0.4 §86) |

Applying that taxonomy to the audit's carried set:

| Finding | Status | Authority |
|---|---|---|
| coverage status underived | CLOSED — corrective | v1.3 §810, §841, §842, §869 |
| v1.2 §790 `Covered` | CLOSED — corrective | v1.3 §810, §842 |
| `ClaimId` / claim identity | CLOSED — corrective | v1.3 §843; v1.4 §1194 |
| v1.2 §749 inverted scope | SUPERSEDED — in substance | v1.4 §1071, §1200 |
| v1.3 §843 missing `scope` | PARTIALLY SUPERSEDED | v1.4 §1069 fixes the release claim only |
| v1.3 claim-status domain | PARTIALLY SUPERSEDED | v1.4 §1088 enumerates release statuses only |
| release manifest's missing evidence reference | PARTIALLY CLOSED | v1.4 §1061 supplies it; §1067 omits it |
| v1.3 §801 six declared-only objects | OPEN | — |
| v1.3 §936 / §994 mutation mismatch | OPEN — and recurred | v1.4 §1105 vs §1107 |
| compiled-pack digest circularity | OPEN | — |
| `EnvironmentAuthority` | OPEN | — |
| `evidence.schema.json` | OPEN | — |
| status-token collisions | OPEN — and worsened | v1.2, v1.3, v1.4 |
| error-code conventions | OPEN — and worsened | v1.2, v1.3, v1.4 |
| README/997 | CLOSED — cosmetic, since Part III | — |

**The practical value of this table** is that it converts twenty-five Parts of findings into four
actionable buckets. Everything marked CLOSED can be struck from any future severity count. Everything
marked SUPERSEDED needs one edit at most and blocks nothing. The OPEN list is six items long, and
three of them — the compiled-pack digest, `EnvironmentAuthority`, and `evidence.schema.json` — are
single-line or single-file matters that have now survived fourteen documents.

---

## §12 — Corrections, in dependency order

| # | Correction | Depends on | Blocks |
|---|---|---|---|
| C-25.1 | **Add `coverage_identity` to §1002 and `coverage_digest` to §1000's record.** | — | candidate digest uniqueness; F-25.2 |
| C-25.2 | **Add `evidence population`, `source` and `build` to §1067's minimum list.** | — | release-grade manifest completeness; F-25.3 |
| C-25.3 | **Add `FROZEN` to §1088 and draw all five failure branches in §1091** (or delete `E_GATE_STALE`/`E_GATE_INVALID`). | — | the release state machine; F-25.4 |
| C-25.4 | **Add `BLOCKED`, `UNKNOWN` and `INVALID` candidates to §1107**, and raise §1107 to MUST as §1166 already is. | — | the §1106 detection requirement; F-25.6 |
| C-25.5 | **Namespace the `status` key** (C-23.3/C-24.7) — four sites, four vocabularies, one undeclared. | — | every generated record; F-25.5 |
| C-25.6 | **Enumerate the verifier status vocabulary** in §1156. | C-25.5 | verifier output; F-25.5 |
| C-25.7 | **Give §1198 each error's emitting section and API.** | — | implementable error enums; F-25.14 |
| C-25.8 | **Give `PENDING` and `EVALUATING` transition rules** or remove them from §1005. | C-25.3 | gate lifecycle; F-25.13 |
| C-25.9 | **Add `scope` and a status set to v1.3 §843's conformance-claim record** (C-24.1, C-24.2) — v1.4 shows the shape in §1069. | — | the claim chain's weak link; F-25.11 |
| C-25.10 | **Unify v1.2's error-code spaces** (C-23.9/C-24.11) and name which space §1179 draws from. | — | generated error enums; F-25.12 |
| C-25.11 | **Apply the exclusion rule to the compiled pack** (C-23.14) — §1112's sentence is the wording. | — | the lineage's oldest defect; F-25.10 |
| C-25.12 | **Reconcile the three manifest-like records** by adding `manifest_digest` to v1.0 §522's `verification` block, or declaring §1061 the supersession of §522. | — | the manifest graph; F-25.3 |

C-25.1 and C-25.4 are the two that change behaviour; C-25.11 has the highest ratio of age to cost.

---

## §13 — Closing judgement

### §13.1 The stage

v1.4 is the **boundary document**, and with it the declared sequence from Part XI is complete at both
ends:

```
§151–§171  Part XI  PROMPT PACK PROTOCOL                 NORMATIVE SPECIFICATION
§201–§271  v0.7     TYPES                                SCHEMA
§272–§361  v0.8     REFERENCE IMPLEMENTATION BLUEPRINT   REFERENCE IMPLEMENTATION
§362–§455  v0.9     EXECUTABLE PROTOCOL KERNEL            REFERENCE IMPLEMENTATION
§456–§555  v1.0     CONFORMANCE / EVIDENCE / RELEASE      TEST + EVIDENCE
§556–§655  v1.1     FIXTURE CORPUS + CONFORMANCE          TEST FIXTURES
§656–§800  v1.2     EVIDENCE + EXECUTION RECORD           EXECUTION EVIDENCE
§801–§997  v1.3     COVERAGE + CONFORMANCE AGGREGATION    DERIVATION
§998–§1200 v1.4     RELEASE GATE + MANIFEST               BOUNDARY + IMMUTABILITY
```

§1200's chain — SPECIFICATION → FROZEN CONTRACT → FIXTURE CORPUS → EXECUTION → EVIDENCE → COVERAGE →
CONFORMANCE → RELEASE CANDIDATE → RELEASE FREEZE → RELEASE GATE → RELEASE MANIFEST → RELEASE CLAIM →
PUBLICATION — is the first diagram in the lineage that spans every layer from prompt packing to a
published artifact, and it is consistent with the diagrams that precede it, whose sections are named
in the recorded headers: v0.8 (§273 repository tree, §274 workspace graph, §287 transition table,
§353 dependency graph), v0.9 (§364 tree, §365 crate dependency graph, §368 identifier tree), v1.0
(§469, §482, §526, §529, §555), v1.1 (§571, §580, §582, §593, §649, §655), v1.2 (§800) and v1.3
(§997). No diagram in that sequence contradicts §1200's ordering. The closest comparison is v0.8 §274's
six-crate chain — `rfl-protocol → rfl-validation → rfl-transition → rfl-evidence → rfl-coverage →
rfl-gate` — which is the same layered shape as §1200's thirteen stages, with the six crates mapping
onto the six middle stages (specification → execution → evidence → coverage → gate → manifest).

Nothing in §0–§1200 has been built. The audited repository still contains only the upstream toolchain,
whose five measured defects are all in its verification layer: `has_prov`'s inversion, `neg()`'s
unstructured findings, crash-as-pass, the 997 cosmetic gap, and `check_fences`'s parity verdict found
in Part XXIII.

### §13.2 What v1.4 gets right that no predecessor did

1. **The scope relation is restored in notation** (§1071, twice) and made a release law (§1200) —
   superseding the lineage's only inverted symbol (F-25.1).
2. **The release manifest carries the evidence reference** the audit has tracked since Part XXI
   (§1061), along with `source` and `build` (F-25.3).
3. **The exclusion rule reaches three artifacts in one document** — manifest (§1064), gate evaluation
   (§1102), with the prose rule stated in §1112 (F-25.10).
4. **The gate becomes a coverage population** (§1183–§1186) with a predicate digest (§1184) and an
   incompleteness rule (§1185) — the verifier is now inside the discipline it enforces (F-25.9).
5. **Self-certification is prohibited at the system level** with an explicit bootstrap requirement and
   a TCB-minimisation instruction (§1188–§1192).
6. **§1051 states the absence-of-observation rule** in the one place it does practical work (F-25.8).
7. **Twelve new boundary guards**, including the two that close the human-override route (§1095,
   §1099) and the TOCTOU and publication-race guards (§1060, §1082).
8. **The release-layer mutation corpus is complete** against its mutation list — the only such mapping
   in the lineage that is (F-25.7).

### §13.3 What v1.4 gets wrong

Two instances of the Part XXIV class — a rule stated whose domain is not (§1001's coverage binding
absent from §1000 and §1002; §1067's minimum list omitting three of §1061's sections) — one internal
set disagreement across five enumerations (§1005/§1015/§1088/§1090/§1091, F-25.4), one recurrence of
the mutation/fixture gap in a new layer (F-25.6), and two long-running vocabulary defects that this
document, like its two predecessors, uses rather than resolves (F-25.5, F-25.12).

### §13.4 The laws of §1200

Fifteen `NO … → NO …` laws — the largest set in the lineage — plus a thirteen-line architectural
invariant that ends with the sentence which has now closed three consecutive documents:

```
EXECUTION ESTABLISHES WHAT RAN.
EVIDENCE ESTABLISHES WHAT WAS OBSERVED.
COVERAGE ESTABLISHES WHICH REQUIREMENTS HAVE EVIDENCE.
CONFORMANCE ESTABLISHES WHETHER THE REQUIRED POPULATION SATISFIES ITS CONTRACT.
THE RELEASE GATE ESTABLISHES WHETHER THE FROZEN CANDIDATE SATISFIES RELEASE POLICY.
THE RELEASE MANIFEST ESTABLISHES EXACTLY WHAT WAS RELEASED.

A VERSION NUMBER DOES NOT CREATE IDENTITY.
A GREEN CI BADGE DOES NOT CREATE EVIDENCE.
A TEST COUNT DOES NOT CREATE COVERAGE.
A CONFORMANCE RESULT DOES NOT CREATE RELEASE ELIGIBILITY.
A SIGNATURE DOES NOT CREATE SEMANTIC CORRECTNESS.
A RELEASE CLAIM CANNOT EXCEED ITS EVIDENCE SCOPE.

NO LAYER MAY PRETEND TO BE THE NEXT LAYER.
```

| Document | § | Laws | Character |
|---|---|---|---|
| v0.8 | 361 | 11 | implementation-shaped |
| v0.9 | 455 | 16 | type-and-engine-shaped |
| v1.2 | 800 | 12 | identity-required |
| v1.3 | 997 | 10 | derivation-required |
| **v1.4** | **1200** | **15** | **boundary-required: every law names a transition between layers that must not be taken without its gate** |

Of the fifteen, **fourteen are decidable by inspecting a record for a missing field or digest**, which
makes v1.4's set the most mechanically checkable in the lineage — a direct consequence of the exclusion
rule now applying to the manifest and the gate evaluation. The fifteenth,
`NO TRACEABLE CLAIM SCOPE → NO RELEASE CLAIM`, is the one that requires reading two records together,
and it is the law that makes C-25.1 (coverage in the candidate digest) more than a tidiness issue.

The closing section's last line — *"NO LAYER MAY PRETEND TO BE THE NEXT LAYER"* — appears in v0.4, v0.8,
v1.0, v1.2, v1.3 and now v1.4. Six statements of the same sentence across fourteen documents is the
clearest possible evidence of what the lineage believes its principal failure mode to be: not a missing
layer, but a layer speaking for the one above it.

### §13.5 Verdict

**v1.4 is the most boundary-disciplined document in the lineage and a close structural echo of its
immediate predecessor.** Its closures are substantial and were measured as such: the scope relation
restored in notation, the evidence reference present in the manifest, the exclusion rule applied
twice more, the gate admitted to the coverage discipline, self-certification banned at the system
level. Its defects are the same *shape* as v1.3's and, in one case, literally the same defect —
which is the more useful observation: **the lineage's defect profile has stabilized even as its
subject matter has advanced.** A rule stated whose domain is not; a mutation list that omits the
non-PASS non-FAIL states; a bare `status` key; two error-code spaces. Four shapes, three documents,
one fix each.

Applied to its own standard, v1.4 fails on two of its fifteen laws as written:

* `NO COMPLETE COVERAGE → NO COMPLETE CONFORMANCE` is respected, but §1001's candidate-coverage
  binding is missing from the candidate's own digest, so two candidates can carry different coverage
  identities and the same identity (C-25.1).
* `NO CRITICAL MUTATION DETECTION → NO RELEASE` is stated, and §1106 requires every critical gate
  mutation to be detected — while §1107's fixture list cannot detect two of the nine §1105 names
  (C-25.4).

**Classification of the document overall: PARTIALLY PROVED** — a specification whose boundary rules
are the strongest in the lineage, whose counted enumerations do not yet agree with one another, and
which closes the audit's highest-priority carried item by supersession rather than by correction.

---

*Part XXV ends. The lineage now stands at **§0–§1200 across fourteen documents**, with one unassigned
number (§200) and one non-unique range (§1–§28).*

*Open items, in priority order: the candidate coverage identity (C-25.1), the gate negative fixtures
(C-25.4), the release state machine's three undeclared blocking states (C-25.3), the manifest
completeness list (C-25.2), the status namespace (C-25.5/C-25.6), the compiled-pack digest
(C-25.11 — open since Part XV, now outnumbered four to one), and the three items that no document has
ever touched: `EnvironmentAuthority`, `evidence.schema.json`, and the reconciliation of the
manifest-like records (C-25.12).*
