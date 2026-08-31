# iSimulator: openspec

OpenSpec-style requirements and change tracking for the iSimulator platform and the iTwin concept.

**Version:** 0.1.0
**Status:** Draft
**Last Updated:** 2026-08-31

---

## Layout

```
openspec/
├── project.md              # Project conventions for specifications
├── specs/                  # Current normative requirements
│   └── isimulator/
│       └── spec.md         # iSimulator requirements v0.1.0
└── changes/                # Proposed changes as OpenSpec deltas
```

## Conventions

- Requirements use **Requirement + Scenario** format (GIVEN / WHEN / THEN).
- Changes are expressed as deltas: `## ADDED Requirements`, `## MODIFIED Requirements`, `## REMOVED Requirements`.
- Every change proposal lives in `changes/<change-id>/` with a `proposal.md`, optional `design.md`, and delta files mirroring the spec layout.
- Specification versions are semantic; a change proposal states its target version bump.
