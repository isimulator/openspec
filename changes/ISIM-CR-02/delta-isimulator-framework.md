# ISIM-CR-02 Delta: specs/isimulator-framework/spec.md (new spec)

**Target version:** isimulator-framework 0.1.0 (ADDED as new spec)
**Source requirements:** ISIM-CR-02 R01-R12

## ADDED Requirements

### Requirement: Enterprise Simulation Primitives (R01)

The framework SHALL define a minimal reusable primitive vocabulary spanning five categories: Structural (Entity, Role, Resource, Capability, Function, Service, Process, Activity), Dynamic (State, Event, Transition, Flow, Queue, Arrival, Departure), Control (Rule, Policy, Constraint, Control, Decision, Trigger), Quantitative (Parameter, Variable, Metric, Capacity, Rate, Duration, Cost, Probability), Simulation (Scenario, Experiment, Run, Observation, Result).

#### Scenario: Primitive is machine-readable
- **GIVEN** the Primitive Library
- **WHEN** a tooling consumer loads a primitive definition
- **THEN** the definition carries id, category, attributes, and composition rules

### Requirement: Simulation Composition (R02)

The framework SHALL define how primitives compose into executable simulations (processes contain activities; activities consume resources; queues connect activities; rules govern transitions).

#### Scenario: Minimal useful simulation
- **GIVEN** primitives for one process, four activities, three resources, and two parameters (order rate, processing times)
- **WHEN** the model is composed
- **THEN** it is a valid executable simulation without any engine-specific content

### Requirement: Declarative Simulation Specification (R03)

The framework SHALL define a machine-readable simulation representation naming the model, parameters, and metrics. Serialization follows ADR-0001 (JSON documents, JSON Schema normative, Pydantic in the reference runtime).

#### Scenario: Specification is engine-neutral
- **GIVEN** a declarative simulation specification
- **WHEN** it is inspected
- **THEN** it contains no reference to a specific simulation engine

### Requirement: Engine Adapter Contract (R04)

The framework SHALL define a standard adapter interface: load, compile, initialize, run, pause, resume, step, observe, terminate, result.

#### Scenario: Adapter conformance
- **GIVEN** an engine adapter claiming the contract
- **WHEN** the conformance suite runs
- **THEN** every contract operation is implemented and returns iSimulator-normalized types

### Requirement: Engine Selection (R05)

A simulation SHALL identify the execution capability it requires (DES, ABM, continuous, hybrid, co-simulation) without naming an engine.

#### Scenario: Capability-based binding
- **GIVEN** a simulation requiring discrete-event capability
- **WHEN** the runtime selects an engine
- **THEN** any adapter providing that capability is eligible

### Requirement: External Engine Integration (R06)

External engines SHALL be integrated behind adapters and SHALL NOT become part of the iSimulator semantic model.

#### Scenario: Engine replacement
- **GIVEN** a simulation executed through one engine adapter
- **WHEN** the adapter is replaced by another providing the same capability
- **THEN** the simulation model is unchanged and results remain comparable in the normalized result model

### Requirement: Visualization Provider Contract (R07)

Visualization SHALL be an integration capability behind a provider contract, not an iSimulator-owned rendering engine.

#### Scenario: Provider swap
- **GIVEN** a visualization provider (process graph, charts, or WebGL)
- **WHEN** it is replaced by another provider
- **THEN** the simulation and interaction models are unaffected

### Requirement: Simulation Validation (R08)

The framework SHALL validate a composed model before execution.

#### Scenario: Invalid model rejected
- **GIVEN** a model with an activity referencing an undefined resource
- **WHEN** validation runs
- **THEN** execution is refused with a specific diagnostic

### Requirement: Result Normalization (R09)

Engine-specific results SHALL be normalized into the iSimulator result model (metrics, observations, timestamps, provenance).

#### Scenario: Cross-engine comparison
- **GIVEN** results of the same simulation from two different engine adapters
- **WHEN** results are compared
- **THEN** comparison operates on normalized metrics, not engine-specific formats

### Requirement: Provenance (R10)

Every execution SHALL record model version, adapter and version, engine and version, configuration, random seed, and timestamps.

#### Scenario: Reproducibility
- **GIVEN** a recorded execution
- **WHEN** it is replayed with identical inputs and seed
- **THEN** the normalized result is identical

### Requirement: Progressive Complexity (R11)

A valid simulation SHALL be constructible from simple primitives and SHALL support progressive elaboration across levels: Primitive, Process, Resource, Flow, Policy, Scenario, Experiment, Prediction, Optimization, Autonomous.

#### Scenario: Level 0 to Level 1
- **GIVEN** a single Activity primitive
- **WHEN** activities are chained into a process
- **THEN** the model remains valid at each step of elaboration

### Requirement: Reference Tooling (R12)

The framework SHALL provide at least one executable reference implementation using an adopted open-source engine selected by an Integration Decision Record.

#### Scenario: Golden path acceptance
- **GIVEN** the OTCHERE order fulfillment primitives (validation capacity 5, fulfillment capacity 10, arrival 100/hour)
- **WHEN** a user composes and runs the simulation, then changes fulfillment capacity to 15 and re-runs
- **THEN** throughput, cycle time, backlog, utilization, and SLA are reported and baseline vs scenario comparison works
- **AND** the user never needed to know which engine executed it
