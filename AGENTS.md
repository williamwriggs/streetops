# StreetOps Development Instructions

StreetOps is an open computational framework for integrating street networks and urban operations into adaptive urban design.

Its purpose is **not** to automate urban design. Its purpose is to make the analytical translation between operational evidence and potential design interventions explicit, inspectable, reproducible, and extensible.

These instructions govern code-generation and refactoring work in this repository, including work performed by Codex or other coding agents.

## Architectural principles

1. **Compose mature upstream libraries rather than reimplement them.**
   - Use OSMnx for OpenStreetMap acquisition, topology, routing, and network utilities.
   - Use NetworkX-compatible graph objects rather than inventing a competing graph engine.
   - Use GeoPandas and Shapely for spatial data structures and geometry operations.
   - Use PySAL for spatial statistics where appropriate.
   - Use MovingPandas for trajectory-specific operations when needed.
   - Use momepy for morphology measures when needed.
   - Use established GTFS/CDS/MDS tooling and schemas where practical.

2. **GeoPandas and NetworkX interoperability is mandatory.**
   StreetOps objects may organize or enrich these structures, but researchers must always be able to access and use the underlying graph and GeoDataFrames directly.

3. **The package owns the translation layer.**
   StreetOps should focus on the relationship between:
   - street structure;
   - observed urban operations;
   - operating rules;
   - public priorities;
   - operational diagnoses;
   - candidate design interventions;
   - scenario evaluation.

4. **Time is a first-class dimension.**
   Street functions and operating conditions may vary by hour, operating period, weekday, season, event, or scenario. Avoid static-only assumptions in data models.

5. **Preserve evidence provenance and uncertainty.**
   Derived outputs should retain information about source, transformation, temporal coverage, spatial resolution, confidence, and whether inputs are observed, synthetic, simulated, or inferred.

6. **Public priorities must be explicit.**
   Accessibility, pedestrian safety, transit reliability, emergency access, freight needs, ecological functions, and civic uses must not be hidden inside opaque optimization objectives.

7. **Translation must initially be transparent and deterministic.**
   Design translation in the core package should use explicit rules and constraints. Do not use an LLM or opaque recommender to generate interventions in the core workflow.

8. **Do not infer universal thresholds.**
   Diagnostics should return continuous measures unless a threshold is explicitly supplied by a researcher, policy profile, or validated standard.

9. **Return data, not only prose or plots.**
   Diagnostics, rules, interventions, and scenario results should be inspectable as typed objects and/or tabular or geospatial outputs.

10. **Keep the public API small.**
    Do not add abstractions merely because they are imaginable. New public classes and methods should solve a demonstrated StreetOps use case.

## Core package contract

StreetOps follows this conceptual pipeline:

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

The initial public abstractions should remain centered on:

- `StudyArea`
- `EventSet`
- `OperatingPeriod`
- `OpsGraph`
- `PrioritySet`
- `Diagnosis`
- `Rule`
- `Intervention`
- `Scenario`

Do not expand this list without a concrete reference workflow and tests.

## OpsGraph principles

`OpsGraph` is the central StreetOps coordination object. It should **compose**, not subclass, NetworkX/OSMnx graphs.

An OpsGraph may contain:

- a `StudyArea`;
- the underlying OSMnx/NetworkX network;
- operational spatial units such as curb segments or zones;
- one or more `EventSet` objects;
- operating rules;
- public priorities;
- diagnoses;
- interventions;
- candidate scenarios.

External code must remain able to access the underlying network and GeoDataFrames directly.

## Translation rule principles

A design-translation `Rule` should explicitly encode:

- condition(s);
- required evidence;
- protected priorities;
- physical or policy constraints;
- candidate interventions;
- evaluation measures.

Every generated intervention should be traceable to the rule, evidence, diagnoses, priorities, and constraints that produced it.

StreetOps should return candidate scenarios rather than pretending there is one universally optimal design.

## Visualization principles

StreetOps visualizations should be analytically meaningful, restrained, and reproducible.

Core visual families should include:

- street/network context;
- mapped operations;
- Operations Rose / temporal operational signature;
- diagnosis maps;
- design-scenario comparisons.

Visualizations should support publication-quality raster and vector export. They should not require proprietary basemaps and should not hard-code any city, mode, or operator.

## Reference workflow

The initial canonical case is Hayes Valley, San Francisco, using privacy-safe or synthetic passenger pick-up/drop-off data where necessary.

The v0.1 reference workflow should demonstrate:

```text
StudyArea
→ EventSet
→ OpsGraph
→ temporal/spatial diagnosis
→ Operations Rose
→ PrioritySet
→ deterministic translation
→ alternative design scenarios
```

Synthetic and observed inputs must always be clearly distinguished.

## Coding standards

- Use Python with a `src/` package layout.
- Prefer `uv` for dependency and command workflows.
- Use type hints for public APIs.
- Use `pytest` for tests.
- Use `ruff` for linting/format checks.
- Avoid hidden network calls in tests.
- Use deterministic fixtures for visual and diagnostic tests.
- Document public APIs and behavior changes.
- Keep optional analytical ecosystems as optional dependency groups where practical.

## Definition of success for v0.1

StreetOps v0.1 is successful when a researcher can reproducibly move from an OSM street network and an event dataset to:

1. a `StudyArea`;
2. an `EventSet`;
3. an `OpsGraph`;
4. an Operations Rose and operational map;
5. quantitative spatial/temporal diagnoses;
6. explicit public-priority constraints;
7. traceable candidate design interventions;
8. evaluated alternative scenarios;

through one documented reference workflow.

## Guardrail

**StreetOps owns the translation between street structure, observed operations, public priorities, and adaptive design; it should not reimplement the computational primitives already provided by OSMnx, GeoPandas, NetworkX, PySAL, MovingPandas, or momepy.**
