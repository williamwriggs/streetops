# StreetOps Roadmap

StreetOps is being developed as an open computational framework for integrating street networks and urban operations into adaptive urban design.

The roadmap is intentionally conservative. The goal is to prove a small, reproducible core before expanding into additional modes, optimization methods, standards, or interfaces.

## v0.1 goal

StreetOps v0.1 is successful when a researcher can reproducibly move from an OpenStreetMap street network and an operational event dataset to:

1. a `StudyArea`;
2. an `EventSet`;
3. an `OpsGraph`;
4. an operational map and Operations Rose;
5. quantitative spatial and temporal diagnoses;
6. explicit public-priority constraints;
7. traceable candidate design interventions;
8. evaluated alternative scenarios;

through one documented Hayes Valley reference workflow.

## Phase 0 — repository specification

Status: **current**

Deliverables:

- `README.md` with project scope and foundational-paper framing;
- `AGENTS.md` with Codex and contributor guardrails;
- `docs/architecture.md` defining the package contract;
- `ROADMAP.md` defining staged development;
- initial issue structure and contribution workflow.

Exit criteria:

- the project clearly distinguishes implemented functionality from roadmap functionality;
- architecture is narrow enough to support an MVP;
- no code claims are made that cannot yet be demonstrated.

## Phase 1 — package scaffold

Objective: create a modern, testable Python package without implementing the full conceptual framework.

Tasks:

- adopt `src/` layout;
- create `pyproject.toml`;
- support `uv` workflows;
- add core dependencies;
- add optional dependency groups;
- configure `pytest`;
- configure `ruff`;
- optionally add `mypy` once the initial API stabilizes;
- expose package metadata and version;
- verify `import streetops as so`.

Exit criteria:

```bash
uv run pytest
uv run ruff check .
```

both succeed and a smoke import works.

## Phase 2 — geographic foundation

Objective: implement the physical street-network side of StreetOps by composing OSMnx rather than duplicating it.

### StudyArea

Implement:

```python
StudyArea.from_place(...)
StudyArea.from_polygon(...)
StudyArea.from_graph(...)
```

Required behavior:

- preserve original OSMnx/NetworkX graph;
- expose node and edge GeoDataFrames;
- preserve CRS;
- preserve source and acquisition metadata;
- support fixture-based tests without live network calls.

Exit criteria:

A test study area can be loaded and external NetworkX/GeoPandas functions can operate directly on its data.

## Phase 3 — operational foundation

Objective: make operations and time first-class data.

### EventSet

Minimum schema:

```text
event_id
geometry
timestamp
event_type
duration
actor_type
source
confidence
```

Required constructors:

```python
EventSet.from_dataframe(...)
EventSet.from_csv(...)
```

Validation:

- geometry;
- timestamps;
- CRS;
- duplicate IDs;
- required values.

### OperatingPeriod

Support recurring windows such as:

```python
OperatingPeriod(
    name="evening_peak",
    start="18:00",
    end="22:00",
    days=["fri", "sat", "sun"],
)
```

Exit criteria:

An EventSet can be filtered, aggregated, and compared across operating periods.

## Phase 4 — OpsGraph v0.1

Objective: create the central StreetOps coordination object.

Implement:

```python
OpsGraph.from_study_area(...)
ops.add_events(...)
ops.network
ops.nodes
ops.edges
ops.events
```

Initial relationship behavior:

- assign event records to nearest street/network elements using upstream spatial-nearest tools;
- retain original event geometry;
- retain assigned network element;
- preserve source/provenance.

Do not subclass NetworkX.

Exit criteria:

A single object can coordinate a StudyArea and one or more EventSets without reducing interoperability.

## Phase 5 — diagnostics v0.1

Objective: implement the smallest analytical layer needed by the Hayes Valley case.

Initial metrics:

- event concentration;
- temporal concentration;
- event density;
- nearest-edge assignment;
- walking burden.

Common diagnosis schema:

```text
spatial_unit
operating_period
metric
value
threshold
status
confidence
source
```

Important constraints:

- do not invent thresholds;
- return continuous values by default;
- preserve provenance;
- distinguish synthetic, observed, simulated, and inferred data.

### Operational entropy

Implement only after the state space is explicitly defined. Potential dimensions:

- time;
- location;
- event type.

Exit criteria:

Synthetic fixtures produce deterministic diagnoses with unit tests.

## Phase 6 — visualization v0.1

Objective: create a recognizable StreetOps visual language tied to analytical outputs.

Implement:

```python
plot_network()
plot_operations()
plot_operations_rose()
plot_diagnosis()
```

### Operations Rose

Initial parameters:

```text
angle = hour or operating period
magnitude = event count, duration, or supplied numeric field
category = optional event type
normalize = bool
```

Requirements:

- Matplotlib based;
- clean white background;
- minimal chart furniture;
- return figure/axes handles;
- deterministic for tests;
- support PNG/SVG/PDF;
- no proprietary basemap requirement;
- no city-specific hard coding.

Exit criteria:

The Hayes Valley example can generate a network map, operations map, and Operations Rose from the public API.

## Phase 7 — public priorities and translation

Objective: implement StreetOps' defining design-translation layer.

Objects:

```text
Priority
PrioritySet
Rule
Intervention
Scenario
```

Each `Rule` must explicitly encode:

- conditions;
- required evidence;
- protected priorities;
- constraints;
- candidate interventions;
- evaluation measures.

Initial example logic:

```text
IF recurring passenger demand exceeds capacity
THEN consider dynamic loading, overflow assignment, or shared meeting point
BUT exclude alternatives that violate accessible boarding, transit priority,
or pedestrian clear path.
```

Requirements:

- deterministic core logic;
- no LLM-generated interventions;
- every intervention traceable to rule + evidence + diagnosis;
- return candidate scenarios rather than one asserted optimum.

Exit criteria:

Given a tested diagnosis and PrioritySet, StreetOps can generate multiple explainable candidate interventions.

## Phase 8 — scenario evaluation

Objective: compare candidate designs without overclaiming optimization.

Initial evaluation should use only measures supported by available data.

Potential dimensions:

- operational performance;
- passenger walking burden;
- accessibility constraints;
- transit conflicts;
- pedestrian conflicts;
- implementation complexity.

Future dimensions may include:

- safety;
- equity;
- freight performance;
- ecological function;
- cost;
- enforcement feasibility;
- resilience.

Exit criteria:

StreetOps can compare candidate scenarios and expose both values and underlying assumptions.

## Phase 9 — Hayes Valley canonical example

Objective: publish the first complete reference implementation.

Directory:

```text
examples/hayes_valley/
```

Workflow:

```text
StudyArea
→ EventSet
→ OpsGraph
→ spatial/temporal diagnosis
→ Operations Rose
→ PrioritySet
→ deterministic translation
→ candidate curb scenarios
→ evaluation
```

Requirements:

- use only distributable data;
- label synthetic data prominently;
- never imply synthetic fixtures are population estimates;
- make the example runnable through one documented command;
- ensure the example exercises public APIs rather than internal shortcuts.

Exit criteria:

A new user can clone the repository and reproduce the full example from documented instructions.

## Phase 10 — pre-v0.1 audit

Before expanding scope:

- review public API consistency;
- remove unnecessary abstractions;
- check duplication of upstream capabilities;
- audit provenance handling;
- audit temporal handling;
- inspect dependency weight;
- improve typing;
- improve tests;
- update README so implemented and planned features are unmistakable.

No major new features should be added during this phase.

## Post-v0.1 expansion

Only after the reference workflow is stable should StreetOps expand into additional modules and cases.

Potential future areas:

### Standards and adapters

- Curb Data Specification (CDS);
- Mobility Data Specification (MDS);
- GTFS / GTFS-Realtime;
- municipal curb inventories;
- structured collision and safety data.

### Operational domains

- freight and delivery;
- transit transfer environments;
- micromobility;
- school arrival/dismissal;
- paratransit and universal access;
- special events;
- emergency access;
- construction;
- charging;
- adaptive public space.

### Analytical capabilities

- PySAL spatial statistics;
- MovingPandas trajectory analysis;
- momepy morphology integration;
- network accessibility;
- uncertainty propagation;
- sensitivity analysis;
- scenario optimization where defensible.

### Benchmark suite

Develop privacy-safe canonical cases for:

- passenger PUDO;
- freight loading;
- transit access;
- school streets;
- special-event operations.

The benchmark suite should become a major research asset, with expected outputs and validation criteria.

## Research milestones

StreetOps development should remain linked to the foundational research agenda.

Milestones include:

1. foundational StreetOps paper;
2. public v0.1 repository release;
3. Hayes Valley reference notebook;
4. first external contributor or replication;
5. first public-agency pilot;
6. validation paper comparing StreetOps output with practitioner design decisions;
7. multi-city operational-signature study;
8. standards integration and broader benchmark release.

## Scope discipline

StreetOps should grow only when a new capability strengthens the central claim:

> StreetOps makes the translation between street structure, observed operations, public priorities, and adaptive design explicit, reproducible, and inspectable.

If a proposed feature does not advance that claim, it probably belongs in an upstream library, an optional plugin, an example, or a different project.
