# Project documentation

This directory contains the technical specifications for `proto-pangya`.

The README at the repository root is intentionally concise. Detailed protocol behavior, architecture decisions, quality requirements, and implementation constraints belong here.

## Documentation map

| Document | Purpose |
| --- | --- |
| [architecture.md](architecture.md) | Module boundaries, dependency rules, package responsibilities, and data flow |
| [protocol.md](protocol.md) | Wire-protocol model, framing, crypto, compression, packet model, and versioning |
| [security.md](security.md) | Defensive parsing, input limits, trust boundaries, anomaly handling, and sensitive data |
| [observability.md](observability.md) | Logging, tracing, diagnostics, and runtime verbosity configuration |
| [anomalies.md](anomalies.md) | Anomaly detection model, severity, categories, and consumer-facing behavior |
| [anomaly-evidence.md](anomaly-evidence.md) | Evidence capture and storage specification for protocol anomalies |
| [testing.md](testing.md) | Unit, golden, fuzz, differential, replay, race, and real-client validation strategy |
| [performance.md](performance.md) | Allocation, throughput, latency, benchmarking, profiling, and backpressure requirements |
| [compatibility.md](compatibility.md) | How protocol behavior is researched, compared, and accepted across sources and client versions |

## Documentation principles

The documentation follows the same architectural rule as the library itself: `proto-pangya` must remain application-agnostic.

Specifications describe protocol responsibilities, public contracts, extension points, and observable behavior. They must not depend on the architecture, naming, persistence model, or domain rules of any particular consumer.

Implementation details may evolve, but compatibility, safety, and public behavior changes should be reflected in these documents before or alongside the code change.
