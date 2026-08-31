# iSimulator Requirements Specification

**Version:** 0.1.0  
**Status:** Draft  
**Last Updated:** 2026-08-31  
**Format:** OpenSpec-style (Requirement + Scenario)  
**Evolvable:** Yes: changes expressed as ADDED / MODIFIED / REMOVED deltas

---

## Purpose

This specification defines the normative behavioral requirements for the **iSimulator** platform and its native digital-twin concept **iTwin**, with particular emphasis on enterprise architecture twinning of organization functions and their operations.

It is intended to be:

- Readable by digital architects and domain experts
- Machine-parseable for tooling
- Versioned and evolvable via OpenSpec delta conventions

---

## Terminology Alignment

- **iTwin**: Our native term for a digital twin as defined in DESIGN.md. We align conceptually with ISO/IEC 30173 (*Digital twin: Concepts and terminology*) while using “iTwin” consistently for models in this project.
- **Entity**: A concrete real-world or logical entity that an iTwin represents (aligned with WSF).
- **Organization iTwin**: An iTwin focused on an organization or significant organizational unit, twinning its functions, operations, capabilities, and governance.

References:
- ISO/IEC 30173:2023: Digital twin: Concepts and terminology
- World Semantic Foundation (WSF) concept catalog
- OpenDEAM (TechNeHub Labs) architecture layers and catalogs

---

## Requirements

### Requirement: Entity-Centric iTwin Foundation

The system SHALL treat a concrete Entity as the primary anchor of every iTwin.

#### Scenario: Creating an iTwin from an Entity
- **GIVEN** a defined Entity (physical, logical, or organizational)
- **WHEN** an iTwin is created for that Entity
- **THEN** the iTwin is explicitly linked to the Entity’s identity and semantics
- **AND** the iTwin cannot exist without a referenced Entity

#### Scenario: Entity identity preservation
- **GIVEN** an iTwin of an Entity
- **WHEN** the iTwin is simulated or inspected
- **THEN** the original Entity identity and core semantics remain accessible and unaltered by the simulation run

---

### Requirement: Semantic Fidelity

The system SHALL preserve and expose the domain semantics of the Entity within its iTwin.

#### Scenario: Domain meaning is inspectable
- **GIVEN** an iTwin that models an Entity with defined operational semantics
- **WHEN** a digital architect or domain expert inspects the iTwin
- **THEN** the meaning of states, operations, and control rules is expressed in domain terms
- **AND** not reduced solely to abstract numerical state variables

---

### Requirement: Operational and Control Modelling

The system SHALL support modelling of the Entity’s operations and control models as first-class elements of an iTwin.

#### Scenario: Operations are first-class
- **GIVEN** an Entity that performs defined operations
- **WHEN** an iTwin is constructed
- **THEN** those operations can be represented and later executed or simulated within the iTwin

#### Scenario: Control models are expressible
- **GIVEN** rules, policies, or feedback logic that govern an Entity
- **WHEN** they are captured in an iTwin
- **THEN** the control logic remains associated with the iTwin and can influence simulation behavior

---

### Requirement: Temporal Stipulations

The system SHALL support temporal rules, validity periods, sequences, and lifecycle states within an iTwin.

#### Scenario: Time-dependent constraints
- **GIVEN** an iTwin with temporal stipulations (durations, deadlines, sequences)
- **WHEN** a simulation is run
- **THEN** the temporal rules are respected in the progression of the simulation

#### Scenario: Lifecycle awareness for Organization iTwins
- **GIVEN** an Organization iTwin
- **WHEN** lifecycle states (e.g., Baseline, Current, Target, Transition, Scenario) are defined
- **THEN** the iTwin can represent and distinguish those states without overwriting history

---

### Requirement: Organization iTwin Support

The system SHALL provide first-class support for Organization iTwins that twin organization functions and their operations.

#### Scenario: Modelling organization functions
- **GIVEN** an organization with defined functions and operations
- **WHEN** an Organization iTwin is created
- **THEN** those functions and operations can be represented using concepts aligned with OpenDEAM catalogs and WSF semantics

#### Scenario: Governance and decision elements
- **GIVEN** policies, goals, or decision logic relevant to an organization
- **WHEN** they are included in an Organization iTwin
- **THEN** they are available to influence or constrain simulation scenarios

---

### Requirement: Scenario Configuration and Simulation

The system SHALL allow configuration of scenarios and execution of simulations against an iTwin while remaining faithful to its semantics and temporal rules.

#### Scenario: What-if scenario
- **GIVEN** a valid iTwin
- **WHEN** a user defines a scenario (initial conditions, external events, control inputs)
- **THEN** the system can execute a simulation of that scenario
- **AND** results remain interpretable in terms of the original Entity semantics

#### Scenario: Non-technical accessibility
- **GIVEN** a completed iTwin and a prepared scenario
- **WHEN** a domain expert (non-programmer) interacts with the system
- **THEN** they can configure basic parameters and run the simulation without writing code

---

### Requirement: Alignment with Open Foundations

The system SHALL be designed so that iTwin models can be grounded in or mapped to WSF concepts and, for organizational cases, OpenDEAM constructs.

#### Scenario: WSF conceptual grounding
- **GIVEN** an iTwin
- **WHEN** its semantic elements are examined
- **THEN** core notions (Entity, Relationship, Time, Policy, Process, etc.) are mappable to the WSF vocabulary

#### Scenario: OpenDEAM organizational grounding
- **GIVEN** an Organization iTwin
- **WHEN** its structure is examined
- **THEN** organizational functions, capabilities, and lifecycle notions are mappable to OpenDEAM layers and catalogs

---

### Requirement: Standards-Aware Terminology

The system and its documentation SHALL acknowledge ISO/IEC 30173 concepts and terminology while consistently using “iTwin” for the project’s native digital-twin models.

#### Scenario: Public documentation
- **GIVEN** public-facing documentation of iSimulator or iTwin
- **WHEN** the broader digital-twin concept is introduced
- **THEN** ISO/IEC 30173 is referenced as the terminology standard
- **AND** iTwin is presented as this project’s specialized, executable view

---

### Requirement: Versioned and Evolvable Specifications

All normative specifications for iSimulator and iTwin SHALL be versioned and support controlled evolution.

#### Scenario: Specification change
- **GIVEN** an existing requirement in this specification
- **WHEN** the requirement must change
- **THEN** the change is expressed using OpenSpec delta conventions (ADDED / MODIFIED / REMOVED)
- **AND** the specification version is updated accordingly

---

## Out of Scope (v0.1.0)

- Concrete simulation algorithms (discrete-event, continuous, hybrid, etc.)
- Full formal metamodel serialization formats
- Runtime performance characteristics
- Detailed AAS / ISO 23247 interoperability implementation
- User interface wireframes

These will be addressed in subsequent specification versions and ADRs.

---

## Change Management

Future changes to this specification SHALL be recorded as deltas, for example:

```markdown
## ADDED Requirements
### Requirement: ...
#### Scenario: ...

## MODIFIED Requirements
### Requirement: ...
(full new text)

## REMOVED Requirements
### Requirement: ...
```

---

**Document Owner:** iSimulator project  
**Related Documents:** DESIGN.md, ORG.md, README.md  
**Status:** Initial draft: ready for iterative refinement
