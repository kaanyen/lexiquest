# LexiQuest

A language-agnostic cognitive screener. LexiQuest is an offline-first React Native app that screens
for dyslexia risk in primary-school children across under-resourced African classrooms. Students
play a short gamified battery on a shared tablet; teachers get plain-language processing cards and
same-day classroom accommodations through the **LexiLens** dashboard. It describes processing
profiles. It never diagnoses.

**Team (Group 6):** Michael Kwabena Sylvester · Nana Yaw Adjei Koranteng · Kweku-Abeiku Attah Anyen · Chelsea Owusu

## Specification

The scope and architecture specification lives in [`spec/`](spec/). Start with
[`spec/README.md`](spec/README.md). Later milestones amend it in place.

## Repository layout

```
apps/
  lexiquest/      Student app (spoke): intake, screening battery, lock screen, local storage, sync
  lexilens/       Teacher dashboard (hub): ingest, baseline, clustering, cards, exports
packages/
  shared/         Telemetry schema, ratios, sync protocol, crypto shared by both apps
ml/               Python analysis, measurement stub, bundled priors (data is never committed)
relay/            Optional online cloud relay + credential issuance
spec/             Scope & architecture specification
docs/             Working notes, spikes, test plans
```

## Status

Week 1: specification and scaffold only. No application code yet.

## Invariants

- **Offline-first:** intake, screening, storage, clustering, and cards work with zero connectivity.
- **Privacy:** no diagnostic content, z-score, or cluster label is ever shown on the student device.
- **No child data in git:** datasets and session telemetry stay out of the repository.
