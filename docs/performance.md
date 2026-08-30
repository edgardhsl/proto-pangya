# Performance

## Goals

Performance work in `proto-pangya` is measurement-driven. Compatibility and safety take precedence over speculative micro-optimizations.

## Hot paths

The primary hot paths are:

- stream reconstruction;
- frame parsing;
- crypto transforms;
- compression/decompression;
- packet registry lookup;
- binary packet decoding and encoding.

## Allocation strategy

Targets for critical operations are zero or near-zero allocations where practical without making ownership unsafe.

Use slices and preallocation deliberately. Avoid repeated buffer growth when encoded sizes are known or can be estimated cheaply.

`sync.Pool` or custom pooling may be introduced only after benchmarks demonstrate meaningful benefit. Oversized buffers must not be retained indefinitely.

## Copy strategy

Avoid unnecessary copies between pipeline stages, but never expose mutable shared buffers with ambiguous lifetime.

A zero-copy optimization is acceptable only when ownership and lifetime are explicit and tested.

## Lookup structures

Packet definitions should become immutable after construction. Hot-path lookups should avoid locks. Alternative lookup structures such as maps or indexed tables must be selected using benchmarks rather than assumption.

## Reflection

Runtime reflection should not be required for critical packet codecs. Explicit or generated codecs are preferred for predictable performance and wire-layout control.

## Concurrency and backpressure

The library must not create unbounded goroutines. Any asynchronous dispatcher, including evidence persistence, requires a bounded queue and explicit overflow behavior.

## Benchmarking

Benchmarks should report:

- `ns/op`;
- `B/op`;
- `allocs/op`;
- throughput where meaningful.

Representative benchmarks should include small control packets, typical gameplay-sized payloads, and larger valid protocol messages.

## Profiling

CPU, heap, allocation, mutex, and blocking profiles should guide optimization of measured bottlenecks.

## Regression budgets

Once stable baselines exist, CI may enforce performance regression budgets for key benchmarks. A regression should not fail solely because of noisy measurements; budgets must account for benchmark variance and require reproducible degradation.

## Observability cost

Logging and anomaly hooks disabled by configuration should add negligible overhead. TRACE and evidence-enabled scenarios should be benchmarked separately so diagnostic features do not hide hot-path regressions.
