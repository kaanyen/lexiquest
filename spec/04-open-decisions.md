# 4. Open Decisions & Research Items

When something here is resolved, fill in **Resolution** and **Date**, then update the affected spec
section and the changelog in [README.md](README.md).

## Product / technical decisions

| ID | Decision | Affects | Resolution | Date |
|----|----------|---------|------------|------|
| D1 | Transport fallback order and retry/de-duplication logic (BLE → Wi-Fi → Internet, or teacher-configurable?) | §5, Epic 6 | | |
| D2 | Target platforms: Android-only, or Android + iOS? | §2 | | |
| D3 | Minimum device baseline: Android API level and RAM floor | §2, §8, C4.6 | | |
| D4 | Target LMS: Moodle / Canvas (LTI 1.3) vs. Google Classroom (REST) vs. other | §6d, Epic 8, Epic 14 | | |
| D5 | Performance targets: cold-start, clustering latency, battery per session | §8, C4.6 | | |
| D6 | Draft placeholder plain-language cluster explanations now, or wait for research? | §6c, US-9.3 | | |
| D7 | At-rest encryption: SQLCipher vs. MMKV built-in, plus key storage/derivation | §7a, US-8b.1 | | |
| D8 | Guardian sign-off: conditional on LMS (§3 Step 4) or mandatory for every child (C2 failure analysis)? | §3, Epic 1 | | |
| D9 | Thresholds for C4 metrics: clustering F1/ARI, ratio-variance delta, sync completeness | C4.1–C4.3 | | |
| D10 | Team name (currently "Group 6") | Spec header | | |

## Research items (from the backlog)

| ID | Question | Blocks | Status |
|----|----------|--------|--------|
| R1 | Evidence-based classroom accommodations per processing cluster, for multilingual low-resource classrooms | US-9.1 | Open |
| R2 | Concrete student-facing exercises per cluster; write vs. source | US-9.1, US-9.2 | Open |
| R3 | Credible, accessible teacher-facing platforms/resources (link vs. adapt) | US-9.2, US-9.4 | Open |
| R4 | Evidence-based remediation-minigame mechanics | Epic 12 | Deferred until Scope 2 stable |
| R5 | Content delivery: bundled at build time vs. fetched when online | US-9.4, Epic 12 | Open |
| R6 | Validated ADHD/dyscalculia task design for this age range and hardware | Epic 10 | Deferred until Scope 2 stable |
| R7 | Offline-verifiable credential scheme (signed JWT vs. self-signed certificates) | US-8b.1, US-8b.5 | Open |
| R8 | Credential revocation without guaranteed connectivity (e.g. TTL trade-off) | US-8b.6 | Open problem |
| S1 | React Native timing-precision spike: is touch/audio timing precise enough on budget Android? | C4.2, Epics 2–3 | Open |

Recommendation from the backlog: run R1–R3 as a single spike, since together they unblock Epic 9.
