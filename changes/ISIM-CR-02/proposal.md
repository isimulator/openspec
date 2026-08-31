# ISIM-CR-02: Executable iTwin Model

**Status:** Proposed
**Target:** itwin-spec, examples
**Scope:** Machine-readable iTwin document model, execution-model boundary, conformance, golden-path artifact
**Predecessor:** ISIM-CR-01 (Interactive Semantic Simulation Architecture)
**Next:** ISIM-CR-03 (Simulation Execution Model)
**Depends on:** ADR-0001 (Accepted: JSON + JSON Schema + Pydantic)

---

## 1. Change Summary

Define the machine-readable model that makes an iTwin loadable: the element types, their required and optional attributes, the relationships between them, the boundary between the semantic representation and its executable form, and the conformance levels a document can claim.

This CR produces the first concrete artifact: the **OTCHERE Order-to-Cash / Fulfillment executable iTwin** in the examples repository.

## 2. Element Contracts

An iTwin document SHALL contain a header and a body.

### 2.1 Header

| Field | Cardinality | Content |
|-------|-------------|---------|
| `spec_version` | 1 | itwin-spec version the document claims conformance to |
| `itwin.id` | 1 | Stable semantic identifier |
| `itwin.name` | 1 | Human-readable name |
| `itwin.entity` | 1 | Reference to the twinned Entity (identity + semantics); an iTwin cannot exist without it |
| `itwin.version` | 1 | Semantic version of this iTwin document |
| `itwin.provenance` | 0..1 | Authorship, source models, timestamps |

### 2.2 Body Element Types

| Element | Role | Key attributes |
|---------|------|----------------|
| Entity | The twinned thing and contained sub-entities | id, concept, identity attributes |
| Capability / Function / Service | What the entity can do | id, name, inputs, outputs |
| Process | Ordered operational behavior | id, activities, sequence/flow, performers |
| Activity | Single unit of work within a process | id, duration model, resource requirements, failure rate |
| Resource | Capacity consumed by activities | id, capacity, availability calendar, cost |
| Event | Something that happens at a time | id, trigger, effects |
| State | Named conditions of the twin | id, variables, initial value |
| Metric | Observable quantity | id, unit, source (model/observed/simulated/forecast) |
| Policy / Rule / Constraint | Governance of behavior | id, condition, effect, scope |
| Control | Manipulable variable | id, semantic target, range, unit, default, validation rule |
| Scenario | Counterfactual configuration | baseline ref, parameter overrides, horizon, random seed |
| Observation | Epistemic record | id, kind (model/observed/simulated/forecast/predicted), value, timestamp, provenance |

Every element carries `id` and optional `concept` (WSF/OpenDEAM concept name) per ADR-0001.

### 2.3 Relationships

Typed relationships connect elements: `performs`, `consumes`, `produces`, `governs`, `triggers`, `constrains`, `observes`, `overrides`, `contains`. Relationship types are extensible; unknown types MUST NOT invalidate a document (forward compatibility), but standard types SHOULD be used where they fit.

## 3. Execution Model Boundary

The iTwin document is the semantic representation. The execution model is derived, never authored:

```
iTwin document (authored, versioned)
        |
        v  compile step (isimulator-core, ISIM-CR-03)
Execution context: state variables, event queue, transitions,
activities, queues, resources, controllers, observers
```

The compile step SHALL NOT change the semantic identity of the iTwin (spec.md: Entity-Centric iTwin Foundation). Execution artifacts are reproducible from iTwin version + scenario version + engine version + seed.

## 4. Serialization and Validation (per ADR-0001)

1. Documents are JSON, validated against the published JSON Schema for the claimed `spec_version`.
2. Pydantic models in isimulator-core are verified against the same schema by a conformance test.
3. A JSON-LD context MAY be published so documents are also valid JSON-LD; additive only.

## 5. Conformance Levels

| Level | Requirement |
|-------|-------------|
| L1 Loadable | Header valid; entity anchor present; all `id` unique; schema-valid |
| L2 Behavioral | At least one Process with Activities; durations and resources expressed |
| L3 Controllable | At least one Control with range and validation; at least one Scenario |
| L4 Observable | Metrics declared with epistemic kinds; at least one Observation record |

The golden-path artifact targets L4.

## 6. Golden-Path Artifact

`examples/otchere-order-to-cash/` SHALL contain:

1. `twin.json`: the OTCHERE Order-to-Cash / Fulfillment iTwin (Order -> Validation -> Credit -> Inventory -> Fulfillment -> Shipment -> Invoice -> Payment; each stage with Resource, Capacity, Processing Time, Queue, Policy, Rule, Failure Rate, SLA, Cost).
2. `scenarios/baseline.json` and at least one what-if scenario (demand +20%).
3. `README.md`: persona-readable explanation of the model, machine-checkable against the schema.

## 7. Acceptance Criteria

- [ ] JSON Schema for itwin-spec v0.1.0 published in itwin-spec, covering all element contracts in section 2
- [ ] Schema examples validate in CI (JSON Schema and Pydantic paths)
- [ ] Execution-model boundary documented; compile step contract stated for ISIM-CR-03
- [ ] OTCHERE golden-path iTwin at conformance L4 in examples
- [ ] openspec delta for specs/itwin-spec merged
- [ ] No implementation in isimulator-core beyond what the boundary contract requires

## 8. Non-Goals

- Simulation execution semantics (ISIM-CR-03)
- Scenario/experiment engine behavior (ISIM-CR-04)
- Runtime API surface (ISIM-CR-05)
- JSON-LD context publication (wsf-alignment workstream)
