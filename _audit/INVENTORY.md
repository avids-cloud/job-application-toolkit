# Inventory

Phase 1 of the packaging mission. Every file in the repo, classified, with rationale and issues. No files moved or deleted yet.

**Repo root:** `/Users/avinashdesilva/Desktop/Claude/job-application-toolkit`
**File count:** 62 files (excluding `.git`)
**Date:** 2026-05-10

## Classification key

- **essential** — core to the end-to-end workflow, kept and promoted
- **supporting** — docs, examples, history, kept as reference
- **redundant** — parallel duplicate, consolidate in Phase 3
- **orphaned** — references something that no longer matches (e.g. broken schema)
- **cruft** — abandoned, PII, or otherwise to be removed

## Top-level documentation

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| README.md | 8,819 | redundant | Root version of the front door. Diverges from github-release version. | Larger than github-release counterpart by 487B; differs (diff --brief). To be diff-merged in Phase 3, then rewritten in Phase 6 for <200 word above-the-fold rule. |
| USAGE.md | 13,663 | redundant | Root walkthrough. Diverges from github-release version. | Larger than github-release counterpart by 2,236B; covers Python render path which github-release version omits. Diff-merge in Phase 3. |
| DESIGN.md | 18,296 | supporting | Architectural rationale; only at root. | Documents the root-vs-github-release split and known issues. Keep, lightly polish. |
| github-release/README.md | 8,332 | redundant | Public-facing front door. Cleaner narrative ("Get started in three steps") and structured directory references. | Differs from root README. To be diff-merged in Phase 3. |
| github-release/USAGE.md | 11,427 | redundant | Public-facing walkthrough, HTML-artifact path only. | Differs from root USAGE. Diff-merge in Phase 3. |
| github-release/CONTRIBUTING.md | 8,266 | essential | Contribution guidelines, only exists in github-release. | None. Promote to root in Phase 3. |

## Onboarding prompts (parallel pair)

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| onboard-executive.md | 29,747 | redundant | Senior-leader interview prompt, root version. | Larger than github-release counterpart by 4,815B. Differs. Diff-merge required. |
| onboard-midcareer.md | 22,074 | redundant | 5-15 year interview prompt, root version. | 3,167B larger than github-release counterpart. Differs. Diff-merge required. |
| onboard-grad.md | 21,500 | redundant | Early-career interview prompt, root version. | 1,774B larger than github-release counterpart. Differs. Diff-merge required. |
| github-release/onboarding/onboard-executive.md | 24,932 | redundant | Same prompt, packaged version. | Smaller; likely had review-round trims applied. |
| github-release/onboarding/onboard-midcareer.md | 18,907 | redundant | Same prompt, packaged version. | Smaller. |
| github-release/onboarding/onboard-grad.md | 19,726 | redundant | Same prompt, packaged version. | Smaller. |

## Apply chain

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| apply.md | 13,944 | redundant | Root-level apply prompt: fit assessment + selection JSON + voice tripwire. | Differs from github-release version (which is 4,161B larger). Diff-merge required. |
| github-release/apply/apply.md | 18,105 | redundant | Packaged apply prompt. Bigger; likely contains additional schema detail or hard rules. | Differs from root. |
| github-release/apply/fit-assessment.md | 2,948 | essential | Standalone fit-assessment prompt. Root has no equivalent. | None. Promote in Phase 3. |

## Render chain

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| render.py | 31,593 | essential | Deterministic Python `.docx` renderer (840 lines). Only at root. | None. Move to `render/` in Phase 3. |
| render.md | 3,392 | essential | Wrapper prompt that drives Claude to invoke `render.py`. Only at root. | None. Move to `render/` in Phase 3. |
| requirements.txt | 31 | essential | `python-docx==1.1.2`, `PyYAML>=6.0`. Only at root. | None. Move to `render/` in Phase 3. |

## Output artifacts

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| cv-artifact.md | 14,919 | orphaned | Root-level HTML CV renderer. | KI-9: schema mismatch with current `apply.md` selection JSON. Broken. **Delete in Phase 3.** |
| cover-letter-artifact.md | 7,958 | orphaned | Root-level HTML cover-letter renderer. | KI-9: same schema mismatch. Broken. **Delete in Phase 3.** |
| github-release/output/cv-artifact.md | 22,197 | essential | Working HTML CV renderer matching current schema. | None. Promote to `output/` in Phase 3. |
| github-release/output/cover-letter-artifact.md | 8,160 | essential | Working HTML cover-letter renderer. | None. Promote in Phase 3. |

## Examples

| Path | Bytes | Class | Rationale | Issues |
|---|---:|---|---|---|
| github-release/examples/example-profile/profile.md | 12,958 | essential | Anonymised example profile, single canonical reference. | None. Promote in Phase 3. |
| github-release/examples/example-profile/voice.md | 2,829 | essential | Example voice rules. | None. Promote. |
| github-release/examples/example-profile/format.md | 3,520 | essential | Example format spec block. | None. Promote. |
| github-release/examples/example-profile/filter.md | 2,856 | essential | Example role filter. | None. Promote. |
| github-release/examples/example-profile/cover-letter.md | 1,967 | essential | Example cover-letter rules. | None. Promote. |
| github-release/examples/example-selection.json | 15,226 | essential | Example apply-output JSON for renderer testing. | None. Promote. |

## candidate-profile-template/ (PII, to be deleted)

All files in this folder contain real personal data (KI-8). Replaced by `examples/example-profile/` after promotion. Cruft.

| Path | Bytes | Class | Issues |
|---|---:|---|---|
| candidate-profile-template/config.yaml | 1,884 | cruft | Real name, phone, email, LinkedIn. |
| candidate-profile-template/competencies.yaml | 3,772 | cruft | Real career detail. |
| candidate-profile-template/voice.md | 1,542 | cruft | Real voice rules. |
| candidate-profile-template/format.md | 2,773 | cruft | |
| candidate-profile-template/filter.md | 1,614 | cruft | |
| candidate-profile-template/cover-letter.md | 1,870 | cruft | |
| candidate-profile-template/early-career.yaml | 560 | cruft | |
| candidate-profile-template/education.yaml | 864 | cruft | Real education history. |
| candidate-profile-template/awards.yaml | 476 | cruft | |
| candidate-profile-template/volunteering.yaml | 618 | cruft | |
| candidate-profile-template/roles/01-alvarium.yaml | 3,925 | cruft | Real role. |
| candidate-profile-template/roles/02-be-intelligent.yaml | 1,557 | cruft | Real role. |
| candidate-profile-template/roles/03-fresh-direct.yaml | 1,859 | cruft | Real role. |
| candidate-profile-template/roles/04-datacom.yaml | 1,145 | cruft | Real role. |

## _audit/ (kept, history)

| Path | Bytes | Class | Rationale |
|---|---:|---|---|
| _audit/audit.md | 23,635 | supporting | File-by-file usability/consistency scoring from prior review. Useful history. |
| _audit/architecture-review.md | 16,026 | supporting | Canonical schema documentation. Internal reference. |
| _audit/known-issues.md | 6,906 | supporting | Documented issues KI-1 to KI-11 with severity and suggested fixes. Reference for future contributors. |
| _audit/review-round-1.md | 15,363 | supporting | Three-persona review feedback log from packaging effort. |
| _audit/review-round-2.md | 8,386 | supporting | Verification of round-1 fixes. |
| _audit/INVENTORY.md | (this file) | supporting | This Phase 1 audit. |

## .claude/ (system)

| Path | Bytes | Class | Issues |
|---|---:|---|---|
| .claude/settings.local.json | 114 | supporting | Local permission allowlist for `Bash(bd prime *)` and `Read`. Keep. |
| .claude/worktrees/unruffled-brown-7c83bb/.git | 90 | cruft | Worktree pointer. |
| .claude/worktrees/unruffled-brown-7c83bb/.gitignore | 75 | cruft | |
| .claude/worktrees/unruffled-brown-7c83bb/.beads/* (10 files) | ~9,000 total | cruft | Beads issue-tracking integration from prior work. Hooks, config, README. |
| .claude/worktrees/unruffled-brown-7c83bb/.claude/settings.json | 389 | cruft | Worktree-scoped settings. |
| .claude/worktrees/unruffled-brown-7c83bb/.claude/settings.local.json | 114 | cruft | |
| .claude/worktrees/unruffled-brown-7c83bb/AGENTS.md | 2,908 | cruft | Worktree-scoped agent instructions, includes mandatory beads workflow. |
| .claude/worktrees/unruffled-brown-7c83bb/CLAUDE.md | 2,032 | cruft | Worktree-scoped Claude Code instructions. |

The whole `.claude/worktrees/unruffled-brown-7c83bb/` directory looks like a parked worktree from a prior task. **Delete in Phase 3** unless flagged otherwise.

## Parallel-pair divergence summary

Eight parallel pairs all differ in content (verified by `diff --brief`).

| Pair | Root size | github-release size | Likely-canonical | Action |
|---|---:|---:|---|---|
| README.md | 8,819 | 8,332 | github-release (cleaner, structured) | Diff-merge in Phase 3, then rewrite in Phase 6 |
| USAGE.md | 13,663 | 11,427 | merge — root has Python render path the github-release version omits | Diff-merge in Phase 3 |
| apply.md | 13,944 | 18,105 | github-release (more thorough, post-review) | Diff-merge in Phase 3 |
| onboard-executive.md | 29,747 | 24,932 | github-release (post-review trims) | Diff-merge, preserve any unique root content |
| onboard-midcareer.md | 22,074 | 18,907 | github-release | Same |
| onboard-grad.md | 21,500 | 19,726 | github-release | Same |
| cv-artifact.md | 14,919 | 22,197 | github-release (working schema) | Take github-release, delete root |
| cover-letter-artifact.md | 7,958 | 8,160 | github-release (working schema) | Take github-release, delete root |

## Known issues referenced

From `_audit/known-issues.md`:

- **KI-8** PII in `candidate-profile-template/` — addressed by deleting the template (decision confirmed).
- **KI-9** root-level HTML artifacts have schema mismatch — addressed by deleting (decision confirmed). Working artifacts come from github-release.
- KI-1 to KI-7, KI-10, KI-11 are minor and remain documented for future contributors. Out of scope for this packaging mission.

## Issues found during this audit (new)

1. **No LICENSE file at any level.** Root nor github-release has one. Adding MIT in Phase 6 (per plan).
2. **Stale worktree.** `.claude/worktrees/unruffled-brown-7c83bb/` is not referenced by any active workflow. Removing in Phase 3.
3. **No CLAUDE.md / AGENTS.md at repo root.** Only the abandoned worktree has them. Not adding (out of scope per plan, the toolkit stays as plain markdown prompts).
4. **README.md and github-release/README.md describe slightly different products.** Root README walks Python-renderer path; github-release README walks HTML-only path. The merged README in Phase 3 will need to honestly present both rendering paths.

## Net Phase 3 actions implied

**Promote (move root <- github-release contents):**

- `github-release/onboarding/*` → `onboarding/`
- `github-release/apply/*` → `apply/`
- `github-release/output/*` → `output/`
- `github-release/examples/*` → `examples/`
- `github-release/CONTRIBUTING.md` → `CONTRIBUTING.md`

**Diff-merge (resolve parallel pairs into single canonical):**

- README.md, USAGE.md, apply.md, all three onboarding files

**Move (root rearrange):**

- `render.py`, `render.md`, `requirements.txt` → `render/`

**Delete:**

- `cv-artifact.md`, `cover-letter-artifact.md` at root (orphaned, KI-9)
- `apply.md` at root (after merge into `apply/apply.md`)
- All three `onboard-*.md` at root (after merge into `onboarding/`)
- `candidate-profile-template/` (PII, KI-8)
- `github-release/` (after promotion)
- `.claude/worktrees/unruffled-brown-7c83bb/` (abandoned)

**Reference updates required:**

- `apply.md` references to `voice.md`, `filter.md`, `cover-letter.md` paths
- `render.md` references to `render.py`, `requirements.txt`, `candidate-profile/`
- `USAGE.md` step-numbered paths to onboarding, apply, render files
- `README.md` directory listing
- `DESIGN.md` mentions of `github-release/` and `candidate-profile-template/`

Phase 1 ends here. Awaiting go-ahead before drafting the Phase 2 architecture proposal.
