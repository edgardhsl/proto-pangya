# Protocol profiles

## Motivation

A PangYa "season" alone may not uniquely identify wire behavior. Region, publisher, build, service revision or other client differences may alter framing, key layout, packet definitions or transforms.

## Profile

`Profile` represents a verified wire dialect. A profile MAY define:

- framing variant;
- client/server cryptographic behavior;
- compression behavior;
- hello/key layout;
- supported services;
- packet definitions;
- protocol-safe limits or overrides.

A profile MUST contain protocol information only. It MUST NOT contain application state or policy.

## Compatibility naming

Profile identifiers SHOULD be stable and descriptive enough to distinguish verified variants without claiming equivalence that has not been proven.

## Version-specific behavior

Shared wire behavior SHOULD remain shared. Genuine differences SHOULD be isolated by profile-aware definitions/codecs rather than scattered `if season == ...` checks.

## Extensibility

New profiles SHOULD be additive. Existing profile semantics MUST NOT silently change to represent a different wire dialect.
