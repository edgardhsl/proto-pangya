# Observability

## Goals

Observability must help diagnose protocol behavior without coupling `proto-pangya` to a specific logging, metrics, tracing, or UI stack.

## Logging contract

The consumer supplies the logging implementation. The library emits structured events through a neutral interface.

Supported verbosity levels should include:

```text
OFF
ERROR
WARN
INFO
DEBUG
TRACE
```

The global level may be overridden by component or protocol context where supported.

## Component scopes

Useful scopes include:

- stream;
- frame;
- crypto;
- compression;
- packet;
- anomaly;
- evidence.

The exact logger API may evolve before v1, but component filtering must not require separate logger implementations.

## Runtime control

Verbosity should be changeable by the consuming process at runtime without rebuilding codec instances when practical.

The implementation should avoid expensive formatting, hex encoding, or payload copying when the relevant log level is disabled.

## Packet tracing

Packet tracing is separate from regular logging.

A trace may optionally include:

- raw frame bytes;
- decrypted bytes;
- decompressed payload;
- packet ID and metadata;
- decoder offsets and field diagnostics.

Tracing must be explicitly enabled and size limited.

## Sensitive data

Sensitive and secret protocol fields remain redacted regardless of logging verbosity. TRACE is not permission to expose credentials or secret material.

Raw-payload traces must support redaction or omission policies for packets known to contain sensitive information.

## Structured fields

Where available, events should use stable fields such as:

- direction;
- service;
- protocol version;
- packet ID;
- packet name;
- frame size;
- payload size;
- duration;
- anomaly category;
- evidence ID.

No consumer-specific identity type is required by the package.

## Performance

Observability disabled at the configured level should impose negligible allocation and CPU overhead on the hot path. Benchmarks should cover logging disabled and TRACE-enabled scenarios separately.
