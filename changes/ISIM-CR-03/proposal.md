# ISIM-CR-03: Reference Engine Integration (SimPy Proof-of-Life)

**Status:** Proposed
**Repository:** isimulator-core (implementation), isimulator/openspec (proposal), isimulator/docs (IDR), isimulator/itwin-spec (no change)
**Change Type:** Implementation / Engine Integration / First IDR
**Predecessor:** ISIM-CR-02 : Reusable Simulation Framework
**Priority:** Immediate
**Implementation Tranche:** Foundation (Phase 2 : Simulation Kernel)
**Author:** iSimulator
**Date:** 2026-09-01

---

## 1. Purpose

Convert the iSimulator framework established by ISIM-CR-02 into a running system by integrating a first reference simulation engine through the adapter contract. The candidate engine is SimPy, evaluated as an Integration Decision Record (IDR-0001) per the technology integration policy of ADR-0002.

This CR does not ship a simulation engine. It ships evidence that the framework, the contract, the schema, the primitive library, and the golden-path example compose into an executable artifact through an external engine integrated by an adapter.

## 2. Why this is the right next slice

ISIM-CR-02 established nine normative artifacts: primitive library, composition model, declarative schema, engine adapter contract, visualization provider contract, scenario model, experiment model, result normalization contract, and a conformance validator. CR-02 also shipped a golden-path example (`otchere-order-to-cash`). None of those artifacts have yet been exercised end-to-end against a real engine.

The framework must be proven before it is broadened. CR-03 is the proof:

- A single engine (SimPy) integrated via the adapter contract.
- A single example (`otchere-order-to-cash`) replayed through the adapter.
- Normalized results emitted through the R09 contract.
- Provenance captured per R10.
- Conformance suite extended to cover the adapter.

Subsequent CRs (CR-04 scenarios/experiments, CR-05 runtime API, CR-06 builder, CR-09 visualization) build on the proven framework.

## 3. Architectural position

```
                   iSimulator Model
                   (itwin-spec/schema)
                          |
                          v
                  Simulation Adapter      <- contract from CR-02 R04
                          |
                          v
                      SimPy              <- candidate per IDR-0001
                          |
                          v
                  Normalized Result       <- contract from CR-02 R09
                          |
                          v
                  Provenance              <- contract from CR-02 R10
```

The adapter sits between the engine-neutral iTwin specification and the engine-specific runtime. The iTwin document does not import SimPy. The adapter imports SimPy. A second engine later (CR-09 Mesa, or FMI co-simulation) plugs in by writing a second adapter against the same contract.

## 4. Integration Decision Record: IDR-0001 SimPy

Per ADR-0002, every external engine integration must be recorded as an IDR with: candidate, license, role, fit, integration cost, lock-in, decision, rationale.

| Field | Value |
|-------|-------|
| IDR | IDR-0001 |
| Candidate | SimPy 4.x |
| License | MIT |
| Role | Adapter (external engine wrapped behind the R04 contract) |
| Capability | discrete-event |
| Functional fit | Process-based DES; covers queues, resources, events, durations : the four capabilities declared by `examples/otchere-order-to-cash/simulation.json::execution::capabilities` |
| Semantic fit | Process + resource + queue abstractions map to iSimulator primitives 1:1 (Process, Activity, Resource, Queue, Arrival, Duration) |
| License | MIT (preferred per ADR-0002 §4) |
| Commercial safety | Permissive; safe for proprietary derivative work |
| Maturity | First release 2002; current 4.x line maintained; large industrial user base (airlines, hospitals, supply chains) |
| Community | Active; cited in academic and industrial literature; Python-only |
| Performance | Process-based, single-threaded; suitable for hundreds of thousands of events; sufficient for the golden-path scale |
| Reproducibility | Deterministic given a fixed `random_seed` (numpy.random.RandomState seeded by the adapter) |
| Deployment | Pure-Python; trivially containerized; no native dependencies |
| Lock-in | Low; adapter boundary isolates iSimulator from SimPy version changes. Replacement engines can be evaluated without re-architecting the framework |
| Decision | **Adopt as first reference engine** |
| Rationale | Lowest-risk DES candidate by every ADR-0002 criterion; pre-existing OTCHERE golden path already declares DES as its execution capability; proves the adapter contract against the simplest viable engine before more exotic ones (Mesa, FMI) |

IDR-0001 lives at `docs/adr/0003-idr-0001-simpy-reference-engine.md` (the next free ADR number after ADR-0002).

## 5. Scope

CR-03 SHALL deliver:

1. **IDR-0001** as an ADR document in `docs/`.
2. **SimPy adapter** in `isimulator-core`, conforming to the R04 contract.
3. **Replay tool** (`isimulator-core/tools/replay.py`) that loads an iTwin simulation specification, dispatches to the adapter by capability, and emits normalized results.
4. **OTCHERE golden-path replay** executed end-to-end through the adapter.
5. **Result normalization** through the R09 contract.
6. **Provenance capture** through the R10 contract.
7. **Conformance tests** covering: load/compile/initialize/run/observe/result operations, capability declaration, normalized output, deterministic replay with fixed seed.
8. **CI workflow** running the conformance suite on push/PR.

CR-03 SHALL NOT:

- Implement a second engine (Mesa, FMI) : those come in CR-09 and later tranches.
- Replace or extend the framework contracts in `itwin-spec` : the contract is normative from CR-02; CR-03 implements against it.
- Add scenario-experiment machinery beyond what the existing golden-path file already exercises.
- Add visualization : visualization is a separate provider contract and belongs to CR-09.
- Introduce new dependencies outside SimPy itself.

## 6. Adapter design

### 6.1 Module layout

```
isimulator-core/
  isimulator_core/
    __init__.py
    adapter/
      __init__.py
      base.py              # AdapterBase: declarative spec in, normalized result out
      simpy_adapter.py     # SimPyAdapter: concrete adapter for SimPy
    replay.py              # CLI entry point: load spec, dispatch, write results
  tests/
    test_simpy_adapter.py
    test_replay_determinism.py
    test_otchere_golden_path.py
  tools/
    replay.py              # shim to isimulator_core.replay:main
  pyproject.toml
  README.md
  CHANGELOG.md
```

### 6.2 AdapterBase contract

`adapter/base.py` defines the abstract surface implemented by every adapter. It mirrors the operations table in `itwin-spec/contracts/engine-adapter.md`:

```
class AdapterBase(ABC):
    capability: str                # declared capability ("discrete-event" etc.)
    adapter_version: str
    engine_name: str
    engine_version: str

    def load(self, spec: dict) -> AdapterHandle: ...
    def compile(self, handle: AdapterHandle) -> ExecutionContext: ...
    def initialize(self, ctx: ExecutionContext, scenario: dict, seed: int) -> ReadyState: ...
    def run(self, state: ReadyState, horizon: dict) -> CompletionState: ...
    def pause(self, state): ...
    def resume(self, state): ...
    def step(self, state, n: int) -> ReadyState: ...
    def observe(self, state) -> list[Observation]: ...
    def terminate(self, state) -> TerminalState: ...
    def result(self, state: TerminalState) -> NormalizedResult: ...
```

Every operation is implemented in `SimPyAdapter`. Unsupported operations (e.g. `pause`/`resume` for SimPy in v1) raise `NotImplementedError` and the conformance suite marks them as optional-with-capability.

### 6.3 SimPyAdapter specifics

- **Process model**: each `activity` becomes a SimPy process that requests its declared `resource` for its `duration`.
- **Queue model**: SimPy's `Resource` queue is the natural fit; declared `queue::capacity` is enforced as a SimPy `Container` or `Resource` with capacity.
- **Arrival model**: SimPy `process` loop generating arrivals at the declared `rate` (inter-arrival time = `1/rate` for exponential, or constant per spec).
- **Random seeding**: numpy `RandomState` seeded with `execution.random_seed`; passed into SimPy via `simpy.core.Random` is not directly supported in SimPy 4, so deterministic replay is achieved by routing all randomness through the seeded `RandomState`.
- **Metrics**: throughput, cycle_time, backlog, utilization, SLA-compliance collected per replication, aggregated across replications (mean, std, min, max).
- **Provenance**: model version (semver of the iTwin spec), adapter version, engine name + version, configuration hash, random seed, started/ended timestamps.

### 6.4 Conformance suite extensions

```
tests/
  test_simpy_adapter.py
    - load accepts the OTCHERE spec
    - compile produces a SimPy environment with correct process graph
    - initialize applies scenario overrides and seed
    - run completes within horizon
    - observe emits metric observations
    - result returns normalized R09 structure
  test_replay_determinism.py
    - two replays with identical seed produce identical metric values
  test_otchere_golden_path.py
    - end-to-end replay of examples/otchere-order-to-cash/simulation.json
    - asserts presence of all 6 declared metrics in normalized result
    - asserts baseline vs demand-plus-20 scenarios produce different throughput
```

## 7. Required Implementation Assets

CR-03 SHALL produce the following artifacts:

| ID | Asset | Location |
|----|-------|----------|
| A | IDR-0001: SimPy reference engine | `docs/adr/0003-idr-0001-simpy-reference-engine.md` |
| B | `AdapterBase` abstract contract | `isimulator-core/isimulator_core/adapter/base.py` |
| C | `SimPyAdapter` concrete implementation | `isimulator-core/isimulator_core/adapter/simpy_adapter.py` |
| D | `replay.py` CLI | `isimulator-core/isimulator_core/replay.py` |
| E | Conformance test suite | `isimulator-core/tests/test_simpy_adapter.py`, `test_replay_determinism.py`, `test_otchere_golden_path.py` |
| F | CI workflow | `isimulator-core/.github/workflows/test.yml` |
| G | `pyproject.toml` with SimPy dependency | `isimulator-core/pyproject.toml` |
| H | Updated README + CHANGELOG | `isimulator-core/README.md`, `CHANGELOG.md` |
| I | OTCHERE replay evidence | Captured normalized result committed to `isimulator-core/tests/fixtures/otchere_replay.json` (deterministic given fixed seed) |
| J | Status row update | `openspec/change-requests/STATUS.md` flips CR-03 from Proposed to Merged |
| K | Project board update | GitHub Project row "ISIM-CR-03" added, links to PR |

## 8. Non-Goals

- No second engine (Mesa, FMI). CR-09 covers Mesa; FMI co-simulation is a future tranche.
- No scenario sweeps / parameter studies. CR-04 covers the experiment framework.
- No runtime HTTP API. CR-05 covers the runtime API.
- No builder UI. CR-06 covers the studio.
- No visualization. CR-09 covers the visualization provider integrations.
- No changes to the framework contracts in `itwin-spec`. The contract is normative; CR-03 is evidence it works.

## 9. Architectural Constraints

These constraints are preserved from CR-02 and remain non-negotiable:

- The iTwin document (`examples/.../simulation.json`) does not import SimPy.
- The adapter is the only place that imports SimPy.
- The normalized result is engine-neutral; downstream consumers (visualization, analytics) do not see SimPy.
- Engine selection binds by declared `execution.capability`, never by engine name.

## 10. Success Criteria

CR-03 is complete when:

- [ ] IDR-0001 lands as an Accepted ADR in `docs/`.
- [ ] `AdapterBase` is published in `isimulator-core`.
- [ ] `SimPyAdapter` is implemented against `AdapterBase`.
- [ ] `replay.py` CLI loads an iTwin spec, dispatches to the adapter by capability, emits a normalized result.
- [ ] OTCHERE golden path replays successfully end-to-end.
- [ ] Conformance suite covers load/compile/initialize/run/observe/result, capability declaration, normalized output, deterministic replay.
- [ ] CI workflow runs the conformance suite on push/PR.
- [ ] No new file under `itwin-spec/` (the contract from CR-02 is sufficient).
- [ ] No iTwin document references SimPy by name or module.
- [ ] The SimPy import is confined to `isimulator-core/isimulator_core/adapter/simpy_adapter.py`.

## 11. Subsequent CR Dependencies

CR-03 establishes the foundation for:

- **CR-04** Scenario Experiment Framework : uses the adapter to run parameter sweeps across scenarios and replications.
- **CR-05** Simulation Runtime API : wraps the adapter behind an HTTP/gRPC interface.
- **CR-09** Visualization Provider Integrations : adapter results feed visualization providers.
- **CR-06** Simulation Builder / Studio : declarative editor produces specs that the adapter can replay.

## 12. Risk register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| OTCHERE spec declares capabilities the SimPy adapter cannot fully represent | Low | Medium | The spec declares only `process`, `queue`, `resource`, `event` : all native to SimPy. Adapter documents any unsupported feature explicitly via capability declaration. |
| Determinism gap: SimPy event ordering vs numpy RandomState seeding | Medium | Medium | All stochastic operations route through the seeded RandomState; test_replay_determinism.py asserts bit-identical outputs across runs. |
| SimPy version drift breaking the adapter | Low | Low | Adapter pins `simpy>=4.0,<5.0` in `pyproject.toml`; CI pins exact version. |
| CI runner Python version mismatch | Low | Low | `.github/workflows/test.yml` pins Python 3.12. |
| Pull-request reviewer unavailability | High | Low | Solo-contributor repo; `gh pr merge --admin` after explicit `merge` instruction. Documented in `openspec/POST-HOC-REVIEWS.md`. |

## 13. Tranche plan

CR-03 is delivered as two PRs:

1. **PR-1 (proposal)** : adds `openspec/changes/ISIM-CR-03/{proposal.md, delta-isimulator-core.md}` and updates `change-requests/STATUS.md`. Merge on `merge`.
2. **PR-2 (implementation)** : opens after PR-1 merges. Adds the assets listed in §7 (A through K). Merge on `merge`.

PR-1 follows pick 1b-ii body style: maps each section of the CR to the artifact it lands. PR-2's body quotes PR-1's `delta-isimulator-core.md` line items.
