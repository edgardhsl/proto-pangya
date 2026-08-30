# Testing strategy

## Principle

Protocol correctness cannot be established by round-trip tests alone. Independent evidence is required because an encoder and decoder may reproduce the same defect.

## Test layers

### Unit tests

Cover binary primitives, bounds behavior, frame rules, crypto helpers, compression wrappers, registry lookup, typed packet codecs, anomaly classification, and evidence policies.

### Golden vectors

Store known inputs and outputs for framing, encryption, decryption, compression, decompression, and packet payloads. Fixtures should record direction, service, version, key material where applicable, packet ID, and provenance.

### Differential tests

Compare behavior with independent compatible implementations or independently derived protocol oracles when legally and technically appropriate.

### Capture replay

Replay recorded protocol sessions through the decoder. Replay tests must vary TCP chunk boundaries so correctness never depends on the original capture segmentation.

### Property tests

Use properties such as primitive write/read symmetry, deterministic encoding, stable fingerprints, and safe handling of arbitrary fragmentation.

### Fuzzing

Fuzz at least:

- binary readers;
- client and server frame decoders;
- stream reconstruction;
- crypto boundaries;
- decompression wrappers;
- packet decoders;
- anomaly generation;
- evidence capture.

Arbitrary bytes must result in a valid value, anomaly, or controlled error without panic or unbounded allocation.

### Race detection

Concurrency-sensitive code must be tested with Go's race detector.

### Benchmarks

Critical paths must report `ns/op`, `B/op`, and `allocs/op`. Benchmark fixtures should include representative small, medium, and large protocol payloads.

### Real-client compatibility

Supported protocol versions should eventually be validated against real client behavior. These tests are compatibility acceptance tests and may live outside the default fast unit-test suite.

## Regression policy

Every protocol bug fixed from fuzzing, captures, or real traffic should produce a permanent regression test whenever practical.

## Fixture quality

Fixtures must be reproducible and should avoid storing unnecessary sensitive data. Captured traffic used in the repository should be sanitized before commit.

## CI expectations

The normal CI baseline should include formatting/linting, unit tests, race-appropriate tests where practical, and deterministic golden/regression suites. Long fuzz campaigns and real-client tests may run separately.
