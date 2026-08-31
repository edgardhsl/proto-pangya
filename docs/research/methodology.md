# Protocol research methodology

## Principle

No single reverse-engineered implementation is authoritative. Claims become supported behavior through reproducible evidence and cross-validation.

## Evidence hierarchy

When sources disagree, prefer roughly:

1. reproducible behavior from the intended real client/profile;
2. packet captures with known context;
3. independently reproduced original/decompiled behavior;
4. agreement among multiple independent implementations;
5. community packet documentation/databases;
6. assumptions inherited from legacy code.

This is guidance; technical analysis still decides conflicts.

## Verification states

Research should distinguish:

- `verified` — reproduced with strong evidence;
- `partial` — meaningful evidence exists but important behavior remains unresolved;
- `unknown` — insufficient evidence;
- `unsupported` — intentionally not implemented for the profile.

## Accepting behavior

Critical framing, crypto and compression behavior SHOULD have more than one validation form where practical: golden vector plus differential result, capture replay, or real-client confirmation.

## Unknowns

Unknown bytes/fields MUST remain explicitly unknown rather than receiving invented names or semantics. Record them in `unknowns.md` until evidence resolves them.
