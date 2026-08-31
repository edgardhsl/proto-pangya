# Performance design

## Principle

Optimize measured bottlenecks. Compatibility and safety take precedence over speculative micro-optimization.

## Hot paths

Primary hot paths are stream reconstruction, frame parsing, crypto, compression, binary read/write, registry lookup and high-level encode/decode.

## Stream buffering

Do not blindly append every network chunk into a secondary accumulation buffer. Complete frames should be processed from incoming bytes when possible; only the incomplete tail should be retained. Retained pending data remains bounded by configured frame limits.

## Allocation and copies

Target zero or near-zero allocations where practical without ambiguous ownership. Preallocate writer capacity when size is known or cheaply estimated. Avoid copies between stages when lifetime is safe; copy data that must outlive reusable buffers.

## Lookup

Finalized registries should be immutable and lock-free for reads. Choose map versus indexed-table approaches with benchmarks rather than assumptions.

## Reflection and generation

Runtime reflection is not required for hot packet codecs. Prefer explicit codecs. Code generation may be introduced only after packet schema patterns are stable enough to justify maintenance cost.

## Compression implementation

Prefer a portable pure-Go compatible implementation when correctness/performance are sufficient. CGO is a fallback decision, not a default.

## Measurement

Benchmarks report `ns/op`, `B/op`, `allocs/op` and throughput where meaningful. Use CPU, heap, allocation, mutex and blocking profiles to investigate measured regressions.
