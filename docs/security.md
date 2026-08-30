# Security

## Trust model

All bytes received from the network are untrusted. Protocol compatibility never overrides memory-safety, resource-safety, or controlled failure requirements.

## Defensive parsing

Every parser must enforce bounds before reading or slicing. Offset arithmetic must be overflow safe. Variable-size collections must be limited before allocation.

The library should define configurable limits for at least:

- maximum frame size;
- maximum packet payload size;
- maximum decompressed size;
- maximum string length;
- maximum collection count;
- maximum retained evidence payload size.

Defaults should be conservative and calibrated against verified protocol traffic.

## Decompression safety

Compressed input must never be allowed to request unbounded output. Expected output sizes and expansion limits are validated before or during decompression.

Malformed compressed data must return a controlled error.

## Resource exhaustion

The library must not create unbounded goroutines, queues, allocations, retained buffers, or evidence records based solely on network input.

Buffer pools must reject oversized buffers and must not accidentally preserve sensitive data across unrelated operations.

## Sensitive data

Protocol payloads may contain credentials or other sensitive values. Logging, tracing, anomalies, and evidence storage must support field redaction and payload omission.

Verbose logging must never automatically disable redaction.

## Anomaly boundary

The package detects protocol-level anomalies but does not make domain-policy decisions. It reports evidence and structured anomaly metadata. Consumers decide how those signals affect their own behavior.

## Failure policy

Untrusted input may produce a decoded value, an anomaly, or a typed error. It must not cause:

- panic;
- out-of-bounds memory access;
- uncontrolled allocation;
- unbounded decompression;
- deadlock;
- infinite loop;
- uncontrolled goroutine creation.

## Fuzzing and regression

Any parser bug discovered through fuzzing or real traffic should result in a permanent regression fixture whenever practical.
