iSimulator is an integration and semantic orchestration framework, not a new simulation-engine, rendering-engine, or domain-specialist platform.

ISIM-CR-02: Reusable Simulation Framework

Purpose: establish the reusable framework by which an enterprise architect, solution engineer, demand engineer, or product manager can construct a useful simulation from simple enterprise primitives, while delegating specialized simulation, visualization, analytics, and AI capabilities to suitable open-source components.

The key design principle should be:

iSimulator defines the semantic, executable, compositional and interaction framework; it adopts and integrates proven engines rather than recreating them.

⸻

1. The fundamental positioning

The architecture should explicitly distinguish four things:

                         iSimulator
                              │
       ┌──────────────────────┼──────────────────────┐
       │                      │                      │
       ▼                      ▼                      ▼
 Semantic Framework     Simulation Framework    Interaction Framework
       │                      │                      │
       └──────────────────────┼──────────────────────┘
                              │
                     Integration Adapters
                              │
       ┌──────────────┬───────┼────────┬──────────────┐
       ▼              ▼       ▼        ▼              ▼
   DES Engine     ABM Engine  FMI    Renderer     Analytics

iSimulator owns:

* the iTwin model;
* enterprise simulation primitives;
* composition;
* scenario definition;
* control semantics;
* experiment definition;
* result/provenance semantics;
* integration contracts;
* interaction/tool contracts.

iSimulator does not own:

* a proprietary DES engine;
* a proprietary ABM engine;
* a proprietary 3D renderer;
* a proprietary ML framework;
* a proprietary LLM;
* specialized industrial/physical simulation models.

This distinction should become a first-class architectural principle.

⸻

2. The “adopt before build” principle

I recommend adding this as an explicit iSimulator architecture rule:

For every substantial technical capability, iSimulator SHALL first identify suitable open-source or open-standard implementations before implementing equivalent functionality.

Evaluation should consider:

Criterion	Question
Functional fit	Does it solve the required problem?
Semantic fit	Can it represent iSimulator concepts without distorting them?
Integration	Can it be wrapped behind an iSimulator adapter?
License	MIT, Apache-2.0, BSD or similarly permissive?
Commercial use	Can enterprise solutions safely consume it?
Extensibility	Can specialized behavior be added?
Maturity	Is it actively maintained and established?
Community	Is there meaningful adoption?
Performance	Does it meet expected workload?
Reproducibility	Can execution be deterministic/reproducible?
Deployment	Local, containerized, cloud and edge options?
Data exchange	Can models/results be exchanged cleanly?
Lock-in	Can iSimulator replace it later?

The output should be an Integration Decision Record, not an informal technology choice.

⸻

3. Proposed technology assessment model

Every external engine considered by iSimulator should receive an assessment such as:

ISIM-ADR-XXX
Technology Integration Decision
Capability:
Simulation / Visualization / Analytics / AI
Candidate:
<technology>
License:
MIT / Apache-2.0 / BSD / etc.
Role:
Embedded / Adapter / Service / Optional
Fit:
High / Medium / Low
Integration Cost:
Low / Medium / High
Lock-in:
Low / Medium / High
Decision:
Adopt / Integrate / Reference / Reject
Rationale:
...

This will prevent the project from accumulating arbitrary dependencies.

⸻

4. Simulation engines: don’t build one

The earlier analysis proposed SimPy as an initial engine. I would now sharpen that:

SimPy becomes a candidate reference engine, not “the iSimulator engine.”

SimPy is attractive because it is a Python process-based discrete-event simulation framework and is MIT licensed. 

The integration would look like:

iTwin
  │
  ▼
iSimulator Simulation Model
  │
  ▼
SimPy Adapter
  │
  ▼
SimPy
  │
  ▼
iSimulator Result Model

The enterprise architect never needs to know that SimPy is underneath.

That is the correct abstraction.

⸻

5. And we should investigate alternatives

The CR should mandate a simulation-engine landscape assessment, rather than prematurely choosing SimPy.

At minimum:

Discrete Event

SimPy

Candidate for enterprise process simulation.

Agent-Based

Mesa

Mesa provides Python agent-based modelling, scheduling, spatial components and browser visualization. 

It could eventually support:

Customer
Employee
Supplier
Agent
Service
Organization

as behavioral actors.

Hybrid / Equation-Based

OpenModelica

This is interesting technically but should not automatically become a dependency. Its licensing is substantially less straightforward for our intended permissive ecosystem: OpenModelica uses the OSMC Public License with GPL/EPL variants and additional conditions. 

Therefore:

Reference/integration candidate, not default embedded dependency.

Co-Simulation

FMI 3.0

FMI is particularly interesting because it is a standard rather than an engine. It defines a model exchange/co-simulation interface and is supported by a large ecosystem of tools. The FMI specification documentation states that its code is BSD-2-Clause licensed. 

This is exactly the type of capability iSimulator should integrate rather than reinvent.

⸻

6. This leads to an important three-level engine architecture

The CR should establish:

Level 1: Native Enterprise Primitives
             │
             ▼
Level 2: iSimulator Execution Abstraction
             │
             ▼
Level 3: External Execution Engines

For example:

Enterprise Process
       │
       ▼
Queue
Resource
Activity
Event
Policy
Capacity
       │
       ▼
iSimulator Execution Model
       │
       ▼
┌─────────────────────────────────┐
│ Simulation Adapter              │
├──────────┬──────────┬────────────┤
│ SimPy    │ Mesa     │ FMI        │
└──────────┴──────────┴────────────┘

This is much more powerful than creating an iSimulator-specific simulation language.

⸻

7. The real iSimulator innovation: compositional simulation

This is where I think your clarification exposes the real opportunity.

We want someone to be able to start with almost nothing:

Order
Customer
Employee
Process
Queue
Resource
Capacity
Time
Cost
Rule
Policy

and progressively construct:

Process
    ↓
Process Network
    ↓
Operational Model
    ↓
Enterprise Simulation
    ↓
Scenario
    ↓
Experiment

This is the framework we should build.

⸻

8. Enterprise simulation primitives

ISIM-CR-02 should therefore define a deliberately small primitive vocabulary.

Structural

Entity
Role
Resource
Capability
Function
Service
Process
Activity

Dynamic

State
Event
Transition
Flow
Queue
Arrival
Departure

Control

Rule
Policy
Constraint
Control
Decision
Trigger

Quantitative

Parameter
Variable
Metric
Capacity
Rate
Duration
Cost
Probability

Simulation

Scenario
Experiment
Run
Observation
Result

This becomes the Enterprise Simulation Primitive Library.

⸻

9. Simple primitives must actually be useful

A user should be able to create something as simple as:

process:
  name: Order Fulfillment
activities:
  - Validate Order
  - Pick Order
  - Pack Order
  - Ship Order
resources:
  - Picker
  - Packer
  - Carrier
parameters:
  order_rate: 100/hour
  pick_time: 5 minutes
  pack_time: 3 minutes

and obtain a working simulation.

No Python.

No SimPy.

No simulation-engine knowledge.

No custom software development.

That is the framework’s practical value proposition.

⸻

10. Then complexity becomes progressive

Level 0: Primitive

Activity

Level 1: Process

Activity → Activity → Activity

Level 2: Resource

Activity + Resource + Capacity

Level 3: Flow

Process + Queue + Arrival

Level 4: Policy

Process + Rule + Decision

Level 5: Scenario

Baseline + Parameter Override

Level 6: Experiment

Scenario + Replication + Metrics

Level 7: Prediction

Experiment + Historical Data

Level 8: Optimization

Experiment + Objective + Constraints

Level 9: Autonomous

Observe → Simulate → Evaluate → Decide → Act

This progression is extremely important for adoption.

⸻

11. This also changes how itwin-spec should be approached

The iTwin should not become a huge simulation metamodel.

Instead:

iTwin Semantic Model
        │
        ▼
Enterprise Simulation Profile
        │
        ▼
Executable Simulation Model

The semantic model establishes meaning.

The simulation profile identifies which aspects of that meaning are executable.

For example:

Process
  │
  ├── semantic definition
  │
  └── simulation behavior
       ├── duration
       ├── resource
       ├── queue
       └── routing

This keeps iTwin reusable beyond simulation.

⸻

12. Engine adapters become first-class assets

Rather than:

isimulator-core/
    simpy/
    mesa/
    ...

we should conceptualize:

Simulation Adapter
        │
        ├── SimPy Adapter
        ├── Mesa Adapter
        ├── FMI Adapter
        └── Future Adapter

The adapter converts:

iSimulator Model
        ↕
Engine Model

and:

Engine Result
        ↕
iSimulator Result

This is the key anti-lock-in mechanism.

⸻

13. Visualization follows exactly the same principle

I agree completely that iSimulator should not build a 3D engine.

We should investigate existing engines and frameworks according to the same decision process.

The architecture becomes:

iSimulator Visualization Model
          │
          ▼
Visualization Adapter
          │
   ┌──────┼──────────┐
   ▼      ▼          ▼
2D Graph  WebGL      3D Engine

For the enterprise use case, we should first investigate:

* React Flow for process/network visualization;
* ECharts/Plotly for analytical visualization;
* Three.js for generic WebGL visualization;
* established digital-twin visualization engines where spatial/industrial requirements justify them.

Three.js should not become an iSimulator-owned rendering subsystem.

It is simply one possible visualization provider.

⸻

14. Specialized engines should be optional

This also gives us a clean boundary for domains outside enterprise simulation.

Suppose later someone wants:

* factory physics;
* warehouse robotics;
* telecom network propagation;
* transportation;
* energy systems;
* physical fluid dynamics.

iSimulator does not need to become those domains.

Instead:

Enterprise iTwin
       │
       ├──────── Enterprise Simulation
       │
       ├──────── External Specialized Model
       │              │
       │              ▼
       │          Specialized Engine
       │
       └──────── FMI / API / Data Adapter

That is a much healthier ecosystem model.

⸻

15. The reusable framework should therefore have five assets

I would make these explicit deliverables of ISIM-CR-02.

Asset 1: Primitive Library

Machine-readable definitions of:

Process
Activity
Resource
Queue
Event
Rule
Policy
Capacity
Metric
...

⸻

Asset 2: Simulation Composition Model

Defines how primitives compose.

Example:

Process
 ├── Activity
 │     ├── Resource
 │     ├── Duration
 │     └── Policy
 │
 ├── Queue
 │
 └── Transition

⸻

Asset 3: Engine Adapter Contract

Defines how an external engine plugs into iSimulator.

load()
compile()
initialize()
run()
pause()
resume()
step()
observe()
terminate()
result()

⸻

Asset 4: Simulation Specification

A declarative representation of an executable simulation.

Example:

simulation:
  name: OTCHERE Order Fulfillment
  model:
    process: order-fulfillment
  parameters:
    order_rate: 100/hour
    fulfillment_capacity: 80/hour
  metrics:
    - throughput
    - backlog
    - cycle_time
    - sla

This is potentially one of the most important iSimulator assets.

⸻

Asset 5: Reference Model Catalog

Reusable enterprise primitives and patterns:

Order
Customer
Supplier
Employee
Team
Process
Queue
Service
Resource
Capability
Work Item
Request
Case
Decision
Policy
SLA

This belongs naturally in itwin-catalogs / enterprise examples rather than hard-coding into the engine.

⸻

16. Tooling should make simulation construction accessible

The eventual Studio should not merely be a visualization application.

It should be a simulation composition environment.

Conceptually:

                 Simulation Builder
┌──────────────┐       ┌──────────────────────────┐
│ Primitives   │       │        Canvas            │
│              │       │                          │
│ Process      │ ────► │ Order → Validate → Ship  │
│ Activity     │       │           │              │
│ Resource     │       │          Queue            │
│ Queue        │       │           │              │
│ Event        │       │         Fulfill           │
│ Rule         │       │                          │
└──────────────┘       └──────────────────────────┘
       Properties
       ─────────────────────
       Processing Time: 5m
       Capacity: 10
       Resource: Picker
       Queue: FIFO
       [Validate] [Simulate]

This makes the framework useful to non-simulation specialists.

⸻

17. Natural language becomes even more powerful

Once the primitive/composition model exists, the LLM does not need to “understand simulation” in an unrestricted way.

It can construct the model using primitives.

For example:

“Create a simple order fulfillment simulation with 100 orders per hour, two validation agents and five fulfillment workers.”

The LLM generates:

Entity
Process
Activities
Resources
Capacity
Arrival Rate
Metrics

Then asks the runtime to validate the model.

That means:

Natural Language
       ↓
Simulation Primitives
       ↓
Executable Model
       ↓
External Engine

This is much safer and much more reusable than asking an LLM to write arbitrary simulation code.

⸻

18. This should become the primary iSimulator workflow

             DEFINE
               │
               ▼
        Semantic Context
               │
               ▼
        Compose Primitives
               │
               ▼
        Validate Model
               │
               ▼
        Select Engine
               │
               ▼
          Execute
               │
               ▼
        Observe Results
               │
               ▼
          Experiment
               │
               ▼
      Scenario Comparison
               │
               ▼
       Forecast / Optimize
               │
               ▼
       Explain / Report

And the user can enter this workflow through:

Visual Builder
      OR
Natural Language
      OR
Programmatic API
      OR
Imported Model

That is the reusable framework we should aim for.

⸻

19. ISIM-CR-02 scope

I would therefore define the CR around these requirements:

ISIM-CR-02-R01: Enterprise Simulation Primitives

Define the minimal reusable primitive vocabulary.

ISIM-CR-02-R02: Simulation Composition

Define how primitives compose into executable simulations.

ISIM-CR-02-R03: Declarative Simulation Specification

Define a machine-readable simulation representation.

ISIM-CR-02-R04: Engine Adapter Contract

Define a standard interface for external simulation engines.

ISIM-CR-02-R05: Engine Selection

Define how a simulation identifies the required execution capability.

ISIM-CR-02-R06: External Engine Integration

Define the mechanism for integrating engines without making them part of the iSimulator semantic model.

ISIM-CR-02-R07: Visualization Provider Contract

Define visualization as an integration capability rather than an iSimulator-owned rendering engine.

ISIM-CR-02-R08: Simulation Validation

Validate the model before execution.

ISIM-CR-02-R09: Result Normalization

Normalize engine-specific results into the iSimulator result model.

ISIM-CR-02-R10: Provenance

Record model, adapter, engine, configuration and execution information.

ISIM-CR-02-R11: Progressive Complexity

A valid simulation SHALL be constructible from simple primitives and SHALL support progressive elaboration.

ISIM-CR-02-R12: Reference Tooling

Provide at least one executable reference implementation using an adopted open-source engine.

⸻

20. Initial technology evaluation matrix

I would put something like this into the CR as an initial assessment, with the understanding that formal ADRs follow actual integration testing.

Capability	Candidate	Initial position
Discrete Event	SimPy	Strong adopt candidate
Agent Based	Mesa	Strong integration candidate
Co-Simulation	FMI 3	Strong standards integration
Equation-based	OpenModelica	Reference/integration candidate; license requires care
Process Graph	React Flow	Strong UI candidate
Charts	ECharts / Plotly	Strong UI candidates
Generic 3D/WebGL	Three.js	Integration candidate
Data Analysis	pandas / NumPy	Adopt
Scientific Computing	SciPy	Adopt
Optimization	OR-Tools / Optuna	Integration candidates
Local LLM Runtime	Ollama	Strong initial candidate
Low-level LLM	llama.cpp	Integration candidate

Notice the difference:

“Adopt” does not mean “iSimulator becomes dependent on it conceptually.”

It means:

This is currently the best candidate implementation of a capability that iSimulator needs.

⸻

21. License policy

I would add an explicit project policy:

Preferred

1. MIT
2. Apache-2.0
3. BSD-2-Clause / BSD-3-Clause

Conditional

4. MPL / EPL and other permissive weak-copyleft licenses
5. Standards with compatible implementation licenses

Avoid as embedded core dependencies

6. GPL/AGPL
7. proprietary SDKs
8. restrictive source-available licenses

This is not because GPL software is inherently unsuitable; it is because iSimulator’s intended role as an easily reusable enterprise framework makes permissive licensing strategically preferable.

OpenModelica is a good example of why this assessment needs to happen before adoption: its current OSMC licensing provides GPL/OSMC-PL alternatives with additional conditions, rather than the straightforward MIT/Apache posture we prefer. 

FMI is much more attractive as a standards integration point because the FMI 3.0 specification’s code is under BSD-2-Clause. 

⸻

22. The most important acceptance test

ISIM-CR-02 should not be accepted because the metamodel exists.

It should be accepted when a solution engineer can do this:

Starting point

Order
Process
Employee
Capacity

Construct

Order
   ↓
Validation
   ↓
Fulfillment
   ↓
Shipment

Add

Validation capacity = 5
Fulfillment capacity = 10
Order arrival = 100/hour

Press

Simulate

Obtain

Throughput
Cycle Time
Backlog
Resource Utilization
SLA

Change

Fulfillment capacity = 15

Run again

Compare

Baseline vs Scenario

And none of that should require the user to know whether the underlying execution was performed by SimPy.

That is the real definition of success for ISIM-CR-02.

⸻

23. Revised CR roadmap

This clarification also improves the entire roadmap:

CR	Purpose
ISIM-CR-01	Interactive Semantic Simulation Architecture
ISIM-CR-02	Reusable Simulation Framework
ISIM-CR-03	Reference Engine Integration
ISIM-CR-04	Scenario Experiment Framework
ISIM-CR-05	Runtime API
ISIM-CR-06	Simulation Builder / Studio
ISIM-CR-07	Analytics Forecasting Optimization
ISIM-CR-08	Local AI Simulation Interaction
ISIM-CR-09	Visualization Engine Integrations
ISIM-CR-10	Operational Twin Synchronization
ISIM-CR-11	Autonomous Simulation Closed Loop

I particularly like separating CR-02 from CR-03.

CR-02 establishes the framework.

CR-03 proves it against an actual external engine.

That gives us a clean architectural test:

                ISIMULATOR
                    │
           ┌────────┴────────┐
           │                 │
       Semantics         Simulation
           │                 │
           ▼                 ▼
      iTwin Model       Adapter API
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
                  SimPy             Mesa

Later:

                           Adapter API
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
             SimPy            Mesa             FMI
              │                │                │
              ▼                ▼                ▼
             DES              ABM          Co-Simulation

This gives iSimulator precisely the role I think we want:

not another simulator, but the semantic and compositional framework that makes heterogeneous simulation capabilities usable as an enterprise architecture asset.

And that, in turn, makes the eventual local-LLM experience substantially more compelling: the model can generate and manipulate well-defined enterprise simulation primitives and scenarios, rather than generating opaque simulation code.