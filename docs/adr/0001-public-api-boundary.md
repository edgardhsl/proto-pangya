# ADR-0001: Keep the initial public API small

- Status: Accepted
- Date: 2026-08-31

## Context

The earlier design exposed conceptual packages for codec, registry, crypto, compression, anomalies, evidence and logging. That would create a large pre-v1 compatibility surface and constrain internal optimization.

## Decision

Initially expose only the root `pangya` package and `packet` package. Keep wire framing, crypto, compression, dispatch and detection implementation under `internal/`.

Promote another package only after a concrete consumer-facing use case proves that its API is independently valuable and stable.

## Consequences

The module is easier to consume, internal refactors remain cheap and v1 compatibility obligations are smaller. Extension points must be deliberately designed through the two public packages rather than by importing internals.
