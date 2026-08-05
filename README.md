# **Street**Ops

### An open computational framework for integrating street networks and urban operations into adaptive urban design

**StreetOps** is a proposed open-source Python framework for connecting the physical structure of streets with the activities, rules, flows, conflicts, and public priorities that shape how streets operate.

StreetOps is intended to complement—not replace—the existing computational urbanism ecosystem. It builds conceptually on tools such as OSMnx, GeoPandas, MovingPandas, PySAL, and momepy, while adding a missing design-translation layer. The framework is designed to integrate street networks, curb segments, transit stops, building entrances, event points, trajectories, accessibility zones, operating rules, and design interventions through a common set of abstractions, algorithms, and visualizations.

The core workflow is:

> **Acquire → Integrate → Represent → Diagnose → Translate → Evaluate**

StreetOps aims to help researchers and practitioners move reproducibly from questions such as:

- Where and when does operational demand concentrate?
- How do movement, stopping, waiting, loading, and exchange interact with street geometry?
- Which public priorities and physical constraints limit possible responses?
- What physical, regulatory, digital, or programmatic interventions are plausible?
- How do alternative designs perform for safety, accessibility, transit, freight, equity, public life, and resilience?

## Core concept

The proposed core representation is an **OpsGraph**: a temporal and rule-aware structure that links street-network elements with operational activity, public priorities, constraints, evidence, and candidate interventions.

```python
import streetops as so

study = so.StudyArea.from_place(
    "Hayes Valley, San Francisco, California"
)

ops = so.OpsGraph.from_osmnx(study.graph)
ops.add_events("pudo_events.csv")
ops.add_transit("gtfs.zip")
ops.add_priorities([
    "accessible_boarding",
    "transit_reliability",
    "pedestrian_safety",
])

diagnosis = ops.diagnose()
scenarios = ops.translate()
results = scenarios.evaluate()
```

The API above is illustrative. StreetOps is currently being specified through its foundational research paper and reference workflows.

## Foundational paper

**StreetOps: An Open Computational Framework for Integrating Street Networks and Urban Operations into Adaptive Urban Design**

The paper defines the research problem, the StreetOps workflow, the OpsGraph abstraction, design-translation logic, visualization system, governance principles, and an initial curb-operations proof of concept.

## Intended applications

StreetOps is being designed for applications including:

- curb management and passenger pick-up/drop-off;
- transit operations and transfer environments;
- freight, delivery, and loading activity;
- pedestrian and accessibility analysis;
- micromobility and bicycle operations;
- school arrival and dismissal;
- special events and entertainment districts;
- emergency and construction access;
- adaptive public-space and street design.

## Project status

StreetOps is in early framework and package-development stages. Initial work is focused on:

1. defining the data model and OpsGraph schema;
2. developing operational signatures and geographic renderings;
3. implementing event, curb, transit, and network adapters;
4. creating transparent diagnosis-to-intervention rules;
5. building reproducible benchmark cases and example notebooks.

## Citation

A formal citation will be added when the foundational paper is publicly available.

## Contributing

The project will welcome contributions from researchers, planners, designers, engineers, public agencies, mobility providers, and open-source developers interested in computational street design and operational urbanism.
