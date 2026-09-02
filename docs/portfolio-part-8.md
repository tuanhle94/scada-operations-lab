# Portfolio Part 8: Five-Minute Demo and Packaging

Do not start this until [Part 7](portfolio-part-7.md) is complete. Do not wait for optional AI or cloud work.

## Cheat Sheet

- **Done means this:** a reviewer can understand the problem, see screenshots, hear a five-minute walkthrough, and inspect the repo without your private context.
- Record a **three-to-five-minute** demo. Practice a **five-minute verbal** version for interviews.

## What

Part 8 is interview packaging. The product is already built. This part makes it inspectable.

## Why

Hiring loops are short. If you cannot show value → trend → alarm → failure → maintenance boundary in five minutes, the rest of the repo will not get read.

## How

Demo script (practice until it fits five minutes):

1. Problem: pipeline pump monitoring, supervisory only, no equipment control.
2. Live `/pump-p101` (or overview → P-101): changing bearing temperature, tag path, quality.
3. Trend: history in Postgres.
4. Drive high temperature: alarm active, ack with comment, clear, journal.
5. One failure: stale or comms loss vs a process alarm.
6. Maintenance event via .NET, visible on the pump screen. API down does not kill the HMI.
7. Stop. Say what you would not put in Ignition (safety logic, PLC control, AI ack).

Also:

1. Final README: what it is, how to run locally, what is simulated, limitations.
2. Architecture diagram.
3. Screenshots of overview, pump, alarms, trend, maintenance, one failure.
4. Link to backups and evidence folders.
5. Known limitations (Maker Edition, simulated I/O, local only).
6. No secrets.

## Checklist

- [ ] README a stranger can follow.
- [ ] Architecture diagram.
- [ ] Local setup and reset instructions.
- [ ] Screenshots of every major screen.
- [ ] Three-to-five-minute demo video.
- [ ] Scenario evidence from Part 5 linked from the README.
- [ ] Limitations and future work.
- [ ] Secrets removed.
- [ ] Five-minute verbal walkthrough practiced.
- [ ] Fill [interview-concepts-part-8.md](interview-concepts-part-8.md) with the spoken demo script.

After this, the core portfolio is packageable. Optional AI or cloud is [portfolio-project.md](portfolio-project.md) extensions only.

Designer practice after packaging: [Project 2 — Ignition Design Challenge](project-2-ignition-design-challenge.md). Do not start it before this checklist is done.
