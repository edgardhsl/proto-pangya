# Architecture

## Goal

`proto-pangya` is a standalone Go module for PangYa wire-protocol concerns. It must be useful without knowledge of any particular application, emulator, persistence layer, UI or deployment model.

## Repository layout

The target structure is intentionally small at the public boundary:

```text
proto-pangya/
├── packet/                  # public packet primitives
├── internal/
│   ├── wire/                # stream reconstruction + frame codecs
│   ├── crypt/               # client/server wire transforms
│   ├── lzo/                 # protocol compression implementation
│   ├── registry/            # immutable dispatch
│   ├── detect/              # anomaly detection internals
│   └── evidence/            # evidence capture internals
├── testdata/                # cross-pipeline captures/vectors/manifests
├── docs/
├── doc.go
├── decoder.go
├── encoder.go
├── config.go
├── profile.go
├── service.go
├── limits.go
├── result.go
├── anomaly.go
├── evidence.go
├── errors.go
└── trace.go
```

Only packages that have a genuine stable consumer-facing responsibility should become public. Do not add `common`, `utils`, `types`, `api` or similar catch-all packages.

## Dependency direction

```text
caller
  ↓
public pangya / packet API
  ↓
internal wire/transforms/dispatch
```

Internal packages MUST NOT import application-specific types.

## High-level flow

```text
bytes ─► Decoder ─► packet.Packet
                  ├► Anomalies
                  └► Evidence

packet.Packet ─► Encoder ─► bytes
```

The library does not own listener lifecycle, sockets, connection workers, database I/O or domain policy.

## Interfaces

Prefer concrete types. Introduce interfaces at true extension boundaries, not preemptively. Generic `Compressor`, `Cipher` or repository abstractions SHOULD NOT exist until more than one implementation/use case justifies them.

## Internal by default

Wire crypto, compression implementation, frame codecs and registry are internal initially. This preserves freedom to optimize/refactor them without expanding the v1 compatibility surface.
