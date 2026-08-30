# Anomaly evidence storage

## Purpose

The evidence subsystem preserves the minimum reproducible material required to investigate protocol anomalies without coupling `proto-pangya` to a specific database, filesystem, object store, or application.

Evidence is optional, policy-driven, bounded, and privacy-aware.

## Design goals

Evidence handling must:

- preserve useful diagnostic context;
- avoid mandatory persistence dependencies;
- support deterministic correlation with anomaly records;
- prevent uncontrolled disk or memory growth;
- protect sensitive protocol data;
- allow consumers to choose storage, retention, and encryption strategies;
- support later replay or forensic analysis when sufficient data was captured.

## Separation of responsibilities

The evidence pipeline has three independent stages:

```text
anomaly detection
      │
      ▼
evidence capture policy
      │
      ▼
EvidenceRecord
      │
      ▼
EvidenceStore (optional)
```

Detection decides that an anomaly exists. Capture policy decides what evidence may be collected. Storage decides if and where that record is persisted.

## Evidence record

A record should contain protocol-neutral metadata and optional artifacts.

A possible public model is:

```go
type EvidenceRecord struct {
    ID         string
    AnomalyID  string
    CapturedAt time.Time

    Direction Direction
    Service   Service
    Version   Version
    PacketID  uint16

    Stage      EvidenceStage
    Reason     string

    RawFrame        []byte
    DecryptedFrame  []byte
    PacketPayload   []byte

    Truncated  bool
    Redacted   bool
    Digest     []byte
    Metadata   map[string]string
}
```

The exact API may change before v1. The contract is that the record is self-describing, bounded, serializable, and independent of consumer-specific entities.

## Evidence stages

Evidence may originate from different pipeline stages:

```text
RAW_FRAME
DECRYPTED_FRAME
DECOMPRESSED_PAYLOAD
PACKET_PAYLOAD
DECODE_CONTEXT
```

Capturing all stages is not the default. Policies should collect only what is necessary for the anomaly category.

For example, an invalid frame length normally needs raw-frame evidence, while a trailing-field anomaly may only require packet payload and decoder metadata.

## Capture policy

Evidence collection must be configurable.

A policy should be able to control at least:

- enabled/disabled state;
- minimum anomaly severity;
- allowed anomaly categories;
- maximum bytes per artifact;
- maximum total bytes per evidence record;
- whether raw encrypted frames may be retained;
- whether decrypted payload may be retained;
- whether packet payload may be retained;
- redaction behavior;
- sampling or deduplication behavior.

Safe defaults should minimize captured data.

## Storage abstraction

Persistence is optional and provided through a small consumer-defined boundary, conceptually:

```go
type EvidenceStore interface {
    Store(context.Context, EvidenceRecord) error
}
```

The library must not require SQL, NoSQL, local files, cloud object storage, or any external persistence SDK.

A no-op implementation is a valid default.

Read/query APIs should not be required by the protocol hot path. If later exposed, they should be separate from the minimal write contract.

## Failure behavior

Evidence persistence must never make protocol decoding dependent on external storage availability.

Storage failure should:

1. return or emit a structured evidence-storage error;
2. be observable through configured logging/metrics hooks;
3. not convert an otherwise decoded packet into an invalid packet solely because evidence could not be saved.

Consumers may choose stricter behavior outside the library.

## Bounded execution

A slow EvidenceStore must not create unbounded memory retention or goroutines.

The package should support caller-controlled synchronous storage or a bounded asynchronous dispatcher. If an asynchronous implementation is provided, it must define queue capacity and overflow behavior explicitly.

Possible overflow policies include dropping newest evidence, dropping oldest evidence, or returning backpressure to the evidence layer. No policy may create an unbounded queue.

## Retention

Retention is a storage concern, but evidence records should carry enough metadata for stores to apply retention policies by time, severity, category, or size.

The library may provide retention recommendations but must not assume a particular database lifecycle mechanism.

## Redaction and sensitive information

Evidence is potentially more sensitive than logs because it may contain raw or decrypted protocol bytes.

The following rules apply:

- known credential or secret fields must be redactable before persistence;
- enabling TRACE logging must not automatically enable evidence capture;
- enabling evidence capture must not automatically permit decrypted payload retention;
- payload artifacts must have strict byte limits;
- redaction must occur before data is handed to a persistent EvidenceStore whenever the field can be identified safely;
- evidence metadata must indicate whether content was redacted or truncated.

Consumers storing sensitive evidence should protect it at rest and restrict access according to their own security requirements.

## Integrity

Each retained artifact should support an integrity digest calculated after redaction/truncation so later tooling can determine whether the stored evidence changed.

SHA-256 is an appropriate default digest unless implementation requirements justify another algorithm.

A digest is an integrity aid, not proof of trusted provenance. Cryptographic signing or chained evidence records may be added later through optional extensions if a use case requires stronger tamper evidence.

## Deduplication

Repeated malformed traffic can generate large volumes of identical evidence.

Evidence policies may calculate a stable fingerprint from protocol context, anomaly category, packet ID, and normalized evidence bytes. Consumers may use that fingerprint to sample or deduplicate records.

Deduplication must not modify anomaly detection itself.

## Evidence identifiers

`Anomaly.ID` and `EvidenceRecord.ID` must be unique enough for correlation without relying on a database-generated identifier.

UUIDs or equivalent random identifiers are suitable. IDs carry no domain meaning.

## Replay

Where captured data is sufficient, evidence should be usable as a fixture for offline parser reproduction.

Replay tooling must never be required on the runtime hot path. A future test utility may convert sanitized evidence into permanent regression fixtures.

## Testing requirements

Evidence handling requires tests for:

- disabled capture producing no artifact;
- severity/category filtering;
- artifact truncation;
- total-record size limits;
- redaction before storage;
- digest stability;
- store failure isolation;
- bounded queue behavior if asynchronous storage exists;
- deduplication/fingerprint stability;
- concurrent use where supported;
- malformed packets not causing excessive evidence allocation.

Fuzz tests should cover evidence generation from malformed decode contexts to ensure capture itself cannot panic or allocate without bounds.

## Non-goals

The evidence subsystem does not define:

- punishment or enforcement;
- incident workflow;
- user/account identity;
- a mandatory evidence database;
- a management dashboard;
- legal retention periods;
- application-specific audit policy.

Those responsibilities remain outside `proto-pangya`.
