# LFR-Tools

Audit and verification work on the RFL-AE specification corpus and its `skills/` toolchain.

## Layout

```text
LFR-Tools/
├── README.md                     this file
│
├── vendor/
│   ├── PROVENANCE-rfl-ae.md      import manifest: source, revision, digests, licence status
│   └── rfl-ae/                   byte-exact snapshot of Abdus2023/RFL-AE @ 1090511
│                                 (immutable audit input — do not edit)
│
└── audit/                        findings, receipts, and design proposals
    ├── rfl-ae-skills-audit-consolidated.md    ← start here
    ├── rfl-ae-skills-review.md                Part I   (§1–§32)
    ├── rfl-ae-skills-review-part2.md          Part II  (§8–§19)
    ├── rfl-ae-skills-review-part3.md          Part III (§20–§33)
    ├── rfl-ae-skills-review-part4.md          Part IV  (§34–§47)
    ├── rfl-ae-skills-review-part5.md          Part V   (§48–§63)
    ├── rfl-ae-skills-review-part6.md          Part VI  (§64–§79)
    ├── rfl-ae-skills-review-part7.md          Part VII (§80–§96)
    ├── rfl-ae-skills-review-part8.md          Part VIII (§97–§112)
    ├── rfl-ae-prompt-instruction-packs.md     Part IX  (design proposal)
    ├── rfl-ae-prompt-packs-part10.md          Part X   (design proposal)
    ├── rfl-ae-prompt-packs-part11.md          Part XI  (normative spec, gate unmet)
    ├── rfl-ae-master-agent-instructions-v0.1.md   the 28-section operating contract
    ├── rfl-ae-prompt-packs-part12.md          Part XII (analysis of the above)
    ├── rfl-ae-master-prompt-instructions-v0.2.md  the 40-section revision
    ├── rfl-ae-prompt-packs-part13.md          Part XIII (v0.1 → v0.2 diff)
    ├── rfl-ae-operational-agent-protocol-v0.3.md  §41–§70, extends v0.2
    ├── rfl-ae-prompt-packs-part14.md          Part XIV (v0.3 analysis)
    ├── rfl-ae-prompt-pack-specification-v0.4.md   §71–§105, implementable
    ├── rfl-ae-prompt-packs-part15.md          Part XV (v0.4, blocking analysis)
    ├── rfl-ae-prompt-instructions-v0.5.md     §106–§149, runtime contract
    ├── rfl-ae-prompt-packs-part16.md          Part XVI (v0.5, closures + matrix)
    ├── rfl-ae-protocol-schemas-v0.6.md        §150–§199, typed object schemas
    ├── rfl-ae-prompt-packs-part17.md          Part XVII (v0.6, closures + regression)
    ├── rfl-ae-executable-protocol-types-v0.7.md   §201–§271, executable types
    ├── rfl-ae-prompt-packs-part18.md          Part XVIII (v0.7, closures + dropped closure)
    ├── rfl-ae-reference-implementation-blueprint-v0.8.md  §272–§361, kernel blueprint
    ├── rfl-ae-prompt-packs-part19.md          Part XIX (v0.8, closures + contradiction)
    ├── rfl-ae-executable-protocol-kernel-v0.9.md  §362–§455, implementation contract
    ├── rfl-ae-prompt-packs-part20.md          Part XX (v0.9, closures + undeclared types)
    ├── rfl-ae-conformance-evidence-release-v1.0.md  §456–§555, conformance + release
    ├── rfl-ae-prompt-packs-part21.md          Part XXI (v1.0, two carried items move)
    ├── rfl-ae-fixture-corpus-conformance-manifest-v1.1.md  §556–§655, fixture corpus
    ├── rfl-ae-prompt-packs-part22.md          Part XXII (v1.1, coverage derived)
    ├── rfl-ae-evidence-execution-record-v1.2.md  §656–§800, evidence + execution records
    ├── rfl-ae-prompt-packs-part23.md          Part XXIII (v1.2, scope relation inverted)
    ├── rfl-ae-coverage-evaluation-conformance-aggregation-v1.3.md  §801–§997, coverage + aggregation
    ├── rfl-ae-prompt-packs-part24.md          Part XXIV (v1.3, derivation closed, domains open)
    ├── rfl-ae-release-gate-manifest-v1.4.md   §998–§1200, release gate + manifest
    ├── rfl-ae-prompt-packs-part25.md          Part XXV (v1.4, scope restored, supersession ledger)
    └── rfl-ae-runall-receipt.log              stage-by-stage execution receipt
```

## Start here

**[`audit/rfl-ae-skills-audit-consolidated.md`](audit/rfl-ae-skills-audit-consolidated.md)** — the reference document. Findings are organised by component and severity rather than discovery order; the per-pass files are the chronological record it de-duplicates.

**57 findings: 7 critical, 12 high, 29 medium, 10 positive.** That count is current **through Part XX**; Parts XXI–XXIII and the v1.1/v1.2 documents are not yet folded into the consolidated file, which is the one stale artifact in `audit/`. For those ranges the per-pass files are authoritative.

## Method

Findings were established by **executing** the audited code, not by reading it. The snapshot in `vendor/rfl-ae/` is the revision audited, imported byte-exact and verified via git content addressing (`100 files, 0 mismatches`). See [`vendor/PROVENANCE-rfl-ae.md`](vendor/PROVENANCE-rfl-ae.md).

Every finding classified `PROVED` names the code path or command that establishes it, and can be re-run against the snapshot.

### Quality of evidence, per section

| Document | Category | Basis |
|---|---|---|
| Parts I–VIII | **Audit** | Executed against the snapshot; classifications are empirical |
| Consolidated | **Audit** | De-duplicated from Parts I–VIII |
| Parts IX–X | **Design proposal** | No empirical claims; every section is `PROPOSED`, not `PROVED` |
| Part XI | **Normative specification** | Binding requirements, **not yet implemented**; its §170 release gate is unmet |
| Master Agent Instructions | **Normative** | 28-section operating contract |
| Part XII | **Analysis** | Receipt mapping + self-consistency of the above |
| Master Prompt Instructions v0.2 | **Normative** | 40-section revision; supersedes v0.1 |
| Part XIII | **Analysis** | v0.1 → v0.2 diff; finding disposition |
| Operational Agent Protocol v0.3 | **Normative** | §41–§70; **extends** v0.2, not self-contained |
| Part XIV | **Analysis** | v0.3 analysis; additive-revision findings |
| Prompt Pack Specification v0.4 | **Normative** | §71–§105; executable-pack schemas |
| Part XV | **Analysis** | v0.4; one **blocking** defect + gate reconciliation |
| Runtime & Execution Contract v0.5 | **Normative** | §106–§149; the enforcement component |
| Part XVI | **Analysis** | v0.5; two findings closed, §145 matrix measured |
| Protocol Schemas v0.6 | **Normative** | §150–§199; sixteen typed object schemas |
| Part XVII | **Analysis** | v0.6; three closures, one regression |
| Executable Protocol Types v0.7 | **Normative** | §201–§271; JSON/Rust/TypeScript types |
| Part XVIII | **Analysis** | v0.7; four closures, one dropped closure |
| Reference Implementation Blueprint v0.8 | **Normative** | §272–§361; kernel, engines, conformance |
| Part XIX | **Analysis** | v0.8; strongest closure set, one contradiction |
| Executable Protocol Kernel v0.9 | **Normative** | §362–§455; types, APIs, engines, release gate |
| Part XX | **Analysis** | v0.9; largest closure set, three undeclared types |
| Conformance, Evidence & Release v1.0 | **Normative** | §456–§555; fixtures, CI, freeze, release gate |
| Part XXI | **Analysis** | v1.0; two long-carried items move |
| Fixture Corpus & Conformance Manifest v1.1 | **Normative** | §556–§655; manifest, corpus, comparison |
| Part XXII | **Analysis** | v1.1; coverage derived by example, one class dropped |
| Evidence & Execution Record v1.2 | **Normative** | §656–§800; identity, status lattice, trust, evidence closure |
| Part XXIII | **Analysis** | v1.2; §749's scope relation inverted, `Covered` undefined |
| Coverage & Conformance Aggregation v1.3 | **Normative** | §801–§997; coverage derivation, policy, claim |
| Part XXIV | **Analysis** | v1.3; derivation closed by rule, five domains left unenumerated |
| Release Gate & Manifest v1.4 | **Normative** | §998–§1200; gate, freeze, manifest, bootstrap |
| Part XXV | **Analysis** | v1.4; scope relation restored, supersession ledger introduced |

**The specification lineage (§0–§1200)** spans **fourteen** supplied documents, thirteen of which form a single numbered progression (v0.2 §0–§40 → v1.4 §998–§1200, one gap at §200). Analysed in Parts XII–XXV.

**Supersession ledger (Part XXV §11).** Because the documents are additive, a defect is never edited out — a later document either **closes it correctively**, **supersedes it in substance** (the earlier text is still wrong but unreachable by a conforming implementation), or leaves it **open**. Findings must be counted per bucket, not per list. Worked example: v1.2 §749's inverted `CLAIMED_SCOPE ⊇ EXECUTED_SCOPE` is now **superseded in substance** by v1.4 §1071 + §1200, and still wrong in text.

> **Four different 997s — do not conflate them.** The union's highest section number is now **§997**. The audited repository's own *"997 sections"* claim (the closed cosmetic finding, Parts II–VIII) is **unrelated** to it, as are v1.2 §788's `997 / 1000 passed` counter-example and v1.3 §924's `997` required fixtures. The first is a closed repository matter; the other three are the specification's own illustrative numbers.

> **Citation rule — §1–§28 is occupied twice.** [`v0.1`](audit/rfl-ae-master-agent-instructions-v0.1.md) is §1–§28 and [`v0.2`](audit/rfl-ae-master-prompt-instructions-v0.2.md) is §0–§40. They share **28 section numbers and zero identical headings** (v0.1 §4 = *AUTHORITY*; v0.2 §2 = *AUTHORITY*). **Any citation from §1 to §28 MUST name its document.** §29–§1200 is unambiguous. See [Part XXI](audit/rfl-ae-prompt-packs-part21.md) §7.

Part XV §2 recorded the defect that stopped work rather than permitting bad work: v0.4 §86 defines the digest over `CanonicalCompiledPack` while §88 places `compiled_digest` inside that artifact. **v1.0 §541 partially closes it** — evidence binds both pack digests, placing the digest's binding locus outside the artifact it identifies. No exclusion rule was stated, so the remedy is positional rather than semantic. See [Part XXI](audit/rfl-ae-prompt-packs-part21.md) §4.

**Recording convention (declared).** All fourteen verbatim documents in `audit/` carry a horizontal rule `---` before each section heading, and use fenced code blocks with language tags in place of the source's inline single-backtick wrapping. **Neither is part of the supplied sources** — they are presentation additions, one separator per section. No wording, number, identifier, or ordering is altered. See [Part XIX](audit/rfl-ae-prompt-packs-part19.md) §11.3 for how this was found and why it is declared rather than removed.

**Header counts are positional, and this is declared here so it is not read as an undercount.** Each recorded document's provenance header states the size of the lineage **at the moment that document was recorded** — so v0.8's header says *eight*, v0.9's *nine*, v1.0's *ten*, v1.1's *eleven*, v1.2's *twelve*, v1.3's *thirteen*, v1.4's *fourteen*. Those are historical statements, not claims about the corpus today; this README is the only file that states the **current** size (§0–§1200 across fourteen documents). The same applies to the phrase *"all N recorded documents"* in each header. Earlier versions of this corpus carried a genuine undercount — the headers said seven/eight/nine while the lineage was already eight/nine/ten — and that defect is closed by this convention, not by repeatedly re-writing fourteen headers.

Parts IX–XII carry a header noting their category, because a proposal, a specification, or an analysis must not inherit the audit's authority. Part XI's §170.1 records the one thing in it that *is* verifiable: six of its fifteen release-gate conditions are evaluable against the existing toolchain, and it fails all six.

**Part XII's principal result:** the operating contract and the current toolchain are **mutually inconsistent.** Ten sections of the contract have direct, executed violations in `vendor/rfl-ae/`. An agent obeying §9 and §27 must downgrade `run_all.sh`'s clean exit-0 to `PARTIAL`, because §8's scope relation cannot be established anywhere in the toolchain. Reconciling this is what `PHASE 0` exists for.

## The four critical findings

| ID | Finding | Receipt |
|---|---|---|
| **C-1** | `renumber.py` fabricates source provenance, and the auditor certifies it | Gapped source `§1,§3,§7` → `101,102,103` with invented comments `§2`,`§3`; `auditlib` reports `mapping errors=0` |
| **C-2** | `renumber.py` is fence-blind and corrupts fenced code bodies | A `## 999.` line inside a ```` ```text ```` block was rewritten, and a provenance comment injected *inside* the fence |
| **C-3** | The provenance invariant is non-monotonic; 10 of 33 documents sit outside it | Deleting 29 of 30 comments → FAIL; deleting **all 30** → PASS |
| **C-7** | Negative-test sensitivity is environment-dependent and the loss is undetected | Same commit, same fixture: `6 findings` without `markdown`, `8` with it — both reported `PASS` |

C-7's two missed defects are the render-dependent ones, including the check for the incident that created the strict-mode rule (*"37 placeholders were spliced outside their fences"*). Removing that check entirely would be undetectable in a degraded environment.

## Ranked priority (amended)

```text
P0 — correctness defects
  1. renumber.py preserves captured source numbers        (C-1)
  2. renumber.py becomes fence-aware                      (C-2)
  3. neg() asserts expected findings, not rc != 0         (C-7)
  4. remove has_prov; require provenance per document class (C-3)
  5. negative-test fixtures use mktemp -d                 (must precede 3)
  6. run_all.sh uses $PY consistently
  7. check_fences becomes a fence parser, not a parity   (new: high)
     test — it reports balanced on an unclosed fence and
     unbalanced on a valid quoted fence
```

Items 1 and 2 are hours of work; each defect is live. Item 5 must precede item 3 or the test becomes nondeterministic.

## Verification status of the audited revision

The pipeline reproduces cleanly at `1090511` — all 7 stages, exit 0 — and every specific number asserted in the upstream commit message was independently reproduced:

```text
29/29 provenance, offset 969   |  178 fences  |  430/430 probes
50 files, 851 local links, 0 broken  |  4/4 negative tests  |  exit 0
```

The audited toolchain is substantially real engineering with unusually good failure-driven invariants. The findings above concern its **verification layer**, not its functionality. See the consolidated document's §5, *What is verified correct*.

## License

The vendored snapshot has **no upstream licence file** (`gh api .../license` → 404); absence of a licence is not a grant of permission. Both repositories are owned by the same account, but the status should be resolved explicitly before this snapshot is relied upon outside that boundary. Recorded factually in [`vendor/PROVENANCE-rfl-ae.md`](vendor/PROVENANCE-rfl-ae.md), not as a legal determination.
