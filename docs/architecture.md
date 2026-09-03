# StreetOps Architecture

## Purpose

StreetOps is an open computational framework for integrating street networks and urban operations into adaptive urban design.

The framework is designed to fill a specific gap in the computational urbanism ecosystem. Existing tools are strong at acquiring street networks, analyzing movement, measuring morphology, detecting spatial patterns, and simulating demand. StreetOps begins after those capabilities: it organizes street structure, operational evidence, rules, public priorities, and design interventions within a reproducible decision workflow.

StreetOps does **not** attempt to automate professional design judgment. It makes that judgment more explicit, inspectable, testable, and transferable.

## Conceptual model

```text
Street network
      +
Urban operations
      +
Rules and public priorities
      ↓
OpsGraph
      ↓
Diagnosis
      ↓
Translation
      ↓
Candidate scenarios
      ↓
Evaluation
```

The computational logic is intentionally compositional. StreetOps should rely on mature upstream tools for their domain primitives and add the missing design-translation layer.

## Technical stack

Core dependencies should remain lean:

- `pandas`
- `geopandas`
- `shapely`
- `networkx`
- `osmnx`
- `numpy`
- `matplotlib`
- `pydantic`
- `pyproj`

Optional analytical ecosystems may be grouped as extras:

- spatial statistics: `libpysal`, `esda`
- trajectories: `movingpandas`
- morphology: `momepy`
- development: `pytest`, `ruff`, `mypy`

StreetOps should not reimplement functionality that already exists robustly in these libraries.

## Proposed repository layout

```text
streetops/
├── pyproject.toml
├── README.md
├── AGENTS.md
├── LICENSE
├── src/
│   └── streetops/
│       ├── __init__.py
│       ├── study.py
│       ├── graph.py
│       ├── events.py
│       ├── periods.py
│       ├── priorities.py
│       ├── rules.py
│       ├── diagnostics.py
│       ├── interventions.py
│       ├── scenarios.py
│       ├── evaluation.py
│       ├── io/
│       │   ├── osm.py
│       │   ├── gtfs.py
│       │   ├── cds.py
│       │   └── generic.py
│       └── viz/
│           ├── network.py
│           ├── operations.py
│           ├── rose.py
│           └── scenarios.py
├── tests/
├── examples/
│   └── hayes_valley/
├── notebooks/
└── docs/
```

This layout is a target, not a requirement to create every file immediately. New modules should be added only when the public API needs them.

## Core abstractions

### StudyArea

`StudyArea` is the geographic container for a StreetOps analysis.

Responsibilities:

- preserve study-area boundary;
- preserve CRS;
- contain or reference the OSMnx/NetworkX street graph;
- expose node and edge GeoDataFrames;
- preserve acquisition metadata and provenance.

Initial constructors:

```python
StudyArea.from_place(...)
StudyArea.from_polygon(...)
StudyArea.from_graph(...)
```

The OSMnx graph must remain directly accessible.

### EventSet

`EventSet` represents operational observations.

Minimum normalized fields:

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

Only `event_id`, `geometry`, `timestamp`, and `event_type` should initially be required.

The object must be mode-agnostic. It should be able to represent passenger PUDO, freight loading, pedestrian observations, micromobility events, transit events, conflicts, violations, or future activity types.

### OperatingPeriod

`OperatingPeriod` defines recurring temporal windows and makes time a first-class component of analysis.

Example:

```python
OperatingPeriod(
    name="evening_peak",
    start="18:00",
    end="22:00",
    days=["fri", "sat", "sun"],
)
```

It should support filtering, aggregation, and scenario assignment.

### OpsGraph

`OpsGraph` is the core StreetOps coordination object.

It should compose:

- `StudyArea`;
- underlying street graph;
- operational spatial units;
- event sets;
- priorities;
- rules;
- diagnoses;
- candidate interventions;
- scenarios.

It should not subclass `networkx.Graph` or an OSMnx graph.

Required interoperability properties should include:

```python
ops.network
ops.nodes
ops.edges
ops.events
```

StreetOps should use upstream functions for spatial-nearest assignment and network calculations where possible.

### PrioritySet

`PrioritySet` represents explicit public priorities and protected functions.

Initial examples:

- pedestrian safety;
- accessible boarding;
- transit reliability;
- emergency access;
- freight access;
- bicycle movement;
- ecological function;
- public-space function.

The first release should avoid pretending that every priority can be meaningfully converted into a numerical weight. Protected constraints should be supported before weighted optimization.

### Diagnosis

A `Diagnosis` is computed evidence about an operating condition.

Common schema:

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

Initial metrics should remain limited to a defensible core:

- event concentration;
- temporal concentration;
- event density;
- nearest-edge assignment;
- walking burden.

Thresholds should be user-supplied or policy-derived, not invented by StreetOps.

### Rule

A `Rule` explicitly connects evidence to candidate interventions.

Each rule should encode:

- condition(s);
- required evidence;
- protected priorities;
- constraints;
- candidate interventions;
- evaluation measures.

Rules should be inspectable and deterministic in the core package.

### Intervention

An `Intervention` is a possible physical, operational, regulatory, digital, temporal, or programmatic change.

Examples:

- dynamic loading;
- protected passenger waiting area;
- overflow assignment;
- shared meeting point;
- curb extension;
- digital wayfinding;
- pricing change;
- enforcement change;
- multimodal loading zone.

Every intervention should retain provenance back to its triggering evidence and rule.

### Scenario

A `Scenario` combines compatible interventions and evaluates them against explicit measures and priorities.

StreetOps should return multiple candidate scenarios rather than one opaque optimum.

## Diagnosis layer

The diagnosis layer should answer questions such as:

- Where does activity concentrate?
- When does demand peak?
- How stable or dispersed is activity?
- How far are desired endpoints from feasible stopping locations?
- Which street or curb elements absorb activity?
- Where do demand and protected public functions conflict?

The diagnostic layer should return reusable data structures before plotting.

### Operational entropy

Where useful, StreetOps may use Shannon entropy to measure concentration or dispersion across categories such as location, time, or event type:

```text
H = -Σ p_i log(p_i)
```

Low entropy indicates concentration or predictability; high entropy indicates dispersion or operational diversity. The package should document exactly which state space is being measured before reporting entropy.

## Translation layer

The translation layer is the main distinguishing contribution of StreetOps.

Translation should initially be rule-based rather than LLM-generated.

Example logic:

```text
IF recurring passenger exchange demand exceeds supplied capacity
AND evidence coverage is sufficient
THEN consider:
    - dynamic loading
    - overflow zone
    - shared meeting point
BUT EXCLUDE alternatives that violate:
    - accessible boarding
    - transit priority
    - pedestrian clear path
```

The output is a set of traceable alternatives, not a claim of universal optimality.

## Evaluation layer

Scenario evaluation should eventually support multiple dimensions:

- safety;
- accessibility;
- transit reliability;
- freight performance;
- pedestrian legibility;
- equity;
- ecological performance;
- operational flexibility;
- cost;
- enforceability;
- implementation feasibility;
- resilience.

The initial release should implement only measures supported by the first reference workflow.

## Visualization architecture

StreetOps should establish a recognizable but restrained figure system.

### Network view

Provides street-network and operational context using clean, publication-quality geography.

### Operations map

Maps events, flows, dwell, concentration, or assigned network elements on top of the street structure.

### Operations Rose

A polar histogram representing temporal or categorical operational signatures.

Example API:

```python
plot_operations_rose(
    ops,
    angle="hour",
    magnitude="event_count",
    category="event_type",
    normalize=True,
)
```

The chart should borrow the visual grammar of orientation roses without copying OSMnx source code.

### Diagnosis map

Maps diagnosis outputs, thresholds, uncertainty, and protected priorities.

### Scenario comparison

Shows existing versus candidate operating/design conditions.

All plotting functions should return Matplotlib axes or figure handles and support PNG, SVG, and PDF output.

## Initial end-to-end API target

```python
import streetops as so

study = so.StudyArea.from_place(
    "Hayes Valley, San Francisco, California"
)

ops = so.OpsGraph.from_study_area(study)

ops.add_events(
    "data/pudo.csv",
    timestamp="timestamp",
    event_type="event_type",
)

ops.set_priorities([
    "pedestrian_safety",
    "accessible_boarding",
    "transit_reliability",
])

diagnosis = ops.diagnose(
    measures=[
        "event_concentration",
        "temporal_concentration",
        "walking_burden",
    ]
)

scenarios = ops.translate()
results = scenarios.evaluate()

so.plot_operations(ops)
so.plot_operations_rose(ops)
so.plot_scenarios(scenarios)
```

This is the target interface for v0.1. It may evolve as implementation reveals better abstractions, but changes should be deliberate and documented.

## Reference case: Hayes Valley

The first canonical example should use Hayes Valley, San Francisco, because it can demonstrate the complete StreetOps logic with passenger pick-up/drop-off operations.

The example should show:

```text
StudyArea
→ EventSet
→ OpsGraph
→ spatial/temporal diagnosis
→ Operations Rose
→ public priorities
→ rule-based translation
→ candidate adaptive curb scenarios
```

Where empirical source data cannot be publicly distributed, privacy-safe or synthetic fixtures should be used and labeled explicitly.

## Validation strategy

Validation should occur at three levels.

### Computational validation

- deterministic tests;
- schema validation;
- CRS handling;
- temporal filtering;
- spatial joins;
- provenance preservation;
- reproducible diagnostics.

### Construct validation

Determine whether StreetOps measures correspond to operational conditions they are intended to represent.

### Decision validation

Compare generated candidate interventions with practitioner judgment, design standards, pilot outcomes, and public priorities.

## What StreetOps should not become

StreetOps should not become:

- another general-purpose GIS;
- another traffic simulator;
- another routing engine;
- a black-box AI urban designer;
- a proprietary data model that traps users;
- a collection of city-specific scripts;
- an ever-growing set of abstractions without reference cases.

Its defensible contribution is the common computational language connecting street structure, operations, priorities, diagnosis, and adaptive design.
