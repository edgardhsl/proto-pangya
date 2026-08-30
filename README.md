# proto-pangya

A Go library for working with the PangYa network protocol.

`proto-pangya` aims to provide a small, reusable, and well-tested protocol layer for PangYa servers, packet tools, proxies, and protocol research. It is designed as an independent dependency and intentionally contains no game-server business logic.

> **Status:** early development. The API and supported protocol surface are not stable yet.

## What it will provide

- TCP stream framing that correctly handles fragmented and coalesced frames
- PangYa client/server packet encryption and decryption
- Protocol-compatible compression and decompression
- Safe binary packet readers and writers
- Typed packet encoding and decoding
- Packet routing by service, direction, and client version/season
- Protocol-level anomaly reporting for malformed or suspicious traffic
- Configurable structured logging and opt-in packet tracing
- Golden-vector, fuzz, differential, replay, and real-client compatibility tests
- Performance-focused hot paths with measurable allocation and latency budgets

## Design

The library sits between the network transport and the application using it:

```text
TCP stream
    ↓
Frame decoder
    ↓
Decrypt
    ↓
Decompress
    ↓
Packet codec
    ↓
Application
```

The reverse path is used for outgoing packets.

`proto-pangya` owns protocol concerns such as framing, binary encoding, cryptographic transformations, compression, packet metadata, and wire-format validation.

The consuming application remains responsible for sessions, authentication policy, players, rooms, gameplay, persistence, authorization, anti-cheat decisions, rate limiting, and other domain rules.

This dependency direction is intentional:

```text
pangya-server ─────► proto-pangya
packet-tool   ─────► proto-pangya
proxy         ─────► proto-pangya
```

`proto-pangya` must never depend on a specific emulator.

## Safety

All network input is treated as untrusted.

Protocol parsers are expected to use strict bounds checking, size limits, overflow-safe offset calculations, decompression limits, controlled handling of unknown packets, and typed errors instead of panics.

The original PangYa encryption is implemented for protocol compatibility and must not be treated as modern secure transport.

## Observability

Logging is controlled by the consuming application rather than by a hard dependency on a logging framework.

Planned verbosity levels are:

```text
OFF · ERROR · WARN · INFO · DEBUG · TRACE
```

Verbose packet tracing and hex dumps are opt-in and separate from normal logging. Sensitive fields must remain redacted regardless of log level.

Protocol anomalies are reported to the caller; enforcement decisions remain outside the library.

## Compatibility strategy

Protocol behavior is validated through multiple independent sources rather than assuming a single implementation is authoritative.

The project uses, when available:

- observed ProjectG client behavior
- real packet captures
- PangDox documentation
- PangCrypt and other public implementations
- existing PangYa emulators
- independently generated golden vectors

When references disagree, reproducible wire behavior and real captures take precedence.

## Testing

Protocol compatibility is not considered proven by round-trip tests alone.

The test suite is intended to combine unit tests, golden vectors, property tests, fuzzing, differential tests, TCP-fragmentation tests, captured-session replay, race detection, benchmarks, and integration tests against real supported clients.

A malformed byte sequence must produce either a valid result or a controlled error — never an out-of-bounds access, unbounded allocation, deadlock, infinite loop, or panic.

## Installation

The module is not ready for production use yet. Once the first usable API is released:

```bash
go get github.com/edgardhsl/proto-pangya
```

## Development roadmap

Initial development will focus on the protocol foundation before expanding packet coverage:

1. Binary reader/writer primitives
2. Client/server framing and TCP stream decoding
3. Encryption and decryption
4. Compression and decompression
5. High-level codec and session protocol context
6. Packet registry and typed packets
7. Logging, tracing, and protocol anomaly API
8. Capture replay and real-client compatibility suites
9. Progressive Auth, Game, Message, Ranking, and common packet coverage

## Contributing

Protocol changes should be evidence-driven. Wire-format changes should include a reproducible fixture, capture, golden vector, or another independently verifiable reference whenever possible.

Parser changes should include malformed-input coverage. Performance-sensitive changes should include benchmarks.

## License

No open-source license has been selected yet. Until a license file is added, standard copyright rules apply.

## Disclaimer

This is an independent interoperability and protocol-research project. PangYa and related names and assets belong to their respective owners. This project is not affiliated with or endorsed by the original developers or publishers of PangYa.
