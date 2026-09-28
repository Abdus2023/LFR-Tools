# Provenance — `vendor/rfl-ae/`

**Artifact:** vendored snapshot of the RFL-AE specification corpus and skills toolchain
**Imported:** 2026-09-28
**Imported by:** Arena agent session on branch `arena/01a0e775-lfr-tools`

> **Placement note.** This file sits at `vendor/PROVENANCE-rfl-ae.md` — **outside** the snapshot directory — so that `vendor/rfl-ae/` remains a byte-exact representation of the upstream tree at the pinned revision. The recommended layout sketched during import showed `PROVENANCE.md` inside `vendor/rfl-ae/`; the stated principle ("it should not become part of the upstream snapshot itself") takes precedence over that sketch, since a file inside the directory would make the snapshot non-exact by construction.

---

## Source identity

| Field | Value |
|---|---|
| Upstream repository | `https://github.com/Abdus2023/RFL-AE` |
| Branch | `arena/01a0e252-rfl-ae` |
| Pinned revision (short) | `1090511` |
| **Pinned revision (full)** | `1090511a4080987168d1b17d48de88137ec1c27a` |
| Commit tree SHA | `d28b96f9c8866fd0001367bbb9f8eb4fbc56b5a7` |
| Import method | `git archive --format=tar HEAD` from a fresh clone at the pinned revision |
| Import scope | **entire repository** (all tracked files) |
| Files imported | 100 |
| Size on disk | 1.7 MB |
| `.git` directory | **not** included (snapshot is plain content) |

### Why the whole repository

The import boundary is the entire repository, not the `skills/` subtree, because the audit's receipts reference root-corpus artifacts. `skills/run_all.sh` consumes the 33 root corpus documents and `README.md`; `verify_closeout.py` derives corpus counts from them. Importing only `skills/` would produce a structurally incomplete evidence set — the recorded procedures could not be re-run against the snapshot.

A future `skills/`-only or `skills/`-plus-corpus import may be useful as a **derived audit fixture**, but it should not replace this canonical artifact.

---

## Integrity verification

Verified at import time by `git hash-object`, comparing each extracted file against the upstream tree's own content-addressed blob entries:

```text
entries:    100
mismatches: 0
modes:      100644 × 99, 100755 × 1   (the executable is skills/run_all.sh)
```

**Every file's git blob hash matches its `git ls-tree -r HEAD` entry.** This is not a size or count comparison — it is git's own content addressing, so a single-byte difference in any file would have produced a mismatch.

### Recorded digests

| Digest | Value |
|---|---|
| Commit tree SHA | `d28b96f9c8866fd0001367bbb9f8eb4fbc56b5a7` |
| Manifest SHA-256 | `608387d491d9630460bc9f40d146191091be3cc7a17b09f0c3e0c2df27c8f4d2` |

The manifest digest is `sha256` over the newline-joined `git ls-tree -r HEAD` output (`<mode> <type> <sha>\t<path>` per line, sorted by git), with a trailing newline.

### Re-verification

To confirm the snapshot has not been modified since import:

```bash
# If the upstream clone is available:
git -C <clone-at-1090511> ls-tree -r HEAD > /tmp/expected.txt
# Recompute and compare against the recorded manifest SHA-256.
```

Or, without network access, confirm no vendored file has changed since the import commit:

```bash
git log --oneline -- vendor/rfl-ae/
# Expect exactly one commit: the import itself.
```

---

## Licence status

**⚠️ No licence file is present in the upstream repository.**

Checked two ways at import time:

```text
$ ls /tmp/rflae | grep -i "licen\|copying\|notice"
(no matches)

$ gh api repos/Abdus2023/RFL-AE/license --jq '.license.spdx_id'
404 Not Found
```

**Consequence:** in the absence of a licence, the default position is *all rights reserved* by the copyright holder. The absence of a `LICENSE` file is not a grant of permission. This snapshot is therefore imported as an **audit input**, and its presence in this repository should not be read as establishing redistribution rights.

**Practical context:** both `Abdus2023/RFL-AE` (upstream) and `Abdus2023/LFR-Tools` (this repository) are owned by the same account, so the import is within a single ownership boundary. That resolves the question in practice for this repository, but it should be resolved *explicitly* — by adding a licence file upstream, or by recording the owner's assertion — before this snapshot is relied upon by any party outside that boundary.

Recorded as a fact about the artifact, not as a legal determination.

---

## Immutability policy

**This snapshot is immutable audit input.**

- It is **not** to be silently synchronised with upstream.
- A new upstream revision requires a **new provenance/import event** — a new directory, a new provenance file, or both — not an edit to this one.
- Fixes to defects identified by the audit (see `audit/`) are to be applied **upstream**, and arrive here as a new import, never as local edits to `vendor/rfl-ae/`.
- Any local modification would invalidate the digests above and break the reproducibility guarantee the snapshot exists to provide.

The snapshot represents `Abdus2023/RFL-AE` at `1090511a4080987168d1b17d48de88137ec1c27a`. It is a historical record of what was audited, not a working copy.

---

## Relationship to the audit

The audit in `audit/` was performed against this exact revision. Importing the source makes its receipts independently checkable:

| Audit artifact | Uses the snapshot |
|---|---|
| `audit/rfl-ae-skills-audit-consolidated.md` | Consolidated findings and priority list |
| `audit/rfl-ae-skills-review*.md` | Per-pass findings, Parts I–VIII |
| `audit/rfl-ae-runall-receipt.log` | Stage-by-stage execution receipt at this revision |
| `audit/rfl-ae-prompt-instruction-packs.md`, `...part10.md` | Design proposals (Parts IX–X) |

Every claim classified `PROVED` in the audit names a code path or command that can be re-executed against `vendor/rfl-ae/`. The receipt log records the one full pipeline run this repository holds.

**Known gap in the recorded receipt:** the committed `rfl-ae-runall-receipt.log` records the run *with* `markdown` installed (stage 7 reported `8 findings`). An earlier run *without* `markdown` produced `6 findings` at the same stage and is **not** committed; it is reconstructed in Part VIII §0.1. A complete evidence set would retain both environments' output. This is recorded here rather than corrected, because reconstructing it would mean editing a receipt.

---

## Snapshot contents

```text
vendor/rfl-ae/
├── .gitignore
├── README.md                    corpus index + status line
├── ARCHITECTURE.md   …         33 root specification documents, corpus §1–§998
│                                (deliberate gap at §108)
├── audit/                      8 themed review documents (upstream's own audit)
├── diagrams/                   generator scripts for the corpus diagrams
└── skills/                     the audited toolchain
    ├── README.md
    ├── run_all.sh              7-stage release gate (the sole executable file)
    ├── ascii-diagram-forge/
    ├── corpus-provenance-numbering/
    ├── markdown-corpus-audit/  incl. probes/ (20 files) and scripts/
    ├── placeholder-splice/
    ├── skill-creator/
    └── spec-turn-closeout/
```

Top-level file counts: 34 root `.md` (33 corpus documents plus `README.md`), 100 files total.
