# Known unknowns

This document deliberately records unresolved wire behavior so hypotheses are not accidentally promoted to protocol contracts.

## How to record an unknown

Use a compact entry containing:

```text
Profile:
Service:
Direction:
Packet/frame:
Status: unknown | partial
Known:
- ...
Unknown:
- ...
Evidence:
- fixture/capture/source reference
```

## Current unknowns

The protocol implementation has not started, so exact frame layouts, supported profile identifiers, hello/key variants, packet schemas and compatibility coverage remain to be established by evidence.

As research progresses, add concrete entries rather than replacing unknown bytes with guessed semantics.
