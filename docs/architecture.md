# Architecture

## Purpose

`proto-pangya` is a standalone Go module responsible only for PangYa wire-protocol concerns. It must be usable without knowledge of any particular application, emulator, persistence layer, UI, or deployment model.

## Dependency rule

The package is a dependency. It never imports or models concepts owned by its consumers.

```text
Consumer
   │
   ▼
proto-pangya
```

Public APIs must use protocol-oriented types and neutral extension interfaces.

## Core responsibilities

The library owns:

- binary packet primitives;
- TCP stream framing;
- client/server frame validation;
- protocol encryption and decryption;
- protocol compression and decompression;
- packet envelopes and typed codecs;
- routing by service, direction, and protocol version;
- protocol-level anomaly detection;
- structured observability hooks;
- evidence generation for anomalies;
- compatibility metadata and typed protocol errors.

The library does not own domain state, authorization policy, punishment decisions, user persistence, gameplay, account management, network listener lifecycle, or application-specific metrics.

## Logical layers

```text
network bytes
    │
    ▼
stream decoder
    │
    ▼
frame codec / validation
    │
    ▼
crypto
    │
    ▼
compression
    │
    ▼
packet envelope
    │
    ▼
typed packet codec
    │
    ├── anomaly events
    ├── evidence records
    └── observability events
```

Outgoing packets traverse the inverse path.

## Package boundaries

The intended package structure is:

```text
packet/       binary primitives and packet abstractions
frame/        frame codecs and stream reconstruction
crypto/       wire-compatible cryptographic transformations
compression/  protocol compression
protocol/     direction, service, version, session context
codec/        high-level encode/decode orchestration
registry/     immutable packet definitions and lookup
packets/      typed packet implementations
anomaly/      anomaly types and detection results
evidence/     evidence records, policies, and storage interfaces
logging/      neutral logging/tracing contracts
internal/     implementation details not part of the public API
```

Exact names may change before v1, but the responsibility boundaries should remain stable.

## Public API design

Public APIs should be small, explicit, idiomatic Go, and interface-light. Interfaces are introduced primarily at external boundaries such as logging, evidence storage, compression backends, or test oracles.

Runtime reflection should not be required for critical packet decoding paths. Packet parsing should favor explicit generated or handwritten codecs.

## Ownership and buffers

APIs must make buffer ownership clear. Zero-copy views may be used internally or exposed only where lifetime rules are unambiguous. Data that must outlive a decode call must own its bytes.

Temporary buffer reuse must not allow a caller to observe later mutation.

## Concurrency

Codec state that is immutable should be safe for concurrent use. Mutable protocol/session context must document whether it is safe for concurrent access.

The library must not create unbounded goroutines or queues internally. Potentially asynchronous extension points must have bounded or caller-controlled behavior.

## Errors

Malformed network input is a normal protocol condition and must return typed errors rather than panic.

Error categories should distinguish at least framing, bounds, crypto, compression, packet decoding, unsupported protocol data, and internal misuse.

## Versioning

Protocol-version differences must be modeled explicitly rather than scattered as conditionals throughout packet handlers. Common wire structures should be shared; true differences should live behind version-aware definitions or codecs.
