# LexiQuest Specification

This directory holds the scope and architecture specification for LexiQuest. Later milestones
**amend** these documents rather than replacing them: edit the relevant file in place and add a
line to the changelog below.

| File | Contents | Source |
|------|----------|--------|
| [01-scope.md](01-scope.md) | Problem framing, key features (Levels 1–3), why ML, outputs, success criteria, failure policy, recourse, out of scope | M1 report, Section B |
| [02-architecture.md](02-architecture.md) | System architecture, interfaces, failure modes, data, measurement plan, misuse, framing changes | M1 report, Section C |
| [03-system-spec.md](03-system-spec.md) | Device runtime, intake flow, screening battery, transport protocols, LexiLens outputs, telemetry fields, security, NFRs, stretch scope | Spec Sheet (preliminary) |
| [04-open-decisions.md](04-open-decisions.md) | Every unresolved decision and research item in one place | Spec Sheet + Backlog |
| [diagrams/](diagrams/) | Architecture diagram (Fig. 1) | M1 report |

**Team:** Group 6: Michael Kwabena Sylvester, Nana Yaw Adjei Koranteng, Kweku-Abeiku Attah Anyen, Chelsea Owusu

## Amendment rules

1. Change the spec in the same PR as the code that depends on the change, when possible.
2. Never delete a superseded decision silently. Strike it through or move it to a "Superseded" note with the milestone that changed it.
3. When an open decision in `04-open-decisions.md` is resolved, record the answer and date there, then update the affected section.

## Changelog

| Milestone | Date | Change |
|-----------|------|--------|
| M1 (Week 1) | 2026-10-02 | Initial scope (Section B), architecture (Section C), and preliminary system spec |
