# apps/lexilens: Teacher dashboard (hub)

React Native + TypeScript app on the teacher's device. Receives telemetry, scores it, clusters it,
and turns it into processing cards. Core features work fully offline.
Spec: [03-system-spec.md §6–7](../../spec/03-system-spec.md).

| Folder | Responsibility | Epic |
|--------|----------------|------|
| `src/auth/` | Teacher PIN, individual accounts, org credential verification | 4, 8b |
| `src/ingest/` | Hub endpoints (BLE GATT server, HTTPS on :8080), relay pull, de-duplication | 6 |
| `src/baseline/` | Age-bracket priors, ratio computation, z-scores, N ≥ 20 recalibration | 7 |
| `src/clustering/` | On-device k-means/GMM | 7 |
| `src/cards/` | Processing card / "no clear pattern" / "could not read this session" | 7 |
| `src/export/` | PDF summary, CSV raw export | 7 |
| `src/teacher-support/` | Plain-language explanations, exercises, resources (partly blocked on R1–R3) | 9 |
| `src/lms/` | Online-only LMS result sync (target LMS pending D4) | 8 |
