# 3. System Specification (Preliminary)

> Milestone M1 · Spec Sheet. Items marked **[DECISION]** or **[RESEARCH]** are tracked in
> [04-open-decisions.md](04-open-decisions.md). Don't build against a blocked item as written.

**Project:** LexiQuest, a language-agnostic cognitive screener
**Team:** Group 6
**Target environment:** primary-school classrooms across under-resourced African environments:
shared tablets, English as L2/L3, limited clinical assessors, intermittent or no internet.
**Core purpose:** early, non-stigmatising cognitive screening for dyslexia risk via gamified
behavioural telemetry, returning actionable same-day teacher accommodations rather than clinical
diagnoses.

## 1. Architecture overview

See [02-architecture.md §C1](02-architecture.md#c1-system-architecture) and
[Fig. 1](diagrams/fig1-architecture.png).

## 2. Device runtime & privacy gates

- **Framework:** React Native + TypeScript.
- **Offline persistence:** SQLite/MMKV local engine retaining all raw session telemetry.
- **Student privacy & screen locking:**
  - In-game motivational feedback (points, star animations, sound effects) during active play.
  - **Zero result exposure:** diagnostic indicators, z-scores, processing cards, and accommodation
    details are never rendered to the student, in any state.
  - **Session termination:** after round 32, show "Quest Complete! Hand tablet back to teacher" and
    immediately lock behind the 4-digit teacher PIN.
- **Storage footprint:** total bundled app ≤ 30 MB, including optimised 16-bit / 22.05 kHz mono WAV
  acoustic assets.
- **Target platforms:** **[DECISION D2]** Android only, or Android + iOS. Budget shared tablets
  suggest Android-only.
- **Minimum OS / device baseline:** **[DECISION D3]** minimum Android API level and RAM, giving a
  concrete floor to test against.

## 3. Intake flow, compliance & privacy gates

Screening cannot start until intake is complete.

1. **Pre-game privacy & local processing consent.** On-device statement ("All gameplay, acoustic
   telemetry, and timing information collected during this screening are stored entirely on this
   physical tablet...") with a mandatory checkbox.
2. **Demographic intake:** age (5–18, discrete); gender (female/male); dyslexia status (No /
   Yes, diagnosed / Probably, Yes / Not known); linguistic background (number of languages,
   free-text language tags, native English yes/no); educational environment (school type,
   class/year, English academic grade).
3. **Study standardisation & acoustic warnings (non-skippable):** dedicated test device, no
   spectators, volume/earphone check, continuous play, no undo on trial responses.
4. **Guardian sign-off for LMS sync (conditional):** triggered only when LMS integration is bound or
   online sync is activated. *Note: the architecture failure analysis
   ([C2](02-architecture.md#failure-modes)) recommends making this mandatory for every child.
   See **[DECISION D8]**.*

## 4. Screening battery (32 rounds)

### Visual battery: 8 themes × 2 rounds = 16 rounds

- Themes: symbol, z, shape, face, fruit, kitchen, plant, animal.
- Round 1 = 2×2 grid; round 2 = 3×3 grid.
- Protocol: 3-second unskippable target preview → interactive grid (1 target + N−1 distractors) →
  15 seconds to tap the target as many times as possible → grid reshuffles on **every** tap (hit or
  miss).

### Auditory battery: 16 stages

- Themes: phonemic discrimination (1–4), auditory confusion (5–8), substitution/omission (9–12),
  rhythm/metre (13–16).
- 4 horizontal buttons; **Button 1 never holds the target.**
- Protocol:
  1. Unconstrained target rehearsal (child-paced, "Continue" to proceed).
  2. Locked sequential playback, left to right, Button 1 → 2 → 3 → 4, with a visual highlight per
     sound.
  3. Decision phase: buttons unlock; "play all sounds again" is available, but tapping any button
     1–4 submits immediately (no individual previews).

## 5. Raw telemetry transfer (hub-and-spoke)

Three transport channels. Fallback order and retry logic: **[DECISION D1]**.

| Channel | Mechanism | Security |
|---------|-----------|----------|
| Bluetooth LE | Teacher device advertises `LEXI_HUB_SERVICE` as a GATT server; student tablet pairs without OS prompts and pushes telemetry JSON chunked into 512-byte MTU characteristics | Link-layer **and** app-layer payload encryption (raw characteristic writes are not encrypted by default) |
| Local Wi-Fi / P2P hotspot | Teacher tablet hosts a hotspot or joins the classroom subnet; student discovers it via mDNS `_lexiquest._tcp.local` and POSTs to `https://<hub_ip>:8080/api/telemetry` | HTTPS with a device-pinned / self-signed certificate. *(The original draft said `http://`; that was a bug, since anyone on the classroom Wi-Fi could intercept telemetry.)* |
| Internet (opportunistic) | Student tablet POSTs de-identified JSON to the secure cloud relay (`POST /v1/telemetry/push`); LexiLens pulls it down | HTTPS |

All three channels must verify the receiving device belongs to the same organisation before
accepting a payload (§7).

## 6. LexiLens dashboard: outputs & teacher support

### 6a. Baseline recalibration & clustering (established)

- **Age brackets:** 5–7, 8–11, 12–18, each with a bundled prior (μ, σ). Once a bracket's local sample
  reaches N ≥ 20, the hub recalibrates μ_local / σ_local and retroactively re-scores earlier records
  in that bracket.
- **Hardware-invariant ratio:** R = RT_directional / RT_neutral, cancelling device touch-sensor
  delay and throttling.
- **On-device unsupervised clustering** (k-means/GMM) maps feature vectors to descriptive processing
  clusters.

### 6b. Analysis outputs (established)

- Plain-language descriptive processing card, per student.
- Same-day classroom accommodation suggestion, per student.
- PDF summary export.
- Raw CSV telemetry export.

### 6c. Teacher support & education layer (preliminary; needs research)

This describes intent, not settled specification. It exists so the gap is visible.

| Item | Status |
|------|--------|
| Per-student help: 1–2 concrete exercises linked to each student's cluster | **[RESEARCH R1, R2]** content source and evidence base |
| General advice for supporting students with learning disabilities | **[RESEARCH R2, R3]** original vs. curated content |
| Teacher education: plain-language explanations of what a cluster/profile means | Achievable now; presentation of existing Epic 7 output. **[DECISION D6]** whether to draft now |
| Links to external platforms/resources | **[RESEARCH R3, R5]** no candidate sources yet; bundled vs. fetched delivery |

### 6d. LMS result sync (established, online-only)

- Best-effort push of the session summary to the school's LMS gradebook via LTI Assignment and Grade
  Services (AGS), when a connection is available.
- Target LMS: **[DECISION D4]**. Moodle/Canvas (LTI 1.3) and Google Classroom (its own REST API,
  not LTI) need different integration work.

### 6e. Telemetry field reference

This is the **schema contract** between capture (Epics 2/3) and analysis (Epic 7). It is defined
once in [`packages/shared`](../packages/shared) and referenced everywhere.

Derived from the DGamesDataSet CSV and its feature dictionary, cross-checked with an Extra Trees
feature-importance run on the 137-participant data. **Caution:** demographic/technical metadata
(language, browser, device, class level) scored highest in a naive full-feature run (F1 0.64 alone
vs. 0.30 for all gameplay telemetry combined). This is very likely a recruitment-channel confound,
not a signal that transfers to our deployment. The ratings below come from the behavioural-only
analysis.

**Auditory fields (per stage, 16 stages)**

| Field | Description | Relevance |
|-------|-------------|-----------|
| `mus_introtime` | Duration of the round | High |
| `mus_thinktime` | Time from buttons unlocking to the child's choice (hesitation) | High |
| `mus_pressedtargetmelody` | Replays of the target cue during rehearsal | High |
| `mus_hits` / `mus_misses` | Correct/incorrect on this round | High |
| `mus_sumhits` / `mus_summisses` | Running totals across prior rounds | High |
| `mus_element` | Which cue was clicked: target vs. distractor 1/2/3 | High |
| `mus_button_id` | Position of the clicked button | High |
| `mus_amountnotunderstood` | Replays of the instructions before starting | Low; candidate to drop |

**Visual fields (per round, 16 rounds)**

| Field | Description | Relevance |
|-------|-------------|-----------|
| `vis_clicktime_one` … `vis_clicktime_six` | Duration between consecutive clicks (pacing / click-interval ratios) | High (all six) |
| `vis_lastclicktime` | Time of the final click in the round | High |
| `vis_totalclicks` | Total clicks in the round | High |
| `vis_hits` / `vis_misses` | Correct/incorrect clicks | High |
| `vis_hit_devided_totalclicks` | Accuracy ratio (hits ÷ total clicks) | High |
| `vis_distr1` / `vis_distr2` / `vis_distr3` | Clicks on each distractor (mirror-confusion signal) | Medium; theoretically important despite a lower score on this small sample |
| `vis_multi_totalclickshits` | "Effect" = hits × total clicks | Medium |
| `vis_efficient_lastclick_hits` | Efficiency ratio (last-click time ÷ hits) | **Data-quality flag:** corrupted in the source CSV (locale number-formatting bug). Compute from `vis_lastclicktime / vis_hits`; never persist or trust the exported column |

**Demographic/technical fields** are captured once at intake (§3), not per round.

## 7. Security & access control (preliminary; needs decisions)

The original spec had no data-protection or identity model beyond a shared 4-digit PIN, which only
gates UI navigation and does nothing to protect the data itself.

### 7a. Data at rest

Telemetry in SQLite/MMKV must be **encrypted at rest**, not just PIN-gated.
**[DECISION D7]** SQLCipher vs. MMKV built-in encryption, and where the key is stored/derived.

### 7b. Transport

- BLE: link-layer or app-layer payload encryption (§5).
- Local Wi-Fi: HTTPS with a device-pinned or self-signed certificate (§5).
- Internet: HTTPS (correct as specified).

### 7c. Teacher identity: individual + organisation

The PIN identifies "a teacher is present", not which teacher, and has no concept of organisation.
Intended model:

- **Individual teacher accounts**, so actions/results are attributable to a person.
- **Organisation-scoped sharing:** hub-and-spoke and LMS sync succeed only between devices/accounts
  of the same organisation (school).
- **Two-part authentication** (offline-first):
  - *One-time enrolment*, online or admin-assisted (QR / local transfer), issuing a signed credential
    tied to the organisation.
  - *Offline verification* thereafter: every local sync checks that credential locally.
- **[RESEARCH R7]** exact credential scheme (signed JWT vs. lightweight self-signed certificates)
  for long offline periods.
- **[RESEARCH R8, open problem]** revocation. A lost/former teacher's device can only learn it is
  revoked once it reconnects; until then its cached credential stays valid. Needs an explicit risk
  trade-off (e.g. credential TTL forcing periodic re-validation), not a silent gap.

### 7d. What does and doesn't need internet

| Concern | Needs internet? |
|---------|-----------------|
| Encrypting data already on a device | No |
| Encrypting BLE / local Wi-Fi transport between nearby devices | No |
| Initial teacher/organisation enrolment | Yes, once (or admin-assisted offline provisioning) |
| Day-to-day org-scoped sync verification after enrolment | No |
| Revoking a lost/former teacher's access | Yes, and only once that device reconnects. **Open problem** |

## 8. Non-functional requirements

- **Offline-first invariant:** intake, screening, session lock, storage, clustering, and card
  generation (§2–6b) work with zero connectivity, permanently. Internet-dependent features degrade
  to "unavailable", never to a broken core flow.
- **Privacy invariant:** no diagnostic content, z-score, or cluster label is ever rendered on the
  student device, in any state. Needs a dedicated test/QA pass.
- **Storage budget:** app ≤ 30 MB, enforced by a CI check.
- **Transport resilience:** sync tolerates partial/interrupted transfers without loss or duplication.
- **Performance targets:** **[DECISION D5]** cold-start time, clustering latency, battery drain per
  session.

## 9. Scope 3 (stretch), preliminary only

| Feature | Description | Status |
|---------|-------------|--------|
| Multi-disorder screening ensemble | ADHD (SART) and dyscalculia (numerical Stroop) modules, multi-head ensemble | **[RESEARCH R6]** no validated dataset/task design for this age range/hardware |
| Supervised small-cohort classifier | SVC/Random Forest via ONNX Runtime Mobile, trained on a 10–20 student diagnosed cohort | Technically scoped; awaiting a diagnosed cohort |
| Adaptive homework & minigames | Personalised practice sheets and remediation minigames from cluster profile | **[RESEARCH R4]** evidence base for remediation mechanics |
| Teacher worksheet auto-tagger | OCR + readability/layout analysis to tag worksheets to clusters | Low priority / high risk; not committed |
| Deep LMS integration | Full LTI 1.3 Advantage launch, roster import, gradebook sync | Depends on D4 |
| Regional multi-language localisation | Native audio, translated UI, local visual metaphors | Not committed this cycle |
