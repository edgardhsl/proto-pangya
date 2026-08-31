# Error specification

## Principles

Malformed network data is an expected protocol condition. It MUST return controlled, classifiable errors rather than panic.

Errors SHOULD support `errors.Is` / `errors.As` where useful and SHOULD preserve causes with wrapping.

## Categories

The implementation should distinguish at least:

- invalid/incomplete framing;
- bounds violations;
- invalid lengths/limits;
- cryptographic transform failure;
- compression/decompression failure;
- unsupported profile or packet behavior;
- packet decode failure;
- invalid configuration;
- internal misuse.

## Partial results

A decode operation MAY return a partial `Result` together with an error when useful diagnostic information, anomalies or evidence were produced before the input became invalid.

Conceptually:

```text
Packet + no anomalies + nil error   = accepted
Packet + anomalies + nil error      = accepted with protocol concerns
partial Result + non-nil error      = invalid/incomplete operation
```

Errors MUST NOT automatically embed sensitive payloads.
