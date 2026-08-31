# ISIM-CR-02: Reconciliation Note

**Date:** 2026-08-31
**Status:** Reconciled

## What happened

Two drafts of ISIM-CR-02 existed: an agent draft (Executable iTwin Model: element contracts, conformance levels, serialization detail) and the authoritative user CR (Reusable Simulation Framework: integration-first positioning, primitive library, adapter contracts, progressive complexity). The user CR is authoritative. This note records how the agent draft's content folds into it.

## Carried forward from the agent draft

| Agent draft content | Lands under |
|---------------------|-------------|
| Header contract and typed element contracts | R01 Enterprise Simulation Primitives (schema detail for the Primitive Library, Asset 1) |
| Typed relationships and forward compatibility | R02 Simulation Composition (composition semantics, Asset 2) |
| Serialization: JSON + JSON Schema + Pydantic per ADR-0001 | R03 Declarative Simulation Specification (Asset 4) |
| Conformance levels L1-L4 | R08 Simulation Validation + R11 Progressive Complexity (levels map to the Level 0-9 progression) |
| Execution-model boundary (compile derives, never mutates) | R04/R06 Engine Adapter Contract and External Engine Integration |
| OTCHERE golden-path artifact | Acceptance test, section 22 of the authoritative CR |

## What the authoritative CR changes

1. Positioning: iSimulator is an integration and semantic orchestration framework, not a new engine platform.
2. "Adopt before build" becomes a first-class architecture rule with Integration Decision Records.
3. Three-level engine architecture: native enterprise primitives, iSimulator execution abstraction, external engines.
4. SimPy demoted from "the engine" to "candidate reference engine"; engine landscape assessment mandated (SimPy, Mesa, FMI 3, OpenModelica with license care).
5. Roadmap renumbered: CR-03 becomes Reference Engine Integration; CR-06 becomes Simulation Builder / Studio; new CR-09 Visualization Engine Integrations; CR-10 Operational Twin Synchronization; CR-11 Autonomous Simulation Closed Loop.
6. License policy: prefer MIT / Apache-2.0 / BSD; avoid GPL/AGPL and proprietary SDKs as embedded core dependencies.
