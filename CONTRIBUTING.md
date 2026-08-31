# Contributing

Contributions are welcome. `proto-pangya` is protocol-research software, so changes to wire behavior require a higher evidence bar than ordinary application code.

## Before contributing

Read [`docs/README.md`](docs/README.md) and the relevant specification/design documents. The documented public boundary and safety requirements are authoritative unless the change also updates the corresponding ADR/specification.

## Protocol changes

A change that introduces or modifies wire behavior should include at least one reproducible source when practical:

- verified packet capture;
- deterministic golden vector;
- original/decompiled behavior that can be reproduced;
- differential agreement with an independent implementation;
- real-client interaction proving the behavior.

Do not assign semantics to unknown bytes without evidence. Record unresolved behavior in [`docs/research/unknowns.md`](docs/research/unknowns.md).

## Tests

Parser changes should include negative coverage and fuzz seeds where appropriate. Critical framing, crypto and compression changes should include golden or differential tests. Performance-sensitive changes should include benchmarks.

Run at minimum:

```bash
gofmt -w .
go vet ./...
go test ./...
go test -race ./...
```

## Performance

Do not introduce pooling, unsafe code, reflection-heavy codecs or concurrency only because they appear faster. Measure first and include `ns/op`, `B/op` and `allocs/op` when the change affects a hot path.

## Pull requests

Keep changes focused. Explain protocol evidence, compatibility impact, tests added and any documentation updated. Large architectural changes should add or update an ADR.
