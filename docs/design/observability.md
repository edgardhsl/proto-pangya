# Observability design

## Logging

Use Go's standard `log/slog` rather than defining a competing logging framework. Public configuration MAY accept `*slog.Logger`; a nil logger should result in quiet/no-op behavior.

The caller controls handlers, output destination, formatting and runtime verbosity. `slog.LevelVar` can be used by callers for dynamic level changes without reconstructing decoders.

A project-defined TRACE level MAY use a `slog.Level` below DEBUG when detailed diagnostics are enabled.

## Structured fields

Stable fields may include component, direction, service, profile, packet ID/name, frame/payload sizes, duration, anomaly code and evidence ID. Application-specific identity is not required.

## Cost

Disabled log levels MUST avoid expensive payload copying, hexadecimal encoding and message construction whenever practical.

## Trace

Packet tracing is distinct from logging. Trace callbacks MAY receive temporary views of bounded wire stages under an explicit lifetime contract. A trace consumer that needs bytes after the callback must copy them.

Trace MAY include raw frame, decrypted data, decompressed payload and packet/decode metadata. Sensitive-data policy still applies.

## Evidence distinction

Trace is ephemeral runtime diagnostics. Evidence is durable, owned, redacted/bounded diagnostic material correlated to anomalies. Enabling one MUST NOT implicitly enable the other.
