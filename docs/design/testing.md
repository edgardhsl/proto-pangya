# Testing design

## Principle

Round-trip tests alone cannot prove protocol compatibility because encoder and decoder can share the same defect.

## Test layers

- unit tests for primitives and deterministic rules;
- negative tests for malformed/boundary input;
- golden vectors for known wire input/output;
- differential tests against independent oracles;
- captured-session replay;
- arbitrary TCP rechunking/fragmentation tests;
- property tests for invariants;
- native Go fuzzing;
- race detection for mutable/shared components;
- benchmarks for hot paths;
- real-client acceptance for claimed profiles.

## Fuzzing

Fuzz readers, stream/frame decoders, transform boundaries, decompression, packet decode, anomaly generation and evidence capture. Arbitrary input must terminate with a valid result or controlled error without panic or unbounded resource use.

Go fuzz seed corpora SHOULD live in the package's `testdata/fuzz/<FuzzName>` structure. Root `testdata/` is reserved for cross-pipeline captures, vectors and manifests.

## Fixtures

Cross-pipeline fixtures should include provenance metadata such as profile, service, direction, packet ID, verification status, source type and integrity digest. Sensitive captures must be sanitized before commit.

## Regression

Every practical bug discovered from fuzzing, captures or real clients should produce a permanent regression fixture/test.

## CI baseline

Normal CI should remain simple: format check, `go vet`, deterministic tests, race testing where practical and vulnerability scanning. Long fuzz campaigns, benchmarks and real-client suites may run manually or on schedules.
