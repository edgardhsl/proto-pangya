# Lifecycle and concurrency

## No hidden I/O

Normal encode/decode operations are synchronous and perform no network, filesystem, database or cloud I/O.

## No hidden goroutines

The library MUST NOT create background goroutines implicitly for decoding, encoding, evidence persistence or logging. Callers remain in control of concurrency and lifecycle.

## Decoder

A decoder owns incremental stream state and is therefore stateful. The default contract is one decoder per independent byte stream. It SHOULD NOT claim concurrent safety unless a measured use case requires it.

## Encoder

An encoder may own profile/key state depending on the verified protocol. It SHOULD be treated as stateful unless its final implementation proves otherwise.

## Immutable state

Profiles and finalized packet definitions/registries SHOULD be immutable and safe to share.

## Buffer lifetime

Temporary views may exist only while their lifetime is explicit. Any packet/evidence data that survives the current decode operation MUST own its bytes.

## Cancellation

Because the core performs bounded synchronous CPU/memory work, normal primitive operations need not accept `context.Context`. Context belongs at genuine external boundaries such as optional persistence contracts.
