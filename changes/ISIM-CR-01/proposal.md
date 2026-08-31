ISIM-CR-01 before touching implementation.

One refinement from the earlier review is important: the CR should not merely describe a future feature set. It should establish the architectural contracts and boundaries that subsequent CRs must implement against. This is consistent with the spec-driven approach already embodied in OpenSpec, where requirements are expressed as verifiable behavior rather than implementation detail. 

ISIM-CR-01: Interactive Semantic Simulation Architecture

Status: Proposed
Target: isimulator organization
Scope: Architecture, specifications, repository responsibilities, reference implementation strategy
Predecessor: Current iSimulator v0.1 structure
Next: ISIM-CR-02: Executable iTwin Model

⸻

1. Change Summary

Establish the target architecture for iSimulator as a semantic, executable, interactive simulation environment capable of:

1. representing a contextualized digital twin;
2. executing its operational behavior;
3. maintaining simulation state;
4. creating and executing counterfactual scenarios;
5. manipulating controls and parameters;
6. collecting and comparing simulation results;
7. producing forecasts and predictive analytics;
8. visually animating the simulated environment;
9. exposing simulation capabilities through APIs;
10. enabling natural-language interaction through a locally hosted language model;
11. maintaining provenance and reproducibility;
12. eventually supporting synchronization with operational reality.

The architecture SHALL preserve the existing separation between the iTwin specification, enterprise specialization, catalogs, simulation core, examples, and alignment repositories rather than collapsing these responsibilities into one implementation repository.

⸻

2. Architectural Intent

iSimulator SHALL be understood as:

A semantic execution environment for representing, simulating, experimenting with, predicting the behavior of, and interacting with contextualized digital twins.

The primary conceptual chain becomes:

Real / Intended World
        │
        ▼
   Contextualized
      iTwin
        │
        ▼
 Executable Model
        │
        ▼
 Simulation Runtime
        │
   ┌────┼────┐
   ▼    ▼    ▼
Scenario Forecast Optimization
   │    │    │
   └────┼────┘
        ▼
   Decision Support
        │
        ▼
 Interactive Experience
        │
        ▼
 Human / AI Interaction

The key architectural principle is:

The LLM, visualization layer and analytics layer consume and operate upon the simulation contract; they do not redefine the semantic or simulation model.

⸻

3. Current Structure: Retained

The existing repository structure should remain the foundation:

isimulator/
├── docs
├── openspec
├── itwin-spec
├── itwin-enterprise
├── itwin-catalogs
├── isimulator-core
├── examples
├── wsf-alignment
├── opendeam-alignment
├── standards-alignment
└── .github

The CR therefore does not introduce wholesale repository restructuring.

Instead it establishes responsibilities and identifies where future capabilities belong.

⸻

4. Target Logical Architecture

The target architecture SHALL consist of the following layers.

┌──────────────────────────────────────────────────────────────┐
│                     INTERACTION LAYER                        │
│                                                              │
│  Human UI       Natural Language       External Applications │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    INTERACTION SERVICES                      │
│                                                              │
│  Twin Explorer │ Scenario Builder │ Control │ AI Tool API   │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    ANALYTICAL SERVICES                       │
│                                                              │
│ Forecast │ Prediction │ Optimization │ Sensitivity │ Report │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    EXPERIMENT SERVICES                       │
│                                                              │
│ Scenario │ Experiment │ Replication │ Comparison │ Results  │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    SIMULATION RUNTIME                        │
│                                                              │
│ Clock │ Events │ State │ Process │ Resources │ Controls      │
│ Scheduler │ Rules │ Policies │ Metrics │ Observers           │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    EXECUTABLE iTWIN                          │
│                                                              │
│ Entity │ State │ Behavior │ Process │ Resource │ Event       │
│ Capability │ Function │ Service │ Policy │ Rule │ Metric     │
└────────────────────────────┬─────────────────────────────────┘
                             │
                             ▼
┌──────────────────────────────────────────────────────────────┐
│                    SEMANTIC FOUNDATION                       │
│                                                              │
│ WSF │ OpenDEAM │ iSimulator Catalogs │ Standards Alignment  │
└──────────────────────────────────────────────────────────────┘

⸻

5. The Five Models We Must Explicitly Separate

The most important architectural outcome of this CR is to prevent the word “model” from becoming ambiguous.

5.1 Semantic Model

What something is.

Entity
Role
Capability
Function
Resource
Service
Process
Information
Relationship
...

Grounded through the semantic foundation and iTwin specification.

⸻

5.2 State Model

What the twin is like now.

State Variable
State Value
State Transition
Condition
Observation
Measurement
Timestamp

⸻

5.3 Behavior Model

How the twin behaves.

Event
Activity
Process
Transition
Rule
Policy
Constraint
Resource Consumption

⸻

5.4 Simulation Model

How behavior becomes executable.

Simulation Clock
Event Queue
Scheduler
Execution Context
Resource Allocation
State Update
Metric Collection
Randomness

⸻

5.5 Scenario Model

How we ask:

“What if?”

Scenario
 ├── Baseline
 ├── Initial State
 ├── Parameter Overrides
 ├── Control Changes
 ├── Events
 ├── Policies
 ├── Constraints
 ├── Horizon
 └── Random Seed

This separation is fundamental.

⸻

6. Requirement: Executable iTwin

Requirement

The system SHALL distinguish an iTwin representation from its executable simulation representation.

Scenario

Given a valid iTwin
When it is loaded by iSimulator
Then the system SHALL be capable of producing an executable simulation context without changing the semantic identity of the iTwin.

This becomes the principal objective of ISIM-CR-02.

⸻

7. Requirement: Simulation Engine Abstraction

The architecture SHALL NOT define iSimulator as a wrapper around one simulation engine.

It SHALL define a simulation-engine abstraction capable of supporting:

Discrete Event
Continuous
Discrete Time
Agent Based
System Dynamics
Hybrid
Co-Simulation

The first reference implementation SHOULD use Discrete Event Simulation, because it provides a natural initial representation for enterprise processes, queues, resources, events and operational capacity.

A candidate implementation engine is SimPy, but SimPy SHALL remain an implementation dependency rather than becoming the iSimulator model itself.

⸻

8. Requirement: Scenario Execution

The runtime SHALL support:

Baseline
Scenario
Scenario Variant
Scenario Comparison

A scenario SHALL be independently identifiable and reproducible.

Example:

Baseline:
Order Volume = 10,000/day
Fulfillment Capacity = 8,000/day
Scenario:
Order Volume = 12,000/day
Fulfillment Capacity = 9,000/day
Automation = +20%

The runtime SHALL be able to execute both and compare resulting metrics.

⸻

9. Requirement: Experiment Management

A simulation run SHALL not be the same thing as an experiment.

Experiment
    │
    ├── Scenario A
    │     ├── Run 1
    │     ├── Run 2
    │     └── Run 3
    │
    └── Scenario B
          ├── Run 1
          ├── Run 2
          └── Run 3

Experiments SHALL support:

* replication;
* random seed;
* parameter sets;
* simulation horizon;
* metrics;
* result comparison;
* provenance.

⸻

10. Requirement: Simulation Provenance

Every simulation result SHALL identify at minimum:

iTwin Version
Executable Model Version
Scenario Version
Parameter Set
Simulation Engine
Engine Version
Random Seed
Data Snapshot
Simulation Start
Simulation End

This is necessary for reproducibility and eventually for validation.

⸻

11. Requirement: Observation Model

The architecture SHALL distinguish:

Model Value
Observed Value
Simulated Value
Forecast Value
Predicted Value

For example:

                         Orders/day
Observed                    8,700
Simulation                  9,100
Forecast                    9,300
Scenario                     10,200

These SHALL NOT be represented as merely different values of the same semantic property.

They represent different epistemic sources.

⸻

12. Requirement: Forecast and Prediction

iSimulator SHALL distinguish:

Simulation

What happens under specified model assumptions?

Forecast

What is likely to happen based on observed historical behavior?

Prediction

What future state/value does the model estimate?

Optimization

Which controllable configuration produces the preferred outcome?

These SHALL be separate analytical services.

⸻

13. Requirement: Control Model

A UI control SHALL map to an explicit model element.

For example:

Order Volume Dial
        │
        ▼
Control Variable
        │
        ▼
Scenario Override
        │
        ▼
Simulation Parameter
        │
        ▼
Operational Effect
        │
        ▼
Metric Change

Controls SHALL have:

* identifier;
* semantic target;
* current value;
* allowed range;
* unit;
* default;
* scenario value;
* validation rule.

This prevents the UI from becoming a collection of hard-coded demo sliders.

⸻

14. Requirement: Interactive Visualization

The architecture SHALL support a visual simulation environment.

The first reference experience SHALL support:

Process Visualization
Entity State
Animation
Simulation Clock
Control Dials
KPIs
Scenario Controls
Timeline
Charts
Scenario Comparison

3D visualization is explicitly optional.

The first golden-path example SHOULD prioritize operational/process visualization over graphical sophistication.

For richer spatial twins later, the architecture can integrate established visualization technology; for example, the iTwin ecosystem itself provides React-based viewer packages. 

⸻

15. Requirement: Natural Language Interaction

The system SHALL provide an optional natural-language interface.

The architecture SHALL be:

User
 │
 ▼
Local LLM
 │
 ▼
Context Builder
 │
 ▼
Tool Selection
 │
 ▼
Typed iSimulator API
 │
 ▼
Simulation Runtime

The LLM SHALL NOT directly manipulate simulation internals.

⸻

16. Initial LLM Tool Contract

The following tools SHOULD become the initial interaction vocabulary:

inspect_twin()
inspect_entity()
inspect_process()
get_state()
get_metric()
create_scenario()
modify_parameter()
modify_control()
run_simulation()
pause_simulation()
resume_simulation()
compare_scenarios()
forecast()
predict()
optimize()
explain_result()
generate_report()

This is an important architectural decision.

The LLM becomes a natural-language client of iSimulator, rather than the intelligence embedded inside the simulation kernel.

⸻

17. Local Model Architecture

The local AI implementation SHOULD initially support an adapter architecture:

                 LLM Gateway
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      Ollama      llama.cpp      vLLM
        │            │            │
        ▼            ▼            ▼
       Local Model Provider

Ollama is an attractive first development target because it gives the local model a simple service interface.

llama.cpp should remain a lower-level deployment option.

The specific model SHALL remain configurable.

A Qwen-family model is a reasonable candidate for initial evaluation, but ISIM-CR-01 shall not mandate Qwen or any other model.

⸻

18. Semantic Context for the LLM

The LLM SHALL receive context from the iTwin rather than relying on its pretrained knowledge.

Conceptually:

User Question
      │
      ▼
Semantic Resolver
      │
      ▼
iTwin Context
      │
      ▼
Relevant State / Process / Scenario
      │
      ▼
LLM

Vector retrieval may subsequently be introduced, but embeddings SHALL be treated as an auxiliary retrieval mechanism.

The authoritative meaning remains the iTwin/semantic model.

⸻

19. Canonical Golden-Path Example

The CR SHALL establish:

OTCHERE Order-to-Cash / Fulfillment

as the initial reference scenario.

Order
  ↓
Validation
  ↓
Credit
  ↓
Inventory
  ↓
Fulfillment
  ↓
Shipment
  ↓
Invoice
  ↓
Payment

Each process stage SHALL have, where relevant:

Resource
Capacity
Processing Time
Queue
Policy
Rule
Failure Rate
SLA
Cost

This becomes the architectural acceptance test.

⸻

20. Golden-Path Interaction

The finished system should eventually support this sequence:

Observe

“Show me the current order-to-cash operation.”

Manipulate

“Increase demand by 20%.”

Simulate

“Run the next 30 days.”

Compare

“Compare this against the baseline.”

Explain

“Why does fulfillment become the bottleneck?”

Forecast

“What is the expected backlog in 30 days?”

Optimize

“What additional capacity is required to maintain 95% SLA?”

Report

“Generate a management report explaining the result.”

This is a much stronger acceptance criterion than simply:

“The simulator runs.”

⸻

21. Repository Responsibilities

The CR establishes the following boundaries:

Repository	Responsibility
itwin-spec	Semantic + executable iTwin specification
itwin-enterprise	Organization specialization
itwin-catalogs	Reusable model/catalog assets
isimulator-core	Simulation execution
examples	Canonical executable examples
wsf-alignment	Semantic grounding
opendeam-alignment	Enterprise model alignment
standards-alignment	External standards mapping
docs	Architecture and conceptual documentation
openspec	Change/specification lifecycle

Future repositories such as:

isimulator-studio
isimulator-ai
isimulator-analytics
isimulator-connectors

SHALL NOT be created until their boundaries are established by subsequent CRs.

This keeps the organization from accumulating empty repositories prematurely.

⸻

22. Technology Baseline

The CR SHOULD establish the following as reference implementation technologies, not mandatory conceptual dependencies.

Runtime

Python
FastAPI
Pydantic
SimPy

Data

SQLite
DuckDB
Parquet
PostgreSQL

Analytics

NumPy
pandas
SciPy
statsmodels
scikit-learn

Optimization

SciPy Optimize
Optuna
OR-Tools

UI: subsequent CR

React
TypeScript
React Flow
ECharts / Plotly
WebSocket

Local AI: subsequent CR

Ollama
llama.cpp
Qwen-family evaluation

These choices should be recorded as ADR candidates, not treated as irrevocable architecture.

⸻

23. Explicit Non-Goals

ISIM-CR-01 SHALL NOT:

* implement the simulation engine;
* implement the UI;
* select a final LLM;
* implement production data synchronization;
* implement 3D visualization;
* implement optimization algorithms;
* define every industry-specific model;
* require one simulation paradigm;
* turn iSimulator into an enterprise-only simulator.

The CR establishes the contracts that permit those capabilities to be implemented later.

⸻

24. Subsequent CR Sequence

After this CR:

ISIM-CR-01
Interactive Semantic Simulation Architecture
        │
        ▼
ISIM-CR-02
Executable iTwin Model
        │
        ▼
ISIM-CR-03
Simulation Execution Model
        │
        ▼
ISIM-CR-04
Scenario Experiment Engine
        │
        ▼
ISIM-CR-05
Runtime API
        │
        ▼
ISIM-CR-06
Interactive Twin Studio
        │
        ▼
ISIM-CR-07
Forecast Prediction Analytics
        │
        ▼
ISIM-CR-08
Local AI Interaction
        │
        ▼
ISIM-CR-09
Twin Synchronization
        │
        ▼
ISIM-CR-10
Autonomous Simulation Closed Loop

This sequencing is deliberate.

CR-02 and CR-03 are the semantic/execution foundation. CR-06 makes it visually useful. CR-08 makes it conversational. CR-09 turns it into a live twin. CR-10 connects it to autonomous operations.

⸻

25. Acceptance Criteria for ISIM-CR-01

The CR should be considered complete only when:

* [ ]	The target iSimulator architecture is documented.
* [ ]	Semantic, state, behavior, simulation and scenario models are distinguished.
* [ ]	Repository responsibilities are documented.
* [ ]	Simulation-engine abstraction is documented.
* [ ]	Scenario and experiment boundaries are documented.
* [ ]	Observation/result/provenance concepts are established.
* [ ]	Forecast/prediction/optimization boundaries are established.
* [ ]	Control model is established.
* [ ]	Natural-language interaction architecture is established.
* [ ]	LLM-to-runtime tool boundary is established.
* [ ]	Local-model adapter architecture is established.
* [ ]	OTCHERE Order-to-Cash/Fulfillment is established as the golden-path example.
* [ ]	Subsequent CR dependencies are documented.
* [ ]	No implementation is started that violates these boundaries.

⸻

26. One important change to our earlier plan

I would not immediately create isimulator-api, isimulator-ai, isimulator-analytics, etc.

The architecture should earn those repository boundaries.

The first implementation cycle should instead be:

itwin-spec
     │
     ▼
itwin-enterprise
     │
     ▼
examples
     │
     ▼
isimulator-core
     │
     ▼
working executable simulation

Then we extract the API, UI, analytics and AI boundaries once the executable model proves them.

This follows a much healthier architecture principle:

First prove the domain boundaries; then crystallize them into repositories.

That is especially important for iSimulator because the current structure is already strong conceptually. The danger now is over-architecting the repository structure before the executable semantics are proven.

⸻

27. Immediate implementation target after approval

The next CR should therefore be:

ISIM-CR-02: Executable iTwin Model

That CR should define the actual machine-readable model for:

Entity
Identity
Relationship
State
Event
Process
Activity
Resource
Capability
Function
Service
Policy
Rule
Metric
Observation
Control
Scenario

and produce the first concrete artifact:

OTCHERE
  └── Order-to-Cash
       └── Fulfillment
            └── Executable iTwin

Once that exists, ISIM-CR-03 can build the simulation kernel against a real model rather than an abstract architecture.

That is the point at which iSimulator starts transitioning from a well-designed repository organization into an actual executable semantic simulation platform.