# Research sources

Research inputs are evidence/oracles, not runtime dependencies and not automatic authorities.

Current source classes include:

- real PangYa client traffic and sanitized packet captures;
- observed ProjectG client behavior and available original/decompiled material;
- [PangDox](https://packets.pangdox.com/);
- PangCrypt and similar public protocol libraries;
- public PangYa protocol/emulator implementations;
- independently generated golden vectors and replay fixtures.

For every committed fixture or protocol claim, prefer recording provenance close to the test data rather than relying on this generic list.

When sources disagree, document the disagreement and the evidence used to resolve it. Do not silently select the most convenient implementation.
