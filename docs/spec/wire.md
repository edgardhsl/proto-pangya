# Wire specification

## Transport

PangYa traffic is carried over TCP. TCP read boundaries are not frame boundaries. The decoder MUST accept arbitrary chunking: partial frames, complete frames and multiple frames in one input.

The library performs no socket I/O. Callers feed bytes to the decoder and receive protocol results.

## Receive pipeline

```text
bytes
  → stream reconstruction
  → frame validation
  → decryption
  → decompression when required by the profile
  → packet envelope
  → raw or typed packet decode
  → anomalies/evidence
```

Outgoing data follows the inverse protocol transformation.

## Framing

Client-to-server and server-to-client framing MUST be modeled independently when their formats differ. Declared lengths MUST be validated before allocation, crypto or decompression.

Incremental stream state MUST retain only the bytes required for an incomplete frame and MUST remain bounded by configured limits.

## Binary encoding

Verified PangYa integer primitives are little-endian unless a specific field is proven otherwise. Reads MUST be bounds checked before slicing or advancing offsets.

## Packet identity

A packet is identified by protocol context, not numeric ID alone. Dispatch MUST consider at least profile, service, direction and packet ID when those dimensions affect wire meaning.

## Raw packets

Unknown but structurally safe packets MUST remain representable as raw packets. Unknown semantics MUST NOT be fabricated solely to make the packet typed.

## Crypto and compression

Wire crypto is an interoperability mechanism, not modern transport security. Client/server variants MUST be validated independently.

Decompression MUST enforce output bounds. A malformed compressed stream MUST return a controlled error.
