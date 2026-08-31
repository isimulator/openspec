# openspec: project.md

**Version:** 0.1.0
**Status:** Draft
**Last Updated:** 2026-08-31

---

Conventions for all specifications in this repository.

## Scope

Normative behavioral requirements for iSimulator and iTwin. Out of scope items are listed per-spec (see `specs/isimulator/spec.md` v0.1.0: simulation algorithms, serialization formats, performance, AAS interoperability, UI wireframes).

## Style

1. Design Specification tone: declarative statements of system behavior; no proposal language ("we should", "we could").
2. No en dashes or em dashes; use colons or semicolons.
3. Terminology per [isimulator/docs glossary](https://github.com/isimulator/docs/blob/main/glossary.md): iTwin, iSimulator, Organization iTwin, Entity.
4. ISO/IEC 30173 is referenced when the broader digital twin concept is introduced.

## Change Workflow

1. Open a change directory under `changes/<change-id>/`.
2. Include `proposal.md` (why, what, version bump) and delta files (`## ADDED / ## MODIFIED / ## REMOVED`).
3. On acceptance, deltas merge into `specs/` and the change directory is archived under `changes/archive/`.
