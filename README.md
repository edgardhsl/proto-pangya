# proto-pangya

> A high-performance, secure, extensible, and test-driven Go library for the PangYa network protocol.

`proto-pangya` is an independent Go library focused on decoding, encoding, framing, encrypting, decrypting, compressing, decompressing, validating, and inspecting PangYa network traffic.

The project is designed to be consumed as a dependency by PangYa server emulators and protocol tooling. It intentionally keeps game rules, persistence, player state, rooms, authentication policy, and server-specific business logic outside the library.

> **Status:** early development / protocol research. The public API is not stable yet.

## Goals

- Provide a clean and idiomatic Go implementation of the PangYa wire protocol.
- Support packet framing independently from TCP read boundaries.
- Implement client-to-server and server-to-client cryptographic transformations.
- Support the compression formats required by the original protocol.
- Provide safe, allocation-conscious binary readers and writers.
- Decode and encode typed packets without coupling the library to a specific emulator.
- Support protocol differences across PangYa seasons and services without spreading version checks through application code.
- Detect malformed or structurally suspicious protocol traffic.
- Expose configurable structured logging and packet tracing without forcing a logging framework on consumers.
- Validate behavior against independent protocol references, real captures, ProjectG behavior, and compatible implementations.
- Keep the hot path measurable through benchmarks and profiling.

## Non-goals

`proto-pangya` is **not** a PangYa game server and will not contain game-domain rules such as player progression, inventory persistence, rooms, matches, economy, anti-cheat decisions, or GM authorization.

Protocol-level anomalies may be detected here, but decisions such as disconnecting, rate-limiting, banning, or applying gameplay security rules belong to the consuming application.

## Design principles

The protocol implementation follows a strict dependency boundary:

```text
Application / Emulator
        │
        ▼
   proto-pangya
        │
        ├── stream framing
        ├── frame validation
        ├── crypto
        ├── compression
        ├── packet codec
        ├── packet registry
        ├── protocol anomalies
        └── observability hooks
```

The library never depends on the server consuming it.

Other applications should also be able to use the package, including packet inspectors, proxies, replay tools, protocol analyzers, debugging tools, and alternative emulators.

## Planned architecture

```text
proto-pangya/
├── packet/          Binary reader/writer and packet primitives
├── frame/           Client/server frame encoding and decoding
├── crypto/          PangYa protocol cryptographic transformations
├── compression/     Protocol compression and decompression
├── protocol/        Service, direction, season and session abstractions
├── codec/           High-level encode/decode pipeline
├── registry/        Packet metadata and typed decoder registry
├── packets/         Typed packet definitions by service/domain
├── anomaly/         Protocol-level anomaly reporting
├── logging/         Logging and trace abstractions
├── internal/        Non-public implementation details
└── testdata/        Golden vectors, captures and protocol fixtures
```

### Receive pipeline

```text
TCP bytes
   ↓
Stream decoder
   ↓
Frame validation
   ↓
Decrypt
   ↓
Decompress when required
   ↓
Packet envelope
   ↓
Typed packet decoder
   ↓
Consumer application
```

### Send pipeline

```text
Typed packet
   ↓
Packet encoder
   ↓
Compress when required
   ↓
Encrypt
   ↓
Frame encoder
   ↓
TCP bytes
```

Framing is deliberately independent from socket reads. A single network read may contain a partial frame, one complete frame, or multiple frames.

## Safety and security

All network input is treated as untrusted.

The implementation is planned around defensive parsing, including:

- strict frame and packet size limits;
- bounds-checked binary reads;
- overflow-safe offset arithmetic;
- limits for collection cardinality;
- decompression output limits;
- protection against malformed compressed payloads;
- invalid direction and protocol-state detection;
- unknown packet handling without process crashes;
- controlled handling of malformed payloads;
- no panic as an acceptable response to network input;
- redaction of secrets and sensitive fields from logs and traces;
- bounded queues/backpressure at integration boundaries.

The original PangYa packet encryption is treated as a **protocol compatibility mechanism**, not as modern secure transport.

## Protocol anomaly detection

The library will distinguish between valid, suspicious, and invalid protocol input.

Examples of protocol-level anomalies include:

- unknown packet IDs;
- unexpected trailing bytes;
- invalid or suspicious frame sizes;
- malformed payloads;
- impossible collection counts;
- invalid compression metadata;
- packets incompatible with a configured service, direction, or protocol version;
- structurally abnormal packet frequency when enabled by the consumer.

The library reports these conditions. Enforcement remains the responsibility of the consuming server or application.

## Logging and packet tracing

Logging is configurable by the application and will not be tied to a specific logging implementation.

Planned levels:

```text
OFF
ERROR
WARN
INFO
DEBUG
TRACE
```

Verbosity can be controlled globally and, where useful, by protocol component or session.

Packet tracing is intentionally separate from normal application logging. Raw frame dumps, decrypted payloads, or hexadecimal output must be explicitly enabled so that normal operation does not pay unnecessary I/O or allocation costs.

Sensitive values such as passwords and secrets must never be exposed merely because a verbose log level was enabled.

## Performance

Performance work will be driven by measurements rather than assumptions.

The implementation will prioritize:

- low-allocation packet parsing;
- buffer views where ownership is safe;
- preallocated writers when encoded sizes are known;
- bounded and carefully measured buffer reuse;
- immutable lookup structures on packet hot paths;
- no runtime reflection in critical packet decoding paths;
- sequential session processing where ordering matters;
- explicit backpressure rather than unbounded goroutine creation.

Relevant changes are expected to include benchmarks reporting `ns/op`, `B/op`, and `allocs/op`.

## Testing strategy

Compatibility is not considered proven by round-trip tests alone. An encoder and decoder can reproduce the same bug and still pass `Decode(Encode(x)) == x`.

The project therefore uses multiple independent forms of validation:

1. **Unit tests** for binary primitives, framing, crypto, compression, registry behavior, and typed packets.
2. **Golden vectors** with known encrypted, decrypted, compressed, decompressed, and framed payloads.
3. **Differential tests** against independent compatible implementations where legally and technically appropriate.
4. **ProjectG behavior comparison** for observed protocol behavior.
5. **Packet capture replay tests** using real protocol sessions and arbitrary TCP fragmentation boundaries.
6. **Property tests** such as binary write/read symmetry.
7. **Go fuzzing** for readers, frame decoders, crypto boundaries, decompression, packet parsing, and stream fragmentation.
8. **Race detection** for concurrency-sensitive code.
9. **Benchmarks** for the complete encode/decode hot path.
10. **Real client integration tests** against supported PangYa client versions.

A malformed byte sequence must result in either a valid decoded value or a controlled error — never an out-of-bounds access, uncontrolled memory growth, deadlock, infinite loop, or panic.

## Compatibility research

Protocol behavior will be established by triangulating independent sources instead of treating any single reverse-engineered implementation as authoritative.

Research inputs may include:

- observed ProjectG client behavior;
- real network captures;
- available server/client binaries and protocol research;
- PangDox packet documentation;
- PangCrypt and similar protocol implementations;
- other public PangYa emulators;
- independently generated golden vectors.

When sources disagree, observed wire behavior and reproducible captures take precedence over assumptions inherited from older implementations.

## Versioning

The project intends to follow Semantic Versioning.

During protocol research and API stabilization, releases will remain in the `v0.x` range. A `v1.0.0` release will indicate that the supported public API and documented compatibility guarantees are considered stable.

## Installation

The package is not ready for production use yet. Once the first public API is available, it will be consumable as a normal Go module:

```bash
go get github.com/edgardhsl/proto-pangya
```

## Usage

The intended API direction is deliberately small and application-oriented. The exact API below is illustrative and may change before the first stable release:

```go
codec, err := pangya.NewCodec(pangya.Config{
    Season:  pangya.Season8,
    Service: pangya.GameService,
    Logger:  logger,
})
if err != nil {
    return err
}

result, err := codec.Decode(data)
if err != nil {
    return err
}

for _, event := range result.Anomalies {
    security.Handle(event)
}
```

The application should not need to know about XOR tables, LZO internals, frame headers, salts, socket fragmentation, or season-specific wire offsets.

## Roadmap

The initial implementation is planned in incremental milestones:

- **F01 — Binary primitives:** reader, writer, bounds validation, unit/property/fuzz tests.
- **F02 — Framing:** client/server frames and fragmentation-safe stream decoder.
- **F03 — Protocol crypto:** client/server encryption and decryption with golden and differential tests.
- **F04 — Compression:** protocol-compatible compression/decompression and safety limits.
- **F05 — Codec:** session, hello/key handling, encode/decode pipeline and typed errors.
- **F06 — Packet registry:** packet metadata, typed decoders/encoders and service/direction/version routing.
- **F07 — Observability and anomaly API:** configurable logging, tracing and protocol anomaly events.
- **F08 — Compatibility suites:** captured sessions, ProjectG comparison and real-client integration.
- **F09 — Packet coverage:** progressively documented and typed Auth, Game, Message, Ranking and common packets.

## Contributing

Protocol contributions should be evidence-driven. Changes that introduce or modify wire behavior should ideally include at least one reproducible test fixture, capture, golden vector, or independently verifiable reference.

For hot-path changes, include benchmarks when performance behavior may be affected. Parser changes should include malformed-input coverage and fuzz seeds whenever applicable.

## License

No open-source license has been selected yet. Until a license is explicitly added to this repository, normal copyright rules apply.

## Disclaimer

This is an independent interoperability and protocol-research project. PangYa and related names and assets belong to their respective owners. This project is not affiliated with or endorsed by the original game developers or publishers.
