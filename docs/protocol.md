# Protocol specification

## Scope

This document defines the protocol model used by `proto-pangya`. It intentionally describes wire concerns only.

## Transport model

PangYa traffic is carried over TCP. TCP read boundaries are not packet boundaries. A read may contain a partial frame, exactly one frame, or multiple frames.

The stream decoder must therefore buffer incomplete data and emit complete frames only when the configured frame rules prove that enough bytes are available.

## Receive pipeline

```text
TCP bytes
  → stream reconstruction
  → frame validation
  → decryption
  → decompression when applicable
  → packet envelope
  → typed packet decoding
```

The send pipeline performs the inverse operations.

## Frame model

Client-to-server and server-to-client framing may differ and must have separate codecs where the wire format differs.

A frame decoder must validate minimum and maximum sizes before allocation or transformation. Invalid lengths must fail before attempting crypto or decompression.

## Binary encoding

Packet primitives are little-endian unless verified otherwise for a specific field.

Binary readers and writers must expose explicit operations for integer widths, floats, fixed-size byte sequences, and protocol strings. Reads are bounds checked and must never advance beyond the input.

## Packet identity

A packet is not identified by numeric ID alone. Routing context includes at least:

- service;
- direction;
- protocol/client version where relevant;
- packet ID.

The registry must allow equal numeric IDs to represent different packets in different contexts.

## Raw and typed packets

Unknown packet IDs must remain inspectable as raw packets rather than forcing a process failure. A raw representation should preserve the packet ID, context, and payload bytes needed for diagnostics or future research.

Known packet definitions may expose typed encode/decode implementations.

## Crypto

The PangYa cryptographic transformations are compatibility mechanisms. Client-to-server and server-to-client behavior must be implemented and tested independently when the wire algorithms differ.

Protocol crypto must not be presented as a modern confidentiality or authentication guarantee.

## Compression

Compression and decompression are isolated from framing and crypto behind protocol-focused abstractions. Decompression must always be constrained by configured output limits.

## Protocol context

Operations that depend on negotiated or configured state use an explicit protocol context containing only wire-relevant information, for example service, direction, protocol version, and session key material.

Domain state must not be introduced into this context.

## Strictness

The decoder distinguishes:

- valid input: structurally accepted;
- suspicious input: decodable but carrying one or more anomaly signals;
- invalid input: cannot be safely or correctly decoded under the configured protocol rules.

Suspicious input may still produce a packet result. Invalid input produces a controlled error.

## Extensibility

New protocol versions and packet definitions should be additive where possible. Existing public packet types should not silently change semantics across versions; version-specific wire differences should be explicit.
