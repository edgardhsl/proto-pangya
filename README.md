<div align="center">

# proto-pangya

**A PangYa wire-protocol library for Go.**

Reusable primitives for decoding, encoding, validating and inspecting PangYa network traffic.

[![Go](https://img.shields.io/badge/Go-library-00ADD8?logo=go&logoColor=white)](https://go.dev/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-early%20development-orange)](#status)

</div>

> [!IMPORTANT]
> `proto-pangya` is under active protocol research. The public API and compatibility surface are not stable yet.

## Overview

`proto-pangya` is an independent Go module focused exclusively on the PangYa wire protocol. It is designed to remain application-agnostic and free of domain, persistence, UI, networking-listener, or deployment concerns.

The library will expose a small public API around decoding, encoding, protocol profiles, packets, anomalies, evidence and diagnostics while keeping wire transforms and dispatch internals private.

## Planned capabilities

- TCP stream reconstruction across fragmented and coalesced reads
- client/server frame parsing and validation
- PangYa-compatible encryption and decryption
- protocol compression and decompression
- bounds-safe binary readers and writers
- raw and typed packet support
- protocol profiles for verified wire dialects
- structured protocol anomalies
- bounded anomaly evidence capture
- application-controlled structured logging and opt-in tracing
- golden-vector, differential, replay, fuzz and real-client compatibility validation
- allocation-conscious hot paths backed by benchmarks

## Installation

There is no stable release yet. Once the first usable API is tagged:

```bash
go get github.com/edgardhsl/proto-pangya
```

## Public API direction

The initial public surface is intentionally small:

```text
github.com/edgardhsl/proto-pangya
github.com/edgardhsl/proto-pangya/packet
```

The root package will provide high-level protocol configuration, `Decoder`, `Encoder`, profiles, results, anomalies, evidence and errors. The `packet` package will expose packet primitives required for typed packet extensions.

Implementation details such as framing, cryptographic transforms, compression and dispatch remain internal until a concrete use case justifies a stable public API.

## Architecture

```text
bytes ──► Decoder ──► packet.Packet
                     ├── Anomalies
                     └── Evidence

packet.Packet ──► Encoder ──► bytes

             internal implementation
        wire · crypt · compression · registry
```

`proto-pangya` performs no hidden network I/O and does not create background goroutines as part of normal encode/decode operations.

## Documentation

Detailed specifications, design decisions and protocol research live in [`docs/`](docs/README.md).

The documentation separates:

- **specification** — externally observable contracts;
- **design** — engineering decisions and constraints;
- **research** — evidence, compatibility status and unknown behavior;
- **ADR** — important architectural decisions and their rationale.

## Status

The project is establishing its protocol foundation before expanding typed packet coverage. Supported behavior will be reported through an evidence-based compatibility matrix rather than broad claims such as “Season X supported”.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Protocol behavior changes should be backed by reproducible evidence whenever practical.

## Security

See [SECURITY.md](SECURITY.md) for vulnerability reporting and parser-security expectations.

## License

Licensed under the [MIT License](LICENSE).

## Disclaimer

This is an independent interoperability and protocol-research project. PangYa and related names and assets are property of their respective owners. This project is not affiliated with or endorsed by the original developers or publishers of PangYa.
