# Compatibility

## Principle

No single reverse-engineered implementation is considered authoritative. Protocol behavior is accepted through reproducible evidence and cross-validation.

## Evidence hierarchy

When sources disagree, prefer evidence in roughly this order:

1. reproducible behavior observed from supported clients;
2. real packet captures with known context;
3. independently reproduced behavior from original binaries or decompiled code;
4. multiple independent protocol implementations that agree;
5. protocol documentation and packet databases;
6. assumptions inherited from legacy code.

The order is guidance, not a substitute for technical analysis.

## Research sources

Compatibility research may use:

- real PangYa client traffic;
- observed ProjectG behavior;
- PangDox;
- PangCrypt and similar protocol libraries;
- public PangYa implementations;
- independently generated fixtures and golden vectors.

References are used as research inputs, not runtime dependencies.

## Version model

Client/protocol versions must be explicit wherever wire behavior differs. Shared behavior should remain common; actual differences should be represented as separate definitions or version-aware codecs.

A packet must not silently reinterpret the same public type in incompatible ways across versions.

## Acceptance criteria

A protocol behavior is considered well-supported when practical evidence includes one or more of:

- a verified capture;
- a deterministic golden vector;
- differential agreement with an independent implementation;
- successful replay;
- successful interaction with the intended client version.

Critical crypto, framing, and compression behavior should use multiple forms of validation.

## Unknown behavior

Unknown fields and packets should remain representable without fabricating semantics. Documentation and code should clearly distinguish verified facts, hypotheses, and unsupported behavior.

## Fixture provenance

Golden vectors and captures should record enough provenance to understand what they validate, including protocol version, service, direction, packet ID, and source type where known.

Sensitive or proprietary material must not be committed unnecessarily.

## Compatibility regressions

A bug affecting previously verified wire behavior requires a regression fixture whenever practical. Breaking compatibility intentionally requires documentation of the affected protocol version and evidence justifying the change.
