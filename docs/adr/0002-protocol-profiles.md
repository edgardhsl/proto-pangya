# ADR-0002: Model compatibility using protocol profiles

- Status: Accepted
- Date: 2026-08-31

## Context

Using only PangYa season numbers risks merging incompatible regional, publisher, build or service wire variants.

## Decision

Use `Profile` as the compatibility unit. A profile represents a verified wire dialect and may include framing, crypto, compression, hello/key and packet-definition differences.

Season may be metadata on a profile but is not sufficient as the primary dispatch key when wire behavior differs.

## Consequences

Compatibility claims become more precise, wire differences remain localized and future regional/build variants do not require scattered season checks.
