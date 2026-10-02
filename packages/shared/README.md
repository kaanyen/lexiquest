# packages/shared: Contracts shared by both apps

Defines things once so the student app and LexiLens can't drift apart.

| Folder | Contents |
|--------|----------|
| `src/telemetry/` | Telemetry schema: the field contract in [spec §6e](../../spec/03-system-spec.md#6e-telemetry-field-reference) |
| `src/ratios/` | Hardware-invariant ratio definitions (e.g. R = RT_directional / RT_neutral) |
| `src/sync-protocol/` | Payload envelope, chunking (512-byte MTU), session IDs for de-duplication |
| `src/crypto/` | Payload encryption and org credential verification helpers (pending R7) |
