# Pipeline Operations SCADA Lab

This project demonstrates a small pipeline-operations Supervisory Control and Data Acquisition (SCADA) application for monitoring simulated pump equipment. Equipment control stays outside the application.

## Current Status

Part 3 is complete: the first vertical slice is packaged for review.

- Live value, Postgres history, trend, and alarm lifecycle (Parts 1–2)
- Evidence screenshots in [`docs/evidence/part-3/`](docs/evidence/part-3/)
- Gateway backup in [`backups/`](backups/)
- Real failures documented in [`docs/evidence/part-3/problems-solved.md`](docs/evidence/part-3/problems-solved.md)

**Next:** [Part 4](docs/portfolio-part-4.md) — Pump UDT, P-101 and P-102, overview, navigation.

## Working with Codex

Repository instructions live in [`AGENTS.md`](AGENTS.md), migrated from the retired Cursor rules. They preserve click-by-click Ignition guidance, the build order, and interview-concept notes for each part. Start a new Codex session in this repository to load them automatically. See [Codex instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md).

## Build order

Follow [docs/build-order.md](docs/build-order.md). Do not skip ahead.

| Part | Outcome |
|---|---|
| 1 | Live bearing temperature in the browser |
| 2 | History, trend, alarm lifecycle |
| 3 | Screenshots, Gateway backup, one written problem |
| 4 | Pump UDT, P-101 and P-102, overview, navigation |
| 5 | Stale, communication loss, alarm flood |
| 6 | .NET maintenance API on the pump screen |
| 7 | Optional-service failures contained |
| 8 | Five-minute demo and interview packaging |

After Part 8: [Project 2 — Ignition Design Challenge](docs/project-2-ignition-design-challenge.md) (separate Ignition project, not an extension of PipelineOps).

Beside the build (PLC literacy, MQTT, OT security, Core exam): [docs/adjacent-skills.md](docs/adjacent-skills.md).
