# ISIM-CR-03 Delta: specs/isimulator-core/adapter.md (new spec) and isimulator-core module layout

**Target repo:** `isimulator-core` (new module layout) and `itwin-spec/contracts/engine-adapter.md` (no change: contract is normative from CR-02)
**Target version:** isimulator-core 0.2.0
**Source requirements:** ISIM-CR-03 R01-R04

---

## ADDED Requirements

### Requirement: Adapter Base Contract (R01)

`isimulator-core` SHALL publish an `AdapterBase` abstract base class that mirrors the operations table in `itwin-spec/contracts/engine-adapter.md::Operations`. Every concrete adapter SHALL inherit from `AdapterBase` and implement at minimum `load`, `compile`, `initialize`, `run`, `observe`, `result`. Optional operations (`pause`, `resume`, `step`, `terminate`) SHALL be implemented when the underlying engine supports them.

#### Scenario: AdapterBase conformance check
- **GIVEN** a concrete adapter class
- **WHEN** `AdapterBase.register(adapter)` is called at module import time
- **THEN** every required method is implemented or a `TypeError` is raised listing the missing methods

### Requirement: SimPy Reference Adapter (R02)

`isimulator-core` SHALL ship `SimPyAdapter` as the first concrete adapter. It SHALL:

- declare `capability = "discrete-event"`;
- import `simpy` only within this module (architectural boundary preservation);
- route all randomness through a `numpy.random.RandomState` seeded with `execution.random_seed` (deterministic replay);
- emit normalized results per the R09 contract (metrics, observations, provenance);
- pass the conformance suite in `tests/test_simpy_adapter.py`.

#### Scenario: SimPyAdapter loads the OTCHERE golden path
- **GIVEN** `examples/otchere-order-to-cash/simulation.json`
- **WHEN** `SimPyAdapter().load(spec)` is called
- **THEN** the adapter returns an `AdapterHandle` with the parsed process graph and no errors

#### Scenario: SimPyAdapter run completes the horizon
- **GIVEN** an initialized ready state with horizon `{value: 30, unit: "days"}`
- **WHEN** `adapter.run(state)` is called
- **THEN** the simulation completes without exception and `result(state)` returns a `NormalizedResult` with all 6 declared metrics populated

### Requirement: Replay CLI (R03)

`isimulator-core` SHALL publish a `replay.py` CLI that:

- takes an iTwin simulation specification path as a positional argument;
- loads the spec;
- selects an adapter by declared `execution.capability` from the registered adapter set;
- runs the simulation under each declared scenario for the declared replications;
- writes the normalized result to stdout (JSON) or to a path given by `--output`.

#### Scenario: Replay CLI runs OTCHERE end-to-end
- **GIVEN** `python -m isimulator_core.replay ../examples/otchere-order-to-cash/simulation.json`
- **WHEN** the CLI executes
- **THEN** a JSON document with all 3 scenarios (baseline, demand-plus-20, capacity-plus-5) and their 6 metrics each is written to stdout

### Requirement: Conformance Suite (R04)

`isimulator-core/tests/` SHALL contain:

- `test_simpy_adapter.py` covering load/compile/initialize/run/observe/result for the SimPyAdapter;
- `test_replay_determinism.py` asserting that two replays with identical seed produce byte-identical normalized results;
- `test_otchere_golden_path.py` end-to-end against `examples/otchere-order-to-cash/simulation.json`.

A `.github/workflows/test.yml` SHALL run the suite on every push and pull request with `python -m pytest`. SimPy SHALL be pinned (`simpy>=4.0,<5.0`).

#### Scenario: Determinism invariant
- **GIVEN** the OTCHERE spec
- **WHEN** `replay.py` is invoked twice with `execution.random_seed = 42`
- **THEN** the two outputs are byte-identical JSON

---

## Repository layout (new in `isimulator-core`)

```
isimulator-core/
  isimulator_core/
    __init__.py
    adapter/
      __init__.py
      base.py              # AdapterBase (R01)
      simpy_adapter.py     # SimPyAdapter (R02)
    replay.py              # CLI entry (R03)
  tests/
    __init__.py
    test_simpy_adapter.py
    test_replay_determinism.py
    test_otchere_golden_path.py
    fixtures/
      otchere_replay.json  # committed deterministic replay evidence
  tools/
    replay.py              # shim to isimulator_core.replay:main
  .github/
    workflows/
      test.yml
  pyproject.toml
  README.md                # updated to describe the adapter layer
  CHANGELOG.md             # 0.2.0 entry
```

`itwin-spec/` is NOT modified in CR-03; the contract is normative from CR-02.
