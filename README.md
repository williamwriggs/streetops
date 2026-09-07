# **Street**Ops

### An open computational framework for integrating street networks and urban operations into adaptive urban design

**StreetOps** is a proposed open-source Python framework for connecting the physical structure of streets with the activities, rules, flows, conflicts, and public priorities that shape how streets operate.

> **Status: Research preview (v0.1.0).** StreetOps is currently a framework specification and early reference implementation. The API shown below describes the intended interface; not all modules or functions are yet implemented.

StreetOps is intended to complement—not replace—the existing computational urbanism ecosystem. It builds conceptually on tools such as OSMnx, GeoPandas, MovingPandas, PySAL, and momepy, while adding a missing design-translation layer. The framework is designed to integrate street networks, curb segments, transit stops, building entrances, event points, trajectories, accessibility zones, operating rules, and design interventions through a common set of abstractions, algorithms, and visualizations.

The core workflow is:

> **Acquire → Integrate → Diagnose → Translate → Generate Scenarios → Evaluate**

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
    "Hayes Valley, San Francisco, California",
    network_type="all",
)

ops = so.OpsGraph.from_study_area(study)
ops.add_curbs("cds_curbs.json")
ops.add_events("pudo_events.csv", event_type="passenger_exchange")
ops.add_transit("gtfs.zip")
ops.set_priorities([
    "accessible_boarding",
    "transit_reliability",
    "pedestrian_safety",
])

diagnosis = ops.diagnose(
    spatial_unit="curb_cluster",
    temporal_unit="operating_period",
    measures=["concentration", "capacity", "walking_burden", "conflict"],
)

scenarios = ops.translate(
    interventions=[
        "dynamic_loading",
        "protected_waiting_area",
        "overflow_zone",
        "digital_wayfinding",
    ],
    preserve_provenance=True,
)

report = scenarios.evaluate()
```

The API above is illustrative and specifies the intended user-facing contract. It does not document completed software.

## Foundational paper

**StreetOps: An Open Computational Framework for Integrating Street Networks and Urban Operations into Adaptive Urban Design**

William Riggs, University of San Francisco.

SocArXiv preprint: https://osf.io/preprints/socarxiv/g3z7j_v1

The paper defines the research problem, the StreetOps workflow, the OpsGraph abstraction, design-translation logic, visualization system, governance principles, and an initial curb-operations proof of concept.

The preprint DOI will be added when assigned.

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

StreetOps v0.1.0 is an **initial research release** focused on specification, architecture, governance, and reproducible reference workflows. The package roadmap separates planned functionality from implemented functionality.

Initial development is focused on:

1. defining the data model and OpsGraph schema;
2. developing operational signatures and geographic renderings;
3. implementing event, curb, transit, and network adapters;
4. creating transparent diagnosis-to-intervention rules;
5. building reproducible benchmark cases and example notebooks.

See [`ROADMAP.md`](ROADMAP.md) for staged implementation and [`docs/architecture.md`](docs/architecture.md) for the current package contract.

## Citation

Please cite the software using the metadata in [`CITATION.cff`](CITATION.cff). The preferred citation currently points to the SocArXiv preprint; its DOI will be added when assigned.

## License

StreetOps is released under the GNU General Public License v3.0. See [`LICENSE`](LICENSE).

## Contributing

StreetOps welcomes contributions from researchers, planners, designers, engineers, public agencies, mobility providers, and open-source developers interested in computational street design and operational urbanism.

Because the project is in an early research stage, contributors should distinguish implemented functionality from proposed interfaces and avoid introducing opaque or non-reproducible design logic.
