# Public API specification

## Scope

The public API MUST remain small, Go-idiomatic and independent of any consuming application.

## Initial public packages

The initial supported package surface SHOULD be limited to:

```text
github.com/edgardhsl/proto-pangya
github.com/edgardhsl/proto-pangya/packet
```

Additional public packages MUST require a concrete, stable use case and an explicit compatibility decision.

## Root package

The root package (`package pangya`) owns the high-level protocol contract, including concepts such as:

- `Decoder` and `Encoder`;
- configuration and limits;
- `Profile`, `Service` and `Direction`;
- decode/encode results;
- protocol errors;
- anomalies and evidence;
- tracing configuration.

It MUST NOT expose internal framing tables, cryptographic tables, compression implementation details or dispatch structures unless those become independently useful stable APIs.

## Packet package

`packet` owns extension-facing packet primitives:

- packet identity/interface;
- raw packets;
- bounds-safe `Reader` and `Writer`;
- typed packet definitions/codec hooks required by extension points.

Avoid generic dumping-ground names such as `common`, `types`, `utils` and `api`.

## Decoder and Encoder

`Decoder` and `Encoder` are separate public concepts. The decoder owns incremental stream state; the encoder owns outgoing wire transformation.

The API MUST NOT require the caller to understand TCP fragmentation, frame headers, crypto tables or compression internals.

## Stability

Before v1, breaking improvements are allowed but public exposure SHOULD still be conservative. Implementation details belong in `internal/` until their API is justified.
