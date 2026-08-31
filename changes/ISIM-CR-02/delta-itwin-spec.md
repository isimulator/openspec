# ISIM-CR-02 Delta: specs/itwin-spec/spec.md (new spec)

**Target version:** itwin-spec 0.1.0 (ADDED as new spec)

## ADDED Requirements

### Requirement: iTwin Document Structure

An iTwin SHALL be a JSON document with a header (`spec_version`, `itwin.id`, `itwin.name`, `itwin.entity`, `itwin.version`) and a body of typed elements. The document SHALL validate against the JSON Schema published for its claimed spec version.

#### Scenario: Valid document loads
- **GIVEN** a JSON document claiming itwin-spec 0.1.0 conformance
- **WHEN** it is validated against the published schema
- **THEN** validation succeeds and every element `id` is unique within the document

#### Scenario: Missing entity anchor
- **GIVEN** a JSON document without `itwin.entity`
- **WHEN** it is validated
- **THEN** validation fails with a specific entity-anchor error

### Requirement: Typed Element Contracts

The spec SHALL define element types: Entity, Capability, Function, Service, Process, Activity, Resource, Event, State, Metric, Policy, Rule, Constraint, Control, Scenario, Observation. Every element SHALL carry `id` and MAY carry `concept` naming a WSF or OpenDEAM concept.

#### Scenario: Control element completeness
- **GIVEN** a Control element
- **WHEN** it is authored
- **THEN** it carries semantic target, allowed range, unit, default value, and validation rule

#### Scenario: Observation epistemic kind
- **GIVEN** an Observation element
- **WHEN** it is authored
- **THEN** its kind is one of: model, observed, simulated, forecast, predicted

### Requirement: Typed Relationships

Elements SHALL be connectable by typed relationships (`performs`, `consumes`, `produces`, `governs`, `triggers`, `constrains`, `observes`, `overrides`, `contains`). Unknown relationship types SHALL NOT invalidate a document.

#### Scenario: Forward compatibility
- **GIVEN** a document containing a relationship type introduced in a later spec version
- **WHEN** it is validated against an older schema
- **THEN** validation reports the unknown type as a warning, not an error

### Requirement: Execution Model Boundary

The iTwin document SHALL be the authored semantic representation. The executable form SHALL be derived by a compile step that does not alter the semantic identity of the iTwin.

#### Scenario: Compile preserves identity
- **GIVEN** a valid iTwin document
- **WHEN** it is compiled to an execution context
- **THEN** the execution context references the same `itwin.id` and element ids
- **AND** the source document is unmodified

### Requirement: Conformance Levels

The spec SHALL define conformance levels L1 Loadable, L2 Behavioral, L3 Controllable, L4 Observable, each adding requirements over the previous level.

#### Scenario: Level declaration
- **GIVEN** an iTwin document
- **WHEN** its conformance level is assessed
- **THEN** the level is the highest level whose requirements are all satisfied
