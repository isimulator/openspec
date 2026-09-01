# Post-Hoc Reviews: ISIM-CR-02 Direct-to-Main Commits

**Date:** 2026-08-31
**Scope:** ISIM-CR-02 (Reusable Simulation Framework)
**Repos:** `isimulator/openspec`, `isimulator/itwin-spec`, `isimulator/docs`, `isimulator/examples`

---

## Why this document exists

ISIM-CR-02 was implemented via four direct-to-`main` commits, not via the standard pull-request workflow. This document records that fact explicitly and explains the reasoning, so the audit trail is complete even though no GitHub PRs were opened.

## The four commits

| Repo | Commit SHA | Date | Headline |
|------|-----------|------|----------|
| `isimulator/openspec` | `ac1a0d3` | 2026-08-31 17:00 SGT | replace ISIM-CR-02 with authoritative Reusable Simulation Framework + reconciliation + framework delta |
| `isimulator/docs` | `42680ac` | 2026-08-31 17:01 SGT | ADR-0002 integration policy; ARCHITECTURE/PLAN v0.3.0 per authoritative ISIM-CR-02 |
| `isimulator/itwin-spec` | `57e4d6f` | 2026-08-31 17:30 SGT | ISIM-CR-02 framework assets: primitive library, sim spec schema, contracts, dual validator |
| `isimulator/examples` | `41d757b` | 2026-08-31 17:30 SGT | OTCHERE order-to-cash golden-path model (ISIM-CR-02 acceptance artifact) |

## Why direct-to-main was used (and is not the standing rule)

The iSimulator GitHub organization was created on the same day these commits landed. At that moment:

- The org had zero repositories with PR history.
- The org had zero external contributors; the sole committer and reviewer was the change author.
- The CR itself (ISIM-CR-02) was authored by the same person performing the implementation; there was no second pair of eyes to act as a reviewer.

Under those conditions, opening a PR and self-merging it would have created audit noise (a PR body the author wrote, "approved" by the author) without adding review value. Direct-to-main was a pragmatic seed-time choice.

**This is not the standing rule.** From ISIM-CR-03 onward, every Change Request MUST be implemented via a pull request:

- Branch from `main`: `feature/isim-cr-<NN>-<slug>`.
- One CR per PR (or one tightly coupled PR set with explicit cross-links).
- PR body MUST map the change to the CR's required assets (e.g. for ISIM-CR-02 §26 A-K, each PR would have quoted its mapped sub-sections).
- Merge only on explicit `merge` from the change owner.

The `openspec/change-requests/STATUS.md` table is the new tracker that enforces this expectation from CR-03 forward.

## Per-commit review notes (as if read at PR time)

### `isimulator/openspec` @ `ac1a0d3`

- **What it changed:** `changes/ISIM-CR-02/{proposal.md, reconciliation.md, delta-isimulator-framework.md, delta-itwin-spec.md}` and `CHANGELOG.md` (0.3.1).
- **Verdict (post-hoc):** Authoritative CR accepted; reconciliation note recorded; normative requirements (R01-R12) defined against a new `specs/isimulator-framework/spec.md`. **Approve.**
- **Audit hook:** `changes/ISIM-CR-02/reconciliation.md` captures the supersession of the earlier Executable iTwin Model draft. No information loss.

### `isimulator/docs` @ `42680ac`

- **What it changed:** `ARCHITECTURE.md` (v0.2.0 -> v0.3.0), `PLAN.md`, `adr/0002-technology-integration-policy.md` (new), `adr/README.md`, `CHANGELOG.md`.
- **Verdict (post-hoc):** ADR-0002 codifies the "adopt before build" principle as authoritative architecture policy. ARCHITECTURE/PLAN updated to match CR-02's integration-first positioning. **Approve.**

### `isimulator/itwin-spec` @ `57e4d6f`

- **What it changed:** Added `primitives/library.json` (34 primitives, 5 categories), `schema/itwin-sim-0.1.0.json` (JSON Schema 2020-12), `composition.md`, `contracts/{engine-adapter,visualization-provider}.md`, `tools/validate.py` (dual-path JSON Schema + Pydantic), `examples/minimal.json`, `.github/workflows/validate.yml`, `CHANGELOG.md` (0.2.0).
- **Verdict (post-hoc):** Covers CR-02 §26 assets A, B, C, D, E, F, G, H, K. Dual-path validator (ADR-0001) implements the contract conformance. CI workflow added in the same commit. **Approve.**

### `isimulator/examples` @ `41d757b`

- **What it changed:** `otchere-order-to-cash/simulation.json` (8-stage process, 7 resources, 3 controls, 6 metrics, 3 scenarios), `otchere-order-to-cash/README.md`, `CHANGELOG.md` (0.2.0).
- **Verdict (post-hoc):** Implements CR-02 §26 asset J (golden-path example) and §24 reference acceptance scenario. Validates against the itwin-spec schema via `tools/validate.py`. **Approve.**

## Drift items observed during review

These were true at the moment of direct-to-main landing and are recorded here so future readers can resolve them:

1. **`openspec/CHANGELOG.md` 0.3.0** entry still uses the superseded "Executable iTwin Model" wording. The 0.3.1 entry points readers to the authoritative version. **Not blocking; reader sees both.**
2. **No GitHub PRs** for any of the four commits. Resolved by this document.
3. **No STATUS table** in `openspec/` at the time of landing. Resolved by the new `change-requests/STATUS.md` (added in the same governance pass that introduced this document).

## Going forward

ISIM-CR-03 (Reference Engine Integration) will be the first CR to use the PR workflow end-to-end:

- Branch: `feature/isim-cr-03-simpy-reference`
- PR body: per-repo asset mapping, quoted CR sections
- Status row added to `openspec/change-requests/STATUS.md` at PR-open time
- Project board row linked to the PR
- Merge on explicit `merge` instruction only
