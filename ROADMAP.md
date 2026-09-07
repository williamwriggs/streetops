# StreetOps Roadmap

StreetOps is being developed as an open computational framework for integrating street networks and urban operations into adaptive urban design.

The roadmap is intentionally conservative. The goal is to prove a small, reproducible core before expanding into additional modes, optimization methods, standards, or interfaces.

## Versioning

`v0.1.0` is the **Initial Research Release**. It establishes the framework specification, architecture, intended public API, governance principles, citation metadata, and implementation roadmap. It should not be interpreted as a completed Python package.

Subsequent `0.x` releases will progressively implement and validate the framework. The first major implementation milestone will be reached when a researcher can reproducibly move from an OpenStreetMap street network and an operational event dataset through the complete Hayes Valley reference workflow:

1. `StudyArea`;
2. `EventSet`;
3. `OpsGraph`;
4. operational map and Operations Rose;
5. quantitative spatial and temporal diagnoses;
6. explicit public-priority constraints;
7. traceable candidate design interventions;
8. evaluated alternative scenarios.

A `v1.0.0` release will be reserved for a stable public framework with a sufficiently mature API for broader research and practitioner use.

## Phase 0 — repository specification

Status: **complete in v0.1.0**

Deliverables:

- `README.md` with project scope and foundational-paper framing;
- `AGENTS.md` with contributor guardrails;
- `docs/architecture.md` defining the package contract;
- `ROADMAP.md` defining staged development;
- citation metadata and licensing;
- explicit separation of implemented functionality from proposed interfaces.

## Phase 1 — package scaffold

Objective: create a modern, testable Python package without implementing the full conceptual framework.

Tasks:

- adopt `src/` layout;
- create `pyproject.toml`;
- support reproducible development workflows;
- add core and optional dependencies;
- configure `pytest` and `ruff`;
- expose package metadata and version;
- verify `import streetops as so`.

Exit criteria: tests and linting succeed and a smoke import works.

## Phase 2 — geographic foundation

Objective: implement the physical street-network side of StreetOps by composing OSMnx rather than duplicating it.

Implement `StudyArea.from_place(...)`, `StudyArea.from_polygon(...)`, and `StudyArea.from_graph(...)` while preserving CRS, source metadata, the original OSMnx/NetworkX graph, and interoperable GeoDataFrames.

## Phase 3 — operational foundation

Objective: make operations and time first-class data.

Implement `EventSet` and `OperatingPeriod`, including validation for geometry, timestamps, CRS, identifiers, provenance, confidence, and recurring temporal windows.

## Phase 4 — OpsGraph

Objective: create the central StreetOps coordination object.

Implement `OpsGraph.from_study_area(...)`, event attachment, network access, spatial assignment, and provenance preservation without subclassing NetworkX.

## Phase 5 — diagnostics

Objective: implement the smallest analytical layer needed by the Hayes Valley case.

Initial metrics include event concentration, temporal concentration, event density, nearest-edge assignment, and walking burden. Diagnostics should preserve provenance, distinguish observed/synthetic/simulated/inferred evidence, and avoid inventing thresholds.

## Phase 6 — visualization

Objective: create a recognizable StreetOps visual language tied to analytical outputs.

Initial outputs include network maps, operations maps, Operations Roses, diagnosis maps, and scenario comparisons. Visualizations should be deterministic, publication-ready, and free of proprietary basemap requirements.

## Phase 7 — public priorities and translation

Objective: implement StreetOps' defining design-translation layer.

Core objects include `Priority`, `PrioritySet`, `Rule`, `Intervention`, and `Scenario`. Translation must be deterministic and inspectable in the core package, with every intervention traceable to evidence, diagnosis, protected priorities, constraints, and a rule.

StreetOps should return multiple candidate scenarios rather than one asserted optimum.

## Phase 8 — scenario evaluation

Objective: compare candidate designs without overclaiming optimization.

Initial evaluation should use only measures supported by available data, including operational performance, walking burden, accessibility constraints, transit conflicts, pedestrian conflicts, and implementation complexity. Additional dimensions can be added as evidence and validation support them.

## Phase 9 — Hayes Valley reference implementation

Objective: publish the first complete end-to-end reference implementation.

The workflow will be:

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
- make the example reproducible from documented instructions;
- exercise public APIs rather than internal shortcuts.

This phase, rather than `v0.1.0`, represents the first complete implementation milestone.

## Phase 10 — implementation audit

Before expanding scope:

- review public API consistency;
- remove unnecessary abstractions;
- check duplication of upstream capabilities;
- audit provenance and temporal handling;
- inspect dependency weight;
- improve typing and tests;
- ensure documentation clearly distinguishes implemented and planned features.

## Broader ecosystem expansion

Only after the reference workflow is stable should StreetOps expand into additional standards, operational domains, analytical capabilities, and benchmark cases.

Potential areas include CDS, MDS, GTFS/GTFS-Realtime, freight, transit transfer environments, micromobility, school streets, paratransit, emergency access, charging, adaptive public space, PySAL statistics, MovingPandas trajectories, momepy morphology, uncertainty propagation, sensitivity analysis, and defensible scenario optimization.

## Research milestones

StreetOps development should remain linked to the foundational research agenda:

1. foundational StreetOps paper and SocArXiv preprint;
2. public `v0.1.0` Initial Research Release;
3. Hayes Valley reference notebook and first complete implementation milestone;
4. first external contributor or replication;
5. first public-agency pilot;
6. validation paper comparing StreetOps output with practitioner design decisions;
7. multi-city operational-signature study;
8. standards integration and broader benchmark release;
9. stable `v1.0.0` public framework.

## Scope discipline

StreetOps should grow only when a new capability strengthens the central claim:

> StreetOps makes the translation between street structure, observed operations, public priorities, and adaptive design explicit, reproducible, and inspectable.

If a proposed feature does not advance that claim, it probably belongs in an upstream library, an optional plugin, an example, or a different project.
