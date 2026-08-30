<div align="center">

# proto-pangya

**PangYa protocol toolkit for Go.**

Decode, encode and inspect PangYa network packets through a reusable, application-agnostic protocol library.

[![Go](https://img.shields.io/badge/Go-library-00ADD8?logo=go&logoColor=white)](https://go.dev/)
[![Status](https://img.shields.io/badge/status-early%20development-orange)](#project-status)

</div>

> [!IMPORTANT]
> This project is under active protocol research. The public API is not stable yet and should not be considered production-ready.

## About

`proto-pangya` is an independent Go library for the PangYa wire protocol. It provides protocol primitives that can be embedded in any compatible Go software without depending on a particular application architecture or implementation.

Its scope is deliberately limited to the wire protocol and related infrastructure. Domain-specific behavior and policy remain outside the library.

## Features

- TCP stream framing with support for fragmented and coalesced frames
- Client-to-server and server-to-client packet encryption/decryption
- Protocol-compatible compression/decompression
- Bounds-checked binary packet readers and writers
- Typed packet encoding and decoding
- Protocol routing by service, direction and supported client version
- Detection and reporting of malformed or suspicious protocol traffic
- Consumer-controlled structured logging and opt-in packet tracing
- Compatibility validation through golden vectors, fuzzing, packet captures and real clients
- Allocation-conscious hot paths backed by benchmarks

## Installation

The module is still under development. Once the first usable version is released, it will be installable as a regular Go dependency:

```bash
go get github.com/edgardhsl/proto-pangya
```

## Architecture

```text
Network bytes
     │
     ▼
┌─────────────────────┐
│     proto-pangya    │
│                     │
│  stream framing     │
│  validation         │
│  crypto             │
│  compression        │
│  packet codec       │
└──────────┬──────────┘
           │
           ▼
     Typed packets
```

Incoming data is framed, validated, decrypted and decompressed before packet decoding. Outgoing packets follow the inverse pipeline.

The package is designed as a standalone dependency. It does not depend on or expose concepts from any particular software consuming it.

## Protocol compatibility

PangYa protocol information comes from reverse engineering and community research, so no single source is treated as authoritative.

Compatibility is established by comparing reproducible behavior across sources such as:

- real PangYa client traffic and packet captures;
- observed ProjectG behavior;
- [PangDox](https://packets.pangdox.com/);
- PangCrypt and other public protocol implementations;
- existing PangYa implementations;
- independently generated test vectors.

When references disagree, reproducible wire behavior and real client captures take precedence.

## Reliability and security

Network data is untrusted input. Protocol code is designed around strict bounds checking, explicit size limits, overflow-safe parsing, decompression limits and controlled errors.

Malformed input must never result in an out-of-bounds access, uncontrolled allocation, deadlock or panic.

Protocol anomalies are exposed to the consumer as structured information. Policy and enforcement are deliberately outside the scope of this package.

PangYa's original packet encryption is implemented for interoperability and must not be considered modern secure transport.

## Observability

Logging is controlled by the consumer. The library does not require a specific logging framework.

Logging is designed around `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` and `TRACE` levels, with optional component and context overrides. Raw packet traces and hex dumps are explicitly opt-in, and sensitive fields remain redacted regardless of verbosity.

## Testing

Protocol compatibility requires more than encode/decode round trips. The project is designed to combine:

- unit and property tests;
- golden protocol vectors;
- differential tests against independent implementations;
- fuzz testing of parsers and stream boundaries;
- replay of captured sessions;
- arbitrary TCP fragmentation/coalescing tests;
- Go race detection;
- allocation and latency benchmarks;
- integration tests with supported PangYa clients.

## Project status

The project is currently building its protocol foundation. Development is progressing through binary primitives, framing, crypto, compression and the high-level codec before expanding typed packet coverage across PangYa services and client versions.

Until the first tagged release, APIs and package boundaries may change without notice.

## Contributing

Contributions are welcome, especially protocol research backed by reproducible evidence.

Changes to wire behavior should include a packet capture, golden vector, test fixture or another independently verifiable reference whenever possible. Parser changes should include malformed-input coverage, and performance-sensitive changes should include benchmarks.

For larger changes, open an issue before implementation so the protocol behavior and API design can be discussed first.

## License

A license has not been selected yet. Until a `LICENSE` file is added, standard copyright rules apply.

## Disclaimer

This is an independent interoperability and protocol-research project. PangYa and related names and assets are property of their respective owners. This project is not affiliated with or endorsed by the original developers or publishers of PangYa.
