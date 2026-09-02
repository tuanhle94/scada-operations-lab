# Project 2: Ignition Design Challenge

Do **not** start this until [Part 8](portfolio-part-8.md) of Project 1 (`PipelineOps`) is packaged.

Official tasks: [Ignition Design Challenge](https://training.inductiveautomation.com/ignition-design-challenge/)

## Cheat Sheet

- This is a **second Ignition project**, not more screens inside `PipelineOps`.
- The challenge is Inductive Automation’s independent Designer training app (Rio Oso coffee roasters).
- Project 1 is the energy-interview portfolio (monitor only). Project 2 is extra **Designer practice** (their spec includes **control**).
- Create a new Designer project named `RioOso`. Do not rename or extend `PipelineOps` to match their brew-station story.
- Maker Edition has **no Vision**. Do Gateway + Perspective. Skip Vision. Try Reports only if the Reporting / SQL Bridge modules are actually installed.

## Why it exists

Project 1 proves a pipeline SCADA path: tags, history, alarms, UDTs, failures, .NET, containment. Interviewers for energy roles care about that story.

The Design Challenge drills Gateway connections, a 20-instance UDT, parameterized views, popups, security levels, alarm notification pipelines, and (if modules exist) reports. Those are useful Designer hours **after** the portfolio is demoable. Doing the challenge first would delay the hireable work. Mixing it into `PipelineOps` would add pump **control**, which Project 1 forbids.

## When

| Gate | Action |
|---|---|
| Project 1 Parts 1–8 incomplete | Ignore this file |
| Part 8 demo packaged | Start Project 2 in a **new** Ignition project |
| Challenge self-assessment done | Write [interview-concepts-project-2.md](interview-concepts-project-2.md) as completed, keep the two projects distinct in interviews |

## How

1. Open the [challenge overview](https://training.inductiveautomation.com/ignition-design-challenge/challenge-overview/).
2. Complete **Gateway and Project** first ([connections and tags](https://training.inductiveautomation.com/ignition-design-challenge/gateway-and-project/connections-and-tags/), [security and alarms](https://training.inductiveautomation.com/ignition-design-challenge/gateway-and-project/security-and-alarms/)).
3. Complete **Perspective** ([station](https://training.inductiveautomation.com/ignition-design-challenge/perspective/station-view/), [overview](https://training.inductiveautomation.com/ignition-design-challenge/perspective/overview-view/), [popup](https://training.inductiveautomation.com/ignition-design-challenge/perspective/popup-view/), [security](https://training.inductiveautomation.com/ignition-design-challenge/perspective/security/), [alarms](https://training.inductiveautomation.com/ignition-design-challenge/perspective/alarms-view/)).
4. Skip **Vision** on Maker.
5. Optionally do [Reports](https://training.inductiveautomation.com/ignition-design-challenge/reports/) if the modules exist.
6. Use their [self-assessment guide](https://training.inductiveautomation.com/ignition-design-challenge/self-assessment-guide/).
7. Keep evidence under `docs/evidence/project-2/` in this repo, or a separate `rio-oso-design-challenge` folder. Do not overwrite Project 1 screenshots.

The challenge wants MySQL or MSSQL. A local MySQL container is fine. Do not point `RioOso` at the `PipelineOps` historian as if they were one plant.

## Do not

- Add brew-station start/stop or setpoint writes to `PipelineOps`.
- Claim in interviews that the pipeline lab “is” the Design Challenge.
- Pause Part 2–8 of Project 1 to work on Rio Oso.

## Interview line (after both exist)

> I built a pipeline-operations monitoring application in Ignition as the portfolio. Separately I completed Inductive Automation’s Design Challenge as Designer practice. That exercise includes operator control of simulated machines. The pipeline project does not; control stays out of that HMI.

## Checklist (start only after Project 1 Part 8)

- [ ] Project 1 Part 8 is complete.
- [ ] New Ignition project `RioOso` created (not `PipelineOps`).
- [ ] Gateway and Project section done.
- [ ] Perspective section done.
- [ ] Vision skipped (Maker) or completed on a license that includes it.
- [ ] Reports attempted or explicitly skipped with a one-line reason.
- [ ] Self-assessment reviewed.
- [ ] Interview cards updated so the two projects are not blended.
