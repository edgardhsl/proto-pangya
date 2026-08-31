# ADR-0003: Decouple evidence capture from persistence

- Status: Accepted
- Date: 2026-08-31

## Context

Anomaly evidence may need durable storage, but hidden storage calls or background queues inside decoding would introduce I/O, lifecycle, backpressure and failure coupling into the protocol hot path.

## Decision

The decoder may capture bounded `Evidence` and return it in results. The library may define a minimal `EvidenceStore` contract for interoperability, but decoding does not invoke storage implicitly.

The core creates no evidence worker, queue or background goroutine.

## Consequences

Protocol decoding remains deterministic and independent of external systems. Callers choose synchronous/asynchronous persistence, retention and infrastructure without changing wire behavior.
