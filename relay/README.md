# relay: Secure cloud relay (online-only, optional)

Receives de-identified telemetry from student tablets when internet is available
(`POST /v1/telemetry/push`) and lets LexiLens pull it down. Also the likely home of org enrolment /
credential issuance. Scope 2 "Should" priority; built after BLE and Wi-Fi sync.
Spec: [03-system-spec.md §5, §7](../spec/03-system-spec.md).
