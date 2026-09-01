# Change Request Status

This file is the canonical tracker for iSimulator Change Requests (CRs).
The GitHub Project board (orgs/isimulator/projects/1) mirrors this table; the project is a presentation layer, this file is the source of truth.

## Status values

| Status | Meaning |
|--------|---------|
| Proposed | CR authored, not yet picked up for implementation |
| In Progress | Implementation branch(es) open, PRs not yet merged |
| Merged | All PRs for the CR landed on `main`, acceptance criteria met |
| Superseded | Replaced by a later CR; original kept for traceability |
| Rejected | Not adopted; reason recorded in the CR |

## Workflow rules (from 2026-08-31 governance pass)

1. Every CR MUST be implemented via pull requests, not direct-to-`main` commits.
2. Branch naming: `feature/isim-cr-<NN>-<slug>` per repo.
3. One CR per PR set; tightly coupled PRs must cross-link in their bodies.
4. PR body MUST map the change to the CR's required assets / acceptance criteria.
5. Merge ONLY on explicit `merge` instruction from the change owner.
6. Status row in this table flips to `Merged` in the PR that closes out the CR (status flips ride with the last PR's changeset).
7. Project board rows are created at PR-open time and linked to the PR.

## Active CRs

| CR | Title | Status | PRs | Merged Date | Notes |
|----|-------|--------|-----|-------------|-------|
| ISIM-CR-01 | Interactive Semantic Simulation Architecture | Merged | direct-to-main (see `POST-HOC-REVIEWS.md`) | 2026-08-31 | Architecture foundation; predates the PR-workflow rule |
| ISIM-CR-02 | Reusable Simulation Framework | Merged | direct-to-main (see `POST-HOC-REVIEWS.md`); 4 commits across openspec, itwin-spec, docs, examples | 2026-08-31 | First multi-repo tranche; sets the §26 A-K pattern for downstream CRs |
| ISIM-CR-03 | Reference Engine Integration (planned) | Proposed | not yet opened | : | First IDR-driven proof; SimPy as candidate reference DES engine per ADR-0002 |
| ISIM-CR-04 | Scenario Experiment Framework (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-03 |
| ISIM-CR-05 | Simulation Runtime API (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-03, ISIM-CR-04 |
| ISIM-CR-06 | Simulation Builder / Studio (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-05 |
| ISIM-CR-07 | Forecast, Prediction, Optimization (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-05 |
| ISIM-CR-08 | Local AI Simulation Interaction (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-02, ISIM-CR-06 |
| ISIM-CR-09 | Visualization Provider Integrations (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-02, ISIM-CR-05 |
| ISIM-CR-10 | Operational Twin Synchronization (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-05, ISIM-CR-09 |
| ISIM-CR-11 | Autonomous Simulation Closed Loop (planned) | Proposed | not yet opened | : | Depends on ISIM-CR-07, ISIM-CR-10 |

## How to read this table

- The "PRs" column names the workflow used, not the PR numbers. The iSimulator org was created on 2026-08-31 with zero pre-existing PR history, so the first two CRs predate the workflow rule. They are recorded here as direct-to-main with a `POST-HOC-REVIEWS.md` reference for traceability.
- The "Notes" column captures any supersession, scope changes, or inter-CR dependencies.
- Future CRs will add rows at the same time the implementation branch is opened; the row's status field updates as PRs merge.
