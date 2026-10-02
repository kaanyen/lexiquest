# apps/lexiquest: Student app (spoke)

React Native + TypeScript app that runs on the shared classroom tablet. Works fully offline.
Spec: [03-system-spec.md §2–5](../../spec/03-system-spec.md).

| Folder | Responsibility | Epic |
|--------|----------------|------|
| `src/consent-intake/` | Consent, demographic intake, standardisation warnings, guardian sign-off | 1 |
| `src/battery/visual/` | 16-round visual search (8 themes × 2×2 / 3×3 grids) | 2 |
| `src/battery/auditory/` | 16-stage auditory sequential memory | 3 |
| `src/session-lock/` | "Quest Complete" screen + teacher PIN lock | 4 |
| `src/storage/` | Encrypted local telemetry persistence (SQLite/MMKV) | 5, 8b |
| `src/sync/` | Push telemetry to the hub over BLE / Wi-Fi / relay | 6 |
| `assets/audio/` | 16-bit / 22.05 kHz mono WAV cues (counts toward the 30 MB budget) | 3, 5 |

**Invariant:** nothing in this app ever renders a score, z-score, cluster, or card.
