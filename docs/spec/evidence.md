# Anomaly evidence specification

## Purpose

Evidence preserves the minimum bounded material required to reproduce or investigate a protocol anomaly. Evidence is distinct from logging and tracing.

## Evidence model

Evidence SHOULD be self-describing and serializable. A durable record should contain typed protocol metadata and a list of artifacts rather than a growing set of stage-specific fields.

Conceptually:

```go
type Evidence struct {
    SchemaVersion uint16
    ID            EvidenceID
    AnomalyID     AnomalyID
    CapturedAt    time.Time
    Profile       Profile
    Service       Service
    Direction     Direction
    PacketID      uint16
    Fingerprint   [32]byte
    Artifacts     []EvidenceArtifact
}

type EvidenceArtifact struct {
    Stage        EvidenceStage
    Data         []byte
    OriginalSize int
    Truncated    bool
    Redacted     bool
    Digest       [32]byte
}
```

The exact public types may evolve before v1; the behavioral constraints in this document remain authoritative.

## Stages

Artifacts MAY represent raw frame, decrypted frame, decompressed payload, packet payload or decode context. Capturing all stages MUST NOT be the default.

## Ownership

Evidence is durable diagnostic data and MUST own retained bytes. It MUST NOT reference reusable decoder buffers.

## Capture policy

Capture MUST be optional, bounded and configurable by severity/category and artifact/record size. Decrypted payload retention MUST require explicit permission.

Known sensitive fields SHOULD be redacted before evidence leaves the protocol layer when reliable field identification is available. Redaction and truncation occur before digest calculation.

## Integrity and fingerprints

SHA-256 is the default integrity digest. Evidence fingerprints SHOULD be deterministic for equivalent normalized anomaly material and MAY be used for deduplication/correlation. They do not prove trusted provenance.

## Persistence boundary

The library MAY define a small persistence contract such as:

```go
type EvidenceStore interface {
    Store(context.Context, Evidence) error
}
```

However, decoding MUST NOT perform hidden storage I/O. Decode results return captured evidence; the caller decides whether and how to persist it.

The core MUST NOT require SQL, filesystems, cloud SDKs, databases or background workers.

## Schema version

Durable evidence MUST carry a schema version so stored records can evolve independently of Go struct layout.
