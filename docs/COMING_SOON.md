# Coming Soon

**Document Version**: 1.9 | **Last Updated**: March 2026

## Transient Alert System

A transient alert ingestion, filtering, and follow-up system is under active development in a separate repository. Once integrated with the Science Scheduler, it will enable automated and coordinated follow-up of astronomical transients across connected observatories.

### What It Does

The Vera C. Rubin Observatory began issuing public alerts in February 2026, detecting hundreds of thousands of changes in the sky each night — supernovae, cataclysmic variables, asteroids, and unknown phenomena. The transient alert system bridges the gap between these alert streams and your observatory's observation queue.

### Key Capabilities (In Development)

- **Multi-broker alert ingestion** — Ingests and normalizes alerts from ALeRCE, Fink, Lasair, ANTARES, and TNS (Transient Name Server for spectroscopic classifications)
- **Multimessenger events** — NASA GCN (Kafka stream) integration for gamma-ray bursts, gravitational wave counterparts, and neutrino events
- **Intelligent scoring** — Three-axis evaluation engine combining science value, urgency (time-decay models for 25+ transient types), and observability from your location
- **Cross-matching** — Deduplication across brokers with 2-arcsecond radius matching and priority rules (TNS name > Rubin dia_object_id > position)
- **Observation planning** — Template-based exposure plans by transient type and brightness, with recommended filters and cadence
- **Tonight's Queue** — A dashboard showing the highest-priority targets observable tonight, with altitude plots, light curves, and classification breakdowns
- **Explorer** — Filterable alert browser for discovering and researching transient candidates

### Planned Integration

The transient system is designed to integrate with the Science Scheduler as an automated target source. When connected, it will be able to:

- Submit follow-up observations directly to the scheduler queue
- Coordinate multi-observatory follow-up for time-critical events
- Track observation completions and update transient records with results

### Current Status

The system is in active development (Phase 6) with core broker ingestion, scoring, observation planning, and a web dashboard substantially complete. Integration with the Science Scheduler will be documented here when available.

---

For a history of all documented features and changes, see the [Changelog](CHANGELOG.md).

---

Have a suggestion for documentation? [Request it here](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md).
