# ADR-0004: Use standard Go logging and separate tracing

- Status: Accepted
- Date: 2026-08-31

## Context

A custom logger interface would duplicate functionality now provided by Go's standard structured logging package and create another public compatibility contract.

## Decision

Use `log/slog` for structured logs. Allow callers to control handlers and runtime levels. Keep packet tracing as a separate opt-in callback mechanism with explicit byte-lifetime and sensitivity rules.

Evidence remains separate from both logging and trace.

## Consequences

The library has no logging dependency outside the standard library, integrates naturally with existing handlers and avoids treating TRACE/evidence as the same diagnostic mechanism.
