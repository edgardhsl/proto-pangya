# Project documentation

This directory is the technical source of truth for `proto-pangya`. The root README is intentionally concise.

## Structure

### Specification

Externally observable contracts and behavior. These documents use normative language (`MUST`, `MUST NOT`, `SHOULD`, `MAY`) where useful.

| Document | Purpose |
| --- | --- |
| [spec/public-api.md](spec/public-api.md) | Public package boundary and API principles |
| [spec/wire.md](spec/wire.md) | Transport, framing, transforms and packet pipeline |
| [spec/profiles.md](spec/profiles.md) | Protocol profile model and compatibility dialects |
| [spec/errors.md](spec/errors.md) | Error model and partial-result behavior |
| [spec/anomalies.md](spec/anomalies.md) | Structured protocol anomaly contract |
| [spec/evidence.md](spec/evidence.md) | Evidence capture, artifacts, integrity and persistence contract |

### Design

Engineering rationale and implementation constraints.

| Document | Purpose |
| --- | --- |
| [design/architecture.md](design/architecture.md) | Repository/package structure and dependency boundaries |
| [design/lifecycle.md](design/lifecycle.md) | State, concurrency, I/O and ownership lifecycle |
| [design/security.md](design/security.md) | Defensive parsing and resource-safety strategy |
| [design/observability.md](design/observability.md) | `slog`, trace callbacks and diagnostic cost |
| [design/performance.md](design/performance.md) | Allocation, copies, benchmarks and profiling |
| [design/testing.md](design/testing.md) | Unit, golden, differential, replay, fuzz and race testing |

### Research

Evidence and unresolved protocol knowledge. Research is not automatically a public contract.

| Document | Purpose |
| --- | --- |
| [research/methodology.md](research/methodology.md) | How protocol claims become verified behavior |
| [research/sources.md](research/sources.md) | Research inputs and their role |
| [research/compatibility-matrix.md](research/compatibility-matrix.md) | Verified compatibility by profile/capability |
| [research/unknowns.md](research/unknowns.md) | Explicitly unresolved packets, fields and behaviors |

### ADR

Architectural Decision Records capture decisions whose rationale should survive future refactors.

- [ADR-0001 — Public API boundary](adr/0001-public-api-boundary.md)
- [ADR-0002 — Protocol profiles](adr/0002-protocol-profiles.md)
- [ADR-0003 — Evidence storage boundary](adr/0003-evidence-storage.md)
- [ADR-0004 — Observability with standard Go primitives](adr/0004-observability.md)

## Documentation rule

Implementation changes that alter a public contract, compatibility claim, security boundary or major architectural decision MUST update the relevant documentation in the same change.
