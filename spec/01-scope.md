# 1. Scope

> Milestone M1 · Section B of the M1 technical report.

## B1. What LexiQuest is

LexiQuest is an offline-first React Native app that screens for dyslexia risk in primary-school-aged
children who can already read, across under-resourced African classrooms where clinical assessors
are scarce, and children often learn in English as a second or third language. Its users are
**students**, who complete a short gamified quiz battery on shared classroom tablets, and
**teachers**, who view results through a companion dashboard, **LexiLens**.

The learned component is an unsupervised clustering model trained on device telemetry and
hardware-invariant behavioural ratios that cancel out device-speed differences (e.g. click timing,
accuracy, and error patterns). The model places each child's processing profile against a normative
baseline. Everything else is deterministic software around that output: quiz UI, telemetry, offline
device-to-device sync, PDF export, bundled practice games, and, only when a connection exists, LMS
syncing. Screening and its explanations always work offline; connectivity only extends what the
app can do.

Success means a teacher walks away with a concrete, non-stigmatising accommodation the same day.
When the system errs, the child bears the cost. A false positive risks labelling; a false negative
leaves a real difficulty unsupported, so the app outputs **descriptive processing cards, never
diagnostic verdicts**.

### Key features

All Level 1 features and most of Level 2 are fully offline; only the items marked "online" need a
connection.

**Level 1 (Core): thin vertical slice, Week 7**

| Feature | Description | Learned? |
|---------|-------------|----------|
| Single quiz module | One gamified mini-quiz (React Native + TS), playable end-to-end | No |
| Telemetry logging | Background capture of click timestamps and accuracy | No |
| Local session storage | On-device storage (SQLite/MMKV), no internet needed | No |
| Teacher PIN gate | Separates student play mode from teacher view | No |
| Baseline z-score | One hardware-invariant ratio standardised against a bundled baseline | Yes (fixed stats) |

**Level 2 (Extended): full system, final demo**

| Feature | Description | Learned? |
|---------|-------------|----------|
| Full quiz battery | All four dyslexia-construct modules | No |
| Full ratio pipeline | All ratios computed and z-scored against baseline | Yes (fixed stats) |
| Unsupervised clustering | On-device k-means/GMM places a child into a processing cluster | Yes |
| Hub-and-spoke sync | Local Wi-Fi/QR transfer to teacher device, offline | No |
| LexiLens dashboard | Maps a cluster to a plain-language accommodation | Yes (consumes output) |
| PDF export | Printable per-child accommodation summary | No |
| Practice game library | Bundled games pre-tagged to processing dimensions | No |
| LMS result sync (online) | Best-effort push of a session summary via LTI AGS | No |

**Level 3 (Stretch): ambitious, may not fully reach**

| Feature | Description | Learned? |
|---------|-------------|----------|
| Multi-disorder screening | Adds ADHD and dyscalculia via an ensemble | Yes |
| Supervised classifier | Trained on a small diagnosed cohort (10–20 students) | Yes |
| Adaptive lesson plans (partly online) | Personalised homework packet from a content bank | Yes |
| Teacher content uploads (online) | Uploaded worksheets auto-tagged to clusters | Partly |
| Full LMS integration (online) | Embedded LTI launch and roster import | No |
| Multi-language localisation | Audio/text/games in regional languages | No |

## B2. Why machine learning (and when it isn't)

A human-only alternative would be an educational psychologist assessing each child individually.
In Ghana there are few such psychologists assessing children, especially in public primary
schools, so most children go undiagnosed. A rule-based approach would set a fixed cut-off on each
behavioural ratio. A statistical approach improves on this by comparing each ratio with a normative
baseline for the child's age, which is exactly what the Level 1 z-score approach does. It is cheap
and needs no training, so it is a valid **fallback** for every Level 2 feature.

ML is needed because dyslexia risk is not one slowness ratio but a combination: a child can be slow
but accurate on visual search and fast but error-prone on auditory tasks. Per-ratio cut-offs treat
each measure separately and miss these profiles. With no diagnosed local data, a supervised model
is ruled out, so we hypothesise that unsupervised clustering can group children into processing
profiles without local labels.

**This is a hypothesis, not a result.** Demographics alone reach F1 = 0.64 on the source dataset,
so clustering must beat both that and per-ratio z-scores on the same label before we claim it adds
anything. If it doesn't, the finding is that LexiQuest needs statistics, not ML.

## B3. What the system outputs

A quest produces raw telemetry (response times, accuracy, misses, replays). On the teacher's device,
LexiLens turns these into behavioural ratios, compares them against age baselines, and assigns the
child to a processing profile. The teacher sees a **processing card**: a short description of how
the child performed relative to peers plus one or two classroom accommodations. The child only sees
"Quest Complete", so no child is labelled in front of classmates. The teacher decides whether to
act, which accommodations to use, and whether to refer for further assessment. A wrong card should
cost a few minutes of teacher attention, never a child's reputation.

The card changes form with confidence rather than showing a probability. **No card ever shows a
percentage or confidence bar.**

| Situation | What the teacher sees |
|-----------|-----------------------|
| Clear profile, child close to a cluster centre | Full card with a description and accommodations |
| Weak profile, child between clusters or near the baseline | "No clear pattern" card with general good-practice tips |
| Unusable session: incomplete, too fast to be genuine, or outside the age range | "Could not read this session" card with the reason and a prompt to replay |

## B4. Success criteria

| Layer | Criterion | Target | Source of numbers |
|-------|-----------|--------|-------------------|
| Organisational | Children with processing difficulties receive classroom support early | Pilot schools adopt LexiQuest for a full term | Team objectives |
| Leading indicator | Teachers keep using the system | ≥ 70% of pilot teachers screen a second class within a month | Team assumption, revised after pilot |
| User outcome | Teachers act on cards the same day | ≥ 60% of profile cards lead to a recorded accommodation the same day | Our definition of success |
| User outcome guardrail | No child is labelled in front of others | No results shown on the student interface | Team objectives |
| Model | Profiles separate diagnosed from control children | F1 ≥ 0.70 **and** above the demographics-only F1 of 0.64 on the same split | Rauschenberger's 0.75–0.77 as ceiling, our re-analysis as floor |
| Model guardrail | Ratios are stable across devices | Ratio variance across simulated device tiers < raw-timestamp variance | Our platform-agnostic claim |

Each link can break independently: a better F1 means nothing if teachers find the accommodations
impractical. User outcomes will be checked once the pilot runs, not assumed from model metrics.

## B5. Failure policy

| Condition | Behaviour | Who absorbs the work |
|-----------|-----------|----------------------|
| Session incomplete or responses implausibly fast | **Decline**: "could not read this session" with reason | Teacher replays with the child |
| Child outside supported age brackets or not yet reading | **Decline**: screening not available | Teacher |
| Child sits between clusters | **Defer**: "no clear pattern" card with tips and a re-screen prompt | Teacher observes and re-screens later |
| Too few children screened for local recalibration | **Degrade**: card marked "compared with the starter baseline" | Nobody |
| Hub unreachable during a session | **Degrade**: store on device, sync later | Nobody |
| Card would be exported to the LMS | **Raise the threshold**: only clear profiles leave the device, only with guardian permission | Teacher and guardian |

Thresholds follow from relative cost. Let *p* be the probability a child belongs to an at-risk
profile. In a private class, a false positive costs an unneeded accommodation (C_fp = 1); a false
negative costs a term without support (C_fn = 10). Acting is worthwhile when
p > C_fp / (C_fp + C_fn) = 1/11 ≈ 0.09. Adding a deferral cost of 0.5 (teacher re-screens) gives
three regions:

| p | Output |
|---|--------|
| < 0.05 | No card |
| 0.05 – 0.50 | "No clear pattern" card |
| > 0.50 | Full card |

## B6. Who bears the cost, and recourse

Students bear the most cost when the system is wrong: a false positive could get a child treated as
slow, especially if revealed to peers; a false negative leaves real difficulties unsupported.
Teachers bear the time cost of acting on cards, re-screening, and handling parent questions.

| Individual | Recourse | How |
|------------|----------|-----|
| Child | A bad session never becomes a profile | Unusable sessions are declined |
| Teacher | Can dismiss or override any card | Dismiss/override controls on every card |
| Guardian | Must consent before screening | School requests consent before any student uses LexiQuest |

LexiQuest only provides a profile; the teacher decides, so no decision about a child is fully
automated. Student data is protected under Ghana's Data Protection Act, 2012 (Act 843), so guardian
consent is required before screening.

## B7. Out of scope

| Out of scope | Reason |
|--------------|--------|
| Diagnosing dyslexia or any condition | Diagnosis needs a qualified professional; a wrong label harms a child far more than a wrong accommodation |
| Screening children who cannot read yet | Questions and baseline assume basic reading |
| Using results for grading, placement, or admission | The cost of a false positive becomes high and lasting |
| ADHD and dyscalculia screening | Require different validated tasks and baselines |
| Local languages | Adds substantial complexity; Ghanaian school curriculum is in English |
