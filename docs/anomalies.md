# Protocol anomalies

## Purpose

The anomaly subsystem identifies protocol behavior that is malformed, unusual, contradictory, or outside verified expectations while keeping enforcement outside the library.

An anomaly is diagnostic information, not a punishment decision.

## Result model

Decode results may be:

- `valid`: accepted without anomaly signals;
- `suspicious`: decoded successfully with one or more anomaly signals;
- `invalid`: rejected because safe or correct decoding is not possible.

## Suggested severity levels

```text
INFO
LOW
MEDIUM
HIGH
CRITICAL
```

Severity describes protocol risk or diagnostic importance, not certainty of malicious intent.

## Suggested categories

- unknown packet;
- invalid direction;
- unsupported version;
- unexpected frame size;
- trailing bytes;
- malformed field;
- invalid collection count;
- invalid compression metadata;
- decrypt/decompress inconsistency;
- reserved-field mismatch;
- unexpected packet sequence where protocol state is known;
- abnormal packet frequency when enabled;
- internal protocol invariant violation.

## Anomaly record

A public anomaly should contain stable structured information, for example:

```go
type Anomaly struct {
    ID         string
    Timestamp  time.Time
    Severity   Severity
    Category   Category
    Direction  Direction
    Service    Service
    Version    Version
    PacketID   uint16
    Message    string
    EvidenceID string
}
```

The exact API is subject to implementation design, but records must be serializable without requiring consumer-specific types.

## Detection principles

Detectors must be deterministic whenever possible. Rules should document the invariant they validate and avoid assigning malicious intent from a single structural anomaly.

An anomaly detector must not mutate decoded domain data in order to hide an inconsistency.

## Rate-related detection

Frequency-based anomaly detection is optional because it depends on time and context. If provided by the package, it must be bounded, configurable, and protocol-oriented rather than tied to application policy.

## Evidence

An anomaly may reference an evidence record containing the minimum diagnostic material needed to reproduce or investigate the condition. Evidence handling is specified in [anomaly-evidence.md](anomaly-evidence.md).

## Enforcement boundary

The package may classify and report input. It must not expose built-in concepts such as ban, kick, account restriction, punishment, or trust score.
