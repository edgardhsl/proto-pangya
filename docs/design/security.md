# Security design

## Trust model

Every network-controlled byte is untrusted. Compatibility never overrides memory safety, resource safety or controlled failure.

## Defensive parsing

Before reading, slicing, allocating or transforming data, validate bounds and declared sizes. Offset arithmetic should prefer forms that cannot overflow, such as checking `n > len(buf)-offset` after validating the offset.

## Limits

Configurable limits should cover at least frame size, packet payload size, decompressed output, strings, collection counts and retained evidence size. Defaults should be conservative and calibrated from verified traffic.

## Decompression

Malformed or adversarial compressed data MUST NOT request unbounded output. Expansion/output bounds must be enforced before or during decompression.

## Resource exhaustion

Network input MUST NOT cause unbounded retained buffers, queues, goroutines, evidence records or decompression. Pending stream bytes are bounded by frame limits.

## Sensitive data

Known credentials/secrets must not be emitted merely because DEBUG/TRACE is enabled. Evidence capture and tracing have independent opt-ins.

## Pooling

Do not add `sync.Pool` until benchmarks prove benefit. If pooling is later introduced, cap retained buffer sizes and prevent observable reuse or sensitive-data leakage.

## Unsafe / CGO

Prefer pure Go and memory-safe implementations. `unsafe` or CGO require explicit justification, compatibility tests and benchmarks showing why safer alternatives are insufficient.
