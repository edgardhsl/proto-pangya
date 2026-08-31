# Protocol anomaly specification

## Purpose

An anomaly is structured protocol diagnostic information. It describes a verified invariant violation or suspicious wire condition; it does not assert malicious intent or prescribe enforcement.

## Required properties

An anomaly SHOULD include:

- unique/correlatable ID;
- stable machine-readable code;
- severity;
- pipeline stage;
- protocol context when known;
- packet ID and byte offset when relevant;
- human-readable explanation;
- evidence reference when captured.

Machine-readable codes MUST remain more stable than human-facing messages.

Example code families:

```text
frame.invalid_length
frame.trailing_data
crypto.invalid_key
compression.invalid_size
packet.unknown_id
packet.trailing_bytes
packet.invalid_count
```

## Severity

Suggested levels are `INFO`, `LOW`, `MEDIUM`, `HIGH` and `CRITICAL`. Severity describes protocol risk/diagnostic importance, not certainty of abuse.

## Initial detection scope

The initial core SHOULD prioritize anomalies derivable from the current wire input and protocol context: malformed fields, unknown IDs, invalid directions, reserved-field mismatches, invalid counts and trailing data.

Time-window/frequency behavior is outside the initial core because it introduces clocks, state and policy beyond a single decode stream.

## Enforcement boundary

The package MUST NOT contain concepts such as ban, kick, punishment, trust score or account restriction.
