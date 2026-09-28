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
    └── rfl-ae-runall-receipt.log              stage-by-stage execution receipt
```

## Start here

**[`audit/rfl-ae-skills-audit-consolidated.md`](audit/rfl-ae-skills-audit-consolidated.md)** — the reference document. Findings are organised by component and severity rather than discovery order; the per-pass files are the chronological record it de-duplicates.

**57 findings: 7 critical, 12 high, 29 medium, 10 positive.**

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

**The specification lineage (§0–§105)** spans three documents and is analysed in Parts XII–XV. Part XV's §2 records the one defect that stops work rather than permitting bad work: §86 defines the digest over `CanonicalCompiledPack` while §88 places `compiled_digest` inside that artifact. Three of v0.4's fifteen release gates cannot be executed until it is resolved.

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
